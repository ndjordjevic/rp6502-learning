# Logic Probe Model 610B (01LP610)

**Model**: 610B / 01LP610  
**Manufacturer**: Electronix Express  
**Type**: Circuit-powered logic probe with audio beeper  
**Price**: ~$23.65  
**Product page**: https://www.elexp.com/products/01lp610logic-probe-model-610b

---

## Overview

The Model 610B is an enhanced logic probe with both **LED indicators and an audio beeper**. It's designed for troubleshooting fast logic circuits with TTL and CMOS compatibility. The audio feedback makes it easier to detect signals without constantly looking at LEDs.

**Key features**:
- TTL and CMOS compatibility
- LED indicators (Red for High, Green for Low)
- Audio beeper with variable/fixed tones
- Pulse detection and pulse memory function
- High-frequency response (up to 20 MHz)
- Fast pulse detection (minimum 30 ns pulse width)

---

## Specifications

| Parameter | Value |
|-----------|-------|
| **Logic families supported** | TTL, CMOS |
| **Power supply** | 5-15V DC (from circuit under test) |
| **Power supply protection** | 20V DC/VAC |
| **Maximum input voltage** | 40V DC/VAC (duration <15 seconds) |
| **Maximum input frequency** | 20 MHz |
| **Input impedance** | 1 MOhm |
| **Minimum pulse width detection** | 30 ns (at 20 kHz - 20 MHz) |
| **Pulse indicator flash time** | 500 ms |
| **Operating temperature** | 0-40°C, <80% RH |
| **Storage temperature** | -20-50°C, <85% RH |
| **Size** | 7.83" x 0.82" x 0.78" (199 x 21 x 20 mm) |
| **Weight** | 1.4 oz (~40g) |

### Minimum detectable pulse width

| Frequency | Minimum pulse width |
|-----------|---------------------|
| 1 kHz | 100 ns |
| 1 kHz - 20 kHz | 50 ns |
| 20 kHz - 20 MHz | 30 ns |

**Pulse amplitude**: +3V

---

## LED and audio indicators

| Input signal | Level | LED indication | Buzzer |
|-------------|-------|----------------|--------|
| **Logic "1"** | TTL: >2.3±0.2V<br>CMOS: >70%Vcc ±10% | HI (Red) ON | Fixed tone |
| **Logic "0"** | TTL: <0.8V±0.2V<br>CMOS: <30%Vcc ±10% | LO (Green) ON | Fixed tone |
| **Bad logic level or open circuit** | None | No LED | No tone |
| **Square wave** | <200 Hz | HI and LO blink at frequency rate | Variable tone at frequency rate |
| **Square wave** | >200 Hz | HI and LO may or may not be ON | Variable tone at frequency rate |
| **Narrow Hi pulse** | TTL: >2.3±0.2V<br>CMOS: >70%Vcc ±10% | LO blinking (intensity depends on pulse duty cycle) | Variable tone at frequency rate |
| **Narrow Lo pulse** | TTL: <0.8V±0.2V<br>CMOS: <30%Vcc ±10% | HI blinking (intensity depends on pulse duty cycle) | Variable tone at frequency rate |

---

## Controls

The Model 610B has switch-selectable functions:
- **TTL/CMOS mode selection**
- **Pulse detection or pulse memory function**

---

## Power connection

1. Connect power leads to the circuit under test
2. Power supply range: 5-15V DC
3. Power supply protection: 20V DC/VAC (protected against overvoltage)

**Important**: The probe draws power from the circuit under test. Ensure the circuit is powered and voltage is within 5-15V DC range.

---

## How to use

### Basic logic level detection

1. Connect power leads to circuit (red to +, black to -)
2. Set mode switch to **TTL** or **CMOS** (match your circuit)
3. Touch probe tip to the signal you want to test
4. Observe:
   - **Red LED + fixed tone** = Logic 1 (high)
   - **Green LED + fixed tone** = Logic 0 (low)
   - **No LED + no tone** = Bad logic level or open circuit

### Pulse detection

1. Set switch to pulse detection mode
2. Touch probe tip to signal
3. **Variable tone** indicates pulses/transitions
4. LED blinking intensity depends on pulse duty cycle

### Pulse memory mode

1. Set switch to pulse memory mode
2. Touch probe tip to signal
3. If a pulse occurred, the indicator will show it (useful for catching brief pulses)

---

## Relevance to RP6502

The Model 610B is a **more advanced logic probe** than the LP-1, with audio feedback:

| Use case | How |
|----------|-----|
| **Quick signal check** | Touch probe to address/data lines - audio tone confirms signal presence |
| **Clock verification** | Check PHI2 clock line - variable tone indicates 8 MHz clock pulses |
| **Power-on check** | Verify power rails (red LED + tone on +3.3V, green on GND) |
| **Pulse detection** | Audio beeper makes it easier to detect pulses without watching LEDs |
| **High-frequency signals** | Can detect pulses as narrow as 30 ns (suitable for 8 MHz CPU) |

**Advantages over LP-1**:
- **Audio feedback** - easier to detect signals without watching LEDs
- **Higher frequency** - 20 MHz max vs unspecified for LP-1
- **Faster pulse detection** - 30 ns minimum vs unspecified for LP-1
- **Variable tone** - different tones for different signal types

**Limitations** (same as LP-1):
- **No voltage measurement** - only shows high/low, not actual voltage levels
- **No timing information** - can't measure frequency, pulse width, or timing relationships
- **Single channel** - can only check one signal at a time
- **No protocol decoding** - can't decode SPI, I2C, etc.

**Comparison with other tools**:
- **vs. LP-1**: Model 610B has audio beeper and higher frequency capability
- **vs. Multimeter**: 610B is faster for checking logic states, but multimeter shows actual voltage
- **vs. Logic Analyzer**: 610B is simpler and instant, but logic analyzer captures timing and multiple channels
- **vs. Oscilloscope**: 610B is quick and cheap, but oscilloscope shows waveforms and analog properties

**Bottom line**: The Model 610B is a **handy quick-check tool with audio feedback** for verifying digital signals. The audio beeper makes it particularly useful when you're probing multiple points - you can hear signal activity without constantly watching the LEDs. It's more capable than the LP-1 but still not a replacement for a multimeter, logic analyzer, or oscilloscope.

---

## Notes

- **Audio feedback**: The variable tone feature makes it easier to detect pulses and signal activity
- **Power protection**: Built-in protection up to 20V, but don't exceed 15V for normal operation
- **CMOS mode**: Use CMOS mode for 3.3V logic (like RP6502), TTL mode for 5V logic
- **High-frequency capable**: Can handle signals up to 20 MHz, suitable for the 8 MHz RP6502 clock and faster signals
- **Pulse memory**: Useful for catching brief pulses you might miss otherwise
