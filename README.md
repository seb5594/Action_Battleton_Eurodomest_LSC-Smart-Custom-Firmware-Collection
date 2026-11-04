# LSC Smart Connect / Battletron / Eurodomest: ready-made firmware

[Deutsch](README.de.md)

Ready-to-flash ESPHome firmware for the smart devices sold by Action (LSC Smart Connect, Battletron, Eurodomest)
with a Beken (BK7231N, BK7231T, BK7238) or Realtek (RTL8710BN) chip. One file per device, **all languages in the
same file**, no account and no cloud. Nothing personal is built in: you set up your WiFi on the device itself.

## Download

Every device has its own release (see the table at the end), named `<product number>-<product name>`. The product
number is on the box and on the label of the device, e.g. `3202087`. A release holds the files of the newest version
of that device only. The files are called

`<chip>-<product number>-action-<product name>-esphome-firmware-<version>-by-seb5594.<what it is>`

e.g. `bk7231n-3207958-action-lsc-battletron-light-bar-esphome-firmware-2.0.1.2-by-seb5594.esphome-ota.uf2`. An update of a device replaces the files of the older version.

## Which file is for what

| File ends in | What it is | Use it for |
|---|---|---|
| `.esphome-ota.uf2` | UF2 package, all chips | UART flashing with **ltchiptool**, or over WiFi on a device that already runs ESPHome (web page, "Firmware Update") |
| `.app-partition-0x11000.bin` | Beken only: app partition for flash address `0x11000` | UART flashing with **bk7231tools** or ltchiptool |
| `.openbeken-ota.rbl` | Beken only: OTA package | OTA page of **OpenBeken** |
| `.tuya-cloudcutter-ota.ug.bin` | Beken only: OTA package in the Tuya format | **tuya-cloudcutter** on a device with the original Tuya firmware |
| `.app-partition-0xb000.bin` | RTL8710BN only: OTA 1 image for flash address `0xB000` | UART flashing with ltchiptool |

If you are not sure, take the `.esphome-ota.uf2`.

## Flashing

The examples use `bk7231n-3207958-action-lsc-battletron-light-bar-esphome-firmware-2.0.1.2-by-seb5594` (a BK7231N device) and a serial adapter on `COM3` (Linux: `/dev/ttyUSB0`).

**UART, the easy way** (all chips):

```bash
pip install ltchiptool
ltchiptool flash write -d COM3 bk7231n-3207958-action-lsc-battletron-light-bar-esphome-firmware-2.0.1.2-by-seb5594.esphome-ota.uf2
```

ltchiptool reads chip and addresses from the file and tells you when to restart the chip (power it off and on,
or pull CEN to GND for a moment).

**UART, the partition at 0x11000** (Beken):

```bash
pip install bk7231tools
bk7231tools write_flash -d COM3 -s 0x11000 bk7231n-3207958-action-lsc-battletron-light-bar-esphome-firmware-2.0.1.2-by-seb5594.app-partition-0x11000.bin
# or
ltchiptool flash write -d COM3 -f beken-7231n -s 0x11000 bk7231n-3207958-action-lsc-battletron-light-bar-esphome-firmware-2.0.1.2-by-seb5594.app-partition-0x11000.bin
```

The family is `beken-7231n`, `beken-7231t` or `beken-7238` (see the table below for your device). The bootloader is
not touched; do not use the `-B` option of bk7231tools.

**UART, RTL8710BN** (the siren):

```bash
ltchiptool flash write -d COM3 rtl8710bn-970772-action-lsc-smart-connect-siren-esphome-firmware-2.0.1.2-by-seb5594.esphome-ota.uf2
```

**Over WiFi, a device that already runs ESPHome:** open `http://<address of the device>/`, choose the
`.esphome-ota.uf2` under "Firmware Update" and upload it.

**Over WiFi, original Tuya firmware:** copy the `.tuya-cloudcutter-ota.ug.bin` into the `custom-firmware` folder of
[tuya-cloudcutter](https://github.com/tuya-cloudcutter/tuya-cloudcutter), run `./run_cloudcutter.sh`, choose your
device and "Flash 3rd Party Firmware". Works with many devices, not all.

**Over WiFi, OpenBeken:** open the OTA update page of the OpenBeken web interface (the wording differs between
versions), choose the `.openbeken-ota.rbl` and start the update.

## After flashing

1. Join the WiFi hotspot `lsc-<name>-<mac>-fallback` (password `ESPHome1337`) and pick your network.
2. Home Assistant finds the device by itself.
3. Under **Firmware Language** you choose the language of the names. The entity IDs always stay English; the device
   restarts and shows the names in the chosen language.

## Updates

The **Firmware** entity of the device checks this repository every 6 hours. When a newer build is published, Home
Assistant offers it like any ESPHome update, one click installs it. Your WiFi settings stay. The newest build of
every device is always the one in its release. The devices read [`manifest.json`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/blob/main/manifest.json)
(version, file and MD5 of every device), there is nothing to flash in it. The "Change Device" button is switched
off by default.

## Devices

Find your device by the chip on its board: the module name tells it (CB... = BK7231N, WB... = BK7231T, T1... = BK7238).

[BK7231T](bk7231t/README.md) (19) · [BK7231N](bk7231n/README.md) (27) · [BK7238](bk7238/README.md) (9) · [RTL8710BN](rtl8710bn/README.md) (1) · [LN882H](ln882h/README.md) (1)

**Released** and **Sold until**: dates where known, otherwise estimates marked `~` (the nearest article numbers; see the main repository).

Every file is an attachment of its release, so every download is counted by GitHub.

| Article | Device | Chip | Released | Sold until | Release (files) |
|---|---|---|---|---|---|
| 970719.1 | LSC Smart Connect LED Bulb A60 RGB + CCT | BK7231T | ~2024 | on sale | [`970719.1-lsc-smart-connect-led-bulb-a60-rgb-cct`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/970719.1-lsc-smart-connect-led-bulb-a60-rgb-cct) |
| 970762 | LSC Smart Connect Power Plug 2.0 | BK7231T | ~2019 | ~2023 | [`970762-lsc-smart-connect-power-plug-2-0`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/970762-lsc-smart-connect-power-plug-2-0) |
| 970772 | LSC Smart Connect Siren | RTL8710BN | ~2019 | ~2022 | [`970772-lsc-smart-connect-siren`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/970772-lsc-smart-connect-siren) |
| 2578539 | LSC Smart Connect RGBCT Bulb | BK7231T | ~2022 | ~2025 | [`2578539-lsc-smart-connect-rgbct-bulb`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/2578539-lsc-smart-connect-rgbct-bulb) |
| 2578677 | LSC Smart Connect Smart Plug | BK7231N | 2021-10 | ~2025 | [`2578677-lsc-smart-connect-smart-plug`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/2578677-lsc-smart-connect-smart-plug) |
| 2578685-bk7231n | LSC Smart Connect Smart Plug | BK7231N | 2022-11 | ~2026 | [`2578685-bk7231n-lsc-smart-connect-smart-plug`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/2578685-bk7231n-lsc-smart-connect-smart-plug) |
| 2578685-bk7231t | LSC Smart Connect Smart Plug | BK7231T | 2022-11 | ~2026 | [`2578685-bk7231t-lsc-smart-connect-smart-plug`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/2578685-bk7231t-lsc-smart-connect-smart-plug) |
| 2578685-wb2s | LSC Smart Connect Smart Plug | BK7231T | 2022-11 | ~2026 | [`2578685-wb2s-lsc-smart-connect-smart-plug`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/2578685-wb2s-lsc-smart-connect-smart-plug) |
| 3000267 | LSC Smart Connect Tunable White GU10 345 lm | BK7231T | ~2021 | ~2024 | [`3000267-lsc-smart-connect-tunable-white-gu10-345-lm`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3000267-lsc-smart-connect-tunable-white-gu10-345-lm) |
| 3000272 | LSC Smart Connect LED Bulb White Ambiance | BK7231T | 2020-12 | ~2023 | [`3000272-lsc-smart-connect-led-bulb-white-ambiance`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3000272-lsc-smart-connect-led-bulb-white-ambiance) |
| 3000273 | LSC Smart Connect Tunable White E27 1400 lm | BK7231T | ~2021 | ~2024 | [`3000273-lsc-smart-connect-tunable-white-e27-1400-lm`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3000273-lsc-smart-connect-tunable-white-e27-1400-lm) |
| 3000342-ir_remote | LSC Smart Connect LED Light Strip RGB + CCT | BK7231T | ~2022 | ~2025 | [`3000342-ir_remote-lsc-smart-connect-led-light-strip-rgb-cct`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3000342-ir_remote-lsc-smart-connect-led-light-strip-rgb-cct) |
| 3000342-plain | LSC Smart Connect LED Light Strip RGB + CCT | BK7231T | ~2022 | ~2025 | [`3000342-plain-lsc-smart-connect-led-light-strip-rgb-cct`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3000342-plain-lsc-smart-connect-led-light-strip-rgb-cct) |
| 3001686 | LSC Smart Connect Warm White Smart Filament E27 806 lm | BK7231T | ~2021 | ~2024 | [`3001686-lsc-smart-connect-warm-white-smart-filament-e27-806-lm`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3001686-lsc-smart-connect-warm-white-smart-filament-e27-806-lm) |
| 3001700 | LSC Smart Connect Warm White Smart Filament E27 806 lm | BK7231T | ~2021 | ~2024 | [`3001700-lsc-smart-connect-warm-white-smart-filament-e27-806-lm`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3001700-lsc-smart-connect-warm-white-smart-filament-e27-806-lm) |
| 3001702 | LSC Smart Connect Warm White Ambiance Smart Filament E14 470 lm | BK7231T | ~2021 | ~2024 | [`3001702-lsc-smart-connect-warm-white-ambiance-smart-filament-e14-470-lm`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3001702-lsc-smart-connect-warm-white-ambiance-smart-filament-e14-470-lm) |
| 3003366 | LSC Smart Connect Outdoor Flood Light | BK7231T | 2022-01 | ~2025 | [`3003366-lsc-smart-connect-outdoor-flood-light`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3003366-lsc-smart-connect-outdoor-flood-light) |
| 3004154 | LSC Smart Connect Mood Light | BK7231N | 2021-11 | ~2024 | [`3004154-lsc-smart-connect-mood-light`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3004154-lsc-smart-connect-mood-light) |
| 3004919 | LSC Smart Connect GU10 RGBCT Bulb | BK7231T | ~2022 | ~2025 | [`3004919-lsc-smart-connect-gu10-rgbct-bulb`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3004919-lsc-smart-connect-gu10-rgbct-bulb) |
| 3005364 | LSC Smart Connect WiFi Smart Outdoor Garden Lamp | BK7231T | ~2022 | ~2025 | [`3005364-lsc-smart-connect-wifi-smart-outdoor-garden-lamp`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3005364-lsc-smart-connect-wifi-smart-outdoor-garden-lamp) |
| 3005457 | LSC Smart Connect Smart Outdoor LED Light Strip | BK7231N | ~2022 | ~2025 | [`3005457-lsc-smart-connect-smart-outdoor-led-light-strip`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3005457-lsc-smart-connect-smart-outdoor-led-light-strip) |
| 3006767 | LSC Smart Connect Tunable White Downlight 360 lm | BK7231T | ~2022 | ~2025 | [`3006767-lsc-smart-connect-tunable-white-downlight-360-lm`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3006767-lsc-smart-connect-tunable-white-downlight-360-lm) |
| 3007213.2 | LSC Smart Connect Ceiling Light | BK7231N | ~2024 | on sale | [`3007213.2-lsc-smart-connect-ceiling-light`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3007213.2-lsc-smart-connect-ceiling-light) |
| 3008407 | LSC Smart Connect LED Light Strip | BK7231N | ~2022 | ~2025 | [`3008407-lsc-smart-connect-led-light-strip`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3008407-lsc-smart-connect-led-light-strip) |
| 3009188 | LSC Smart Connect Outdoor Spotlights | BK7231N | ~2022 | ~2025 | [`3009188-lsc-smart-connect-outdoor-spotlights`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3009188-lsc-smart-connect-outdoor-spotlights) |
| 3017060 | Eurodomest HDMI Sync Box | BK7231N | 2025 | on sale | [`3017060-eurodomest-hdmi-sync-box`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3017060-eurodomest-hdmi-sync-box) |
| 3200074 | LSC Smart Connect LED Filament Bulb | BK7231T | 2023-04 | ~2026 | [`3200074-lsc-smart-connect-led-filament-bulb`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3200074-lsc-smart-connect-led-filament-bulb) |
| 3200654.1 | LSC Smart Connect Neon LED Strip | LN882H | ~2024 | on sale | [`3200654.1-lsc-smart-connect-neon-led-strip`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3200654.1-lsc-smart-connect-neon-led-strip) |
| 3200719 | LSC Smart Connect Outdoor Flood Light RGBW | BK7231N | ~2023 | ~2026 | [`3200719-lsc-smart-connect-outdoor-flood-light-rgbw`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3200719-lsc-smart-connect-outdoor-flood-light-rgbw) |
| 3202086 | LSC Smart Connect XXL LED Strip (10 m) | BK7231N | 2024-08 | on sale | [`3202086-lsc-smart-connect-xxl-led-strip-10-m`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3202086-lsc-smart-connect-xxl-led-strip-10-m) |
| 3202086.2 | LSC Smart Connect XXL LED Strip (10 m) | BK7231N | 2025-11 | on sale | [`3202086.2-lsc-smart-connect-xxl-led-strip-10-m`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3202086.2-lsc-smart-connect-xxl-led-strip-10-m) |
| 3202086.3 | LSC Smart Connect XXL LED Strip (10 m) | BK7231N | 2025-11 | on sale | [`3202086.3-lsc-smart-connect-xxl-led-strip-10-m`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3202086.3-lsc-smart-connect-xxl-led-strip-10-m) |
| 3202087 | LSC Smart Connect Smart Plug with Energy Monitor | BK7231N | 2023-12 | on sale | [`3202087-lsc-smart-connect-smart-plug-with-energy-monitor`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3202087-lsc-smart-connect-smart-plug-with-energy-monitor) |
| 3202087.1-1 | LSC Smart Connect Smart Plug with Energy Monitor | BK7238 | 2025-12 | on sale | [`3202087.1-1-lsc-smart-connect-smart-plug-with-energy-monitor`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3202087.1-1-lsc-smart-connect-smart-plug-with-energy-monitor) |
| 3202087.1-2 | LSC Smart Connect Smart Plug with Energy Monitor | BK7238 | 2025-12 | on sale | [`3202087.1-2-lsc-smart-connect-smart-plug-with-energy-monitor`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3202087.1-2-lsc-smart-connect-smart-plug-with-energy-monitor) |
| 3202087.2 | LSC Smart Connect Power Plug 20 A with Energy Monitor | BK7238 | 2026-04 | on sale | [`3202087.2-lsc-smart-connect-power-plug-20-a-with-energy-monitor`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3202087.2-lsc-smart-connect-power-plug-20-a-with-energy-monitor) |
| 3202088 | LSC Smart Connect Smart Plug with Energy Monitor | BK7231T | ~2024 | on sale | [`3202088-lsc-smart-connect-smart-plug-with-energy-monitor`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3202088-lsc-smart-connect-smart-plug-with-energy-monitor) |
| 3202653.1 | LSC Battletron Mousepad XXL | BK7231N | ~2024 | on sale | [`3202653.1-lsc-battletron-mousepad-xxl`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3202653.1-lsc-battletron-mousepad-xxl) |
| 3202887 | LSC Smart Connect Power Strip 3680 W | BK7231N | 2024-12 | on sale | [`3202887-lsc-smart-connect-power-strip-3680-w`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3202887-lsc-smart-connect-power-strip-3680-w) |
| 3202888 | LSC Smart Connect Power Strip 3680 W | BK7231N | ~2024 | on sale | [`3202888-lsc-smart-connect-power-strip-3680-w`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3202888-lsc-smart-connect-power-strip-3680-w) |
| 3203632.1 | LSC Smart Connect LED Strip (5 m) | BK7231N | 2025-09 | on sale | [`3203632.1-lsc-smart-connect-led-strip-5-m`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3203632.1-lsc-smart-connect-led-strip-5-m) |
| 3204432 | LSC Smart Connect Mood Light | BK7231N | ~2024 | on sale | [`3204432-lsc-smart-connect-mood-light`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3204432-lsc-smart-connect-mood-light) |
| 3206306 | LSC Smart Connect Ceiling Lamp 1500 lm | BK7231N | 2024-12 | on sale | [`3206306-lsc-smart-connect-ceiling-lamp-1500-lm`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3206306-lsc-smart-connect-ceiling-lamp-1500-lm) |
| 3206528 | LSC Battletron Desk LED Strip (3 m) | BK7231N | 2024-12 | on sale | [`3206528-lsc-battletron-desk-led-strip-3-m`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3206528-lsc-battletron-desk-led-strip-3-m) |
| 3207853 | LSC Smart Connect Convector Heater 2000 W | BK7238 | 2026 | on sale | [`3207853-lsc-smart-connect-convector-heater-2000-w`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3207853-lsc-smart-connect-convector-heater-2000-w) |
| 3207958 | LSC Battletron Light Bar | BK7231N | ~2025 | on sale | [`3207958-lsc-battletron-light-bar`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3207958-lsc-battletron-light-bar) |
| 3211131 | LSC Smart Connect Power Strip (3 Sockets + 2 USB) | BK7231N | 2025-01 | on sale | [`3211131-lsc-smart-connect-power-strip-3-sockets-2-usb`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3211131-lsc-smart-connect-power-strip-3-sockets-2-usb) |
| 3216802 | LSC Smart Connect Panel Lights | BK7231N | 2025-08 | on sale | [`3216802-lsc-smart-connect-panel-lights`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3216802-lsc-smart-connect-panel-lights) |
| 3218147 | LSC Smart Connect Frameless Ceiling Light | BK7231N | ~2025 | on sale | [`3218147-lsc-smart-connect-frameless-ceiling-light`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3218147-lsc-smart-connect-frameless-ceiling-light) |
| 3218153 | LSC Smart Connect LED Strip (20 m) | BK7231N | ~2025 | on sale | [`3218153-lsc-smart-connect-led-strip-20-m`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3218153-lsc-smart-connect-led-strip-20-m) |
| 3218223 | LSC Battletron Monitor LED Strip (90 cm) | BK7231N | ~2025 | on sale | [`3218223-lsc-battletron-monitor-led-strip-90-cm`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3218223-lsc-battletron-monitor-led-strip-90-cm) |
| 3219270 | LSC Smart Connect LED Strip (20 m) | BK7231N | ~2025 | on sale | [`3219270-lsc-smart-connect-led-strip-20-m`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3219270-lsc-smart-connect-led-strip-20-m) |
| 3221699 | LSC Smart Connect Floor Lamp | BK7238 | ~2025 | on sale | [`3221699-lsc-smart-connect-floor-lamp`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3221699-lsc-smart-connect-floor-lamp) |
| 3224757 | LSC Smart Connect Ceiling Skyscape Light | BK7238 | 2026-05 | on sale | [`3224757-lsc-smart-connect-ceiling-skyscape-light`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3224757-lsc-smart-connect-ceiling-skyscape-light) |
| 3225765 | Eurodomest HDMI Sync Box (2nd generation) | BK7238 | ~2026 | on sale | [`3225765-eurodomest-hdmi-sync-box-2nd-generation`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3225765-eurodomest-hdmi-sync-box-2nd-generation) |
| 3225769 | LSC Smart Connect Wall Washer Light | BK7238 | ~2026 | on sale | [`3225769-lsc-smart-connect-wall-washer-light`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3225769-lsc-smart-connect-wall-washer-light) |
| 3225770 | LSC Smart Connect Smart LED Strip Kit | BK7238 | ~2026 | ~2026 | [`3225770-lsc-smart-connect-smart-led-strip-kit`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/releases/tag/3225770-lsc-smart-connect-smart-led-strip-kit) |

*The firmware is published here by an automatic build whenever a device changes or is added.*
