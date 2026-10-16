<p align="center">
  <img src="assets/logo.svg" width="160" alt="Mi R3G to OpenWrt logo">
</p>

<h1 align="center">Xiaomi Mi Router 3G (R3G): stock firmware → OpenWrt, web UI only</h1>

<p align="center">
  <a href="https://buymeacoffee.com/dr_gaussx"><img src="https://img.shields.io/badge/Buy%20Me%20a%20Coffee-dr_gaussx-FFDD00?style=flat&logo=buy-me-a-coffee&logoColor=black" alt="Buy Me a Coffee"></a>
  <img src="https://img.shields.io/badge/tested-stock%202.28.44-green" alt="tested on stock 2.28.44">
  <img src="https://img.shields.io/badge/OpenWrt-23.05.5-blue" alt="OpenWrt 23.05.5">
</p>

<p align="center"><i>🇮🇹 Questa guida è disponibile anche in italiano: <a href="README.it.md">README.it.md</a></i></p>

## What this is

Flash **OpenWrt on a Xiaomi Mi Router 3G (R3G, the one with USB port)** straight from the **stock web interface** — no UART, no soldering, no SSH exploit needed. Tested on stock firmware **2.28.44**.

The trick: a patched `HDR1` image built with [xmir-patcher](https://github.com/openwrt-xiaomi/xmir-patcher)'s `xqimage.py` (it exploits the well-known `mkxqimage` verification bug, whose R3G payload explicitly covers 2.25.124 / 2.28.44). The stock web UI accepts it as a regular firmware update.

> ⚠️ **Disclaimer — read this first.** I assume **no responsibility** for what you do with this guide or these files. If you follow it, you do so **at your own risk**, knowing that you may **void your warranty** and/or **permanently break ("brick") your device**. Never interrupt power during flashing.

## Requirements

- Xiaomi Mi Router 3G (R3G **v1 with USB port** — do NOT confuse with R3Gv2 / Mi 4A Gigabit, different hardware!)
- Its stock admin password, a PC on its network (`192.168.31.1` by default)
- ~10 minutes of patience

## Option A — use the ready-made image (fastest)

1. Download `miwifi_r3g_openwrt_23.05.5.bin` from the [**Releases**](../../releases) page.
2. Verify integrity (see [Checksums](#checksums) below).
3. Log in to the stock web UI (`http://192.168.31.1`), go to **Settings → Upgrade → manual update**, select the file and confirm.
4. Wait **3–5 minutes without touching anything** (yellow LED blinking = flashing, solid blue = done). The Xiaomi WiFi disappears — that's expected.
5. OpenWrt is now at `http://192.168.1.1` (connect via **LAN cable** — WiFi is disabled by default). Log in as `root` with **no password** and set one immediately (`System → Administration`).

## Option B — patch the firmware yourself (for those who don't trust binaries)

1. Download the **official** OpenWrt 23.05.5 images for `xiaomi_mi-router-3g` (`ramips/mt7621`):
   - `openwrt-23.05.5-ramips-mt7621-xiaomi_mi-router-3g-squashfs-kernel1.bin`
   - `openwrt-23.05.5-ramips-mt7621-xiaomi_mi-router-3g-squashfs-rootfs0.bin`
   - Verify them against https://downloads.openwrt.org/releases/23.05.5/targets/ramips/mt7621/sha256sums
2. Clone xmir-patcher and put the two files in its `firmware/` folder:
   ```sh
   git clone https://github.com/openwrt-xiaomi/xmir-patcher.git
   cd xmir-patcher
   cp /path/to/*kernel1.bin /path/to/*rootfs0.bin firmware/
   ```
3. Build the patched HDR1 image:
   ```sh
   # Linux
   python3 xqimage.py R3G miwifi_r3g_openwrt_23.05.5.bin
   # Windows (bundled python)
   python\python.exe xqimage.py R3G miwifi_r3g_openwrt_23.05.5.bin
   ```
4. Flash it with step 3 of Option A.

## After flashing

- Set a root password, enable/configure WiFi to taste.
- If your upstream network already uses `192.168.1.0/24`, change the LAN address (e.g. `Network → Interfaces → LAN → 192.168.31.1`) to avoid conflicts.
- Upgrade to the latest stable with a standard sysupgrade (keeps settings): download
  `openwrt-<version>-ramips-mt7621-xiaomi_mi-router-3g-squashfs-sysupgrade.bin` from
  https://downloads.openwrt.org/releases/, check the sha256, then flash from
  `System → Backup / Flash firmware`, or via SSH:
  ```sh
  cd /tmp && wget <sysupgrade-url> && sha256sum -c <sha256sums-entry> && sysupgrade -v *.bin
  ```
  (Verified path: 23.05.5 → 24.10.8 with settings kept.)

## Checksums

`miwifi_r3g_openwrt_23.05.5.bin` (10,752,296 bytes) — also in `CHECKSUMS.txt`:

```
SHA256: 16AEDCA4BE80EEFED1F6C35F6597F942D0C5D42314925C8C05BDC648274953D8
MD5:    4139576DE275B9EC5055B56FE7B4FEF7
```

Built from these official inputs (both sha256-verified against downloads.openwrt.org):

```
65f973996fe0162a5bd4809efa1a1e79515158cabba3d22d7950eb5815ce01db  *openwrt-23.05.5-ramips-mt7621-xiaomi_mi-router-3g-squashfs-kernel1.bin
a01fd3f096225b747a046775b36d9f8fbc0251bdfadbe8937a88cd5640808b7b  *openwrt-23.05.5-ramips-mt7621-xiaomi_mi-router-3g-squashfs-rootfs0.bin
```

## Credits

- [xmir-patcher](https://github.com/openwrt-xiaomi/xmir-patcher) by remittor & contributors (HDR1 patch / `xqimage.py`)
- [OpenWrt](https://openwrt.org/) project and the [mir3g device page](https://openwrt.org/toh/xiaomi/mir3g)

<p align="center">
  <a href="https://buymeacoffee.com/dr_gaussx"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" width="200" alt="Buy Me a Coffee"></a>
</p>
