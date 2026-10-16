<p align="center">
  <img src="assets/logo.svg" width="160" alt="Logo Mi R3G verso OpenWrt">
</p>

<h1 align="center">Xiaomi Mi Router 3G (R3G): da firmware stock a OpenWrt, solo interfaccia web</h1>

<p align="center">
  <a href="https://buymeacoffee.com/dr_gaussx"><img src="https://img.shields.io/badge/Offrimi%20un%20caff%C3%A8-dr_gaussx-FFDD00?style=flat&logo=buy-me-a-coffee&logoColor=black" alt="Buy Me a Coffee"></a>
  <img src="https://img.shields.io/badge/testato-stock%202.28.44-green" alt="testato su stock 2.28.44">
  <img src="https://img.shields.io/badge/OpenWrt-23.05.5-blue" alt="OpenWrt 23.05.5">
</p>

<p align="center"><i>🇬🇧 This guide is also available in English: <a href="README.md">README.md</a></i></p>

## Cos'è

Installare **OpenWrt sullo Xiaomi Mi Router 3G (R3G, quello con la porta USB)** direttamente **dall'interfaccia web stock** — niente UART, niente saldature, nessuno exploit SSH. Testato su firmware stock **2.28.44**.

Il trucco: un'immagine `HDR1` patchata costruita con `xqimage.py` di [xmir-patcher](https://github.com/openwrt-xiaomi/xmir-patcher) (sfrutta il noto bug di verifica di `mkxqimage`, il cui payload per R3G copre esplicitamente 2.25.124 / 2.28.44). L'interfaccia web stock la accetta come un normale aggiornamento firmware.

> ⚠️ **Liberatoria — leggere prima.** **Non mi assumo alcuna responsabilità** per ciò che fai con questa guida o questi file. Se la segui, lo fai **a tuo rischio e pericolo**, consapevole di poter **invalidare la garanzia** e/o **rompere definitivamente ("brick") il tuo dispositivo**. Mai togliere corrente durante il flash.

## Requisiti

- Xiaomi Mi Router 3G (R3G **v1 con porta USB** — NON confondere con R3Gv2 / Mi 4A Gigabit, hardware diverso!)
- La password admin dello stock, un PC collegato alla sua rete (`192.168.31.1` di default)
- ~10 minuti di pazienza

## Opzione A — immagine già pronta (la più veloce)

1. Scarica `miwifi_r3g_openwrt_23.05.5.bin` dalla pagina [**Releases**](../../releases).
2. Verificane l'integrità (vedi [Checksum](#checksum) sotto).
3. Entra nell'interfaccia web stock (`http://192.168.31.1`), vai su **Impostazioni → Aggiornamento → aggiornamento manuale**, seleziona il file e conferma.
4. Aspetta **3–5 minuti senza toccare niente** (LED giallo lampeggiante = flash in corso, blu fisso = finito). La WiFi Xiaomi sparisce — è normale.
5. Ora OpenWrt è su `http://192.168.1.1` (collegati **via cavo LAN** — la WiFi è spenta di default). Entra come `root` **senza password** e impostane subito una (`System → Administration`).

## Opzione B — patchati il firmware da solo (per chi non si fida dei binari)

1. Scarica le immagini **ufficiali** OpenWrt 23.05.5 per `xiaomi_mi-router-3g` (`ramips/mt7621`):
   - `openwrt-23.05.5-ramips-mt7621-xiaomi_mi-router-3g-squashfs-kernel1.bin`
   - `openwrt-23.05.5-ramips-mt7621-xiaomi_mi-router-3g-squashfs-rootfs0.bin`
   - Verificale con https://downloads.openwrt.org/releases/23.05.5/targets/ramips/mt7621/sha256sums
2. Clona xmir-patcher e metti i due file nella sua cartella `firmware/`:
   ```sh
   git clone https://github.com/openwrt-xiaomi/xmir-patcher.git
   cd xmir-patcher
   cp /percorso/di/*kernel1.bin /percorso/di/*rootfs0.bin firmware/
   ```
3. Costruisci l'immagine HDR1 patchata:
   ```sh
   # Linux
   python3 xqimage.py R3G miwifi_r3g_openwrt_23.05.5.bin
   # Windows (python incluso)
   python\python.exe xqimage.py R3G miwifi_r3g_openwrt_23.05.5.bin
   ```
4. Flashala come al punto 3 dell'Opzione A.

## Dopo il flash

- Imposta una password di root, abilita/configura la WiFi a piacere.
- Se la tua rete a monte usa già `192.168.1.0/24`, cambia l'indirizzo LAN (es. `Network → Interfaces → LAN → 192.168.31.1`) per evitare conflitti.
- Aggiorna all'ultima stabile con un normale sysupgrade (mantiene le impostazioni): scarica
  `openwrt-<versione>-ramips-mt7621-xiaomi_mi-router-3g-squashfs-sysupgrade.bin` da
  https://downloads.openwrt.org/releases/, controlla lo sha256, poi flasha da
  `System → Backup / Flash firmware`, oppure via SSH:
  ```sh
  cd /tmp && wget <url-sysupgrade> && sha256sum -c <riga-sha256sums> && sysupgrade -v *.bin
  ```
  (Percorso verificato: 23.05.5 → 24.10.8 mantenendo le impostazioni.)

## Checksum

`miwifi_r3g_openwrt_23.05.5.bin` (10.752.296 byte) — anche in `CHECKSUMS.txt`:

```
SHA256: 16AEDCA4BE80EEFED1F6C35F6597F942D0C5D42314925C8C05BDC648274953D8
MD5:    4139576DE275B9EC5055B56FE7B4FEF7
```

Costruita da questi input ufficiali (entrambi verificati sha256 su downloads.openwrt.org):

```
65f973996fe0162a5bd4809efa1a1e79515158cabba3d22d7950eb5815ce01db  *openwrt-23.05.5-ramips-mt7621-xiaomi_mi-router-3g-squashfs-kernel1.bin
a01fd3f096225b747a046775b36d9f8fbc0251bdfadbe8937a88cd5640808b7b  *openwrt-23.05.5-ramips-mt7621-xiaomi_mi-router-3g-squashfs-rootfs0.bin
```

## Crediti

- [xmir-patcher](https://github.com/openwrt-xiaomi/xmir-patcher) di remittor e collaboratori (patch HDR1 / `xqimage.py`)
- Progetto [OpenWrt](https://openwrt.org/) e la [pagina del mir3g](https://openwrt.org/toh/xiaomi/mir3g)

<p align="center">
  <a href="https://buymeacoffee.com/dr_gaussx"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" width="200" alt="Buy Me a Coffee"></a>
</p>
