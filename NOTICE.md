# Third-party firmware notices

The `.bin` files under [`firmware/`](firmware/) are unmodified binaries built by their upstream projects (or, for Meshtastic, by a third party from upstream source). This repo did not write them and redistributes them under their original licenses. Each entry below names the license, the copyright holder, and the exact source the binary corresponds to.

| Firmware | Staged file | License | Copyright | Corresponding source |
|---|---|---|---|---|
| M5Launcher | `firmware/m5launcher/Launcher-m5stack-cardputer.bin` | MIT | © 2023 shikarunochi; bmorcelli and contributors | [bmorcelli/Launcher @ `2.8.0`](https://github.com/bmorcelli/Launcher/tree/2.8.0) · [LICENSE](https://github.com/bmorcelli/Launcher/blob/2.8.0/LICENSE) |
| Evil-Cardputer | `firmware/evil-cardputer/Evil-ADV-V1.4.5.bin` | MIT | © 2023 7h30th3r0n3 | [7h30th3r0n3/Evil-M5Project @ `7d615fe`](https://github.com/7h30th3r0n3/Evil-M5Project/tree/7d615fe) (binary published in-repo at `binaries/Evil-ADV-V1.4.5.bin`; license text in its README) |
| Bruce | `firmware/bruce/Bruce-m5stack-cardputer.bin` | **AGPL-3.0** | Bruce contributors | [BruceDevices/firmware @ `1.16.1`](https://github.com/BruceDevices/firmware/tree/1.16.1) (formerly `pr3y/Bruce`) · [LICENSE](https://github.com/BruceDevices/firmware/blob/1.16.1/LICENSE) |
| Cardputer User Demo | `firmware/cardputer-user-demo/K132-Adv-Cardputer-Adv-UserDemo-V0.3_0x0.bin` | MIT | © 2023 M5Stack | [m5stack/M5Cardputer-UserDemo @ `ADV-V0.3`](https://github.com/m5stack/M5Cardputer-UserDemo/tree/ADV-V0.3) · [LICENSE](https://github.com/m5stack/M5Cardputer-UserDemo/blob/main/LICENSE) (on `main`; the `ADV-V0.3` tag predates it) |
| Meshtastic (Cardputer Mesh Kit) | `firmware/meshtastic-mesh-kit/meshtastic-for-cardputer-adv-with-cap-lora.2-8-0-13-08-2026.bin` | **GPL-3.0** | Meshtastic contributors | [meshtastic/firmware @ `a00675e`](https://github.com/meshtastic/firmware/tree/a00675e00c3445927d5e24ce820bc40bde34aaaf), build target `m5stack-cardputer-adv` · [LICENSE](https://github.com/meshtastic/firmware/blob/a00675e00c3445927d5e24ce820bc40bde34aaaf/LICENSE) |
| NEMO | `firmware/nemo/M5Nemo-v3.2.2-M5Cardputer.bin` | **GPL-2.0 or GPL-3.0** | © 2023 Noah Axon | [n0xa/m5stick-nemo @ `v3.2.2`](https://github.com/n0xa/m5stick-nemo/tree/v3.2.2) · [LICENSE](https://github.com/n0xa/m5stick-nemo/blob/v3.2.2/LICENSE) |
| Marauder | `firmware/marauder/esp32_marauder_v1_15_0_20260820_m5cardputer_adv.bin` | MIT | © 2020 Just Call Me Koko | [justcallmekoko/ESP32Marauder @ `v1.15.0`](https://github.com/justcallmekoko/ESP32Marauder/tree/v1.15.0) · [LICENSE](https://github.com/justcallmekoko/ESP32Marauder/blob/v1.15.0/LICENSE) |
| Porkchop | `firmware/porkchop/firmware_v0.1.8b-PSTH-PRE-RELEASE.bin` | MIT | © 2025 0ct0 | [0ct0sec/M5PORKCHOP @ `v0.1.8b-PSTH`](https://github.com/0ct0sec/M5PORKCHOP/tree/v0.1.8b-PSTH) · [LICENSE](https://github.com/0ct0sec/M5PORKCHOP/blob/v0.1.8b-PSTH/LICENSE) |

## How the Meshtastic source was pinned

The staged Meshtastic binary embeds the version string `2.8.0.a00675e`. Commit [`a00675e`](https://github.com/meshtastic/firmware/commit/a00675e00c3445927d5e24ce820bc40bde34aaaf) on `meshtastic/firmware`'s `develop` branch ("unset can have what it likes (#11496)", 2026-08-13) matches the build date in the filename and already carries the `m5stack-cardputer-adv` variant (`variants/esp32s3/m5stack_cardputer_adv/`). The binary's embedded build paths show it was compiled on Windows rather than by Meshtastic CI, so it is a third-party build of that commit.

## Your rights

For the GPL and AGPL entries, the complete corresponding source is available at the links above, and you may obtain, modify and redistribute it under those licenses. If any link stops working, open an issue in this repo and the matching source will be provided.

Several of these are offensive/dual-use Wi-Fi and BLE tools. Use them only on hardware and networks you own or have explicit permission to test.
