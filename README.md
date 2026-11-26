# PandaAssistant

PandaAssistant turns a BIGTREETECH Panda Touch into a Home Assistant touchscreen with ESPHome and LVGL: clock, weather, energy readings and quick controls. Work in progress.

> **Note:** This project is vibe coded with Claude and checked by a human.

> **Warning:** Use this project at your own risk. I am not responsible for any damage. See the [Disclaimer](#disclaimer).

## Status

| Part | State |
|---|---|
| Hardware test config | Passes validation and code generation on ESPHome 2026.11.1. **Not yet compiled or run on a device.** |
| Home screen | Design mockup only |
| Energy and Rooms screens | Not designed yet |

Flashing replaces the stock Panda Touch firmware. Make a backup first.

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
docker run --rm -v "${PWD}":/config -it ghcr.io/esphome/esphome compile
