# BIOS / UEFI settings (E480)

These are the settings verified on the E480's own Insyde UEFI BIOS. Enter the BIOS at the Lenovo logo (**F1**).

| Option | Path in the E480 BIOS | Setting |
|:---|:---|:---|
| Boot mode | **Startup → UEFI/Legacy Boot** | **UEFI Only** |
| Secure Boot | **Security → Secure Boot** | **Disabled** |
| Quick Boot | **Startup → Quick Boot** | Enabled |
| USB | **Config → USB** (`USB UEFI BIOS Support`, `Always On USB`) | Enabled |
| Intel SpeedStep | **Config → Power → Intel SpeedStep** | Enabled |
| Hyper-Threading | **Config → CPU → Hyper-Threading Technology** | Enabled |
| Serial ATA | **Config → Serial ATA → SATA Controller Mode Option** | **AHCI** |
| Boot order | **Startup → Boot** | USB stick first (or **F12** at boot) |

Caveats:

- **Secure Boot** is only changeable when *UEFI/Legacy Boot* is already set to *UEFI Only*.
- **VT-d** (`Security → Virtualization → Intel VT-d Feature`) does not matter: `DisableIoMapper=true` already handles it.
- **CFG Lock** has no visible switch on this BIOS — the config sets `AppleXcpmCfgLock=true` instead.
- The config also sets `csr-active-config = 0x0000` (SIP fully enabled).
