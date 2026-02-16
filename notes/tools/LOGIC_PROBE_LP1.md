# Logic Probe LP-1

**Model**: LP-1  
**Manufacturer**: Circuit Specialists (discontinued)  
**Type**: General-purpose handheld logic probe  
**Price**: ~$13.89 (discontinued, may find used/variants)

**Note**: There is also an "LP1" logic/test probe from Andromeda Research ($179) designed specifically for in-circuit EEPROM/microcontroller programming. This document covers the basic general-purpose LP-1 logic probe.

---

## Overview

The LP-1 is a simple, handheld logic probe for testing digital circuits. It provides instant visual indication of logic states (high/low) and can detect pulses. It's a basic, low-cost tool for quick digital signal checking without needing a multimeter or oscilloscope.

**Key features**:
- Handy, quick and easy to use
- Supports DTL, TTL, and CMOS logic families
- High-frequency response
- Instant indication of logic states
- Pulse memory function

---

## Specifications

| Parameter | Value |
|-----------|-------|
| **Logic families supported** | DTL, TTL, CMOS |
| **Power supply** | Up to 18V DC (from circuit under test) |
| **Size** | 14 x 3 cm |
| **Color** | Grey |
| **Material** | Plastic |
| **LED indicators** | Red, Green, Orange (three-color) |

---

## Controls

1. **Mode switch (upper)**: TTL / CMOS
   - Set to **"TIL"** (TTL) for TTL/DTL circuits (5V logic)
   - Set to **"CMOS"** for CMOS circuits (3.3V, 5V, etc.)

2. **Pulse switch (lower)**: PULSE / MEM (memory position)
   - **PULSE**: Detect pulses/transitions
   - **MEM** (memory position): Stores pulse detection

---

## Power connection

1. Connect **RED clip** to **+ terminal** of the power source of the circuit under test
2. Connect **BLACK clip** to **- terminal** (ground) of the power source

**Important**: The supply voltage should not exceed 18V DC. The LP-1 draws power from the circuit under test, so the circuit must be powered.

---

## LED indicators

| LED state | Meaning |
|-----------|---------|
| **All LEDs OFF** | High impedance (floating pin, not connected) |
| **Red LED ON** | High state (Logic 1) |
| **Green LED ON** | Low state (Logic 0) |
| **Orange LED ON** | Pulse detected (when in PULSE or MEM mode) |

---

## How to use

### Basic logic level detection

1. Connect RED clip to + terminal and BLACK clip to - terminal of the circuit's power source
2. For **TTL and DTL**: Set the upper select switch to the side marked **"TIL"**
   For **CMOS**: Set the upper select switch to the side marked **"CMOS"**
3. Contact the probe pin to the point to be tested
4. The LEDs indicate its logic states:
   - **ALL LEDs OFF** = High impedance (floating pin, not connected)
   - **RED LED ON** = High State (Logic 1)
   - **GREEN LED ON** = Low State (Logic 0)
   - **ORANGE LED ON** = Pulse detected

### Pulse detection and memory mode

1. Set the lower select switch to the side marked **"PULSE"** first
2. Contact the probe pin to the point to be tested
3. The LEDs indicate its basic state (red/green for high/low)
4. Then set the lower select switch to the side marked **"MEM"** (memory position)
5. The **orange LED** lights when any pulse or level transition is detected
6. The pulse direction, in comparison with the original basic state, can be determined
7. **After use, reset the logic probe** by setting the lower select switch back to the side marked **"PULSE"**

---

## Relevance to RP6502

The LP-1 is a **quick, simple tool** for basic digital signal checking:

| Use case | How |
|----------|-----|
| **Quick signal check** | Touch probe to address/data lines to see if they're high/low |
| **Clock verification** | Check PHI2 clock line - should show pulses (orange LED) |
| **Power-on check** | Verify power rails are present (red LED on +3.3V, green on GND) |
| **Floating pin detection** | If LED stays off, pin might be disconnected |
| **Pulse detection** | Catch brief pulses or glitches on control lines |

**Limitations**:
- **No voltage measurement** - only shows high/low, not actual voltage levels
- **No timing information** - can't measure frequency, pulse width, or timing relationships
- **Single channel** - can only check one signal at a time
- **No protocol decoding** - can't decode SPI, I2C, etc.

**Comparison with other tools**:
- **vs. Multimeter**: LP-1 is faster for checking logic states, but multimeter shows actual voltage
- **vs. Logic Analyzer**: LP-1 is simpler and instant, but logic analyzer captures timing and multiple channels
- **vs. Oscilloscope**: LP-1 is quick and cheap, but oscilloscope shows waveforms and analog properties

**Bottom line**: The LP-1 is a handy **quick-check tool** for verifying digital signals are present and at valid logic levels. It's not a replacement for a multimeter, logic analyzer, or oscilloscope, but it's useful for rapid troubleshooting when you just need to know "is this pin high or low?"

---

## Notes

- **Power from circuit**: The LP-1 draws power from the circuit under test - make sure the circuit is powered
- **CMOS mode**: Use CMOS mode for 3.3V logic (like RP6502), TTL mode for 5V logic
- **Reset after pulse memory**: Always reset the lower switch back to "PULSE" after using memory mode
- **High-frequency response**: The LP-1 can detect fast pulses, making it useful for digital signal checking
- **Package contents**: 1 x Logic Probe (probe only, other products not included)
