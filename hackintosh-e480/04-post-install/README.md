# Post-install — ThinkPad E480

## 1. Copy the EFI to the internal disk

Once the system is installed and boots from the USB stick:

1. Mount the internal disk's EFI partition:
   ```sh
   diskutil mount disk0s1
   ```
2. Copy the `EFI/` folder from the USB stick to the mounted EFI partition.
3. Reboot (with the USB stick still connected, just in case) and confirm the system boots without it. Then you can remove it.

## 2. Generate your own SMBIOS (IMPORTANT)

The `config.plist` in this repository ships **without** valid serial/MLB/UUID. To use iMessage, FaceTime, iCloud etc., generate your own:

```sh
# macOS
python macserial -m MacBookPro15,4
```

Open the `config.plist` in [ProperTree](https://github.com/corpnewt/ProperTree) and use **File → OC Snapshot** (or edit manually):

- `PlatformInfo → Generic → SystemSerialNumber`
- `PlatformInfo → Generic → MLB`
- `PlatformInfo → Generic → SystemUUID`
- `PlatformInfo → Generic → ROM` (12 hex digits, e.g. `A1B2C3D4E5F6`)

> Validate the serial on the [Apple site](https://checkcoverage.apple.com/). It should return "Please enter a valid serial number" — meaning an unused serial.

## 3. Remove verbose boot

The `config.plist` ships with `boot-args = -v` for debugging. For a clean boot:

1. Edit `NVRAM → Add → 7C436110-... → boot-args` in ProperTree: `alcid=15` (drop `-v`).
2. Reset NVRAM afterwards (see below).

## 4. Reset NVRAM (when needed)

In the OpenCore picker, press `Space` to reveal tools and choose **CleanNvram**. Useful when:
- Boot hangs after a config change
- Boot audio/sound issues
- Leftover vars from another SMBIOS

## 5. Sleep and hibernation

- Normal sleep works (HibernationFixup).
- For hibernation modes 3/25, adjust:
  ```sh
  sudo pmset -a hibernatemode 0
  ```
- "Black screen on wake" has `ReservedMemory` disabled by default; if it happens, enable the `Fix black screen on wake` entry under `UEFI → ReservedMemory`.

## 6. Updates

- **Ventura 13.x / Sonoma 14.x**: System Settings → General → Software Update.
- **Upgrade to a newer macOS**:
  1. Back up (Time Machine)
  2. Update OpenCore + kexts to the latest versions
  3. Make sure `config.plist` still passes (`ocvalidate`)
  4. Then run the upgrade

## 7. Backup the EFI

Always keep a working copy of the `EFI` folder (on a USB stick or another OS). It will save you.
