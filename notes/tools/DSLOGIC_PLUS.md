# DSLogic Plus USB Logic Analyzer

**Model**: DSLogic Plus  
**Manufacturer**: DreamSourceLab (DreamSource Technology Co., Ltd)  
**Datasheet**: DSLogic Plus Datasheet  
**User guide**: DSView User Guide v1.3.0  
**Local copies**:
- `~/iCloud Drive/Manuals/DSLogic_Plus_Datasheet.pdf`
- `~/iCloud Drive/Manuals/DSView_User_Guide.pdf`

**Product page**: https://www.dreamsourcelab.com/product/dslogic-series/  
**Software**: DSView (open source, cross-platform)  
**Download**: http://www.dreamsourcelab.com/download/  
**Source code**: http://www.github.com/DreamSourceLab/DSView

---

## Overview

The DSLogic Plus is a USB-based 16-channel logic analyzer in a CNC aluminum case with shielded fly wires. It captures digital signals up to 400 MHz sample rate, with two operating modes:

- **Buffer mode**: High sample rate, limited by hardware memory (256 Mbit).
- **Stream mode**: Long capture duration (up to 16G samples), limited by USB 2.0 bandwidth.

---

## Key specifications

| Parameter | Value |
|-----------|-------|
| Channels | 16 |
| Max sample rate (buffer, 4 ch) | 400 MHz |
| Max sample rate (buffer, 16 ch) | 100 MHz |
| Max sample rate (stream, 3 ch) | 100 MHz |
| Max sample rate (stream, 16 ch) | 20 MHz |
| Hardware memory | 256 Mbit |
| Max sample depth (buffer) | 256M / num_channels (or 16G with RLE) |
| Max sample depth (stream) | 16G |
| Min acquisition pulse | 5 ns |
| Threshold voltage | 0 - 5V (0.1V step, adjustable) |
| Safe input voltage | -30V to +30V (with fly wires) |
| Input impedance | 250 kOhm // ~13 pF |
| Interface | USB Type-C (USB 2.0) |
| Size | 79 x 74 x 9 mm |
| Power | 5V DC via USB, 2W max |
| ESD protection | Yes |

---

## Operating modes

### Buffer mode
- Data stored in hardware memory during capture, transferred to PC after completion.
- **Advantage**: Higher sample rate (up to 400 MHz).
- **Limitation**: Shorter capture duration (limited by 256 Mbit memory).
- Supports RLE compression to extend effective sample depth.
- Supports advanced triggers (16 stages, serial/protocol triggers).
- Example: 16 channels @ 100 MHz = ~167 ms capture.

### Stream mode
- Data transferred to PC in real-time via USB during capture.
- **Advantage**: Much longer capture duration (hours at low sample rates).
- **Limitation**: Lower max sample rate due to USB 2.0 bandwidth.
- Supports loop capture (continuous scrolling display).
- Example: 16 channels @ 1 MHz = ~4.77 hours capture.

---

## Sample rate selection guide

The sample rate should be **10x to 100x** the frequency of the signal being measured:

| Signal | Frequency | Recommended sample rate |
|--------|-----------|------------------------|
| UART 115200 baud | ~115 kHz | 2 MHz |
| I2C 400 kHz | 400 kHz | 4 - 10 MHz |
| SPI 1 MHz | 1 MHz | 10 - 20 MHz |
| 6502 bus @ 8 MHz | 8 MHz | 80 - 100 MHz (buffer mode) |
| SPI 40 MHz | 40 MHz | 400 MHz (buffer, 4 ch) |

---

## Trigger capabilities

### Simple trigger
- Rising edge, falling edge, either edge, high level, low level.
- Multiple channels combined with logical AND.
- Trigger position adjustable (percentage of sample duration).

### Advanced trigger (buffer mode only)
- **Stage trigger**: Up to 16 sequential trigger stages.
- Each stage: two sets of conditions with AND/OR logic.
- Per-channel: X (don't care), 0, 1, R (rising), F (falling), C (either edge).
- Counter: trigger after N occurrences.
- **Serial/protocol trigger**: Generic serial bus trigger template.
- Example: Trigger on specific I2C byte (e.g., address 0x50).
- Example: Trigger on specific SPI data (e.g., 0x1234).

---

## Protocol decoders

DSView includes a large library of protocol decoders (based on sigrok/libsigrokdecode):

**Base decoders**: I2C, UART, SPI, CAN, I2S, JTAG, 1-Wire, DMX512, PWM, Parallel, SWD, USB PD, USB signalling, SWIM, SD card (SD mode), PS/2, MDIO, Stepper motor, Timing, Z80, AC'97, Counter, IR NEC, IR RC-5, AM230x, AVR PDI, and many more.

**Stack decoders**: LIN, 24xx EEPROM, 93xx EEPROM, USB request/packet, 1-Wire network layer, AVR ISP, nRF24L01(+), SPI flash/EEPROM, Modbus, MIDI, I2C demux/filter, ARM ETMv3/ITM/TPIU, JTAG/STM32, and many more.

Decoders relevant to RP6502:
- **SPI**: Communication between Pico (RIA) and the 6502 system.
- **I2C**: If any I2C peripherals are connected.
- **Parallel**: For analyzing the 6502 address/data bus.
- **UART**: Serial communication debugging.
- **1-Wire, PS/2**: Potential peripherals.

---

## DSView software features

- **Cross-platform**: Windows, macOS, Linux.
- **Capture modes**: Single, Repetitive, Loop.
- **Navigation**: Zoom (wheel/right-click window), pan, pattern search.
- **Measurements**: Width, period, frequency, duty cycle, pulse counting, edge distance.
- **Cursors**: Multiple cursor insertion with edge snap, distance measurement.
- **File operations**: Save/open .dsl files, export to CSV/VCD/Gnuplot, screenshots.
- **Protocol list viewer**: Tabular view of decoded data with search and export.

---

## Hardware connection

1. Connect DSLogic Plus to PC via USB Type-C cable (use a quality, short cable directly to motherboard USB port).
2. Open DSView -- indicator light turns green when recognized.
3. Connect shielded fly wires to the sampling ports.
4. Connect channel signal + ground wires to the circuit under test.

**Grounding tips**:
- For low-frequency signals (<5 MHz): Share a common ground wire.
- For high-frequency signals: Individually ground each channel for best signal integrity.

**Safety**:
- If using a grounded PC, the DSLogic ground terminals are connected to earth ground -- do not connect ground to hot/live circuits.
- Overcurrent protection is built in, but avoid short circuits.

---

## Relevance to RP6502

The DSLogic Plus is one of the **most valuable tools** for debugging the RP6502:

| Use case | How |
|----------|-----|
| **6502 address/data bus analysis** | Connect channels to address lines (A0-A15) and data lines (D0-D7). Use buffer mode at 80-100 MHz to capture bus cycles. |
| **Clock verification** | Capture the 8 MHz PHI2 clock signal to verify it's clean and at the correct frequency. |
| **SPI debugging** | Monitor SPI communication between the Pico RIA and the 6502 system. Use SPI protocol decoder. |
| **UART debugging** | Capture and decode serial communication. |
| **Timing analysis** | Verify chip select, read/write, and address decode timing against 6502 datasheet requirements. |
| **Protocol triggering** | Set up triggers on specific bus events (e.g., trigger when a specific address is accessed). |
| **Long captures** | Use stream mode for capturing extended sequences of bus activity at lower sample rates. |

**Recommended setup for RP6502 bus debugging**:
- **Mode**: Buffer, 16 channels @ 100 MHz (or 8 channels @ 200 MHz for higher resolution).
- **Threshold**: 3.3V logic -> set threshold to ~1.6V.
- **Channels**: Assign to address bus, data bus, PHI2, R/W, chip selects as needed.

The DSLogic Plus complements the DSO-TC3 and multimeters: while those tools measure analog properties (voltage, resistance), the logic analyzer captures and decodes **digital signal timing and protocols** -- essential for understanding what the 6502 CPU is actually doing on the bus.
