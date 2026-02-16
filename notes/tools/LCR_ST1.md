# FNIRSI LCR-ST1 Intelligent Bridge Tweezers (LCR Meter)

**Model**: LCR-ST1  
**Manufacturer**: Shenzhen FNIRSI Technology Co., LTD  
**Manual**: LCR-ST1 User Manual V2  
**Local copy**: `~/iCloud Drive/Manuals/LCR-ST1_User_Manual-2.pdf`  
**Website**: http://www.fnirsi.cn/

---

## Overview

The FNIRSI LCR-ST1 is a tweezer-shaped LCR bridge meter for precision measurement of **resistance (R)**, **capacitance (C)**, **inductance (L)**, and **diodes**. The tweezer form factor makes it ideal for testing SMD (surface-mount) components and working in tight spaces on a PCB.

- **Display**: 1.14" color screen
- **Power**: Built-in 250 mAh rechargeable lithium battery, USB Type-C (5V/1A)
- **Size**: 28 x 19 x 150 mm, 41g
- **Test frequencies**: 100 Hz, 1 kHz, 10 kHz
- **Test voltages**: 0.3V, 0.6V
- **Data export**: Auto-saves to Excel via simulated USB drive

---

## Controls

| Control | Short press | Long press |
|---------|------------|------------|
| **Power button** | Power on / Reset (zero) | Power off |
| **Hold/Setting button** | Data hold (saves to Excel) | Enter/exit settings |
| **Dial (scroll wheel)** | Left/right: switch measurement mode (Auto, R, C, L, Diode); Press: select test level/frequency area | Left/right: adjust values in settings; Up/down: select menu items |

**Secondary parameter**: Press the dial button to cycle through D, Q, Z, X auxiliary readings.

---

## Measurement ranges and accuracy

### Capacitance

| Range | 100 Hz | 1 kHz | 10 kHz |
|-------|--------|-------|--------|
| 1 pF - 1 nF | -- | 2% +/-3 | 2% +/-3 |
| 1 nF - 1 uF | 2% +/-3 | 0.5% +/-3 | 0.5% +/-3 |
| 1 uF - 1 mF | 2% +/-3 | 2% +/-3 | 2% +/-3 |
| 1 mF - 22 mF | 5% +/-3 | 5% +/-3 | -- |

### Inductance

| Range | 100 Hz | 1 kHz | 10 kHz |
|-------|--------|-------|--------|
| 1 uH - 10 uH | -- | 2% +/-3 | 2% +/-3 |
| 10 uH - 1 mH | 2% +/-3 | 0.5% +/-3 | 0.5% +/-3 |
| 1 mH - 1 H | 2% +/-3 | 2% +/-3 | 2% +/-3 |
| 1 H - 10 H | 5% +/-3 | 5% +/-3 | -- |

### Resistance

| Range | 100 Hz | 1 kHz | 10 kHz |
|-------|--------|-------|--------|
| 10 mOhm - 1 Ohm | 2% +/-3 | 2% +/-3 | 2% +/-3 |
| 1 Ohm - 1 kOhm | 1% +/-3 | 0.5% +/-3 | 0.5% +/-3 |
| 1 kOhm - 1 MOhm | 1% +/-3 | 0.5% +/-3 | 1% +/-3 |
| 1 MOhm - 10 MOhm | 5% +/-3 | 5% +/-3 | -- |

### Diode

- Forward voltage drop < 0.7V

---

## How to use

### Basic operation

1. **Power on**: Short press the power button. Select language on first use.
2. **Select measurement mode**: Scroll the dial left/right to switch between Auto, Resistance, Capacitance, Inductance, Diode.
3. **Select test frequency**: Press the dial to highlight the frequency area, then scroll to choose 100 Hz, 1 kHz, or 10 kHz.
4. **Select test voltage**: Press the dial to highlight the voltage area, then scroll to choose 0.3V or 0.6V.
5. **Measure**: Touch the tweezer tips to the component. The reading appears automatically.
6. **Data hold**: Short press the Hold button to freeze the reading. Data is also auto-saved to Excel.

### Short circuit zeroing (calibration)

Before measuring, zero the tweezers to remove lead/probe offset:

1. Select the desired test frequency.
2. Short the tweezer tips together (or use the included short-circuit piece).
3. Short press the power button to enter zeroing mode.
4. The instrument auto-measures and executes zeroing.

**Do this for each frequency you plan to use.**

### Continuity

- In **diode mode**, if resistance < 50 Ohm, continuity is triggered (buzzer sounds).

### Data export

- Data held with the Hold button is auto-saved to an internal Excel file.
- Connect via USB Type-C cable to a computer -- the device appears as a USB drive.
- Open the Excel file to view saved measurements.

---

## Test frequency selection guide

Different test frequencies work better for different component values:

| Component type | Recommended frequency |
|---------------|----------------------|
| Large capacitors (>1 uF) | 100 Hz |
| Medium capacitors (1 nF - 1 uF) | 1 kHz (best accuracy: 0.5%) |
| Small capacitors (<1 nF) | 10 kHz |
| Large inductors (>1 mH) | 100 Hz |
| Medium inductors (10 uH - 1 mH) | 1 kHz (best accuracy: 0.5%) |
| Small inductors (<10 uH) | 10 kHz |
| Resistance (general) | 1 kHz (best accuracy: 0.5%) |

**Note**: The same component may yield different results at different frequencies due to non-ideal behavior and parasitic effects. This is normal for LCR measurements.

---

## Settings

Long press the Hold/Setting button to enter settings:

| Setting | Options |
|---------|---------|
| Language | Chinese, English |
| Volume | 0-5 levels |
| Backlight | 0-100 brightness |
| Auto Power Off | Off / 5 / 15 / 30 minutes |
| Restore | Factory reset (clears all data) |
| About | Model and firmware version |

---

## Firmware upgrade

1. Power off the device.
2. Long press the **dial button** and **power button** together to enter upgrade mode.
3. Connect to a computer via USB Type-C data cable.
4. A folder popup appears -- drag the firmware file into it.
5. Device restarts automatically when done.

---

## Safety and precautions

- **Do not measure live/powered circuits** -- will damage the instrument.
- **Discharge capacitors before measuring** -- charged capacitors can damage the instrument.
- **Ensure good contact** between tweezer tips and component to avoid measurement errors.
- **In-circuit measurements are unreliable** -- parallel paths on the PCB affect readings. Values are for reference only.
- **Auto mode does not support diode measurement** -- switch to diode mode manually.
- Auto-detected component type is for reference only; misidentification is possible.

---

## Relevance to RP6502

The LCR-ST1 is useful for:

- **Verifying SMD components**: If you ever need to check individual capacitors, resistors, or inductors (e.g., spare parts or pulled components).
- **Checking capacitor health**: The LCR meter measures at AC frequencies, giving you ESR-related info (D/Q parameters) that a regular multimeter cannot provide.
- **Precision capacitance measurement**: Better accuracy (0.5% at 1 kHz) than multimeters for capacitor verification.
- **Inductance measurement**: Neither the EX350 nor the DMT-99 can measure inductance; the LCR-ST1 fills this gap.

**Limitations for RP6502**:
- The tweezer form factor is designed for SMD components. Through-hole components on the RP6502 board may be harder to grip with tweezers.
- In-circuit measurements on the assembled board will be unreliable due to parallel paths -- measure components individually when possible.

---

## Comparison with multimeters for component testing

| Feature | LCR-ST1 | Extech EX350 | FNIRSI DMT-99 |
|---------|---------|--------------|---------------|
| Capacitance | 1 pF - 22 mF (0.5% at 1 kHz) | 6 nF - 60 mF (4%) | 9.999 nF - 99.99 mF (2%) |
| Inductance | 1 uH - 10 H | No | No |
| Resistance | 10 mOhm - 10 MOhm | 0.1 Ohm - 60 MOhm | 999.9 Ohm - 99.99 MOhm |
| Test frequencies | 100 Hz, 1 kHz, 10 kHz | N/A | N/A |
| D/Q/Z/X parameters | Yes | No | No |
| Form factor | Tweezers (SMD-friendly) | Standard probes | Standard probes |
| Data export | Excel via USB | No | Recording mode (on-screen) |
