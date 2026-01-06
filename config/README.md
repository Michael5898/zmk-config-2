# ZMK Firmware Configuration for Custom Macropad

This repository contains the ZMK firmware configuration for a custom macropad based on the XIAO nRF52840 microcontroller.

## Hardware Specifications

- **Controller**: XIAO nRF52840 Plus (ARM Cortex-M4F, USB + BLE)
- **Keys**: 11 mechanical switches in a 3×4 grid (top-left position unused)
- **Encoders**: 2 rotary encoders with integrated push buttons
- **Connectivity**: USB-C for power/flashing, BLE for wireless operation

## Key Layout

```
┌──────┬────────────┬──────────────┐
│      │ Mute       │ Layer Toggle │
├──────┼────────────┼──────────────┤
│ Rew  │ Play/Pause │ Skip         │
├──────┼────────────┼──────────────┤
│ Copy │ Paste      │ Cut          │
├──────┼────────────┼──────────────┤
│ M    │ D          │ Z            │
└──────┴────────────┴──────────────┘
```

- **Encoder Left**: Volume Up/Down
- **Encoder Right**: Page Up/Down

### Layer 0 (Default)
- Mute: Consumer Mute (encoder left button)
- Layer Toggle: Momentary layer 1 (encoder right button)
- Rewind: C_RW
- Play/Pause: C_PP
- Skip: C_FF
- Copy: Ctrl+C
- Paste: Ctrl+V
- Cut: Ctrl+X
- M: Ctrl+Shift+M
- D: Ctrl+Shift+D
- Z: Ctrl+Z

### Layer 1 (Function)
- BT Previous, BT Next, BT Clear on top row
- Other keys transparent (pass through to default layer)

## Building the Firmware

The firmware is automatically built via GitHub Actions when changes are pushed to this repository.

### Manual Build (Local)

1. Install West and dependencies:
```bash
pip3 install west
```

2. Initialize workspace:
```bash
west init -l config
west update
west zephyr-export
```

3. Build firmware:
```bash
west build -s zmk/app -b xiao_ble -- -DZMK_CONFIG="$(pwd)/config" -DSHIELD=macropad
```

4. The firmware file will be at: `build/zephyr/zmk.uf2`

## Flashing

1. Connect the XIAO nRF52840 via USB
2. Double-press the reset button to enter bootloader mode
3. A USB drive named "XIAO-SENSE" should appear
4. Drag and drop the `zmk.uf2` file onto the drive
5. The board will automatically reboot with the new firmware

## Pin Assignments

Based on the schematic (Macropad_v19.sch):

### Matrix
- **Rows**: 
  - Row 0: P1.11 (D6)
  - Row 1: P0.05 (D5)
  - Row 2: P0.04 (D4)

- **Columns**:
  - Col 0: P0.29 (D3)
  - Col 1: P0.28 (D2)
  - Col 2: P0.03 (D1)
  - Col 3: P0.02 (D0) - Encoder buttons

### Encoders
- **Encoder Left (EN1)**:
  - A: P1.12 (D7)
  - B: P1.13 (D8)
  - Button: Connected via diode to matrix at Col 3 (P0.02/D0), Row 1

- **Encoder Right (EN2)**:
  - A: P1.14 (D9)
  - B: P1.15 (D10)
  - Button: Connected via diode to matrix at Col 3 (P0.02/D0), Row 0

## Configuration Files

- `config/west.yml` - West manifest for ZMK dependencies
- `build.yaml` - Build matrix for GitHub Actions
- `config/boards/shields/macropad/` - Shield definition
  - `macropad.overlay` - Hardware pin definitions and matrix configuration
  - `macropad.keymap` - Key bindings and behavior configuration
  - `macropad.conf` - Feature flags and configuration options
  - `macropad.zmk.yml` - Shield metadata
  - `Kconfig.shield` - Kconfig shield selection
  - `Kconfig.defconfig` - Default configuration values

## License

SPDX-License-Identifier: MIT
