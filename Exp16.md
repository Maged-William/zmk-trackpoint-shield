# Exp16: BLE Smoothness Test — Synthetic Rectangle Over BLE

## Hypothesis

The Exp05 synthetic rectangle pattern produces perfectly smooth cursor motion over USB (100 Hz interrupt-driven SPI). The same pipeline over direct BLE (right NiceNano → PC) should also be smooth — BLE HID pointing reports at 100 Hz are well within the bandwidth of a standard BLE connection. If choppiness or stuttering appears, the bottleneck is likely the BLE connection interval or HID report servicing, not the SPI pipeline.

## Plan

1. **Pro Mini firmware**: Clone Exp05 synthetic rectangle generator (4-speed, 100 Hz, interrupt-driven SPI). Update MOT_PIN from D2 → D14 for current wiring. No PS/2, no sleep, no power switching — pure synthetic test.
2. **ZMK shield**: Remove USB logging snippet (`zmk-usb-logging`) so the right half builds for BLE. Remove layer-toggle Kconfig (not relevant). Keep SPI, driver debug logging, shell.
3. **Build** via GH Actions on both repos.
4. **Flash** Pro Mini (CH340G) + NiceNano (UF2).
5. **Test**: Observe cursor on PC over BLE. Verify rectangle trace at all 4 speeds (fast right, slow down, extra fast left, normal up). Compare smoothness against Exp05 USB result.

## Files Changed

| File | Change |
|------|--------|
| `promini-trackpoint/trackpoint-spi-slave/trackpoint-spi-slave.ino` | Rewrite to Exp05 synthetic rectangle, MOT_PIN D2→D14, add D13 blink |
| `zmk-trackpoint-shield/build.yaml` | Remove `snippet: zmk-usb-logging` |
| `zmk-trackpoint-shield/boards/shields/corne_trackpoint/corne_trackpoint_right.conf` | Remove `CONFIG_PMW3610_ALT_LAYER_TOGGLE=y` |
| `experiments/Exp16/Exp16.md` | This file |
| `Experiments.md` | Add Exp16 row |

## Results

TBD

## Conclusion

TBD

## Next Experiment

TBD
