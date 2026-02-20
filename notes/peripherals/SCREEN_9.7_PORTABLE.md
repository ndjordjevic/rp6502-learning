# 9.7" 2K IPS Portable Display (Picocomputer Monitor)

Compact portable display used with the RP6502. Connected and working: blinking cursor in top-left corner when VGA Pico is powered (as in author's video).

## Product Overview

- **Size:** 9.7 inches
- **Resolution:** 2048×1536 (2K, 4:3 aspect ratio)
- **Panel:** IPS (178° viewing angles, good color)
- **Use case:** Secondary monitor, SBC/retro display (e.g. Picocomputer, Raspberry Pi, Orange Pi)

## Connectivity

| Input / Power | Notes |
|---------------|--------|
| **Mini-HDMI** | Video input — use for RP6502 |
| **Type-C (USB-C)** | Video input and/or power |
| **Power** | 5V via USB (power bank or adapter) |

## Connection to RP6502

- **RP6502** outputs **VGA**.
- This display has **Mini-HDMI** (no VGA).
- **Chain:** RP6502 VGA → **VGA-to-HDMI adapter** (with USB power if needed) → **HDMI to Mini-HDMI cable or adapter** → this display.
- **Power:** 5V to the display via its USB port (same supply or separate).

## Key Features (from listing)

- High-resolution 2K (2048×1536), 4:3
- Mini-HDMI and Type-C inputs
- 5V USB power (no separate PSU)
- IPS, plug-and-play, no drivers
- Slim, portable; metal bracket/stand included

## Package (typical)

- 9.7" IPS display
- Power cable (USB)
- HDMI cable (may need Mini-HDMI adapter or cable for this panel)
- Display metal bracket (stand)

## Physical (from product info)

- **Panel:** ~214×174 mm total; ~200×151 mm visible
- **Thickness:** ~12 mm

## Notes

- 4:3 aspect ratio suits retro resolutions and text.
- Confirmed working with RP6502: VGA Pico in U4, VGA→HDMI adapter, then HDMI to this display; blinking cursor on power-up.
