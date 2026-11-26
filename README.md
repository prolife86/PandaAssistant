# PandaAssistant

PandaAssistant turns a BIGTREETECH Panda Touch into a Home Assistant touchscreen with ESPHome and LVGL: clock, weather, energy readings and quick controls. Work in progress.

## Status

| Part | State |
|---|---|
| Hardware test config | Passes validation and code generation on ESPHome 2026.11.1. **Not yet compiled or run on a device.** |
| Home screen | Design mockup only |
| Energy and Rooms screens | Not designed yet |

Flashing replaces the stock Panda Touch firmware. Do this at your own risk and make a backup first.

## Planned home screen

- Clock, date and weather
- Solar, battery and indoor temperature
- Buttons for lights, an evening scene and the garage
- Tabs for Home, Energy and Rooms

## Hardware

BIGTREETECH Panda Touch: ESP32-S3, 16 MB flash, 8 MB PSRAM, 5" 800×480 display, GT911 touch.

| Function | GPIO |
|---|---|
| Display clock / data enable / reset | 5 / 38 / 46 |
| Display red | 6, 7, 8, 9, 10 |
| Display green | 11, 12, 13, 14, 15, 16 |
| Display blue | 17, 18, 48, 47, 39 |
| Backlight (PWM) | 21 |
| Touch SCL / SDA | 1 / 2 |
| Touch reset / interrupt | 41 / 40 |

## Installation

### 1. Back up the stock firmware

Connect through the USB-C port on the back of the magnetic mounting dock. With esptool v5 (replace `COMx` with your serial port):

```
esptool --chip esp32s3 --port COMx read-flash 0x0 0x1000000 pandatouch-stock.bin
```

To restore it:

```
esptool --chip esp32s3 --port COMx write-flash 0x0 pandatouch-stock.bin
```

Keep the backup private and out of this repository.

### 2. Create `secrets.yaml`

Place it next to the config file. Never commit it.

```yaml
wifi_ssid: "your-network"
wifi_password: "your-wifi-password"
api_key: "base64-encoded 32-byte key"
ota_password: "choose-a-password"
ap_password: "at-least-8-characters"
```

You can generate an API key with `openssl rand -base64 32`.

### 3. Compile

Run this from the folder holding both files:

```
docker run --rm -v "${PWD}":/config -it ghcr.io/esphome/esphome compile panda-assistant-test.yaml
```

### 4. Flash

For the first install, write the **factory** binary over USB-C, using [ESPHome Web](https://web.esphome.io) or `esptool ... write-flash 0x0`. Later updates can go over Wi-Fi.

## Hardware test

The test screen shows:

- Red, green and blue blocks, each of which should match its label
- Four corner buttons, each of which should report its own position when tapped

If the screen flickers, try `pclk_frequency: 16MHz`. The default of 14.8 MHz works out to about 32 Hz refresh.

## Credits

- [BTT Panda Touch ESPHome conversion](https://community.home-assistant.io/t/btt-panda-touch-esphome-ha-dashboard-conversion/1026634) by HaMMeR_Oz, the starting point for the test config
- [PandaTouch_IDF](https://github.com/bigtreetech/PandaTouch_IDF) and [PandaTouch_PlatformIO](https://github.com/bigtreetech/PandaTouch_PlatformIO) by BIGTREETECH, used to check pins and timings

This is an independent project, not affiliated with BIGTREETECH or Home Assistant.
