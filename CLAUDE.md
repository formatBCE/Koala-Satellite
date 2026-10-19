# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

Koala Satellite is a DIY voice satellite for Home Assistant Assist. The hardware is an ESP32-S3 board and a Seeed ReSpeaker Lite board (XMOS XU-316). The firmware is ESPHome YAML only. The repository has no application source code, no test suite, and no linter configuration. The firmware is a fork of the Home Assistant Voice PE firmware (`esphome/home-assistant-voice-pe`). Keep the behavior aligned with Voice PE where you can.

Other content:
- `casing/`: 3D-print files (STEP, STL, 3MF) and assembly images. Do not edit these files.
- `instructions/`: user guides for assembly, parts, and software.
- `respeaker_lite_i2s_dfu_firmware_48k_v1.1.0.bin`: XMOS firmware for manual flash only.
- `koala-factory.bin`: a legacy binary. The current factory firmware comes from GitHub Pages.
- `ota/`: the CI workflow writes this directory. Do not edit it by hand.

## Commands

ESPHome is not installed on this machine. Install it before you build (`pip install esphome` or `uvx esphome ...`).

```bash
# Validate the configuration (fast, no compile)
esphome config config/koala-satellite.yaml

# Compile the factory firmware (the same configuration that CI publishes)
esphome compile config/koala-satellite.yaml

# Compile and flash over USB or OTA
esphome run config/koala-dashboard.yaml
```

`config/koala-dashboard.yaml` needs a `config/secrets.yaml` file with `wifi_ssid` and `wifi_password`. `koala-satellite.yaml` does not need secrets, because it provisions Wi-Fi through BLE Improv.

Build artifacts go to `config/.esphome/build/`.

## Configuration architecture

The firmware is a set of packages. Most of them come from `formatBCE/Respeaker-Lite-ESPHome-integration` at `respeaker_lite_ref`: the voice satellite core (`packages/core/*.yaml`) and the ReSpeaker Lite hardware (`packages/respeaker-lite/hardware.yaml`). That repository's CLAUDE.md describes them, and the header of its `packages/core/base.yaml` lists the IDs that a device package must supply. This repository holds only the Koala hardware, in `packages/koala/device.yaml` (about 800 lines).

`config/common/koala-base.yaml` joins them: one remote package for Lite, then `!include` of the Koala device package. The two top-level files include `koala-base.yaml`.

- To change voice, media, timer, alarm, or button behavior, change the Lite repository, release it, and bump `respeaker_lite_ref` here.
- `packages/koala/device.yaml` hooks into core items with `id: !extend <id>` (`alarm_on`, `external_media_player`). It also supplies the hooks that `core/controls.yaml` calls: `control_leds_status_off`, `control_leds_factory_reset_coming_up`, and `control_leds_factory_reset_confirmed`.
- `alarm_on` in the device package uses the `then:` trigger form on purpose. A map replaces the core trigger list. A list would add to it.
- To verify a refactor, run `esphome config` before and after, and diff the merged output.

- `config/koala-satellite.yaml`: the factory firmware. It adds `improv_serial`, `esp32_improv` (BLE), and `dashboard_import`. It also adds a package that disables BLE after the Wi-Fi connection and enables BLE again on disconnect. The `ota: http_request` and `update:` blocks stay commented out on purpose. The comment says "Commented out for auto-publishing". Do not uncomment these blocks in a commit. Uncomment them only for a local manual build.
- `config/koala-dashboard.yaml`: the configuration for users who build in their own ESPHome dashboard. Users copy only this file. It gets `config/common/koala-base.yaml` from this repository at `koala_ref`, and it uses `!secret` Wi-Fi credentials and a fixed name.
- The factory firmware's `dashboard_import` points at `config/common/koala-base.yaml@${koala_ref}`, so an adopted device imports the release it runs.

Important parts of the configuration:
- **External components** (in Lite's `packages/respeaker-lite/hardware.yaml`) pull git refs with `refresh: 0s`:
  - `formatBCE/esphome@${i2s_audio_ref}` (the `respeaker_microphone` branch) replaces the core `i2s_audio` component. A change in that branch changes the build output without a commit here.
  - `formatBCE/Respeaker-Lite-ESPHome-integration@${respeaker_lite_ref}` supplies `respeaker_lite` (the XMOS control, mute, and DFU auto-update). The DFU `url` and the `kenobi` wake word model also come from that repository at the same ref, not from the `.bin` file in this repository.
  - `respeaker_lite_ref` pins a Lite release tag. Update it on purpose when Koala needs a newer Lite release, and make sure that the tag exists before a Koala release.
- **LED state machine** (in `packages/koala/device.yaml`): the `control_leds` script is the single dispatcher. It reads globals (`init_in_progress`, `improv_ble_in_progress`, `voice_assistant_phase`, `is_timer_active`, and others) and runs one `control_leds_*` script. When you add a new device state, add a global and a branch in `control_leds`. Then call `script.execute: control_leds`. Do not drive the LEDs directly from event handlers.
- **Voice assistant phases** are numeric substitutions (`voice_assist_*_phase_id`) in Lite's `packages/core/base.yaml`. They match the Voice PE values.
- **Audio path**: I2S input and output share pins GPIO7, GPIO8, and GPIO9 through the `i2s_input` and `i2s_output` IDs. The speaker chain is `i2s_audio` → `mixer` → `resampler` at 48 kHz. The ReSpeaker DFU firmware must be the 48 kHz build.
- **Sounds** come from URLs in the Voice PE repository through `substitutions` in Lite's `packages/core/voice-assistant.yaml`.
- `esphome.min_version` follows the ESPHome release that the firmware needs. Update it when you use newer ESPHome features.

## Release process

The release is `.github/workflows/release.yaml`. A person starts it manually (`workflow_dispatch`) with a `version` input.

1. Set the new version in three places: `esphome.project.version` in `packages/koala/device.yaml`, and `koala_ref` in `config/koala-satellite.yaml` and `config/koala-dashboard.yaml`. The CI step fails if a value is different from the input. The check reads the **first** `version:` line of `packages/koala/device.yaml`, so do not add another `version:` key before `esphome.project`. If Koala needs a newer Lite release, set `respeaker_lite_ref` in `config/common/koala-base.yaml`, and make sure that the Lite tag exists.
2. CI runs `uvx ewt-gen config/koala-satellite.yaml`. This command builds the firmware and generates the ESP Web Tools site.
3. CI adds an `ota` block (md5, path, release URL) to `manifest.json` and copies `firmware.ota.bin` as `koala-<version>.ota.bin`.
4. CI deploys the site to GitHub Pages (`https://formatbce.github.io/Koala-Satellite`). Factory devices get OTA updates from `manifest.json` on this site. The `update:` block is commented out in the YAML, so `ewt-gen` (through `--publish-url`) must supply it. This repository does not confirm that behavior.
5. CI replaces `ota/` with the new manifest and binary and commits as `github-actions[bot]`. CI then creates a GitHub release with that tag.

Version format is calendar based: `YYYY.M.N` (for example, `2026.6.0`).
