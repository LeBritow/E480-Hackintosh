# ThinkPad E480 Hackintosh (macOS Ventura / Sonoma)

OpenCore EFI para o **Lenovo ThinkPad E480** (i5-8250U / i5-8550U) rodando **macOS Ventura 13.7** e **Sonoma 14.7**.

## Aviso legal

Este repositório é para fins educacionais. O macOS é software proprietário da Apple Inc. Você deve ter uma licença legalmente adquirida para usá-lo. Não oferecemos suporte para uso comercial ou pirataria.

## Status

Funcional e testado no E480 com **i5-8250U** + **Intel UHD Graphics 620**. Toda a EFI foi atualizada e validada contra o OpenCore 1.0.7 (`ocvalidate`: 0 erros).

| Item | Detalhe |
|:---|:---|
| Bootloader | OpenCore 1.0.7 |
| macOS | Ventura 13.7 e Sonoma 14.7 (testados) |
| SMBIOS | MacBookPro15,4 |

## Especificações do notebook testado

| Componente | Modelo |
|:---|:---|
| CPU | Intel Core i5-8250U (Kaby Lake-R, 4C/8T) |
| iGPU | Intel UHD Graphics 620 (faked para HD 620) |
| RAM | 8 GB DDR4 |
| SSD | NVMe / SATA (ambos OK) |
| Áudio | Conexant CX20753/4 (`layout-id: 15`) |
| Ethernet | Realtek RTL8111/8168 |
| WiFi | Intel Wireless-AC 3165 |
| Bluetooth | Intel (integrado) |
| Trackpad/TrackPoint | PS/2 + SMBus |

## O que funciona

- ✅ Aceleração gráfica (UHD 620 faked como HD 620, `framebuffer` 3072 MB)
- ✅ Áudio interno e combo headphone/mic (AppleALC `layout-id: 15`)
- ✅ WiFi Intel (AirportItlwm)
- ✅ Bluetooth (IntelBluetoothFirmware)
- ✅ Trackpad, TrackPoint e teclado (VoodooRMI + VoodooPS2)
- ✅ Teclas de brilho (BrightnessKeys)
- ✅ Ethernet (RealtekRTL8111)
- ✅ Bateria (SMCBatteryManager)
- ✅ Hibernação / sleep (HibernationFixup)
- ✅ USB (USBMap)
- ✅ Câmera integrada
- ✅ HDMI na iGPU (2K@60Hz / 4K@30Hz)
- ⚠️ AirDrop/Handoff parcialmente funcionais (limitação do WiFi Intel)

## O que não funciona

- ❌ AirDrop de iPhone → Mac pode falhar (limitação Intel)
- ❌ Tecla `Insert` (não existe no Magic Keyboard)
- ⚠️ dGPU AMD (RX 550 / Radeon) — desabilitada via `disable-external-gpu`; modelos com dGPU podem precisar de SSDT extra

## Pré-requisitos

- Pendrive USB (≥ 8 GB)
- Instalador do macOS Ventura (via `macrecovery` ou Download Completo)
- EFI desta pasta

## Como usar

### 1. Preparar o pendrive

1. Formate o pendrive como **FAT32** (MS-DOS) com esquema **GPT/GUID**.
2. Crie as pastas `EFI` e `com.apple.recovery.boot` na raiz.
3. Copie a pasta `EFI` deste repositório para dentro do pendrive.
4. Gere o instalador:
   ```sh
   python macrecovery.py -b Mac-B4831CEBD52A0C4C download -os latest
   ```
   Copie o conteúdo de `com.apple.recovery.boot/` para o pendrive.

### 2. Configurações de BIOS

- **Security** → **Intel SGX**: `Software Controlled`
- **Startup** → **UEFI/Legacy Boot**: `Both`, prioridade **UEFI First**
- **Startup** → **Quick Boot**: `Enabled`

### 3. Instalar

1. Boote pelo pendrive (menu F12 → USB).
2. No picker, escolha a opção do instalador.
3. **Utilitário de Discos** → Apague o disco interno como **APFS** / **GUID**.
4. **Instalar macOS** → escolha o disco interno.
5. Deixe o pendrive conectado durante as reinicializações (uma delas vai bootar de volta para o pendrive — é normal).

### 4. Pós-instalação

Depois de instalado, monte a partição EFI do **disco interno** e copie a pasta `EFI` para ela:

```sh
diskutil mount disk0s1
```

Copie `EFI/` do pendrive para a partição EFI do disco. Agora o macOS pode dar boot sem o pendrive.

> ⚠️ **IMPORTANTE:** gere um SMBIOS **próprio** e edite o `config.plist` (veja abaixo). O repositório **não** inclui serial/MLB/UUID válidos — se você usar os mesmos de outra pessoa, iMessage/FaceTime quebram.

## Gerar SMBIOS próprio (obrigatório)

```sh
python macserial -m MacBookPro15,4
```

Edite `PlatformInfo → Generic` no `config.plist` (use o ProperTree com `OC Snapshots`):

- `MLB` (Board Serial)
- `SystemSerialNumber`
- `SystemUUID`
- `ROM`

## Estrutura da EFI

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

## Versões (kexts)

| Kext | Versão |
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

## Atualizando o macOS

- **Ventura 13.x / Sonoma 14.x**: Ajustes → Geral → Atualização de Software.
- **Instalar o Sonoma direto pela App Store**: com o Ventura instalado, pesquise por "macOS Sonoma" na App Store e clique em **Obter/Instalar** — o instalador baixa e reinicia sozinho. O mesmo vale para outros macOS compatíveis. Faça backup antes e mantenha o OC/kexts atualizados (o EFI atual aguenta Ventura, Sonoma e Sequoia).

## Créditos

- [OpenCore Team](https://github.com/acidanthera) — OpenCore e kexts
- [Dortania](https://dortania.github.io/OpenCore-Install-Guide/) — guia
- [SukkaW](https://github.com/SukkaW/ThinkPad-E480-Hackintosh) — EFI de referência original

## Suporte

Abra uma [issue](https://github.com/SEU_USUARIO/E480-Hackintosh/issues) com o log de boot (`-v`) se algo falhar.

---

*Este projeto não é afiliado à Apple Inc. Todos os nomes de marcas são propriedade de seus respectivos donos.*
