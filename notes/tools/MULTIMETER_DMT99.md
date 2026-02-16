# FNIRSI DMT-99 Intelligent Digital Multimeter

**Model**: DMT-99  
**Manufacturer**: Shenzhen FNIRSI Technology Co., LTD  
**Manual**: FNIRSI DMT-99 Manual (6 languages, V2.7)  
**Local copy**: `~/iCloud Drive/Manuals/DMT-99_Manual_6_languages_ZH_EN_RU_DE_PT_ES_V2.7.pdf`  
**Website**: http://www.fnirsi.cn/

---

## Overview

The FNIRSI DMT-99 is a 10,000 count intelligent digital multimeter with a 2.4-inch full-color TFT display (240x320). It measures AC/DC voltage, AC/DC current (up to 10A), resistance, capacitance, frequency, temperature, diode, continuity, NCV, and live wire detection. It features three operating modes (Normal, Recording, Monitoring) and is USB rechargeable (1500 mAh).

**Note**: No CAT safety rating is listed in the manual. For mains voltage work, prefer the Extech EX350 (CAT III 600V). The DMT-99 is fine for low-voltage DC board work like the RP6502.

---

## Buttons

| Button | Function |
|--------|----------|
| **Right** | Cycle: voltage, resistance, diode/continuity, capacitance |
| **Up (HOLD/REL)** | Short press: data hold. Long press 2s: relative mode (long press again to exit) |
| **Middle (Auto/Menu)** | Short press: switch to auto mode. Long press 2s: enter/exit menu |
| **Down** | Cycle: frequency, temperature, NCV, LIVE |
| **Left** | Cycle: low current, high current |
| **Power** | Long press 2s: on/off |

---

## Terminals

| Terminal | Use |
|----------|-----|
| **COM** (black) | Common ground for all measurements. |
| **V/Ohm** (red) | Voltage, resistance, capacitance, diode, frequency, temperature. |
| **mA** (red) | Low current (max 999.9 mA). Fuse blows if >1A. |
| **10A** (red) | High current (max 9.999 A). Fuse blows if >10A. |

**For RP6502 pre-IC testing** use **COM** and **V/Ohm** only.

---

## Operating modes

### 1. Normal mode (default)

All measurement functions available. Auto mode (middle button) auto-identifies voltage, resistance, and continuity.

### 2. Recording mode

Same measurements as normal mode plus:
- Real-time measurement curve on display
- Records min and max values
- Save measurements (short press middle button) -- up to 30 records stored in history

Supports: voltage, resistance, diode/continuity, capacitance, current, frequency, temperature.

### 3. Monitoring mode

Threshold-based monitoring with buzzer alerts:
- Set min and max thresholds (via menu)
- Buzzer sounds when measured value is within the threshold range
- Display shows whether value is below min, within range, or above max

Supports: voltage, current, temperature.

---

## Functions used for RP6502 pre-IC testing

### Continuity

- Press **right** button to select **diode/continuity** mode.
- Leads: black -> COM, red -> V/Ohm.
- The meter auto-detects: if resistance is measured, it shows the value; if below threshold, it buzzes.
- **Threshold**: buzzer sounds when resistance < **50 Ohm** (higher than EX350's 10 Ohm threshold).
- Use to check: no shorts between power and ground; continuity of rails, ground, and socket pins.

### Resistance

- Press **right** button to select **resistance** mode.
- Leads: black -> COM, red -> V/Ohm.
- Auto-ranging.
- **Board must be unpowered.**
- Use to verify resistor values and that decoupling caps are not shorted.

### DC voltage

- Press **right** button to select **voltage** mode.
- The meter auto-identifies AC/DC.
- Leads: black -> COM, red -> V/Ohm.
- Use to check 3.3V rails after confirming no shorts (current-limited supply recommended).

### Capacitance

- Press **right** button to select **capacitance** mode.
- Leads: black -> COM, red -> V/Ohm.
- Auto-ranging.
- **Board must be unpowered.**
- Use to spot-check pre-soldered decoupling caps (~100 nF near IC sockets).
- In-circuit readings may be higher than rated value due to parallel paths on the PCB.

### Diode test

- Press **right** button to select **diode/continuity** mode (meter auto-identifies).
- Displays forward bias voltage (typically 0.4-0.9V for good diode).
- Reversed polarity or damaged diode shows **OL**.

---

## Other useful functions

- **Data Hold (HOLD)** -- Short press the up button to freeze reading. Short press again to resume.
- **Relative (REL)** -- Long press the up button for 2s to enter relative mode (stores reference, shows difference). Long press again to exit.
- **Auto mode** -- Short press middle button to auto-identify voltage, resistance, or continuity.
- **Recording mode** -- Real-time curve + min/max + save measurements. Useful for monitoring voltage stability over time.
- **Monitoring mode** -- Set voltage thresholds and get buzzer alerts. Could be useful for watching 3.3V rail stability.

**Not needed for RP6502**: NCV (AC mains only), LIVE wire detection (AC mains only - single-probe mode for identifying hot vs neutral/ground in wall outlets), frequency, current, temperature.

---

## Menu navigation

1. Long press **middle button** 2s to enter menu.
2. Use **up/down/left/right** buttons to navigate.
3. Short press **middle button** to confirm.
4. Long press **middle button** 2s to exit menu.

**Menu items:**
- Mode selection (Normal / Recording / Monitoring)
- Threshold settings (for monitoring mode)
- History (view saved records)
- System settings (language, brightness, temperature unit, auto-off time)
- Restore factory settings
- About (model and firmware version)

---

## Power and maintenance

- **Battery**: 1500 mAh rechargeable (USB). Charge when low-battery warning appears.
- **Do not use while charging.**
- **Weight**: 185g.

---

## Comparison with Extech EX350

| Feature | FNIRSI DMT-99 | Extech EX350 |
|---------|---------------|--------------|
| Display | 2.4" color TFT (10,000 count) | LCD (4,000 count) |
| Safety rating | None listed | CAT III 600V |
| Power | USB rechargeable (1500 mAh) | 2x AA batteries |
| Continuity threshold | < 50 Ohm | < 10 Ohm |
| Modes | Normal, Recording, Monitoring | Standard only |
| Lo Z / LPF | No | Yes (AC only) |
| NCV | Yes | Yes |
| LIVE (single-probe) | Yes | No |
| Max/Min recording | Via Recording mode | Dedicated Max/Min button |
| DC voltage accuracy | +/-(0.5%+3) | +/-(0.5%+2) |
| Resistance accuracy | +/-(0.5%+3) | +/-(0.8%+2) |
| Capacitance accuracy | +/-(2.0%+5) | +/-(4%+5) |

**For RP6502**: Both meters work well for DC board testing. Key differences:
- The EX350 has a **lower continuity threshold** (10 Ohm vs 50 Ohm) -- more sensitive for detecting marginal connections.
- The DMT-99 has a **color screen** and **recording/monitoring modes** -- useful for tracking voltage over time.
- The EX350 has **CAT III 600V** safety rating -- use it for any mains voltage work.
- The DMT-99 has **better capacitance accuracy** (+/-2% vs +/-4%).

---

## Quick reference for RP6502 pre-IC checks

| Check | Function | Leads | Notes |
|-------|----------|-------|-------|
| Shorts (power vs GND) | Continuity (right btn) | COM, V/Ohm | Should be **OL** (open). Buzzes < 50 Ohm. |
| Ground continuity | Continuity | COM, V/Ohm | Should buzz between GND points. |
| Rail continuity | Continuity | COM, V/Ohm | Should buzz along +3V3A, +3V3B. |
| Resistor values | Resistance (right btn) | COM, V/Ohm | Board unpowered; compare to BOM. |
| Decoupling caps | Capacitance (right btn) | COM, V/Ohm | Board unpowered; approximate in-circuit. |
| 3.3V rails | Voltage (right btn, DC) | COM, V/Ohm | Only after no shorts; use current-limited supply. |

---

## Specifications (relevant to RP6502)

- **DC voltage**: +/-(0.5%+3); ranges 9.999V / 99.99V / 999.9V.
- **AC voltage**: +/-(1%+3); ranges 9.999V / 99.99V / 750.0V.
- **Resistance**: +/-(0.5%+3); ranges 999.9 Ohm to 9.999 MOhm. High range (99.99 MOhm): +/-(1.5%+3).
- **Capacitance**: +/-(2.0%+5); ranges 9.999 nF to 999.9 uF. High range (9.999-99.99 mF): +/-(5.0%+20).
- **Frequency**: +/-(0.1%+2); ranges 9.999 Hz to 9.999 MHz.
- **Continuity**: Buzzer sounds when resistance < 50 Ohm.
- **Display**: 10,000 count, 2.4" color TFT.

For full specifications and safety details, see the FNIRSI DMT-99 manual.
