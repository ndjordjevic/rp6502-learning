# Raspberry Pi Keyboard and Hub — Picocomputer Keyboard

Official Raspberry Pi keyboard with built-in USB hub. Planned for use with the RP6502; **not yet tested** — waiting for the Micro USB to USB-A (OTG) adapter to connect it to the RIA.

## Product

- **Name:** Raspberry Pi Keyboard and Hub (official)
- **Link:** https://www.raspberrypi.com/products/raspberry-pi-keyboard-and-hub/
- **Connector (to host):** Micro USB type B (on keyboard)
- **Included cable:** USB type A (host) to Micro USB type B (keyboard)

## Features

- 79-key keyboard (78-key US, 83-key JP)
- **Built-in USB hub:** 3× USB 2.0 type A ports (mouse, flash drive, etc.)
- Num Lock, Scroll Lock, Caps Lock with LEDs
- Automatic keyboard language detection
- Designed to connect to a USB **host** (e.g. Raspberry Pi, or RP6502 RIA); not OTG on the keyboard side — the **hub** is what needs the OTG adapter on the host side

## Connection to RP6502

- **RIA (Pico 2 W)** has a **Micro USB** port (USB host for keyboard).
- The keyboard’s cable is **USB-A (male)** at the host end and **Micro USB (male)** at the keyboard end.
- So between RIA and keyboard we need: **Micro USB (RIA)** ↔ **USB-A (keyboard cable)**.
- **Solution:** Use a **Micro USB male to USB-A female** OTG adapter: plug the adapter’s Micro USB into the RIA, then plug the keyboard’s cable (USB-A) into the adapter; Micro USB end of keyboard cable goes into the keyboard.

```
RIA (Micro USB) ← OTG adapter (Micro USB male, USB-A female) ← keyboard cable (USB-A male) → keyboard (Micro USB)
```

- **Adapter ordered:** e.g. USB to Micro USB adapter 2-pack, “Micro USB Male to USB A Female” OTG type (so the RIA sees a hub, keyboard works as in the author’s video).

## Status

- **Not yet tested** — adapter is ordered; will test once it arrives and RIA is installed.

## References

- Author’s video: RIA USB is programmed to accept a hub; need an OTG adapter cable (or hub built for OTG). Then “ordinary USB keyboard” works — this keyboard is a keyboard + hub, so it fits that setup.
