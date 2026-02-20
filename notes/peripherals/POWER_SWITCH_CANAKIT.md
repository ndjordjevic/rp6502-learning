# CanaKit Pi 4 PiSwitch (USB-C) — Power Switch for Picocomputer

In-line power switch used to turn the RP6502 on/off without unplugging. Purchased the **USB-C** version (Pi 4 PiSwitch) instead of the Micro USB version (Pi 3 PiSwitch).

## Product

- **Name:** CanaKit Raspberry Pi 4 PiSwitch (USB-C)
- **Link:** https://www.canakit.com/raspberry-pi-4-on-off-power-switch.html
- **Connector:** USB-C (in-line: USB-C in, USB-C out)
- **Features:** In-line power switch, LED indicator, low-resistance design

## Use with RP6502

- **RIA (Pico 2 W)** is powered via **Micro USB**, not USB-C.
- So the PiSwitch (USB-C) cannot plug directly into the RIA.

**Solution:** Use a **USB-C to Micro USB cable** (or adapter) between the switch output and the RIA:

```
Power supply (USB-C or USB-A) → PiSwitch (USB-C) → USB-C to Micro USB cable → RIA (Micro USB)
```

The switch only breaks power; a standard USB-C–to–Micro-B cable that carries 5 V and GND is sufficient. Use a decent-quality cable to avoid voltage drop.

## Note

- CanaKit Pi 3 PiSwitch (Micro USB) would plug directly into the RIA; we have the Pi 4 (USB-C) version and use an adapter cable instead.

## Fallback if CanaKit + adapter doesn’t work

If the CanaKit Pi 4 PiSwitch (USB-C) with a USB-C–to–Micro USB cable doesn’t work well (e.g. voltage drop, flaky power), get one of these instead (both plug **directly** into the RIA, no adapter):

1. **The Pi Hut Micro-USB Cable with On/Off Switch**
   - **Link:** https://thepihut.com/products/micro-usb-cable-with-on-off-switch
   - Micro-USB female to Micro-USB male. On/off switch, red LED, ~30 cm, power only. Designed for Pi Zero / Pi 3.

2. **CanaKit Raspberry Pi 3 PiSwitch (Micro USB)** — genuine CanaKit, same brand as the one we have
   - **Link:** https://www.ebay.com/itm/375787621306 (eBay; used condition, check availability)
   - Micro USB in-line switch, LED indicator, low-resistance design. Compatible with Pi 3 / Pi Zero; plugs directly into RIA (Pico 2 W).
