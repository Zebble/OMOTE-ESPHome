# OMOTE-ESPHome
ESPHome implementation for the ESP32-based OMOTE Remote Control

- [x] ESPHome 2026.11.0 or later (the first release with the built-in `tca8418` and `lis3dh` components)

Hardware Support:
- [ ] Rev1
- [x] Rev5
- [ ] Rev5-3661

Components:
- [x] Display using mipi_spi
- [x] Touchscreen using ft63x6
- [x] LVGL
- [x] LCD Backlight PWM using ledc
- [x] Keypad Backlight PWM using ledc
- [x] IR Receiver with Power control
- [x] IR Transmitter
- [x] Battery Charging binary sensor
- [x] Matrix Keypad using the built-in [tca8418](https://esphome.io/components/tca8418) component (added in [PR#18521](https://github.com/esphome/esphome/pull/18521))
- [x] Power button, USB 3v3 and SD Card detect through Matrix Keypad pins
- [x] LIS3DH accelerometer using the built-in [lis3dh](https://esphome.io/components/motion/lis3dh) component (added in [PR#17393](https://github.com/esphome/esphome/pull/17393))
- [ ] SD Card
- [x] Battery Fuel Gauge (MAX17048G) using the max17043 component

Features:
- [x] Example Matrix Keypad binary sensors
- [x] LVGL built-in demo display - touchscreen enabled
- [x] Brightness control of LCD backlight, restored on boot
- [x] Brightness control of Keypad backlights, restored on boot
- [ ] Sleep / Wake-up using timer and LIS3DH (proof of concept tested; not yet added to the framework configuration)
- [x] Battery gauge
- [ ] Remote Control Framework - the base works; see [FRAMEWORK.md](FRAMEWORK.md) for how it fits together and what is still to do
