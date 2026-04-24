# firmware_beni_releases

Public release artifacts for the Beni device firmware.

This repository is **binaries-only** — no source. It exists so that
already-deployed Beni devices can fetch OTA updates from a stable,
publicly accessible URL without needing a GitHub auth token.

- Source lives in the private `Ammer-Tech/firmware_beni` repository.
- Releases here are published automatically by the
  `firmware-release.yml` workflow in the source repo every time a `v*`
  tag is cut.
- Devices poll
  `https://github.com/Ammer-Tech/firmware_beni_releases/releases/latest/download/manifest.json`
  — GitHub stably redirects that URL to the latest non-prerelease tag.

## Release artifacts

Each `v*` release contains:

| File | Purpose |
|------|---------|
| `manifest.json` | OTA manifest. `{ "version", "url", "sha256" }`. Devices read this first. |
| `beni_firmware.bin` | App-only image. OTA downloads this directly into `ota_1`. |
| `beni_firmware-<tag>-esp32s3.bin` | Merged flash image for first-time USB flashing (bootloader + partition table + app + SPIFFS models, one file). |
| `bootloader.bin`, `partition-table.bin` | Individual partitions, for low-level recovery. |
| `flasher_args.json` | esptool argument reference for the partition layout. |

## Flashing a fresh device

See the README in `Ammer-Tech/firmware_beni` (access may be restricted)
or use the single-image path:

```
esptool.py --chip esp32s3 --port <PORT> -b 460800 \
    write_flash 0x0 beni_firmware-<tag>-esp32s3.bin
```

Already-flashed devices update themselves over Wi-Fi — no manual
action needed.
