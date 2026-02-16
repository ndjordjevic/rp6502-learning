# USB Power Meter / Voltage Current Meter (Package A - USB)

**Model**: USB Digital Capacity Meter Voltage Current Monitor  
**Brand**: Aideepen  
**Type**: USB power meter / volt-ammeter  
**Package**: Package A (USB interface only)  
**Certification**: CE  
**Origin**: Mainland China

---

## Overview

The USB Power Meter is a digital voltmeter/ammeter that plugs inline between a USB power source and a USB device. It measures voltage, current, power, capacity, and other parameters in real-time. Useful for testing USB chargers, power banks, measuring device power consumption, and capacity testing.

**Key features**:
- Real-time voltage, current, and power measurement
- Capacity accumulation (mAh)
- Power accumulation (mWh)
- Fast charging protocol detection
- Temperature measurement
- Impedance measurement
- Multiple data storage groups
- Display on/off function

---

## Specifications

| Parameter | Value |
|-----------|-------|
| **Voltage measurement range** | DC 4.5 - 36V |
| **Current measurement range** | 0 - 5A (or 0 - 8A depending on model) |
| **Power measurement range** | 0 - 150W |
| **Capacity accumulation range** | 0 - 99,999 mAh |
| **Power accumulation range** | 0 - 99,999 mWh |
| **Impedance measurement range** | 1 - 999.9 Ohm |
| **Temperature measurement range** | 0 - 80°C |
| **Timing range** | 0 - 999 hours 59 minutes |
| **USB D+ voltage range** | 0 - 2.99V |
| **USB D- voltage range** | 0 - 2.99V |
| **Refresh time** | >500 ms per update |
| **Measurement rate** | ~0.5 times/second (2 second intervals) |
| **Display** | IPS true color digital LCD screen |
| **Self-consumption current** | <0.02A |
| **Operating temperature** | -10 to 60°C |
| **Working humidity** | 10-80% RH (no condensation) |
| **Working air pressure** | 80-106 kPa |
| **Product dimensions** | 77.6 x 27 x 11 mm (USB version) |

---

## Interface

**Package A (USB version)**:
- **INPUT**: USB-A male connector (connects to power source/charger)
- **OUTPUT**: USB-A female port (connects to device being powered/charged)

**Connection**: Power source → INPUT → Meter → OUTPUT → Device

---

## Display parameters

The meter displays multiple parameters simultaneously:
- **Voltage (V)**: Input/output voltage
- **Current (A)**: Current draw
- **Power (W)**: Calculated power (V × A)
- **Capacity (mAh)**: Accumulated charge capacity
- **Energy (mWh)**: Accumulated energy
- **Resistance (Ω)**: Calculated impedance
- **Time**: Elapsed time (hours:minutes)
- **Temperature**: Device temperature (°C or °F)
- **Group number**: Data storage group (NO.X)
- **Fast charge protocol**: Detected charging protocol

---

## Controls

**Multi-function button**:

1. **Short press**: Switch between display screens/interfaces
2. **Triple-tap quickly**: Reset power (mWh) and timing to zero for current group
3. **Long press (2 seconds)**: Change accumulated data storage group number (NO.X)

**Display on/off**:
- Short press the switch button to turn display on/off
- Screen goes to rest/off state but device continues charging
- Useful for night charging without bright display

---

## Fast charging protocol detection

The meter can detect various fast charging protocols:
- **QC2.0 / QC3.0** (Qualcomm Quick Charge)
- **FCP / SCP** (Huawei Fast Charge)
- **APPLE 2.4A / 2.1A / 1A / 0.5A**
- **Android DCP / AFC**

**Important notes**:
1. Both charger and device must support fast charging for fast charge voltage to be detected
2. If charger supports fast charging but device doesn't, fast charge voltage cannot be detected
3. If charger doesn't support fast charging, fast charge voltage cannot be detected even if device supports it

---

## How to use

### Basic measurement

1. Connect **INPUT** (USB-A male) to power source (charger, power bank, USB port)
2. Connect **OUTPUT** (USB-A female) to device being powered/charged
3. Meter automatically powers on and displays measurements
4. Observe real-time voltage, current, power, capacity, etc.

### Capacity testing

1. Connect meter between power source and device
2. Triple-tap button to reset capacity and time to zero
3. Let device charge/discharge
4. Monitor accumulated capacity (mAh) and energy (mWh)
5. Useful for testing actual capacity of power banks or battery capacity

### Data groups

1. Long press button for 2 seconds to change group number (NO.1, NO.2, etc.)
2. Each group stores separate accumulated data
3. Useful for testing multiple devices or multiple charge cycles

### Display management

1. Short press button to cycle through different display screens
2. Short press to turn display off (device continues charging)
3. Short press again to turn display back on

---

## Relevance to RP6502

The USB Power Meter is useful for **power consumption measurement** and **USB power supply testing**:

| Use case | How |
|----------|-----|
| **Power consumption measurement** | Connect between USB power source and RP6502 board to measure actual current draw |
| **Power supply testing** | Test USB power supplies/chargers to verify voltage and current capability |
| **Capacity testing** | Measure how much energy the RP6502 consumes over time (mAh, mWh) |
| **Voltage monitoring** | Verify USB power supply provides stable 5V (or whatever voltage the board expects) |
| **Current monitoring** | Monitor current draw during operation to detect abnormal power consumption |
| **Power bank testing** | Test how long a power bank can power the RP6502 |

**Limitations**:
- **USB only** - Package A only has USB-A interface, not Type-C
- **Not suitable for direct board power** - The RP6502 is typically powered via USB through the Pico modules, so you'd measure at the USB input
- **Limited to USB voltage range** - 4.5-36V range, but USB is typically 5V

**Comparison with other tools**:
- **vs. Multimeter**: USB meter is inline and shows real-time power/capacity, but multimeter can measure any voltage/current
- **vs. DPS-150**: USB meter is simpler for USB devices, but DPS-150 is a full power supply with CV/CC modes
- **vs. Logic Analyzer/Oscilloscope**: USB meter measures power consumption, but those tools analyze signals

**Bottom line**: The USB Power Meter is a **convenient tool for measuring USB power consumption** and testing USB power supplies. For RP6502 work, it's useful for monitoring how much power the board draws from USB, testing USB power supplies, and capacity testing if you power the board from a power bank.

---

## Notes

- **Package A**: USB-A interface only (input and output are both USB-A)
- **Measurement accuracy**: Digital voltmeter class, but exact accuracy not specified
- **Self-consumption**: <0.02A - minimal impact on measurements
- **Refresh rate**: Updates approximately every 2 seconds (0.5 times/second)
- **Temperature**: Measures device temperature (likely internal sensor)
- **Reset function**: Triple-tap quickly to reset current group's accumulated data
- **Data groups**: Multiple groups allow storing separate test sessions
