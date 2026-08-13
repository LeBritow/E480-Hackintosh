# Changelog

## v1.1.0

- Documentado suporte a **macOS Sonoma 14.7** (testado: upgrade pela App Store, sem mudanças na EFI)

## v1.0.0

- **OpenCore 0.7.x → 1.0.7** (motivo: OC antigo + instalador novo = kernel panic `Invalid frame pointer` após `EXITBS:START`)
- **Kexts atualizados**: Lilu 1.7.2, VirtualSMC 1.3.7, WhateverGreen 1.7.0, AppleALC 1.9.7, VoodooRMI 1.4.3, VoodooPS2Controller 2.3.7, VoodooSMBus 3.0, IntelBluetoothFirmware/IntelBTPatcher 2.4.0, BlueToolFixup 2.7.2, NVMeFix 1.1.3, ECEnabler 1.0.6, HibernationFixup 1.5.4, BrightnessKeys 1.0.3
- **config.plist** validado com `ocvalidate` do OC 1.0.7: **0 erros**
- SMBIOS **MacBookPro15,4** (apropriado para Kaby Lake-R i5-8250U)
- `ig-platform-id` `0x87C00000` + `device-id` `0x1659` (UHD 620 faked como HD 620 — setup correto do E480)
- `disable-external-gpu` (dGPU AMD desligada)
- `layout-id` 15 (Conexant CX20753/4)
- SMBIOS público **limpo** — usuário deve gerar o próprio (genSMBIOS/macserial)
