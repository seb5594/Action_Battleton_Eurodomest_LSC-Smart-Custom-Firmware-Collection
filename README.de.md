# LSC Smart Connect / Battletron / Eurodomest: fertige Firmware

[English](README.md)

Flashbare ESPHome-Firmware für die Smart-Home-Geräte von Action (LSC Smart Connect, Battletron, Eurodomest) mit
Beken-Chip (BK7231N, BK7231T, BK7238) oder Realtek-Chip (RTL8710BN). Eine Datei pro Gerät, **alle Sprachen in
derselben Datei**, ohne Konto und ohne Cloud. Nichts Persönliches ist eingebaut: Das WLAN richtest du am Gerät
selbst ein.

## Download

Jedes Gerät hat ein eigenes Release (siehe die Tabelle am Ende), benannt `<Artikelnummer>-<Produktname>`. Die
Artikelnummer steht auf der Verpackung und auf dem Typenschild, z. B. `3202087`. Ein Release enthält nur die Dateien
der neuesten Version dieses Geräts. Die Dateien heißen

`<Chip>-<Artikelnummer>-action-<Produktname>-esphome-firmware-<Version>-by-seb5594.<was es ist>`

z. B. `bk7231n-3207958-action-lsc-battletron-light-bar-esphome-firmware-2.0.1.2-by-seb5594.esphome-ota.uf2`. Ein Update eines Geräts ersetzt die Dateien der älteren Version.

## Welche Datei wofür

| Datei endet auf | Was es ist | Zum |
|---|---|---|
| `.esphome-ota.uf2` | UF2-Paket, alle Chips | Flashen per UART mit **ltchiptool**, oder per WLAN auf einem Gerät, das schon ESPHome nutzt (Weboberfläche, „Firmware Update“) |
| `.app-partition-0x11000.bin` | Nur Beken: App-Partition für Flash-Adresse `0x11000` | Flashen per UART mit **bk7231tools** oder ltchiptool |
| `.openbeken-ota.rbl` | Nur Beken: OTA-Paket | OTA-Seite von **OpenBeken** |
| `.tuya-cloudcutter-ota.ug.bin` | Nur Beken: OTA-Paket im Tuya-Format | **tuya-cloudcutter** auf einem Gerät mit der Original-Tuya-Firmware |
| `.app-partition-0xb000.bin` | Nur RTL8710BN: OTA-1-Image für Flash-Adresse `0xB000` | Flashen per UART mit ltchiptool |

Im Zweifel nimm die `.esphome-ota.uf2`.

## Flashen

Die Beispiele nutzen `bk7231n-3207958-action-lsc-battletron-light-bar-esphome-firmware-2.0.1.2-by-seb5594` (ein BK7231N-Gerät) und einen USB-Seriell-Adapter an `COM3` (Linux:
`/dev/ttyUSB0`).

**UART, der einfache Weg** (alle Chips):

```bash
pip install ltchiptool
ltchiptool flash write -d COM3 bk7231n-3207958-action-lsc-battletron-light-bar-esphome-firmware-2.0.1.2-by-seb5594.esphome-ota.uf2
```

ltchiptool liest Chip und Adressen aus der Datei und sagt dir, wann du den Chip neu starten sollst (kurz
stromlos machen oder CEN kurz auf GND ziehen).

**UART, die Partition bei 0x11000** (Beken):

```bash
pip install bk7231tools
bk7231tools write_flash -d COM3 -s 0x11000 bk7231n-3207958-action-lsc-battletron-light-bar-esphome-firmware-2.0.1.2-by-seb5594.app-partition-0x11000.bin
# oder
ltchiptool flash write -d COM3 -f beken-7231n -s 0x11000 bk7231n-3207958-action-lsc-battletron-light-bar-esphome-firmware-2.0.1.2-by-seb5594.app-partition-0x11000.bin
```

Die Familie ist `beken-7231n`, `beken-7231t` oder `beken-7238` (siehe die Tabelle unten). Der Bootloader bleibt
unberührt; die Option `-B` von bk7231tools nicht benutzen.

**UART, RTL8710BN** (die Sirene):

```bash
ltchiptool flash write -d COM3 rtl8710bn-970772-action-lsc-smart-connect-siren-esphome-firmware-2.0.1.2-by-seb5594.esphome-ota.uf2
```

**Per WLAN, Gerät mit ESPHome:** `http://<Adresse des Geräts>/` öffnen, unter „Firmware Update“ die
`.esphome-ota.uf2` wählen und hochladen.

**Per WLAN, Original-Tuya-Firmware:** die `.tuya-cloudcutter-ota.ug.bin` in den Ordner `custom-firmware` von
[tuya-cloudcutter](https://github.com/tuya-cloudcutter/tuya-cloudcutter) kopieren, `./run_cloudcutter.sh` starten,
dein Gerät und „Flash 3rd Party Firmware“ wählen. Funktioniert bei vielen Geräten, nicht bei allen.

**Per WLAN, OpenBeken:** die OTA-Update-Seite der OpenBeken-Weboberfläche öffnen (die Bezeichnung unterscheidet
sich je nach Version), die `.openbeken-ota.rbl` wählen und das Update starten.

## Nach dem Flashen

1. Dem WLAN-Hotspot `lsc-<Name>-<MAC>-fallback` beitreten (Passwort `ESPHome1337`) und dein Netzwerk wählen.
2. Home Assistant findet das Gerät von selbst.
3. Unter **Firmware Language** wählst du die Sprache der Namen. Die Entitäts-IDs bleiben immer englisch; das
   Gerät startet neu und zeigt die Namen in der gewählten Sprache.

## Updates

Die Entität **Firmware** des Geräts fragt dieses Repository alle 6 Stunden. Gibt es einen neueren Build, bietet
Home Assistant ihn wie jedes ESPHome-Update an, ein Klick installiert ihn. Deine WLAN-Einstellungen bleiben. Der
neueste Build jedes Geräts liegt immer in seinem Release. Die Geräte lesen die
[`manifest.json`](https://github.com/seb5594/Action_Battleton_Eurodomest_LSC-Smart-Custom-Firmware-Collection/blob/main/manifest.json) (Version, Datei und MD5 jedes Geräts), darin ist nichts zum
Flashen. Der Knopf „Change Device“ ist standardmäßig ausgeschaltet.

## Geräte

Das Gerät findest du über den Chip auf der Platine: der Modulname verrät ihn (CB... = BK7231N, WB... = BK7231T, T1... = BK7238).

[BK7231T](bk7231t/README.de.md) (19) · [BK7231N](bk7231n/README.de.md) (27) · [BK7238](bk7238/README.de.md) (9) · [RTL8710BN](rtl8710bn/README.de.md) (1) · [LN882H](ln882h/README.de.md) (1)

**Erschienen** und **Verkauft bis**: Daten, wo bekannt, sonst Schätzungen mit `~` (nach den nächsten Artikelnummern; siehe Hauptrepository).

Jede Datei hängt an ihrem Release, deshalb zählt GitHub jeden Download.

| Artikel | Gerät | Chip | Erschienen | Verkauft bis | Release (Dateien) |
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

*Die Firmware wird hier von einem automatischen Build veröffentlicht, sobald sich ein Gerät ändert oder neu ist.*
