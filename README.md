# E480 Hackintosh — macOS Ventura / Sonoma via OpenCore

<p align="center">
  💻 <strong>ThinkPad E480 · OpenCore Edition</strong>
  <br />
  <img src="https://img.shields.io/badge/OpenCore-1.0.7-informational.svg" alt="OpenCore 1.0.7">
  <img src="https://img.shields.io/badge/macOS-Ventura%2013.7-success.svg" alt="macOS Ventura 13.7">
  <img src="https://img.shields.io/badge/macOS-Sonoma%2014.7-success.svg" alt="macOS Sonoma 14.7">
  <br />
  <img src="https://img.shields.io/badge/SMBIOS-MacBookPro15%2C4-informational.svg" alt="SMBIOS MacBookPro15,4">
  <img src="https://img.shields.io/badge/status-maintained-success.svg" alt="Status: Maintained">
  <br />
  <a href="https://github.com/LeBritow/E480-Hackintosh/releases"><strong>⬇️ Download the EFI</strong></a>
  <br />
  <a href="https://github.com/LeBritow/E480-Hackintosh/issues">🐛 Report a bug</a>
  ·
  <a href="https://github.com/LeBritow/E480-Hackintosh/blob/main/CHANGELOG.md">📜 Changelog</a>
  ·
  <a href="https://github.com/LeBritow/E480-Hackintosh/blob/main/hackintosh-e480/03-installation/README.md">📖 Install guide</a>
</p>

OpenCore EFI for the **Lenovo ThinkPad E480** running **macOS Ventura 13.7** and **Sonoma 14.7**. Everything here was verified on my own E480 (i5-8250U, UHD 620, 24 GB, no dGPU) — this is the EFI I boot daily.

> **⚠️ Disclaimer:** This project is provided **as is**, without warranty of any kind. Installing macOS on non-Apple hardware may violate your local software license agreements and could damage hardware or data. I am **not responsible for any damages**, lost data, or broken equipment resulting from the use of this repository. You use it entirely at your own risk.

> **SMBIOS notice:** All SMBIOS data in this repository are **placeholders** (`AAAAAAAA...`). Before using the EFI, **generate your own values with GenSMBIOS** — a public serial number can cause iMessage/FaceTime blacklisting.

## Screenshots

*(Photos coming soon — up to 4 screenshots in `assets/screenshots/`.)*

## Status

| Item | Detail |
|:---|:---|
| Bootloader | OpenCore 1.0.7 |
| macOS | Ventura 13.7 and Sonoma 14.7 (both tested) |
| SMBIOS | `MacBookPro15,4` |
| Validation | `ocvalidate` (OC 1.0.7): 0 issues |

## What works / what doesn't

| Component | Status |
|:---|:---|
| Graphics | ✅ Full acceleration — UHD 620 faked as HD 620 (3072 MB) |
| Wi-Fi / Ethernet | ✅ Intel WiFi (AirportItlwm), Realtek RTL8111 |
| Audio | ✅ Conexant CX20753/4 (AppleALC, `layout-id: 15`) |
| Bluetooth | ✅ Working (IntelBluetoothFirmware) — ⚠️ AirDrop/Handoff partially broken (Intel limitation) |
| Trackpad / TrackPoint / keyboard | ✅ VoodooRMI + VoodooPS2 |
| Brightness keys | ✅ BrightnessKeys |
| Battery | ✅ SMCBatteryManager |
| Sleep / hibernation | ✅ HibernationFixup |
| USB | ✅ USBMap |
| Camera | ✅ |
| HDMI | ✅ through iGPU (display works; resolution depends on your panel/cable) |
| dGPU models (RX 550) | ⚠️ **Not supported by this repo** — it targets the iGPU-only E480. Hybrid units need an extra SSDT to disable the dGPU. |

## Tested hardware

| Component | Model |
|:---|:---|
| CPU | Intel Core i5-8250U (Kaby Lake-R, 4C/8T) |
| iGPU | Intel UHD Graphics 620 (faked as HD 620) |
| RAM | 24 GB DDR4 |
| dGPU | None (iGPU-only model) |
| Storage | NVMe / SATA SSD |
| WiFi | Intel Wireless-AC 3165 |
| Audio | Conexant CX20753/4 |

Full specs: [`hackintosh-e480/01-specifications/`](hackintosh-e480/01-specifications/specs.md)

## Quick start

1. **Generate your own SMBIOS** — see [SMBIOS section](#generate-your-own-smbios) below.
2. **Set the BIOS** as in [`hackintosh-e480/02-bios-settings/`](hackintosh-e480/02-bios-settings/README.md) (UEFI Only, Secure Boot disabled, AHCI).
3. **Create the installer USB** — [`hackintosh-e480/03-installation/`](hackintosh-e480/03-installation/README.md) (uses `scripts/macrecovery.py`).
4. **Install** — same guide.
5. **Post-install** — [`hackintosh-e480/04-post-install/`](hackintosh-e480/04-post-install/README.md) (move the EFI to the internal disk, clean up boot args).

## Generate your own SMBIOS

The EFI ships with placeholder values. On a Mac with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS):

```sh
cd GenSMBIOS
python3 genSMBIOS.command
# pick MacBookPro15,4 → copy the Serial / Board Serial / UUID it prints
```

Write the values into `EFI/OC/config.plist` → `PlatformInfo → Generic`:

| Key | Value |
|:---|:---|
| `SystemProductName` | `MacBookPro15,4` |
| `SystemSerialNumber` | *your generated serial* |
| `MLB` | *your generated Board Serial* |
| `SystemUUID` | *your generated UUID* |
| `ROM` | your **real** Ethernet MAC address (stable value) |

Each install must use a **fresh set** — never reuse a serial already in use (iCloud/iMessage risk).

## EFI layout

```
EFI/
├── BOOT/
│   └── BOOTx64.efi
└── OC/
    ├── ACPI/            # SSDT-EC-USBX-LAPTOP, SSDT-PLUG-DRTNIA, SSDT-PNLF, SSDT-XOSI
    ├── Drivers/         # HfsPlus, OpenRuntime
    ├── Kexts/           # 22 kexts (see table below)
    ├── Tools/           # OpenShell, CleanNvram
    ├── config.plist
    └── OpenCore.efi
```

## Kext versions

| Kext | Version |
|:---|:---|
| Lilu | 1.7.2 |
| VirtualSMC (+ plugins) | 1.3.7 |
| WhateverGreen | 1.7.0 |
| AppleALC | 1.9.7 |
| VoodooRMI | 1.4.3 |
| VoodooSMBus | 3.0 |
| VoodooPS2Controller | 2.3.7 |
| IntelBluetoothFirmware / IntelBTPatcher | 2.4.0 |
| BlueToolFixup | 2.7.2 |
| NVMeFix | 1.1.3 |
| ECEnabler | 1.0.6 |
| HibernationFixup | 1.5.4 |
| BrightnessKeys | 1.0.3 |
| AirportItlwm | 2.2.0 |
| RealtekRTL8111 | 2.4.2 |
| CtlnaAHCIPort | 341.0.2 |
| USBMap | 1.0 |

## Updating macOS

- **Ventura 13.x / Sonoma 14.x**: System Settings → General → Software Update.
- **Install Sonoma from the App Store**: with Ventura installed, search for "macOS Sonoma" and click **Get** — it downloads and reboots on its own. Back up first and keep OC/kexts up to date (this EFI supports Ventura and Sonoma).

## Repository layout

```
EFI/                     OpenCore EFI — placeholder SMBIOS (OC 1.0.7)
scripts/                 Recovery download utilities (macrecovery)
assets/screenshots/      Screenshots
hackintosh-e480/         Full project documentation
  ├── 01-specifications/
  ├── 02-bios-settings/
  ├── 03-installation/
  ├── 04-post-install/
  ├── 05-open-core-config/    config.plist + key values explained
  └── 06-credits/
```

## Releases

Ready-to-use EFI zips are published under [Releases](https://github.com/LeBritow/E480-Hackintosh/releases). **Generate your own SMBIOS** before using any of them.

## Contributing

Found a fix, a better kext, or a tip for this hardware? Open an **issue** or a **pull request**.

## Credits

See [`hackintosh-e480/06-credits/`](hackintosh-e480/06-credits/README.md).

---

*This project is not affiliated with Apple Inc. All trademarks are the property of their respective owners.*
