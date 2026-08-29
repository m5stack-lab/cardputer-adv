# ADR 0001: Porkchop Firmware Setup Workflow for M5 Cardputer ADV (macOS)

## Status
Accepted — 2026-08-23

## Context
- This project targets the M5Stack Cardputer ADV (the same process applies to Cardputer 1.1 / Core 2) running Octo's "Porkchop" custom firmware — a Wi-Fi/BLE recon firmware with Wigle and WPA-sec upload integration.
- Reference walkthrough: "M5 Porkchop Setup Guide" (Cal's Channel), which documents the process on **Windows** using M5Burner's Windows build, Windows File Explorer, COM ports, etc.
- Our team standardizes on **macOS**, so every OS-specific step in that walkthrough (driver handling, file paths, port naming, GUI tooling) needs a macOS-native equivalent before it can be treated as our setup procedure.
- Hard constraint carried over unchanged regardless of host OS: the Cardputer ADV's ESP32-S3 radio is **2.4 GHz only** — it will not associate with 5 GHz/6 GHz networks.
- Firmware moves fast (the source video references 1.8D as "current as of this recording"); this ADR documents the *process*, not a pinned version. Always grab the newest release Octo has published, verifying "octo" as the author (impostor uploads using the Porkchop name have circulated).

## Decision
We will set up Porkchop on macOS using the **manual SD-card flash** method as the primary/documented path, with the **browser-based Web Flasher** as the secondary/quick-update path. We will **not** standardize on M5Burner's over-the-air firmware download as the primary path — the source material explicitly notes OTA downloads can leave the device stuck in a boot loop.

Concretely, the macOS procedure is:

### 1. Pre-flight requirements
- Target Wi-Fi network must be 2.4 GHz. ESP32-S3 cannot join 5 GHz/6 GHz SSIDs — if your router band-steers under one combined SSID, split the bands or use a phone hotspot's 2.4 GHz-only mode.
- Get the newest Porkchop release from Octo's official GitHub; confirm authorship before downloading.

### 2. Install a serial driver (macOS-only step, not covered in the original video)
Windows generally has the USB-serial driver preinstalled or auto-fetched via Windows Update, so the source walkthrough skips this. macOS does not ship it. Before the Cardputer ADV appears as a serial device in Chrome or M5Burner:
- Install the USB-to-UART driver matching your Cardputer's chip (CH9102 or CP2104, depending on revision) from WCH or Silicon Labs.
- Re-plug the device (or reboot) after installing.
- On Apple Silicon Macs, you may need to approve the new system extension in **System Settings → Privacy & Security → Allow**, then restart.

### 3. Flash via the Web Flasher (Chrome or Edge only — requires Web Serial API)
- Open the beomelli launcher web flasher (same URL as the video — it's a browser tool, OS-agnostic).
- Select M5Stack → Cardputer / Cardputer ADV.
- Hold the top-right "BtnGo" button, plug in the USB-C cable, then release.
- Click Connect. macOS lists the port as something like `/dev/cu.usbserial-XXXX` or `/dev/cu.wchusbserialXXXX` — the equivalent of the video's `COM49` — pick that entry in Chrome's serial picker, not a Bluetooth or debug-console device.
- Click Install Cardputer / Cardputer ADV and let it finish.

### 4. Install M5Burner for macOS
- Go to m5stack.com → Software → Developer Tools → M5Burner, and download the **macOS** build.
- Open the `.dmg` and drag M5Burner.app to Applications.
- M5Burner may not be notarized for every release; Gatekeeper can block first launch ("can't be opened because Apple cannot check it for malicious software"). Right-click → Open → Open again to bypass, or run `xattr -cr /Applications/M5Burner.app` in Terminal, then relaunch.

### 5. Download firmware `.bin` files via M5Burner
- Open M5Burner, update if prompted, then browse the Cardputer section's custom firmware list (EvilCardputer, Porkchop, etc.).
- Click Download on the firmware you want — do **not** click Burn yet.
- Unlike Windows (where the video opens the extracted M5Burner zip's `packages/firmware` folder directly), the macOS app is a bundle, not meant to be browsed as a folder. M5Burner on macOS caches downloads under its own application-support directory. Use Finder's **Go → Go to Folder** (⌘⇧G) and check `~/Library/Application Support/M5Burner` (or `~/Documents/M5Burner`) for a `firmware` folder holding the `.bin` files.
  - **Open question:** confirm the exact cache path for the installed M5Burner version — M5Stack has relocated this between releases. Update this step once verified.
- Rename the relevant `.bin` file to something identifiable (e.g. `porkchop-1.8d.bin`), same as the video does on Windows.

### 6. Move firmware onto the SD card
- Insert the Cardputer's microSD card via the Mac's built-in SD slot or a USB card reader.
- In Finder, open the mounted SD volume. Create a `downloads` folder at its root if one doesn't exist.
- Drag the renamed `.bin` file into `downloads`, removing older/conflicting firmware files.
- **Always eject properly** — Finder sidebar eject icon, right-click → Eject, or drag to the Dock's Eject icon — before physically removing the card. Pulling it unmounted risks filesystem corruption (macOS is stricter about this than the Windows flow the video shows).

### 7. Reinsert SD card and boot
- Put the card back in the Cardputer ADV and power on; it boots Porkchop from the SD-resident firmware.

### 8. Configure Wi-Fi and reach the Web UI
- On-device: Menu (Esc) → Settings → Network → enter the 2.4 GHz SSID/password.
- Main menu → press `F` (or Coms → File Transfer) to start the transfer service; confirm "jacking in" reports success.
- Your Mac must join the same 2.4 GHz network as the device.
- Open `http://porkchop.local`. macOS resolves `.local` mDNS/Bonjour hostnames natively — no separate Bonjour install needed, unlike some Windows setups that require Apple's Bonjour Print Services for `.local` resolution.
- If `porkchop.local` doesn't resolve, fall back to the on-device IP address.

### 9. Wigle / WPA-sec keys and sync
- In the web UI, load your Wigle API name + token (from wigle.net → account) and WPA-sec API key into Creds, then save.
- Use Sync in the web UI (or Coms → Pig Sync on-device) to upload queued handshakes/war-drive logs.

## Consequences
- **Positive:** SD-card flashing sidesteps the boot-loop failure mode reported for OTA downloads, and is OS-portable — same manual steps regardless of host machine.
- **Positive:** `.local` resolution and the recon/upload workflow need zero extra networking setup on macOS (Bonjour is native) — actually simpler than the Windows path.
- **Negative:** macOS requires an explicit USB-serial driver install and a Gatekeeper bypass for M5Burner that the Windows walkthrough never hits — added first-time friction.
- **Open item:** M5Burner's macOS firmware cache path isn't confirmed here; verify against the installed version and update Step 5.
- **Risk carried over unchanged:** the device only associates to 2.4 GHz networks — an ESP32-S3 hardware constraint, not something host-OS choice affects.

## Alternatives Considered
- **OTA download directly on-device via M5Burner:** simplest, but the source material explicitly flags it as prone to boot loops for some firmware — rejected as the primary path, though still usable for quick updates once a known-good base firmware is confirmed working.
- **Web Flasher only, skip M5Burner:** viable and fully OS-agnostic. Kept M5Burner in the decision because it's the path used for browsing/discovering the custom-firmware catalog, not just installing Porkchop.

## References
- Source walkthrough: "M5 Porkchop Setup Guide" (YouTube, Cal's Channel) — Windows-based; adapted here for macOS.
- Porkchop firmware & README: Octo's GitHub (verify authorship before downloading).
- Web flasher: beomelli.github.io/launcher/webflasher
- M5Burner: m5stack.com → Software → Developer Tools

## Reminder (carried over from source material)
Educational/authorized use only. Only test hardware and networks you own or have explicit permission to test. Do not run deauth/BLE-spam attacks (Oinks/Blues) or recon tooling against networks without authorization.
