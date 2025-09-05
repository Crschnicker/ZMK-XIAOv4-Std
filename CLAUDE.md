# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview
This is a ZMK firmware configuration repository for a custom split keyboard called "SkreeCustm". It builds firmware for the Nice!Nano v2 controller with left/right split configuration and optional ZMK Studio support.

## Build Configuration
- **Board**: nice_nano (Nice!Nano v2)
- **Shields**: skreecustom_left, skreecustom_right
- **Build targets** (from `build.yaml`):
  - Left half: `nice_nano` + `skreecustom_left` with studio-rpc-usb-uart snippet
  - Right half: `nice_nano` + `skreecustom_right`
  - Settings reset: `nice_nano` + `settings_reset`

## Key Files Structure
- `build.yaml` - Defines GitHub Actions build matrix
- `boards/shields/skreecustom/` - Custom shield definition
  - `skreecustom.dtsi` - Main device tree include with matrix transform
  - `skreecustom_left.overlay` - Left half GPIO configuration
  - `skreecustom_right.overlay` - Right half GPIO configuration
  - `skreecustom.keymap` - Keymap with 4 layers defined
  - `skreecustom-layouts.dtsi` - Physical layout definition
- `config/skree-custom.conf` - Kconfig options (RGB underglow, ZMK Studio, etc.)
- `.github/workflows/blank.yml` - GitHub Actions workflow using ZMK's build-user-config

## Building Firmware

### Method 1: GitHub Actions (Recommended)
1. Fork this repository to your GitHub account
2. Push any commit to trigger the build
3. Download artifacts from Actions tab containing .uf2 files

### Method 2: Local Build
Requires ZMK development environment setup:
```bash
# Install dependencies (Ubuntu/Debian example)
sudo apt install -y git wget python3-pip cmake ninja-build gperf ccache dfu-util device-tree-compiler gcc-arm-none-eabi

# Install west
pip3 install --user west

# Initialize workspace
west init -l config
west update
west zephyr-export

# Build firmware
west build -s zmk/app -b nice_nano -- -DSHIELD="skreecustom_left" -DZMK_CONFIG="$(pwd)/config" -DZMK_EXTRA_MODULES="$(pwd)"
# Output: build/zephyr/zmk.uf2

west build -s zmk/app -b nice_nano -- -DSHIELD="skreecustom_right" -DZMK_CONFIG="$(pwd)/config" -DZMK_EXTRA_MODULES="$(pwd)"
```

## Keyboard Features
- 48-key split layout (4x6 main keys per half + 6 thumb keys)
- 4 layers configured in keymap
- Bluetooth support with profile switching (Layer 1)
- RGB underglow support (WS2812 LED strip)
- ZMK Studio support enabled
- Matrix ghosting fix for polyimide switches

## Common Development Tasks
- **Modify keymap**: Edit `boards/shields/skreecustom/skreecustom.keymap`
- **Change RGB settings**: Edit `config/skree-custom.conf`
- **Update GPIO pins**: Edit overlay files in `boards/shields/skreecustom/`