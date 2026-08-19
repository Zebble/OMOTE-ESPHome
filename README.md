# OMOTE-ESPHome
ESPHome implementation for the ESP32-based OMOTE Remote Control

- [x] ESPHome 2025.9.x

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
- [x] Matrix Keypad using the built-in tca8418 component
- [x] Power button, USB 3v3 and SD Card detect through Matrix Keypad pins
- [x] LIS3DH accelerometer
- [ ] SD Card
- [x] Battery Fuel Gauge (MAX17048G) using the max17043 component

Features:
- [x] Example Matrix Keypad binary sensors
- [x] LVGL built-in demo display - touchscreen enabled
- [x] Brightness control of LCD backlight, restored on boot
- [x] Brightness control of Keypad backlights, restored on boot
- [x] Sleep / Wake-up using the LIS3DH (hardware tested; commented out in omote.yaml)
- [x] Battery gauge
- [ ] Remote Control Framework!
