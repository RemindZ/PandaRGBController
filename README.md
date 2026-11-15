# Panda RGB Controller - standalone firmware

Patched BIGTREETECH Panda RGB Controller firmware: effects run without a paired
printer, and breathing is smoother and slower.

**Download: [latest release](https://github.com/RemindZ/PandaRGBController/releases/latest)**

The current build is the official `v1.0.2` with the
[v1.0.1 standalone](Firmware/v1.0.1-standalone/README.md) patch carried over.
Everything new in v1.0.2 is kept.

**What v1.0.2 adds** (no official changelog): Klipper/Moonraker printer support
next to Bambu, and an unfinished Home Assistant card that is hidden in the UI.
"H2D Style" is renamed "Printer Status Mode". With no printer it still forces
the blue strobe.

## Changes

1. **Effects without a printer.** With no printer connected, `get_display_state()`
   defaults to state 0, a forced blue strobe that no UI setting overrides.
   v1.0.2 has one copy per printer type, and both now default to state 1
   (render the selected effect):
   `0x4b7ce` (Bambu) and `0x4b812` (Klipper): `c.li a0,0` → `c.li a0,1` (`01 45` → `05 45`)
2. **Smoother, slower breathing.** The breath step drops from ±3.0 to ±0.6,
   about 200 steps per breath instead of about 40. Brightness and colour are
   unchanged. The same constants also drive the Printer Status Mode fade.
   `0x6514` / `0x6518`: `-1.0 / +1.0` → `-0.2 / +0.2`

https://github.com/user-attachments/assets/c2ee5b44-35fc-4796-9993-27afb878ad7a

Only these 10 bytes differ from stock, plus the recomputed image checksum and
SHA-256. The image passes ESP-IDF's checks.

## Build

```
python3 Firmware/v1.0.2-standalone/apply_patch.py Firmware/v1.0.2/panda_rgb_controller_v1.0.2.bin out.bin
```

```
stock       f521852ccc77b3d7866c0c4c38ca44bcf6b49693775cb7766f0303ad46191392
standalone  7e208883856d94d7fdb89863614b45bc7df7f9cbdf282269bde1dcad03f532b2
```

## Flash / revert

Web UI → Settings → firmware update → pick the `.bin`, then reboot. Or:

```
curl -X POST http://<controller-ip>/ota -H "OTA-Type: ota_fw" \
  --data-binary @panda_rgb_controller_v1.0.2_standalone.bin
```

To revert, flash the stock `Firmware/v1.0.2/panda_rgb_controller_v1.0.2.bin` the same way.
