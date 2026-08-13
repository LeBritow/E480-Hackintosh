# ThinkPad E480 Hackintosh (macOS Ventura / Sonoma)

OpenCore EFI for the **Lenovo ThinkPad E480** (i5-8250U / i5-8550U) running **macOS Ventura 13.7** and **Sonoma 14.7**.

## Disclaimer

This repository is for educational purposes only. macOS is proprietary software owned by Apple Inc. You must have a legally acquired license to use it. No commercial or piracy support is provided.

## Status

Functional and tested on an E480 with **i5-8250U** + **Intel UHD Graphics 620**. The whole EFI was updated and validated against OpenCore 1.0.7 (`ocvalidate`: 0 issues).

| Item | Detail |
|:---|:---|
| Bootloader | OpenCore 1.0.7 |
| macOS | Ventura 13.7 and Sonoma 14.7 (both tested) |
| SMBIOS | MacBookPro15,4 |

## Tested hardware

| Component | Model |
|:---|:---|
| CPU | Intel Core i5-8250U (Kaby Lake-R, 4C/8T) |
| iGPU | Intel UHD Graphics 620 (faked as HD 620) |
| RAM | 24 GB DDR4 |
| dGPU | None (iGPU-only model) |
| SSD | NVMe / SATA (both work) |
| Audio | Conexant CX20753/4 (`layout-id: 15`) |
| Ethernet | Realtek RTL8111/8168 |
| WiFi | Intel Wireless-AC 3165 |
| Bluetooth | Intel (on-board) |
| Trackpad/TrackPoint | PS/2 + SMBus |

## What works

- ✅ Graphics acceleration (UHD 620 faked as HD 620, 3072 MB framebuffer)
- ✅ Internal speakers and headphone/mic combo (AppleALC `layout-id: 15`)
- ✅ Intel WiFi (AirportItlwm)
- ✅ Bluetooth (IntelBluetoothFirmware)
- ✅ Trackpad, TrackPoint and keyboard (VoodooRMI + VoodooPS2)
- ✅ Brightness keys (BrightnessKeys)
- ✅ Ethernet (RealtekRTL8111)
- ✅ Battery (SMCBatteryManager)
- ✅ Sleep / hibernation (HibernationFixup)
- ✅ USB (USBMap)
- ✅ Built-in camera
- ✅ HDMI through iGPU (2K@60Hz / 4K@30Hz)
- ⚠️ AirDrop/Handoff partially working (Intel WiFi limitation)

## What doesn't work

- ❌ AirDrop from iPhone to Mac may fail (Intel limitation)
- ❌ `Insert` key (not present on the Magic Keyboard)
- ⚠️ **dGPU models (RX 550 / Radeon) are not supported** — this repo targets the iGPU-only E480. If your unit has the AMD dGPU, an extra SSDT to disable it will be required.

## Prerequisites

- USB stick (≥ 8 GB)
- macOS Ventura installer (via `macrecovery` or full download)
- EFI from this repository

## Usage

### 1. Prepare the USB stick

1. Format the USB stick as **FAT32** (MS-DOS) with **GPT/GUID** scheme.
2. Create `EFI` and `com.apple.recovery.boot` folders at the root.
3. Copy the `EFI` folder from this repository into the USB stick.
4. Generate the installer:
   ```sh
   python macrecovery.py -b Mac-B4831CEBD52A0C4C download -os latest
   ```
   Copy the contents of `com.apple.recovery.boot/` to the USB stick.

### 2. BIOS settings

- **Security** → **Intel SGX**: `Software Controlled`
- **Startup** → **UEFI/Legacy Boot**: `Both`, priority **UEFI First**
- **Startup** → **Quick Boot**: `Enabled`

### 3. Install

1. Boot from the USB stick (F12 menu → USB).
2. In the picker, choose the installer entry.
3. **Disk Utility** → Erase the internal disk as **APFS** / **GUID**.
4. **Install macOS** → select the internal disk.
5. Keep the USB stick connected through all reboots (one of them boots back to the USB stick — that's normal).

### 4. Post-install

After installation, mount the EFI partition of the **internal disk** and copy the `EFI` folder to it:

```sh
diskutil mount disk0s1
```

Copy `EFI/` from the USB stick to the internal disk's EFI partition. Now macOS can boot without the USB stick.

> ⚠️ **IMPORTANT:** generate your **own** SMBIOS and edit the `config.plist` (see below). This repository does **not** include a valid serial/MLB/UUID — using someone else's will break iMessage/FaceTime.

## Generate your own SMBIOS (required)

```sh
python macserial -m MacBookPro15,4
```

Edit `PlatformInfo → Generic` in the `config.plist` (use ProperTree with OC Snapshots):

- `MLB` (Board Serial)
- `SystemSerialNumber`
- `SystemUUID`
- `ROM`

## EFI layout

```
EFI/
├── BOOT/
│   └── BOOTx64.efi
└── OC/
    ├── ACPI/            # SSDT-EC-USBX-LAPTOP, SSDT-PLUG-DRTNIA, SSDT-PNLF, SSDT-XOSI
    ├── Drivers/         # HfsPlus, OpenRuntime
    ├── Kexts/           # Lilu, VirtualSMC, WhateverGreen, AppleALC, etc.
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
- **Install Sonoma straight from the App Store**: with Ventura installed, search for "macOS Sonoma" in the App Store and click **Get/Install** — the installer downloads and reboots on its own. The same applies to other supported macOS versions. Back up first and keep OC/kexts up to date (this EFI supports Ventura, Sonoma and Sequoia).

## Credits

- [OpenCore Team](https://github.com/acidanthera) — OpenCore and kexts
- [Dortania](https://dortania.github.io/OpenCore-Install-Guide/) — guide
- [SukkaW](https://github.com/SukkaW/ThinkPad-E480-Hackintosh) — original reference EFI

## Support

Open an [issue](https://github.com/LeBritow/E480-Hackintosh/issues) with a boot log (`-v`) if something fails.

---

*This project is not affiliated with Apple Inc. All trademarks are the property of their respective owners.*
