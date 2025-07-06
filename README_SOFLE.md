# ZMK Sofle Keyboard Final Status

## Overview
This document summarizes the successful restoration of Bluetooth functionality for an Eyelash Sofle split keyboard that had stopped working.

## Hardware
- **Keyboard**: Eyelash Sofle split keyboard (from AliExpress)
- **MCU**: Nice!Nano v2 boards (nRF52840-based)
- **Original Issue**: Bluetooth connectivity broken, keyboard was detectable but authentication failed

## Solution Applied
Successfully reflashed ZMK firmware using GitHub Actions build system with the original working configuration.

## Final Working Configuration

### Repository Structure
- **Repository**: https://github.com/eramirez51/zmk-sofle.git
- **Board Config**: `boards/arm/eyelash_sofle/` (custom board definition)
- **Main Config**: `config/eyelash_sofle.conf` (original working settings)
- **Keymap**: `config/eyelash_sofle.keymap` (standard Sofle layout)
- **Build Config**: `build.yaml` (eyelash_sofle_left/right boards)

### Working Firmware Settings
From `config/eyelash_sofle.conf`:
```
# Sleep configuration
CONFIG_ZMK_IDLE_SLEEP_TIMEOUT=3600000
CONFIG_ZMK_SLEEP=y

# Hardware support
CONFIG_EC11=y                          # Rotary encoder
CONFIG_ZMK_RGB_UNDERGLOW=y             # RGB underglow
CONFIG_ZMK_EXT_POWER=y                 # External power management  
CONFIG_ZMK_POINTING=y                  # Mouse support
CONFIG_ZMK_BACKLIGHT=y                 # LED backlight

# Bluetooth & HID
CONFIG_BT_CTLR_TX_PWR_PLUS_8=y         # Higher Bluetooth power
CONFIG_ZMK_HID_REPORT_TYPE_HKRO=y      # HID over BLE
CONFIG_BT_GATT_ENFORCE_SUBSCRIPTION=n  # Better compatibility

# Performance tuning
CONFIG_ZMK_KSCAN_DEBOUNCE_PRESS_MS=8
CONFIG_ZMK_KSCAN_DEBOUNCE_RELEASE_MS=8
CONFIG_ZMK_HID_REPORT_TYPE_NKRO=n      # Disabled for compatibility
```

### GitHub Actions Build Process
```yaml
# Builds for both keyboard halves
- Left Half: west build -s zmk/app -b eyelash_sofle_left
- Right Half: west build -s zmk/app -b eyelash_sofle_right  
- Settings Reset: west build -s zmk/app -b nice_nano_v2 -- -DSHIELD=settings_reset
```

## Successful Resolution Process

### 1. Initial Diagnosis ✅
- Confirmed hardware functional via USB connection
- Identified Bluetooth authentication failures as root cause

### 2. Firmware Restoration ✅
- Used GitHub Actions to build fresh firmware
- Applied settings reset firmware to clear corrupted Bluetooth state
- Reflashed both halves with original working configuration

### 3. Split Communication Fix ✅
- Established proper split keyboard Bluetooth pairing sequence:
  1. Turn on RIGHT half first, wait 15 seconds
  2. Turn on LEFT half, wait 15 seconds for split pairing
  3. Connect from device (iPad/computer)

### 4. Final Results ✅
- **Bluetooth Connectivity**: Working perfectly
- **Split Communication**: Both halves functional
- **Key Matrix**: All keys working properly
- **Device Compatibility**: Confirmed working on iPad

## Working Connection Protocol
```
1. Power on RIGHT keyboard half → wait 15 seconds
2. Power on LEFT keyboard half → wait 15 seconds (halves pair via BT)
3. Device shows "Eyelash Sofle" in Bluetooth settings
4. Connect from device → both halves functional
```

## Compatibility Status
- ✅ **iPad**: Full functionality confirmed
- ❌ **Linux (Pop!_OS 22.04)**: Bluetooth stack compatibility issues
- ✅ **USB (Linux)**: Full functionality as backup

## Files Successfully Used
- `firmware/eyelash_sofle_left.uf2` - Working left half firmware
- `firmware/eyelash_sofle_right.uf2` - Working right half firmware  
- `firmware/settings_reset.uf2` - Bluetooth state reset utility

## Key Lessons Learned
1. **Settings Reset Critical**: Bluetooth corruption requires settings reset firmware to clear
2. **Split Pairing Sequence Important**: RIGHT half must boot first for proper split communication
3. **Linux Bluetooth Issues**: Some Linux distributions have ZMK compatibility problems
4. **Original Config Worked**: Complex Bluetooth fixes weren't needed, original settings were optimal

## Complete Reset and Flash Instructions

### When to Reset
Reset is needed when:
- Bluetooth won't connect or authenticate
- Split halves aren't communicating 
- Keys don't work even when connected
- After failed pairing attempts

### Step-by-Step Reset Process

#### Phase 1: Settings Reset (Clears Bluetooth corruption)
1. **Left Half Reset**:
   - Double-press reset button to enter bootloader mode
   - Copy `firmware/settings_reset.uf2` to mounted NICENANO drive
   - Wait for automatic reboot (10 seconds)

2. **Right Half Reset**:
   - Double-press reset button to enter bootloader mode  
   - Copy `firmware/settings_reset.uf2` to mounted NICENANO drive
   - Wait for automatic reboot (10 seconds)

#### Phase 2: Main Firmware Flash
3. **Left Half Firmware**:
   - Double-press reset button to enter bootloader mode
   - Copy `firmware/eyelash_sofle_left.uf2` to mounted NICENANO drive
   - Wait for automatic reboot

4. **Right Half Firmware**:
   - Double-press reset button to enter bootloader mode
   - Copy `firmware/eyelash_sofle_right.uf2` to mounted NICENANO drive  
   - Wait for automatic reboot

#### Phase 3: Test Connection
5. **Establish Split Communication**:
   - Turn on RIGHT half first → wait 15 seconds
   - Turn on LEFT half → wait 15 seconds (for split pairing)
   
6. **Connect to Device**:
   - Look for "Eyelash Sofle" in Bluetooth settings
   - Connect and test both halves

### Bootloader Mode Instructions
- **Method 1**: Double-tap the reset button quickly
- **Method 2**: Hold reset while connecting USB cable
- **Confirmation**: Device appears as "NICENANO" drive in file manager

### Required Files
```
firmware/
├── settings_reset.uf2      # Bluetooth state reset
├── eyelash_sofle_left.uf2  # Left half main firmware  
└── eyelash_sofle_right.uf2 # Right half main firmware
```

### Troubleshooting Reset Issues
- **Drive not appearing**: Try different USB cable or port
- **File copy fails**: Ensure sufficient space, try copying one file at a time
- **Still not working**: Repeat entire reset process from Phase 1

## Troubleshooting Reference
- **No Bluetooth Advertisement**: Complete reset process above
- **Split Communication Broken**: Follow proper power-on sequence (RIGHT first, then LEFT)
- **Authentication Failures**: Linux-specific issue, use iPad or USB connection instead
- **Partial Key Function**: Usually split communication issue, power cycle with correct sequence

---
**Status**: ✅ **FULLY FUNCTIONAL**  
**Date Completed**: 2025-07-06  
**Final Working State**: Bluetooth functional on iPad, USB backup available for Linux