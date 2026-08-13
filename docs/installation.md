# Installing macOS Ventura / Sonoma on the E480

Step-by-step guide to install macOS on the ThinkPad E480 using this EFI. Tested with **Ventura 13.7** (clean install) and **Sonoma 14.7** (App Store upgrade).

## What you need

- **Hardware:** ThinkPad E480 (i5-8250U/i5-8550U), 8 GB+ RAM, SSD
- **USB stick** (≥ 8 GB)
- **Access to a macOS** (optional but helpful) or a system with `python3`
- **BIOS configured** (see below)

## Step 1 — Configure BIOS

Reboot and enter the BIOS (`F1`):

| Setting | Value |
|:---|:---|
| Security → Intel SGX | Software Controlled |
| Startup → Boot Mode | Both (UEFI + Legacy) |
| Startup → Boot Priority | UEFI First |
| Startup → Quick Boot | Enabled |

## Step 2 — Create the installer USB

### On macOS

```sh
# 1. Download macOS Ventura
softwareupdate --list-all

# 2. Or use the recovery script (faster):
cd Utilities/macrecovery
python3 macrecovery.py -b Mac-B4831CEBD52A0C4C download -os latest
```

This generates the `com.apple.recovery.boot/` folder with `BaseSystem.dmg` and `BaseSystem.chunklist`.

### Prepare the USB stick

1. **Disk Utility** → erase the USB stick as:
   - Name: `USB`
   - Format: **MS-DOS (FAT)**
   - Scheme: **GUID Partition Map**
2. Mount the USB stick and create the `EFI` and `com.apple.recovery.boot` folders at its root.
3. Copy the contents of `com.apple.recovery.boot/` into the matching folder on the USB stick.
4. Copy the `EFI/` folder from this repository to the USB stick root.

## Step 3 — Install

1. Plug the USB stick in and power on the E480.
2. Open the boot menu (`F12`) and select the USB stick.
3. In the OpenCore picker, choose the installer entry (e.g. "macOS Base System").
4. **Utilities → Disk Utility**:
   - Select the internal disk (careful: do NOT erase the USB stick!)
   - Erase → Name `Macintosh HD`, Format **APFS**, Scheme **GUID**
5. Close Disk Utility → **Install macOS** → select `Macintosh HD`.
6. The machine reboots several times. **Keep the USB stick connected for ALL reboots** — the OpenCore on the USB stick keeps the boot going.
7. If the picker stops showing the installer and only shows the disk, just pick the disk entry (the installation continues).

## Step 4 — Post-install

Follow the [post-install guide](post-install.md):

1. Generate your own SMBIOS
2. Copy the EFI to the internal disk
3. Remove `-v` from boot args

## Step 5 — Upgrade to Sonoma (optional)

With Ventura installed and working, Sonoma upgrades **without the USB stick**:

1. Make a backup (Time Machine).
2. System Settings → General → Software Update, **or** search for "macOS Sonoma" in the **App Store** and click **Get**.
3. The installer downloads (~12 GB), reboots on its own and does everything — the current EFI (OC 1.0.7 + up-to-date kexts) already supports Sonoma with no changes.

## Troubleshooting

| Problem | Fix |
|:---|:---|
| Kernel panic right after `EXITBS:START` | Update OpenCore/kexts (this EFI is already up to date) |
| Black screen | Check `boot-args`; try `igfxonln=1` |
| No audio | `alcid=15` is already configured |
| WiFi missing | AirportItlwm needs the version matching your installed macOS |
| Keeps booting back to the USB stick | Did you copy the EFI to the internal disk? (post-install) |
| iMessage won't activate | Generate your own serial/MLB/UUID (don't use the repo's) |

If nothing helps, open an issue with the log:
1. Add `-v` to `boot-args`
2. Press `F2` in the OpenCore picker to save the log (`opencore-YYYY-MM-DD-HHMMSS.txt`)
3. Attach the log to the issue
