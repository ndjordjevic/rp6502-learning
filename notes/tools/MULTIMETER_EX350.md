# Extech EX350 Series Multimeter

**Models**: EX350 (4000 count), EX355 (6000 count, with temperature probe)  
**Manual**: Extech EX350 Series User Guide (True RMS Digital Multimeters)  
**Local copy**: `~/iCloud Drive/Manuals/Extech-EX350-Series-Manual.pdf`

---

## Overview

The Extech EX350 Series are True RMS digital multimeters with a large backlit LCD, auto/manual range, and CAT III 600V rating. They are suitable for RP6502 pre-IC testing: continuity, resistance, and DC voltage on a 3.3V board.

---

## Safety (summary)

- **Keep hands behind the finger guards** when using test leads.
- **Remove test leads** before opening the battery compartment.
- **Do not measure voltage** with the dial on resistance, diode, capacitance, or current -- risk of meter and circuit damage.
- **Disconnect power and discharge capacitors** before measuring resistance, continuity, diode, or capacitance.
- **Replace batteries** when the low-battery icon appears.
- **CAT III 600V** -- do not exceed rated voltage between terminals or to ground.

---

## Terminals

| Terminal | Use |
|----------|-----|
| **COM** (black) | Common; use with black lead for all measurements. |
| **V/Ohm** (red) | Voltage, resistance, capacitance, diode, temperature (EX355). |
| **mA/uA** (red) | Current under 600 mA; use red lead here for mA/uA current. |
| **10A** (red) | Current up to 10 A; use red lead here for high current. |

**For RP6502 pre-IC testing** use **COM** and **V/Ohm** only (continuity, resistance, DC voltage).

---

## Functions used for RP6502 pre-IC testing

### Continuity

- Dial: **Ohm** (resistance).
- Press **M (MODE)** until the **continuity** symbol appears (buzzer on).
- Leads: black -> COM, red -> V/Ohm.
- **Threshold**: below ~10 Ohm the buzzer sounds; above ~400 Ohm (EX350) or ~600 Ohm (EX355) display shows **OL**.
- Use to check: no shorts between power and ground; continuity of rails, ground, and socket pins.

### Resistance

- Dial: **Ohm**.
- Press **M** until **Ohm only** (no continuity symbol).
- Leads: black -> COM, red -> V/Ohm.
- **Power off** on the board before measuring.
- Use to verify resistor values and that decoupling caps are not shorted.

### DC voltage

- Dial: **V** or **mV**.
- Press **M** to select **DC**.
- Leads: black -> COM, red -> V/Ohm.
- Red to positive, black to negative.
- Use to check 3.3V rails after confirming no shorts (current-limited supply recommended).

### Capacitance

- Dial: **capacitance** position.
- Press **M** until the **F** (Farads) symbol appears.
- Leads: black -> COM, red -> V/Ohm.
- **Board must be unpowered.**
- For caps <= 1 uF: short leads together, press **REL** to zero out lead capacitance, then measure.
- Large caps may take a few seconds to stabilize.
- **In-circuit note**: readings may be higher than rated value due to parallel paths on the PCB. Look for roughly correct values; zero/near-zero suggests a cracked or open cap.
- Use to spot-check pre-soldered decoupling caps (~100 nF near IC sockets).

---

## Other useful functions

- **Data Hold (H)** -- Freezes reading. Press H again to resume.
- **Relative (REL)** -- Stores a reference and shows difference. Works in voltage, current, temperature, and capacitance modes only (not resistance).
- **Max/Min** -- Records max and min during measurement.
- **Backlight** -- Press and hold the HOLD/backlight button ~2 s.

**Not needed for RP6502**: Lo Z (AC only), LPF (variable frequency AC), NCV, frequency/Hz, current, temperature.

---

## Power and maintenance

- **Batteries**: 2x AA. Replace when low-battery icon shows.
- **Auto Power Off**: ~15 minutes. To disable: with meter **off**, hold **M** and turn the dial on; meter beeps 5 times.
- **Fuses**:
  - mA/uA: 600 mA (FF 0.6A H 600V), 6.3x32 mm.
  - 10A: 10 A (F 10A H 600V), 6.3x32 mm.
  Replace with same type and rating.

---

## Quick reference for RP6502 pre-IC checks

| Check | Function | Leads | Notes |
|-------|----------|--------|--------|
| Shorts (power vs GND) | Continuity (Ohm + M) | COM, V/Ohm | Should be **OL** (open). |
| Ground continuity | Continuity | COM, V/Ohm | Should beep between GND points. |
| Rail continuity | Continuity | COM, V/Ohm | Should beep along +3V3A, +3V3B. |
| Resistor values | Resistance (Ohm, no continuity) | COM, V/Ohm | Board unpowered; compare to BOM. |
| 3.3V rails | DC V | COM, V/Ohm | Only after no shorts; use current-limited supply. |
| Decoupling caps | Capacitance (F) | COM, V/Ohm | Board unpowered; use REL for caps <= 1 uF. |

---

## Specifications (relevant to RP6502)

- **DC voltage**: 0.5% accuracy; up to 600 V.
- **Resistance**: 0.1 Ohm to 40 MOhm (EX350) / 60 MOhm (EX355).
- **Continuity**: Buzzer < ~10 Ohm; OL > ~400 Ohm (EX350) or ~600 Ohm (EX355).
- **Display**: 4000 count (EX350) or 6000 count (EX355); backlit.
- **Input impedance**: 10 MOhm (voltage).

For full specifications and safety details, see the Extech EX350 Series User Guide.
