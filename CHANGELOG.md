# Changelog

## v1.2.0

- **SMBIOS now placeholders** (`AAAA...`) matching the T440p repo convention — validated with `ocvalidate` 1.0.7 (0 issues)
- Added `scripts/` (macrecovery — recovery partition download utility)
- Docs restructured in the numbered style of the T440p repo: `hackintosh-e480/01-specifications` … `06-credits`
- New `hackintosh-e480/05-open-core-config/` — full config reference + key values explained
- README rewritten to the T440p standard (status table, disclaimer, releases, kext versions)

## v1.1.0

- Documented support for **macOS Sonoma 14.7** (tested: App Store upgrade, no EFI changes)
- Docs translated to English
- Hardware table updated (24 GB RAM, iGPU-only model, no dGPU)

## v1.0.0

- **OpenCore 0.7.x → 1.0.7** (reason: old OC + new installer = kernel panic `Invalid frame pointer` after `EXITBS:START`)
- **Kexts updated**: Lilu 1.7.2, VirtualSMC 1.3.7, WhateverGreen 1.7.0, AppleALC 1.9.7, VoodooRMI 1.4.3, VoodooPS2Controller 2.3.7, VoodooSMBus 3.0, IntelBluetoothFirmware/IntelBTPatcher 2.4.0, BlueToolFixup 2.7.2, NVMeFix 1.1.3, ECEnabler 1.0.6, HibernationFixup 1.5.4, BrightnessKeys 1.0.3
- **config.plist** validated with the OC 1.0.7 `ocvalidate`: **0 issues**
- SMBIOS **MacBookPro15,4** (appropriate for Kaby Lake-R i5-8250U)
- `ig-platform-id` `0x87C00000` + `device-id` `0x1659` (UHD 620 faked as HD 620 — the correct E480 setup)
- `disable-external-gpu` (disables the AMD dGPU on hybrid models)
- `layout-id` 15 (Conexant CX20753/4)
- Public SMBIOS **cleared** — users must generate their own (genSMBIOS/macserial)
