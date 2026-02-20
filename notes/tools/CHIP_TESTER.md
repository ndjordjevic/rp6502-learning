# Chip Tester Pro V2

**Product**: BackBit Chip Tester Pro V2
**Price**: $199 USD
**Store**: https://store.backbit.io/product/chip-tester/
**Documentation**: https://backbit.io (latest version)
**Firmware updates**: https://backbit.io/downloads/Firmware
**Forum**: https://forum.backbit.io
**Video tutorials**: YouTube — Evie's Revue

---

## Overview

The Chip Tester Pro V2 tests DIP (Dual Inline Package) ICs up to 48 pins. It is USB-C powered (2A+ supply recommended), has a backlit 16x2 LCD, rotary encoder for navigation, and supports firmware upgrades via MicroSD card.

**WARNING**: Do NOT connect the Chip Tester to live electronics — this may cause damage.

---

## Features

- Tests 5V TTL and 3.3V LVCMOS digital logic (DIP chips up to 48 pins)
- Fully automated 5V power routing (no power jumpers needed)
- Normally closed ZIF socket (stronger connection than ordinary ZIF)
- Clamped I/O pins protect against under/over-voltage
- Short detection to prevent chip damage
- Automatically discharges chip after testing
- Burn-in mode for continuous/repeated testing
- ROM ripping to MicroSD card
- SHA1 checksum matching for ROM verification
- Firmware upgradeable (updates via SD card)
- LED result indicator (green = pass, red = fail)

---

## Controls

- **Rotary encoder**: Navigate menus, select tests, scroll through results
- **Rotary button press**: Select item (long press = return to first item in category)
- **TEST button**: Run the selected test

---

## Test Modes

| Mode | How to Activate | Description |
|------|----------------|-------------|
| **Normal** | Default | Insert chip, press TEST, get result |
| **Burn-in** | Rotate knob counter-clockwise | Tests chip continuously. Shows pass/fail count. Press knob on summary to stop. Check for overheating periodically |
| **Offset** | Rotate knob clockwise | Same test at vertical offset (chip further down in socket). Most users won't need this |

---

## Test Results — Important Notes

- **PASSED** = strong likelihood the chip is good, but not 100% guaranteed
- **FAILED** = could be a bad chip, OR a poor connection (bent/tarnished pins)
- After a test, twist the knob to see **detailed results** (which specific tests passed/failed)

### Voltage thresholds

| | LOW signal | HIGH signal |
|---|-----------|------------|
| Chip Tester Input | 0–1.2V | 2.3–5V |
| Chip Tester Output | 0V | 3.3V |
| TTL Input | 0–0.8V | 2–5V |
| TTL Output | 0–0.4V | 2.4–5V |
| CMOS Input | 0–1.5V | 3.5–5V |
| CMOS Output | 0V | 5V |

A damaged chip outputting voltages between LOW and HIGH specs could pass the tester but fail in real circuits. Confirm passing chips work in the actual application.

---

## Firmware Upgrade

1. Download latest firmware from https://backbit.io/downloads/Firmware
2. Place `CTPRO.UPG` in the root of a FAT32-formatted MicroSD card
3. Insert card and power cycle the tester
4. File is erased on successful upgrade (remove read-only if needed)

---

## Test Categories

| Category | Description |
|----------|-------------|
| **4000 Series** | CD4xxx and MC14xxx CMOS chips. Detection tests for 14/16/24-pin |
| **74LS TTL Series** | 74xx, 74Sxx, 74HCTxx. Detection tests for 14/16/20/24-pin |
| **74HC CMOS Series** | Same as 74LS tests but at 3.3V CMOS levels |
| **Atari** | TIA, POKEY, ANTIC, GTIA, PIA, SALLY, MARIA, FREDDIE, etc. |
| **Intel** | 8031–8088 CPUs, 8155/8255/8259 peripherals, etc. |
| **MOS/CSG/Amiga** | 6502/6510 CPUs, 6522 VIA, 6526 CIA, SID, VIC-II, DENISE, PAULA, etc. |
| **Motorola** | MC6800/6809 CPUs, MC6821 PIA, MC6845 CRTC, etc. |
| **Op Amps** | LM311, LM358, LM339, etc. (TTL-output only, no internal offset) |
| **Other Series/Mfr** | Z80, AY8910, WDC (W65C02, W65C22, W65C816), 555/556, and many more |
| **RAM** | Static and Dynamic RAM, sorted by pin count (16–48 pin) |
| **ROM** | EPROM/ROM ripping and testing, PAL/GAL readback, CornBit programming |

---

## RP6502 ICs That Can Be Tested

These are the ICs from the RP6502 board that the Chip Tester Pro V2 can test:

| IC | RP6502 Role | Chip Tester Category | Testable? |
|----|------------|---------------------|-----------|
| **U1 — W65C02S** | Main 6502 CPU | Other Series/Mfr → WDC → W65C02 | Yes |
| **U5 — W65C22S** | VIA (I/O chip) | Other Series/Mfr → WDC → W65C22 | Yes |
| **U3 — AS6C1008** | 128KB SRAM (32-pin) | RAM → 32-pin Static → 628128 | Yes |
| **U6 — 74AC00** | Quad NAND gate | 74HC CMOS → 7400 (at 3.3V) | Yes |
| **U7 — 74AC02** | Quad NOR gate | 74HC CMOS → 7402 (at 3.3V) | Yes |
| **U8 — 74HC30** | 8-input NAND gate | 74HC CMOS → 7430 (at 3.3V) | Yes |
| **U2 — Pico 2 W** | RIA (Pico module) | — | No (not a DIP IC) |
| **U4 — Pico 2** | VGA (Pico module) | — | No (not a DIP IC) |

### Testing notes for RP6502 ICs

- **74AC chips**: Use the **74HC CMOS** test category (tests at 3.3V CMOS levels, which matches 74AC operating voltage)
- **74HC30**: Use the **74HC CMOS** test category → 7430
- **AS6C1008 (128KB SRAM)**: This is a 32-pin 128K×8 static RAM. Use RAM → 32-pin Static → 628128 (128K×8 equivalent)
- **W65C02S and W65C22S**: Found under Other Series/Mfr → WDC
- **Pi Pico modules**: Cannot be tested with this tester (they are not DIP ICs)

---

## Recommended Testing Workflow for RP6502

### Before inserting ICs into the board

1. Set up ESD protection (anti-static mat + grounded wrist strap)
2. Test each IC individually in the Chip Tester before inserting into the board
3. Test in this order (simplest first):
   - U6 (74AC00) — 74HC CMOS → 7400
   - U7 (74AC02) — 74HC CMOS → 7402
   - U8 (74HC30) — 74HC CMOS → 7430
   - U3 (AS6C1008) — RAM → 32-pin Static → 628128
   - U5 (W65C22S) — Other Series/Mfr → WDC → W65C22
   - U1 (W65C02S) — Other Series/Mfr → WDC → W65C02
4. If any IC fails, try burn-in mode to confirm, then check detailed results
5. If a chip passes, it has a strong likelihood of being good — but confirm in the real board

### After board assembly (troubleshooting)

- If the board doesn't work, remove ICs and re-test individually
- A chip that passed testing but fails in-circuit may have marginal voltage output (see voltage thresholds above)

---

## RAM Testing Details

RAM tests have 4 passes:
1. Random pattern
2. Inverted pattern
3. Alternating pattern
4. Inverted alternating pattern

For chips under 1MB, the entire RAM is written before being read back.

---

## ROM Ripping

- Ripped ROMs are saved to MicroSD card
- Details logged in `CTPRO.LOG` file in root
- After ripping, tests show checksum
- Place `sha.txt` in SD root for SHA1 checksum matching (format: `sha1sum FILENAME`)
- GALs and PALs are read back as EPROMs (not fuse readout)

---

## Optional Accessories

| Accessory | Purpose |
|-----------|---------|
| **4116 DRAM Adapter** | Applies -5V and 12V for testing 4116/4096/4027 DRAM |
| **Buffered DRAM Adapter** | Required for dynamic RAM with V2 tester |
| **CartRip** | Test RAM / extract ROM from cartridges (C64, Atari, etc.) |
| **CornBit / CornBitXL** | Multi-ROM replacements, programmable via the tester |

---

## Included Items

- USB-A to USB-C power cable (you provide your own 2A+ USB power supply)
- Quick reference card

---

## Resources

- **Product page**: https://store.backbit.io/product/chip-tester/
- **Documentation & downloads**: https://backbit.io/downloads
- **Forum (support & chip requests)**: https://forum.backbit.io
- **Video tutorials**: YouTube — Evie's Revue
- **Email**: backbitio@gmail.com
