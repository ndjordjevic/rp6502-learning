# RP6502 Setup Notes

## With VGA Pico

| Item | Notes |
|------|-------|
| **Power** | VGA Pi Pico USB port. USB power supply, or USB to host PC (power + serial in one cable) |
| **Power switch** | Optional; inline between supply and VGA USB |
| **Keyboard** | USB keyboard via hub (RIA expects hub: OTG hub, or normal hub + OTG adapter) |
| **Display** | VGA monitor, or HDMI monitor + VGA-to-HDMI adapter |
| **Serial console** | When VGA USB → host PC: minicom or `rp6502.py` (CDC over USB) |

## Without VGA Pico

| Item | Notes |
|------|-------|
| **Power** | RIA Pi Pico USB port → **USB power supply** (not to computer) |
| **Console** | RIA UART pins → USB-serial adapter (3.3V!) → host PC. minicom at 115200 8N1 |
| **Keyboard** | USB keyboard via hub (OTG hub, or normal hub + OTG adapter) |
| **Display** | None (serial-only console) |

**Why UART + adapter?** RIA USB is in host mode (for keyboard/hub). A Pico can't be USB device and host at once, so RIA does not expose a serial port over USB. You must use the UART pins.

**USB-serial adapter**: FTDI, CP2102, or CH340G — must be 3.3V logic. Adapter TX→RIA RX, RX→RIA TX, GND→GND.

## Keyboard (both setups)

- RIA USB acts as **host** and expects a **hub**
- **Normal hub** → OTG adapter cable between RIA and hub
- **OTG hub** → plug directly into RIA

## Power details

- ~0.5 W (Picocomputer); VGA-to-HDMI adds ~0.25 W
- With VGA: power enters via VGA USB. Standalone supply or PC USB.
- Without VGA: power enters via RIA USB.
