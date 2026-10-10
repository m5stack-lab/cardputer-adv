# ADR 0002: Firmware Catalog — M5Launcher Base + Swappable Payloads

## Status
Accepted — 2026-08-23, firmware staged same day. Doom skipped for now — see Consequences. Amended 2026-10-10: Meshtastic source pinned and Bruce's canonical repo confirmed; licenses for every staged binary recorded in [NOTICE.md](../NOTICE.md).

## Context
- ADR 0001 established the manual macOS process for getting a single firmware onto the Cardputer ADV via SD card.
- This project's actual goal (per the top-level README) is to streamline that process for *multiple* firmwares that get swapped between, not just one.
- To support swapping without reflashing over serial each time, we need a base firmware that can boot/chain-load other firmware payloads from the SD card, plus a defined, named set of payload firmwares to track and periodically update.
- A research pass (web search, 2026-08-23) checked each requested firmware against its actual GitHub repo, README, and release notes. Doom's source was confirmed directly. The Meshtastic mesh kit firmware's source is intentionally left unpinned for now — a working `.bin` is already staged, and none of the researched candidate repos matched its filename or version.
- A follow-up pass the same day pulled each firmware's actual release assets via the GitHub API/CLI to confirm exact filenames and, where it exists, dedicated Cardputer ADV support — resolving several "not explicitly confirmed" items from the first pass. Results are in the Staged Firmware section below.

## Decision
Use **M5Launcher** as the base/boot firmware. Maintain a staging folder (`firmware/`) with one subdirectory per payload firmware, matching the catalog below. Downloaded `.bin` files for a given firmware land in its named subfolder, ready to be copied to the SD card's `downloads` folder per the ADR 0001 process.

### Base firmware

| Firmware | Repo | Author | Description |
|---|---|---|---|
| M5Launcher | [bmorcelli/Launcher](https://github.com/bmorcelli/Launcher) | bmorcelli | Multi-firmware launcher for ESP32 boards (M5Stack, LILYGO, Marauder, CYD, etc). Manages SD card files, installs binaries OTA, has a WebUI for SD management, FAT/SPIFFS backup-restore. This is what lets the device boot into any payload firmware below without reflashing over serial. |

Forks/mirrors exist (`konsumer/m5-launcher`, `maxallion/Cardputer-Launcher`, `TheRealHaoLiu/cardputer-adv-launcher`) — bmorcelli's is the original and the one we're tracking.

### Payload firmwares

| # | Firmware | Repo | Author | Description | ADV support | Verification |
|---|---|---|---|---|---|---|
| 1 | Evil-Cardputer | [7h30th3r0n3/Evil-M5Project](https://github.com/7h30th3r0n3/Evil-M5Project) | 7h30th3r0n3 | Wi-Fi exploration/ethical-hacking tool: scan/monitor/interact with networks, GPS-linked SSID capture exportable to Wigle CSV, BadUSB support. "Evil-Cardputer" is a build target within this repo, not a standalone one. | **Confirmed** — dedicated `Evil-ADV-*.bin` build exists | High confidence |
| 2 | Bruce | [BruceDevices/firmware](https://github.com/BruceDevices/firmware) (formerly `pr3y/Bruce`) | pr3y | Offensive-security/red-team ESP32 firmware, AGPL-licensed, authorized-testing-only. Cardputer and Cardputer ADV share one binary; ADV adds pins for IR/RF/GPS. | Confirmed (single shared binary, no separate ADV asset) | High confidence — `pr3y/Bruce` now redirects to `BruceDevices/firmware` (confirmed 2026-10-10) |
| 3 | Cardputer User Demo | [m5stack/M5Cardputer-UserDemo](https://github.com/m5stack/M5Cardputer-UserDemo) | M5Stack (official) | Official stock demo firmware for hardware evaluation. | **Confirmed** — dedicated `ADV-V0.3` release exists | High confidence |
| 4 | Cardputer Mesh Kit Meshtastic Firmware | [meshtastic/firmware](https://github.com/meshtastic/firmware) @ [`a00675e`](https://github.com/meshtastic/firmware/commit/a00675e00c3445927d5e24ce820bc40bde34aaaf) (`develop`, 2026-08-13), build target `m5stack-cardputer-adv` | Meshtastic | Meshtastic (LoRa mesh comms) firmware for M5Stack's "Cardputer Mesh Kit" (LoRa + GNSS) hardware. GPL-3.0. | Confirmed — upstream has a dedicated `m5stack_cardputer_adv` variant | **Pinned 2026-10-10** — the binary embeds version `2.8.0.a00675e`; that commit matches the filename's build date. Built on Windows by a third party, not Meshtastic CI |
| 5 | NEMO | [n0xa/m5stick-nemo](https://github.com/n0xa/m5stick-nemo) | n0xa | Wi-Fi/BLE tool with defensive-leaning features — "Deauth Hunter" and "BLE Hunter." | Confirmed (single shared "M5Cardputer" binary, no separate ADV asset) | High confidence |
| 6 | Marauder | [justcallmekoko/ESP32Marauder](https://github.com/justcallmekoko/ESP32Marauder) | justcallmekoko | Suite of Wi-Fi/Bluetooth offensive and defensive tools for ESP32. | **Confirmed** — dedicated `*_m5cardputer_adv.bin` build exists | High confidence |
| 7 | Doom | [romalik/m5cardputer_doom](https://github.com/romalik/m5cardputer_doom) | romalik | prBoom-GBA-derived Doom port for Cardputer; HAL from M5's Official User Demo, WAD embedded in binary, SD save/load, built on ESP-IDF v4.4.6. | Not confirmed | **Skipped for now** — no prebuilt binary exists; local build attempt blocked on host disk space, see Consequences |
| 8 | Porkchop | [0ct0sec/M5PORKCHOP](https://github.com/0ct0sec/M5PORKCHOP) | 0ct0sec ("octo") | Wi-Fi/BLE recon firmware: promiscuous capture + EAPOL extraction, GPS wardriving w/ Wigle export, 2.4 GHz spectrum analysis, BLE spam, beacon injection, ESP-NOW sync (PigSync), WPA-sec integration. Matches ADR 0001's source material exactly — mode names OINK, DO NO HAM, WARHOG, PIGGYBLUES, HOG ON SPECTRUM all confirmed in the README. | Confirmed (subject of ADR 0001) | High confidence |

### Staged firmware (downloaded 2026-08-23)

| Firmware | Staged file | Version |
|---|---|---|
| M5Launcher | `m5launcher/Launcher-m5stack-cardputer.bin` | 2.8.0 |
| Evil-Cardputer | `evil-cardputer/Evil-ADV-V1.4.5.bin` | 1.4.5 (ADV-specific build; lags behind non-ADV `v1.5.4`) |
| Bruce | `bruce/Bruce-m5stack-cardputer.bin` | 1.16.1 |
| Cardputer User Demo | `cardputer-user-demo/K132-Adv-Cardputer-Adv-UserDemo-V0.3_0x0.bin` | ADV-V0.3 |
| Cardputer Mesh Kit Meshtastic | `meshtastic-mesh-kit/meshtastic-for-cardputer-adv-with-cap-lora.2-8-0-13-08-2026.bin` | 2.8.0.a00675e (build 2026-08-13) |
| NEMO | `nemo/M5Nemo-v3.2.2-M5Cardputer.bin` | 3.2.2 |
| Marauder | `marauder/esp32_marauder_v1_15_0_20260820_m5cardputer_adv.bin` | 1.15.0 |
| Doom | *(skipped — see Consequences)* | — |
| Porkchop | `porkchop/firmware_v0.1.8b-PSTH-PRE-RELEASE.bin` | v0.1.8b-PSTH |

### Staging folder layout

```
firmware/
  m5launcher/
  evil-cardputer/
  bruce/
  cardputer-user-demo/
  meshtastic-mesh-kit/
  nemo/
  marauder/
  doom/            # empty — skipped, see Consequences
  porkchop/
```

## Consequences
- **Positive:** one place (`firmware/`) holds every firmware we track, organized to match this catalog — any future download-automation can target these folder names directly.
- **Positive:** every staged binary has a known source and license, recorded in [NOTICE.md](../NOTICE.md) — the GPL/AGPL ones (Bruce, NEMO, Meshtastic) link to their exact corresponding source.
- **Positive:** 7 of 9 entries now have a staged, verified `.bin` on disk, three of them (Evil-Cardputer, Cardputer User Demo, Marauder) with binaries built specifically for Cardputer ADV rather than a shared/best-effort build.
- **Negative / open items:**
  - **Doom — skipped.** No prebuilt binary exists anywhere; the only tracked repo is source-only (ESP-IDF, `idf.py build`). A local build was attempted 2026-08-23: prerequisites installed cleanly (cmake, ninja via Homebrew; ESP-IDF v4.4.6 cloned; `romalik/m5cardputer_doom` cloned with submodules at commit `d8de0d18`), and the shareware `doom1.wad` step turned out to already be done upstream — the repo ships the WAD pre-converted to embeddable C source (`main/doom/source/iwad/doom1.c`), so no separate WAD download/verification was even needed. The build itself never ran: it was blocked by the host Mac's disk being nearly full system-wide (245GB APFS container at 99.9% used, unrelated to this project). Decision was to skip Doom rather than chase disk cleanup. To resume later: free disk space, then `git clone --recursive https://github.com/romalik/m5cardputer_doom`, `idf.py set-target esp32s3 && idf.py build`, then `merge_firmware.sh` to produce the flashable image.
  - **Meshtastic mesh kit firmware** — source is now pinned (`meshtastic/firmware@a00675e`), but the staged file is a third-party Windows build of a `develop` commit, not an official release asset. Download-automation should pull an official Meshtastic release for the `m5stack-cardputer-adv` target instead.
  - **Bruce** — resolved 2026-10-10: `pr3y/Bruce` redirects to `BruceDevices/firmware`, so that is the canonical repo to automate against.
  - **Evil-Cardputer ADV build (1.4.5) lags the regular Cardputer build (1.5.4)** — the ADV-specific binary hasn't been updated as recently; worth checking upstream before relying on it for anything version-sensitive.

## References
- Research pass conducted 2026-08-23 via web search against each project's GitHub repo, README, and release notes.
- [ADR 0001](0001-porkchop-firmware-setup-macos.md) — manual macOS SD-card flashing process this catalog feeds into.

## Reminder (carried over from ADR 0001)
Several of these (Bruce, Marauder, Evil-Cardputer, Porkchop, NEMO) are offensive/dual-use Wi-Fi and BLE tools. Educational/authorized use only — only test hardware and networks you own or have explicit permission to test.
