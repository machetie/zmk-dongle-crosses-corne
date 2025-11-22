# ZMK Dual Split Keyboard Dongle Implementation

This project implements a ZMK firmware configuration for two different 42-key split keyboards (Crosses-42 and Corne Wireless) connecting to a single USB dongle.

## Repository Structure

- `dongle_config/`: Configuration for the central USB dongle.
- `crosses_42_firmware/`: Configuration for the Crosses-42 peripherals (with trackballs).
- `corne_firmware/`: Configuration for the Corne Wireless peripherals.

## Setup & Build

### Prerequisites
- ZMK Firmware (main branch with pointing device support)
- Zephyr 3.2+

### Building
The `dongle_config/build.yaml` defines the build targets.
To build the firmware, you can use the ZMK build tools or GitHub Actions if configured.

Generated artifacts:
1. `dual_dongle-nice_nano_v2-zmk.uf2`
2. `crosses_left-nice_nano_v2-zmk.uf2`
3. `crosses_right-nice_nano_v2-zmk.uf2`
4. `corne_left-nice_nano_v2-zmk.uf2`
5. `corne_right-nice_nano_v2-zmk.uf2`

## Configuration Required

**IMPORTANT:** Before building, you MUST update the pin assignments in the following files with your actual hardware pins:

1. `crosses_42_firmware/config/boards/shields/crosses_left/crosses_left.overlay`
   - Update `cs-gpios` (currently placeholder 20)
   - Update `irq-gpios` (currently placeholder 21)

2. `crosses_42_firmware/config/boards/shields/crosses_right/crosses_right.overlay`
   - Update `cs-gpios` (currently placeholder 22)
   - Update `irq-gpios` (currently placeholder 23)

Refer to your Nice!Nano v2 pinout and PAW3204 sensor wiring.

## Pairing and Testing Procedure

### Initial Setup
1. **Clear all existing bonds:**
   - On each Nice!Nano controller, reset Bluetooth bonds (use reset button combination or flash with `&bt BT_CLR` behavior).
2. **Flash firmware:**
   - Flash dongle with `dual_dongle-nice_nano_v2-zmk.uf2`
   - Flash Crosses-42 left with `crosses_left-nice_nano_v2-zmk.uf2`
   - Flash Crosses-42 right with `crosses_right-nice_nano_v2-zmk.uf2`
   - Flash Corne left with `corne_left-nice_nano_v2-zmk.uf2`
   - Flash Corne right with `corne_right-nice_nano_v2-zmk.uf2`
3. **Power on sequence:**
   - Plug dongle into PC via USB.
   - Power on Crosses-42 left half.
   - Power on Crosses-42 right half.
   - Power on Corne left half.
   - Power on Corne right half.
   - All peripherals should auto-pair to the dongle.

### Verification Tests
1. **Basic Key Input:** Type on both keyboards, switch freely.
2. **XY Trackball (Crosses-42 Left):** Verify cursor movement.
3. **Scroll Trackball (Crosses-42 Right):** Verify vertical scrolling (no cursor movement).
4. **Layer Switching:** Verify layers work on all devices.
5. **Battery Reporting:** Check host OS for battery levels of all 4 peripherals.
6. **Connection Stability:** Test after idle time.
7. **Simultaneous Input:** Use trackball + typing.

## Troubleshooting

- **Peripherals won't pair:** Clear bonds on all devices, re-flash, ensure dongle is powered via USB.
- **Choppy trackball movement:** Increase `CONFIG_ZMK_SPLIT_BLE_CENTRAL_POSITION_QUEUE_SIZE` to 15-20 in `dual_dongle.conf`.
- **Scroll trackball moves cursor:** Verify `scroll-mode` in `crosses_right.overlay`.
- **Keys don't match:** Ensure keymaps are synchronized.
- **Battery levels missing:** Check `CONFIG_ZMK_SPLIT_BLE_CENTRAL_BATTERY_LEVEL_FETCHING=y`.
- **Connection drops:** Increase BLE power (`CONFIG_BT_CTLR_TX_PWR_PLUS_8=y`).
