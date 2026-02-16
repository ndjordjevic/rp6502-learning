# FNIRSI DSO-TC3 (3-in-1 Oscilloscope / Component Tester / Signal Generator)

**Model**: DSO-TC3  
**Manufacturer**: Shenzhen FNIRSI Technology Co., LTD  
**Manual**: DSO-TC3 Manual V1.9 (6 languages)  
**Local copy**: `~/iCloud Drive/Manuals/DSO-TC3_Manual-V1.9-2023.03.29.pdf`  
**Website**: http://www.fnirsi.cn/

---

## Overview

The FNIRSI DSO-TC3 is a 3-in-1 handheld instrument combining:

1. **Digital Oscilloscope (DSO)** -- 10 MSa/s, 500 KHz bandwidth, max 400V input
2. **Component Tester (M-Tester)** -- auto-identifies transistors, diodes, resistors, capacitors, inductors
3. **Signal Generator (DDS)** -- 6 waveforms, 1-100 KHz, 0-3.3V output

Plus a **Toolbox** with continuity test, voltage measurement (0-40V), DS18B20 temperature, DHT11 humidity, IR decoding, and auto-calibration.

**Display**: 2.4" color TFT (240x320), LED backlight  
**Power**: Built-in rechargeable lithium battery, USB Type-C charging (+5V)  
**Size**: 79 x 103 x 31 mm, foldable stand  

---

## Home screen functions

The device has four main modes selectable from the home screen:

| Mode | Description |
|------|-------------|
| **M-Tester** | Component tester (transistors, diodes, R, C, L) |
| **Oscilloscope** | Digital oscilloscope |
| **Generator** | Signal generator (DDS) |
| **Tools** | Toolbox (continuity, voltage, temp, IR decode, calibration) |

---

## Buttons

| Button | Short press | Long press |
|--------|------------|------------|
| **Power** (left side) | Start up / Return | Off |
| **Confirm** (center) | Enter / confirm / re-measure | Enter system settings |
| **Right** | Move right / toggle | Show/hide oscilloscope parameters |
| **Left** | Move left / switch | Stop/Run in oscilloscope mode |
| **Up** | Move up / switch / value + | Continuous increase |
| **Down** | Move down / switch / value - | Continuous decrease |
| **Reset** (side hole) | Tap to reset device | -- |

---

## Signal connectors (top, MCX)

Three MCX coaxial sockets on top (outer rings share a common ground):

| Connector | Purpose | Max voltage |
|-----------|---------|-------------|
| **DSO** | Oscilloscope signal input | 40 Vpk |
| **DDS** | Signal generator output | 3.3V |
| **IN (0~40V)** | Voltage measurement input | DC 40V |

Use MCX-tipped test leads/probes for these connectors.

---

## Test socket (component tester)

A 14-hole double-row socket with locking lever, at the bottom left of the screen:

- **1-2-3 area**: General component testing. Pins labeled 1, 2, 3 (same labels are internally shorted). Insert 2-pin or 3-pin components into any different-labeled holes, in any order.
- **K-A-A area**: High-voltage testing (~30V DC). K = positive, A = negative. For zener diodes: anode -> A, cathode -> K.
- **Lever up**: socket relaxed (insert/remove components). **Lever down**: locked for testing.

**Safety**:
- Discharge capacitors before measuring -- can burn out the instrument.
- Do not test in-circuit (powered).
- External circuit must be unpowered.

---

## 1. Component tester (M-Tester)

Auto-identifies and measures components inserted in the test socket. Press confirm button to start a test.

### What it can test

| Component | Range | Key measurements |
|-----------|-------|------------------|
| **NPN/PNP transistors** | beta 10-600 | hFE, Vbe, Ic/Ie, Iceo, Ices |
| **MOSFETs** (N/P channel) | Vgs < 5V | Vt, Cg, Rds, protection diode Uf |
| **JFETs** | Vgs < 5V | Id under Vgs, protection diode Uf |
| **IGBTs** | Vgs < 5V | Gate capacitance, drain current |
| **Diodes** | Vf < 4.5V | Forward voltage, junction capacitance, reverse leakage |
| **Zener diodes** | 0.01-32V (K-A-A area) | Reverse breakdown voltage (max 24V measured) |
| **SCRs/TRIACs** | Vt < 5V, Ig < 6mA | Gate voltage |
| **Capacitors** | 25 pF - 100 mF | Capacitance, loss factor (Vloss) |
| **Resistors** | 0.01 Ohm - 50 MOhm | Resistance value |
| **Inductors** | 10 uH - 1000 mH | Inductance, DC resistance (only when R < 2.1k Ohm) |
| **Batteries** | 0.1 - 4.5V | Voltage, polarity |
| **LEDs** | -- | Detected as diode (higher Vf); LEDs flash during test |

### Limitations

- MOSFET/IGBT turn-on voltage must be < 5V, otherwise only equivalent parameters shown.
- SCR trigger current must be < 6mA.
- Capacitors below 20 pF: test with a 20 pF cap in parallel.
- Air-core coils and power inductors: try testing in series with a known inductor.

### Relevance to RP6502

The component tester can verify passive components on the RP6502 board. However, for ICs (CPUs, logic chips, RAM), use the dedicated **Chip Tester Pro V2** instead -- the DSO-TC3 cannot test ICs in the same way.

Useful for:
- Testing individual resistors, capacitors, and diodes before/after desoldering
- Verifying replacement components
- Checking Vloss on old capacitors to assess health

---

## 2. Oscilloscope (DSO)

### Specifications

| Parameter | Value |
|-----------|-------|
| Real-time sample rate | 10 MSa/s |
| Analog bandwidth | 500 KHz |
| Input impedance | 1 MOhm |
| Coupling | AC / DC |
| Max input voltage | 400V (with 10X probe) |
| Vertical sensitivity (1X) | 10 mV - 10V per division |
| Time base range | 1 us - 10 s per division |
| Trigger modes | Auto, Normal, Single |
| Trigger types | Rising edge, Falling edge |
| Waveform freeze | Yes |

### Real-time measurements (toggle with long-press right button)

- Vmax, Vmin, Vave, Vrms, Vpp, Frequency, Duty cycle, Cycle time

### Probe settings

- **1X mode**: measures up to +/-40V
- **10X mode**: measures up to +/-400V
- The oscilloscope 1X/10X setting **must match** the probe's physical 1X/10X switch.

### Relevance to RP6502

Useful after the board is assembled and powered:
- Verify clock signals (though 500 KHz bandwidth limits what can be observed at higher frequencies)
- Check power supply ripple on 3.3V rails (DC coupling)
- Debug digital signals (with bandwidth limitation in mind)
- Verify signal generator output for testing

**Note**: The 500 KHz bandwidth is quite limited. The RP6502's W65C02S runs at up to 8 MHz, which is well above this oscilloscope's bandwidth. For proper digital signal analysis, use a logic analyzer instead.

---

## 3. Signal generator (DDS)

Outputs signals via the **DDS** MCX connector.

| Waveform | Frequency | Amplitude | Duty cycle |
|----------|-----------|-----------|------------|
| Sine | 1-100 KHz | 0-3.3V | 50% |
| Square | 1-100 KHz | 3.3V | 50% |
| Pulse | 1-100 KHz | 3.3V | 0-100% |
| Triangle | 1-100 KHz | 0-3.3V | 50% |
| Ramp | 1-100 KHz | 0-3.3V | 0-100% |
| DC | -- | 0-3.3V | -- |

### Relevance to RP6502

Could be useful for:
- Generating test signals to verify oscilloscope operation
- Providing a known signal to test logic circuits after assembly

---

## 4. Toolbox

### Continuity test

- Uses test socket pins 1, 2, 3 (any two).
- Buzzer sounds when circuit has low resistance ("connected").
- Similar to multimeter continuity but uses the component test socket, not probes.

### Voltage test

- Uses the **IN (0~40V)** MCX connector on top.
- Measures DC voltage up to 40V.
- Could be used to check the RP6502's 3.3V rails (but a multimeter with probes is more practical).

### DS18B20 / DHT11

- For temperature/humidity sensors inserted in the test socket.
- Not relevant for RP6502 pre-IC testing.

### IR decode

- Decodes NEC protocol infrared remote signals.
- Not relevant for RP6502.

### Auto calibration

- Insert the included 3-pin short wire into socket 1-2-3.
- Follow on-screen prompts until progress bar reaches 100%.
- Run this periodically for accurate component measurements.

---

## System settings

Long press the confirm button to enter settings:

- Boot LOGO (on/off)
- System language
- System volume
- Backlight brightness
- Default mode (which mode to start in)
- About (firmware version)

---

## Firmware upgrade

1. Open the upgrade software on a PC (Windows 10+ only).
2. Connect via USB cable.
3. While holding the **down** button, press **power** to enter upgrade mode.
4. Select the firmware file in the PC software.

**Warning**: Only use official firmware files. Using other files can cause irreparable damage.

---

## Power and maintenance

- **Battery**: Built-in 3.7V rechargeable lithium battery, USB Type-C charging.
- **Charging indicator**: Red = charging, Green = fully charged.
- **Do not use while charging** (especially for high-voltage tests).
- **Long-term storage**: Discharge to 3.7V, then charge/discharge cycle every quarter.
- **Calibrate** periodically using the auto-calibration function.

---

## Safety

- Do not touch exposed metal parts when measuring high voltage.
- Do not exceed max input voltages (40Vpk for DSO, 40V DC for IN port).
- Match probe attenuation (1X/10X) with oscilloscope setting.
- Discharge capacitors before using the component tester.
- Do not test components in-circuit while circuit is powered.

---

## Quick summary for RP6502 use

| DSO-TC3 Function | Useful for RP6502? | Notes |
|------------------|--------------------|-------|
| Component tester | Somewhat | Good for verifying individual R, C, diodes. Cannot test ICs like Chip Tester Pro V2. |
| Oscilloscope | Limited | 500 KHz bandwidth too low for 8 MHz CPU clock. OK for power rail ripple check. |
| Signal generator | Maybe later | Could test circuits with known signals after assembly. |
| Continuity (toolbox) | Not ideal | Uses test socket, not probes. Multimeter is more practical. |
| Voltage (toolbox) | Not ideal | Uses MCX connector, not probes. Multimeter is more practical. |

**Bottom line**: The DSO-TC3 is a nice portable all-in-one tool, but for RP6502 work, your dedicated multimeters (EX350, DMT-99) and Chip Tester Pro V2 are better suited for pre-IC testing. The DSO-TC3's main value will come later when you need a quick oscilloscope for power rail checks or debugging analog signals on the assembled board.
