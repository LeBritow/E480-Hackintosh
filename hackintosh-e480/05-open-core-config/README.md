# OpenCore configuration

The production `config.plist` is [here](config.plist) (same file as `EFI/OC/config.plist`).

## Key values

| Section | Key | Value | Why |
|:---|:---|:---|:---|
| ACPI | Add | `SSDT-PLUG-DRTNIA`, `SSDT-EC-USBX-LAPTOP`, `SSDT-PNLF`, `SSDT-XOSI` | CPU power management, EC, backlight, OSI/XOSI |
| ACPI | Patch | `Change _OSI to XOSI` | needed by SSDT-XOSI |
| DeviceProperties | `Pci(0x2,0x0)` | `AAPL,ig-platform-id = 0x87C00000`, `device-id = 0x1659` | UHD 620 faked as HD 620 (the classic E480 setup) |
| DeviceProperties | `Pci(0x2,0x0)` | `framebuffer-unifiedmem = 3072 MB` | VRAM |
| DeviceProperties | `Pci(0x2,0x0)` | `disable-external-gpu` | keeps the AMD dGPU (on hybrid models) out of macOS |
| DeviceProperties | `Pci(0x1b,0x0)` | `layout-id = 15` | Conexant CX20753/4 audio |
| Kernel | Quirks | `AppleXcpmCfgLock = true` | CFG Lock is not exposed in this BIOS |
| Kernel | Quirks | `DisableIoMapper = true` | handles VT-d |
| Kernel | Add | 22 kexts | see the versions table in the README |
| Misc | Boot | `ShowPicker = true`, `Timeout = 5` | picker shown; hold for install |
| NVRAM | boot-args | `-v keepsyms=1 debug=0x100 alcid=15` | verbose during install, `alcid=15` for audio |
| NVRAM | csr-active-config | `0x0000` | SIP fully enabled |
| PlatformInfo | Generic | `MacBookPro15,4`, placeholder values | correct SMBIOS; generate your own |

## Quirk notes

- **`SetupVirtualMap`, `ProvideCustomSlide`, `EnableWriteUnprotector`**: standard set for this firmware.
- **`RequestBootVarRouting`** enabled: required for NVRAM writes to survive.
- **`UnblockFsConnect`** stays disabled; the built-in firmware handles the E480 fine.
- The whole `config.plist` validates clean with the OpenCore 1.0.7 `ocvalidate` (0 issues).
