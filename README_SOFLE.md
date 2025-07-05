# ZMK Sofle Keyboard Setup Guide

## Overview
This guide documents the process of setting up ZMK firmware for the Sofle split keyboard with Bluetooth functionality.

## Prerequisites
- Sofle split keyboard with ZMK-compatible microcontrollers
- Linux system with development tools
- USB cable for flashing firmware

## Setup Process

### 1. Repository Setup ✓
- Cloned ZMK Sofle configuration from: https://github.com/a741725193/zmk-sofle.git
- Repository contains:
  - Board definitions for "eyelash_sofle" variant
  - Keymap configurations
  - Build system files
  - Pre-configured layout files

### 2. Build Environment Setup ✓
Successfully installed required dependencies:
- cmake 4.0.3: Build system generator
- ninja 1.13.0: Fast build system
- west 1.4.0: Zephyr's meta-tool for project management

Commands used:
```bash
brew install cmake ninja pipx
pipx install west
```

Verification:
- west --version: v1.4.0 ✓
- cmake --version: 4.0.3 ✓
- ninja --version: 1.13.0 ✓

### 3. ZMK Workspace Setup ✓
Initialized ZMK workspace:
```bash
west init -l config
west update
```

### 4. Configuration Review ✓
Current configuration includes:
- **Bluetooth**: Enabled with extended power for better connectivity
- **Sleep Mode**: 1 hour idle timeout (CONFIG_ZMK_IDLE_SLEEP_TIMEOUT=3600000)
- **RGB Underglow**: WS2812 strip support with auto-off features
- **Rotary Encoder**: EC11 support for both halves
- **Pointing Device**: Mouse functionality enabled
- **Backlight**: LED backlight support
- **Debounce**: 8ms press/release debounce timing
- **NKRO**: Disabled for better compatibility

### 5. Build Environment Setup
Installed required dependencies:
- arm-none-eabi-gcc 15.1.0: ARM GCC toolchain ✓
- pyelftools: Python ELF tools ✓

### 6. GitHub Actions Setup ✅
Created automated build workflow:
- `.github/workflows/build.yml` - GitHub Actions workflow
- Builds both left and right firmware automatically
- Caches dependencies for faster builds
- Uploads firmware as artifacts

#### How to Use:
1. Push this repository to GitHub
2. Actions will automatically build firmware
3. Download `.uf2` files from Actions artifacts

#### Option B: Local Build
Requires additional Zephyr SDK setup:
```bash
# Install Zephyr SDK (requires additional setup)
# Build commands:
west build -s zmk/app -b eyelash_sofle_left
west build -s zmk/app -b eyelash_sofle_right
```

### 7. Current Build Configuration
From `build.yaml`:
- **Left Half**: eyelash_sofle_left with nice_view shield
- **Right Half**: eyelash_sofle_right with nice_view_custom shield  
- **Studio Version**: Left half with ZMK Studio support
- **Settings Reset**: Available for troubleshooting

### 8. Firmware Flashing Process
Once firmware is built, flash to both keyboard halves:

#### Prerequisites
- USB cable
- Keyboard in bootloader mode

#### Flashing Steps
1. **Enter Bootloader Mode**:
   - Connect keyboard via USB
   - Press and hold RESET button while plugging in
   - Or double-tap RESET button quickly

2. **Flash Firmware**:
   - Left half: Flash `eyelash_sofle_left.uf2`
   - Right half: Flash `eyelash_sofle_right.uf2`
   - Simply drag and drop .uf2 files to mounted drive

3. **Verify Connection**:
   - Both halves should appear in Bluetooth devices
   - Test key presses and split communication

#### Troubleshooting
- If connection fails, use settings reset build
- Check battery levels on both halves
- Verify both halves are paired correctly

### 9. Status
✅ Repository cloned and configured
✅ Build environment set up
✅ ZMK workspace initialized
✅ Configuration reviewed
🔄 Ready for firmware build and flash

## Configuration Files
- `config/eyelash_sofle.keymap` - Main keymap configuration
- `config/eyelash_sofle.conf` - Bluetooth and hardware configuration
- `build.yaml` - GitHub Actions build configuration

## Bluetooth Configuration
The firmware will include:
- Bluetooth Low Energy (BLE) support
- Split keyboard communication
- Battery level reporting
- Sleep mode for power saving

## Build Process
Will use west build system to compile firmware for:
- Left half (eyelash_sofle_left)
- Right half (eyelash_sofle_right)

## Flashing Process
Firmware will be flashed via USB to both keyboard halves using appropriate bootloader.

---
*Documentation updated: 2025-01-07*