# RP6502 Learning Plan

## Overview
The RP6502 (Picocomputer 6502) is a modern single-board computer built around the classic WDC W65C02S 8-bit microprocessor, enhanced with two Raspberry Pi Pico 2 modules acting as intelligent coprocessors. This plan guides you through understanding the system from hardware to software.

---

## Phase 0: Understanding Projects in RP6502 Workspace

**Goal**: Get familiar with the workspace structure and understand what each project contains

**Resources:**
- Detailed guide: `notes/WORKSPACE_PROJECTS.md`

**Workspace Projects Checklist:**
- [x] **rp6502/** - Main firmware source code (RIA and VGA)
- [x] **examples/** - Example 6502 programs to learn from
- [x] **schematic/** - Hardware design files (KiCad)
- [x] **picocomputer.github.io/** - Official RP6502 documentation source
- [x] **vscode-cc65/** - CC65 development template
- [x] **vscode-llvm-mos/** - LLVM-MOS development template
- [x] **ehbasic/** - Enhanced 6502 BASIC interpreter
- [x] **llvm-mos-sdk/** - LLVM-MOS compiler and SDK
- [x] **cc65/** - CC65 C/assembly compiler and libraries
- [x] **pico-sdk/** - Official Raspberry Pi Pico SDK
- [x] **pico-examples/** - Official Pico example programs
- [x] **pico-extras/** - Additional Pico libraries (scanvideo, audio)
- [x] **Hunter-Adams-RP2040-Demos/** - Educational Pico demos
- [x] **documentation/** - Raspberry Pi documentation source

---

## Phase 1: High-Level Chip Overview

**Goal**: Understand what each chip does at a high level before assembly

### 1.1 Main Components Overview
**Resources:**
- Schematic: `/Users/nenaddjordjevic/CProjects/schematic/rp6502.kicad_sch`
- Documentation: https://picocomputer.github.io/hardware.html
- Documentation (local, in workspace): `/Users/nenaddjordjevic/CProjects/picocomputer.github.io/`
- Schematic image (provided)

**Learning Objectives:**
- [x] **U1 - W65C02S**: Main 8-bit CPU - executes your programs
- [x] **U2 - RP6502-RIA (Pico 2 W)**: Interface adapter - controls CPU, provides modern I/O
- [x] **U3 - AS6C1008**: 128KB SRAM - main system memory
- [x] **U4 - RP6502-VGA (Pico 2)**: Video adapter - generates VGA/HD output (optional)
- [x] **U5 - W65C22S**: VIA chip - classic 6502 I/O chip (GPIO, timers)
- [x] **U6 - 74AC00**: Quad NAND gates - address decoding logic
- [x] **U7 - 74AC02**: Quad NOR gates - address decoding logic
- [x] **U8 - 74HC30**: 8-input NAND gate - address decoding logic

---

## Phase 2: Assembly & Hardware Setup

**Goal**: Build the computer and get it working

### 2.1 Pre-Assembly Checklist
**Resources:**
- Documentation: https://picocomputer.github.io/hardware.html
- Assembly guide in hardware docs
- YouTube videos: https://youtube.com/playlist?list=PLvCRDUYedILfHDoD57Yj8BAXNmNJLVM2r

**Learning Objectives:**
- [x] Verify you have all ICs (check Active Parts List)
- [x] Review assembly instructions
- [x] Understand IC orientation (pin 1 indicators, notch/dot orientation)
- [x] Set up anti-static mat and wrist strap at bench and verify grounding (wrist strap should show 1-2M ohm to ground - this is correct, the resistor protects you from shock)
- [x] Tools: multimeters, logic analyzer, logic probes, chip tester, oscilloscope, power supply, LCR meter, USB power meter, microscope
  - [x] Chip Tester Pro V2 — [notes/tools/CHIP_TESTER.md](notes/tools/CHIP_TESTER.md)
  - [x] Extech EX350 multimeter — [notes/tools/MULTIMETER_EX350.md](notes/tools/MULTIMETER_EX350.md)
  - [x] FNIRSI DMT-99 multimeter — [notes/tools/MULTIMETER_DMT99.md](notes/tools/MULTIMETER_DMT99.md)
  - [x] FNIRSI DSO-TC3 (oscilloscope / component tester / signal generator) — [notes/tools/DSO_TC3.md](notes/tools/DSO_TC3.md)
  - [x] FNIRSI LCR-ST1 (LCR tweezer meter) — [notes/tools/LCR_ST1.md](notes/tools/LCR_ST1.md)
  - [x] FNIRSI DPS-150 (DC power supply) — [notes/tools/DPS_150.md](notes/tools/DPS_150.md)
  - [x] FNIRSI SG-003A (signal generator / process calibrator) — [notes/tools/SG_003A.md](notes/tools/SG_003A.md)
  - [x] DSLogic Plus (logic analyzer) — [notes/tools/DSLOGIC_PLUS.md](notes/tools/DSLOGIC_PLUS.md)
  - [x] Logic Probe LP-1 — [notes/tools/LOGIC_PROBE_LP1.md](notes/tools/LOGIC_PROBE_LP1.md)
  - [x] Logic Probe Model 610B — [notes/tools/LOGIC_PROBE_610B.md](notes/tools/LOGIC_PROBE_610B.md)
  - [x] USB Power Meter (Aideepen USB volt-ammeter) — [notes/tools/USB_POWER_METER.md](notes/tools/USB_POWER_METER.md)

### 2.1.1 Pre-IC Testing & Inspection (Pre-Soldered Board)
**Goal**: Test and inspect the board before inserting expensive ICs to catch any manufacturing defects

**Test all ICs with chip tester** (see [notes/tools/CHIP_TESTER.md](notes/tools/CHIP_TESTER.md)):
- [x] U1 — W65C02S (40-pin, Other Series/Mfr → WDC → W65C02)
- [x] U3 — AS6C1008 SRAM (32-pin, RAM → 32-pin Static → 628128)
- [x] U5 — W65C22S VIA (40-pin, Other Series/Mfr → WDC → W65C22)
- [x] U6 — 74AC00 Quad NAND (14-pin, 74HC CMOS → 7400)
- [x] U7 — 74AC02 Quad NOR (14-pin, 74HC CMOS → 7402)
- [x] U8 — 74HC30 8-input NAND (14-pin, 74HC CMOS → 7430)

**Visual Inspection:**
- [x] Check for solder bridges between adjacent pads/pins
- [x] Verify all passive components are installed (resistors, capacitors)
- [x] Check for cold solder joints (dull, grainy appearance)
- [x] Verify component orientation matches silkscreen markings
- [x] Check for missing components (compare to BOM)
- [x] Inspect IC sockets for proper installation and pin alignment
- [x] Verify connectors (VGA, audio, GPIO headers) are properly soldered
- [x] Check for any physical damage (cracks, lifted pads, scratches)

*How to do it (magnifier + microscope):* Use the **magnifier** first for a full-board pass: check that nothing is obviously missing, no big bridges, and connectors/sockets look straight. Then use the **microscope** on high-risk areas: **VGA and audio connector pins** (tight pitch, most likely bridges), **IC socket rows** (especially 40-pin U1/U5 and 32-pin U3), and any joint that looks dull or odd under the magnifier. Good joints are shiny and concave; cold joints are dull/grainy; bridges are blobs between two pins. For **BOM check**, use the assembly BOM (e.g. `picocomputer.github.io` repo → `docs/source/_static/rp6502-reva-assembly/bom.csv`): 10× 0.1µF caps (C1–C9, C11), 2× 47µF (C10, C12), 24 resistors (R1–R24), J1 (2×12), J2 (2×6), J3 VGA, J4 audio, REBOOT button, sockets U1, U3, U5 (40/32/40-pin), U6–U8 (14-pin), U2/U4 (20-pin header sockets). Match silkscreen designators and count; note polarity only where it matters (47µF caps, REBOOT button).

**Continuity Testing (Multimeter) - Essential Checks:**
- [x] **Power Rails Isolation**: Verify +3V3A and +3V3B rails are isolated from each other (no continuity between them)
- [x] **No Shorts - +3V3A**: Verify NO continuity between +3V3A and GND (with no power applied) — critical safety check
- [x] **No Shorts - +3V3B**: Verify NO continuity between +3V3B and GND (with no power applied) — critical safety check
- [x] **Ground Plane**: Verify all GND connections have continuity (check 2-3 GND points like connector shells, socket GND pins)

*Note: Detailed socket pin mapping and bus continuity checks can be deferred unless troubleshooting issues.*

**Resistance Checks:**
- [x] Verify resistor values match BOM (measure a few key ones):
  - R1, R6, R11, R18: 8.06 kΩ
  - R2, R7, R12: 4.02 kΩ
  - R3, R8, R13: 2 kΩ
  - R4, R9, R14: 1 kΩ
  - R5, R10, R15: 499 Ω
  - R16, R17: 47 Ω
  - R19, R22: 220 Ω
  - R20, R23: 100 Ω
  - R21, R24: 1.8 kΩ

**Capacitance Checks:**
- [x] Verify capacitor values match BOM (spot-check with capacitance meter):
  - C1–C9, C11: 0.1 µF (C1: ~570 nF confirms C1+C2+C3+C6+C7+C8; C4: ~190 nF confirms C4+C5; C9/C11: resistance confirms presence)
  - C10, C12: 47 µF (resistance confirms presence)
 
**Socket Inspection:**
- [x] Check socket orientation matches silkscreen (pin 1 indicators) — verified visually
- [x] Verify socket pins are straight and properly seated — verified during visual inspection

*Note: Detailed pin-by-pin continuity testing can be deferred unless troubleshooting issues. Factory-soldered boards typically have reliable socket connections.*

**Power Supply Testing (CAUTION - Only if safe):**
- [x] **Pre-check**: DO NOT apply power unless you already verified **no shorts** (+3V3A↔GND, +3V3B↔GND, +3V3A↔+3V3B) with the multimeter
- [x] **Set bench supply** (DPS-150): **3.3 V**, current limit **0.10 A** (100 mA) to start
- [x] **Power +3V3A rail (brief test)**:
  - [x] Connect supply **+** to **U1 socket pin 6** (+3V3A connection point)
  - [x] Connect supply **-** to **U1 socket pin 21** (GND connection point)
  - [x] Turn on supply and observe:
    - [x] Voltage on +3V3A is **3.3 V** (measured at U3 pin 32 and C4 GND)
    - [x] Supply current is **0 mA** (very low, normal for board with only passives — no shorts detected)
- [x] **Power +3V3B rail (brief test)**:
  - [x] Connect supply **+** to **C4 rail-side pad** (+3V3B connection point)
  - [x] Connect supply **-** to **U1 socket pin 21** (GND connection point)
  - [x] Turn on supply and observe:
    - [x] Voltage on +3V3B is **3.3 V** (measured at U3 pins 32 and 16)
    - [x] Supply current is **0 mA** (very low, normal for board with only passives — no shorts detected)
- [x] **Stop immediately** if you see: current hitting the limit / rapidly rising current, voltage collapsing, smoke/odor, or anything heating up

### 2.2 First Step: VGA Pico and Display Check (Done)
**What I did first:** Loaded the VGA Pico firmware, installed the VGA Pico in its socket (U4), connected the board's VGA output to the 9.7" portable display via a VGA-to-HDMI adapter, and powered the VGA Pico. A blinking cursor appeared in the top-left corner of the screen, as expected (as shown in the author's video).

**Resources:**
- Firmware releases: https://github.com/picocomputer/rp6502/releases
- Documentation: https://picocomputer.github.io/hardware.html#step-5-pi-pico-firmware

**Learning Objectives:**
- [x] Download latest VGA firmware (.uf2 file)
- [x] Load VGA firmware on Pico 2 (hold BOOTSEL → connect USB → copy .uf2 to mounted drive → wait for LED)
- [x] Install Pico 2 (VGA) in U4 socket (note orientation)
- [x] Connect VGA output to display via VGA-to-HDMI adapter (e.g. 9.7" portable Mini-HDMI display; power adapter from USB if needed)
- [x] Power the VGA Pico; observe blinking cursor in top-left corner (as in author's video)

*Next steps (assembly of remaining ICs, RIA Pico, RIA firmware, etc.) to be added later.*

### 2.3 Assembly Steps (Pre-Soldered Board) — Next Steps
**Note**: For pre-soldered boards, passive components and connectors are already installed. Only ICs need to be inserted.

**Learning Objectives:**
- [ ] **Install logic chips** (U6, U7, U8) in sockets:
  - [ ] U6 - 74AC00 Quad NAND (14-pin, note orientation)
  - [ ] U7 - 74AC02 Quad NOR (14-pin, note orientation)
  - [ ] U8 - 74HC30 8-input NAND (14-pin, note orientation)
- [ ] **Install main ICs** in sockets:
  - [ ] U1 - W65C02S (40-pin, note orientation - pin 1 indicator)
  - [ ] U3 - AS6C1008 SRAM (32-pin, note orientation)
  - [ ] U5 - W65C22S VIA (40-pin, note orientation)
- [ ] **Install Raspberry Pi Pico modules** (VGA Pico already in U4; RIA remaining):
  - [ ] U2 - Pico 2 W (RIA) - goes in socket (note orientation)
- [ ] Double-check all IC orientations (pin 1 matches socket pin 1)
- [ ] Verify all ICs are fully seated in sockets

### 2.4 Firmware Installation — Remaining
**Learning Objectives:**
- [ ] Download latest RIA firmware (.uf2 file)
- [ ] Load RIA firmware:
  - [ ] Hold BOOTSEL button on Pico 2 W
  - [ ] Connect USB to computer
  - [ ] Copy RIA .uf2 file to mounted drive
  - [ ] Wait for LED to turn on

---

## Phase 3: Testing & Verification

**Goal**: Verify the computer works correctly

### 3.1 Initial Power-On Test
**Learning Objectives:**
- [ ] Connect power to RIA (via USB)
- [ ] Check LED indicators (RIA and VGA should have LEDs)
- [ ] Verify no smoke or overheating
- [ ] Check that both Picos are running (LEDs on)

### 3.2 Basic Functionality Tests
**Learning Objectives:**
- [ ] **Console Access**: Connect via one of:
  - [ ] USB CDC (serial) - if VGA connected
  - [ ] UART pins on RIA (115200 8N1)
  - [ ] VGA monitor + USB keyboard
- [ ] **Monitor Boot**: Should see RP6502 monitor prompt
- [ ] **VGA Test**: If VGA installed, should see console on monitor
- [ ] **Keyboard Test**: Type in monitor, should see characters
- [ ] **Basic Commands**: Try `help` command in monitor

### 3.3 Troubleshooting
**Common Issues:**
- [ ] No console output - check connections, firmware
- [ ] VGA not working - check PIX bus connections, VGA firmware
- [ ] Monitor not responding - check reset, try reboot button
- [ ] USB not working - check USB cable, try different port

**Resources:**
- Forums: https://github.com/picocomputer/community/discussions
- Discord: https://discord.gg/TC6X8kTr6d

---

## Phase 4: Deep Dive into Chip Interactions

**Goal**: Understand how chips communicate and work together

### 4.1 6502 Bus Architecture
**Resources:**
- Schematic: `/Users/nenaddjordjevic/CProjects/schematic/rp6502.kicad_sch`
- W65C02S datasheet

**Learning Objectives:**
- [ ] Understand 6502 bus signals:
  - [ ] Address bus (A0-A15): 16 bits, 64KB address space
  - [ ] Data bus (D0-D7): 8 bits, bidirectional
  - [ ] RWB: Read/Write Bar (high=read, low=write)
  - [ ] PHI2: System clock (generated by RIA)
  - [ ] RESB: Reset (controlled by RIA)
  - [ ] IRQB, NMIB: Interrupt signals
- [ ] Learn address decoding:
  - [ ] How 74AC00, 74AC02, 74HC30 decode addresses
  - [ ] Memory map implementation
  - [ ] Chip select signals

### 4.2 RIA Bus Interface
**Resources:**
- `/Users/nenaddjordjevic/CProjects/rp6502/src/ria/sys/ria.c`
- `/Users/nenaddjordjevic/CProjects/rp6502/src/ria/ria.pio`
- Documentation: https://picocomputer.github.io/ria.html

**Learning Objectives:**
- [ ] Understand how RIA interfaces with 6502 bus
- [ ] Study PIO program for bus interface
- [ ] Learn clock generation (PHI2)
- [ ] Understand reset control (RESB)
- [ ] Study address decoding in software
- [ ] Learn memory access patterns

### 4.3 Memory System
**Learning Objectives:**
- [ ] **System RAM (U3)**: How SRAM is accessed
  - [ ] Write enable (WE#) generation
  - [ ] Chip enable (CE#) logic
  - [ ] Output enable (OE#) control
- [ ] **Extended RAM (XRAM)**: 64KB managed by RIA
  - [ ] Access via RIA registers (RW0/RW1)
  - [ ] XRAM vs System RAM differences
  - [ ] When to use each
- [ ] **Memory Map Details**:
  - [ ] $0000-$FEFF: System RAM
  - [ ] $FFD0-$FFDF: VIA registers
  - [ ] $FFE0-$FFFF: RIA registers
  - [ ] $10000-$1FFFF: XRAM (virtual)

### 4.4 PIX Bus Communication
**Resources:**
- `/Users/nenaddjordjevic/CProjects/rp6502/src/ria/sys/pix.c`
- `/Users/nenaddjordjevic/CProjects/rp6502/src/vga/sys/pix.c`
- Documentation: https://picocomputer.github.io/ria.html#pico-information-exchange-pix

**Learning Objectives:**
- [ ] Understand PIX bus physical layer:
  - [ ] 5 wires: PHI2 + PIX0-3
  - [ ] Double data rate (data on both clock edges)
  - [ ] 32-bit frame format
- [ ] Learn frame structure:
  - [ ] Device ID (bits 31-29)
  - [ ] Channel ID (bits 27-24)
  - [ ] Register address (bits 23-16)
  - [ ] Data value (bits 15-0)
- [ ] Study XRAM broadcasting
- [ ] Understand XREG configuration

### 4.5 Power Distribution
**Learning Objectives:**
- [ ] Understand +3V3A and +3V3B rails
- [ ] Learn decoupling capacitor placement
- [ ] Study power sequencing
- [ ] Understand current requirements

---

## Phase 5: RIA Firmware Deep Dive

**Goal**: Understand how the RIA firmware works

### 5.1 RIA Core Architecture
**Resources:**
- `/Users/nenaddjordjevic/CProjects/rp6502/src/ria/`
- Documentation: https://picocomputer.github.io/ria.html

**Learning Objectives:**
- [ ] Understand RIA initialization sequence (`main.c`)
- [ ] Study bus interface implementation (`sys/ria.c`, `ria.pio`)
- [ ] Learn CPU clock control (`sys/cpu.c`)
- [ ] Understand memory management (`sys/mem.c`)
- [ ] Study PIX bus implementation (`sys/pix.c`, `sys/pix.h`)

**Key Files to Study:**
```
src/ria/main.c          - Main initialization and task loop
src/ria/sys/ria.c       - RIA bus interface
src/ria/sys/cpu.c       - CPU clock and control
src/ria/sys/mem.c       - Memory management
src/ria/sys/pix.c       - PIX bus communication
src/ria/ria.pio         - PIO program for bus interface
```

### 5.2 RIA Registers & Memory Map
**Resources:**
- Documentation: https://picocomputer.github.io/ria.html#registers

**Learning Objectives:**
- [ ] Master RIA register map ($FFE0-$FFFF)
  - [ ] UART registers ($FFE0-$FFE2)
  - [ ] XRAM access ($FFE4-$FFEB)
  - [ ] OS call mechanism ($FFEC-$FFF9)
  - [ ] Interrupt vectors ($FFFA-$FFFF)
- [ ] Understand Extended RAM (XRAM) - 64KB at $10000-$1FFFF
- [ ] Learn Extended Stack (XSTACK) - 512 bytes for OS calls
- [ ] Study Extended Registers (XREG) - virtual hardware configuration

**Practice:**
- Write simple programs that read/write RIA registers
- Experiment with XRAM access patterns
- Try basic OS calls

### 5.3 RIA Features & Services
**Resources:**
- `/Users/nenaddjordjevic/CProjects/rp6502/src/ria/` subdirectories

**Learning Objectives:**
- [ ] **USB HID** (`hid/`): Keyboard, mouse, gamepad support
- [ ] **Audio** (`aud/`): PSG and OPL2 FM synthesis
- [ ] **Networking** (`net/`): WiFi, Bluetooth, modem emulation
- [ ] **Storage** (`usb/msc.c`): USB Mass Storage, FAT32 filesystem
- [ ] **Monitor** (`mon/`): Boot monitor and ROM loader
- [ ] **API** (`api/`): OS call implementations

**Key Files:**
```
src/ria/hid/kbd.c       - Keyboard driver
src/ria/hid/mou.c       - Mouse driver
src/ria/hid/pad.c       - Gamepad driver
src/ria/aud/psg.c       - Programmable Sound Generator
src/ria/aud/opl.c       - OPL2 FM synthesis
src/ria/net/wfi.c       - WiFi interface
src/ria/net/ble.c       - Bluetooth LE
src/ria/usb/msc.c       - USB Mass Storage
src/ria/api/*.c         - OS API implementations
```

---

## Phase 6: VGA Firmware Deep Dive

### 6.1 VGA Core Architecture
**Resources:**
- `/Users/nenaddjordjevic/CProjects/rp6502/src/vga/`
- Documentation: https://picocomputer.github.io/vga.html

**Learning Objectives:**
- [ ] Understand VGA initialization (`main.c`)
- [ ] Study scanvideo library integration (`scanvideo/`)
- [ ] Learn PIX bus client implementation (`sys/ria.c`, `sys/pix.c`)
- [ ] Understand video mode system (`modes/`)
- [ ] Study terminal implementation (`term/`)

**Key Files:**
```
src/vga/main.c              - VGA main loop
src/vga/scanvideo/          - Scanvideo library
src/vga/sys/pix.c           - PIX bus client
src/vga/modes/mode*.c       - Video modes
src/vga/term/term.c         - ANSI terminal
```

### 6.2 Video Modes
**Resources:**
- Documentation: https://picocomputer.github.io/vga.html#video-programming

**Learning Objectives:**
- [ ] **Mode 0**: Console mode (80-column text)
- [ ] **Mode 1**: Character mode (text with color per character)
- [ ] **Mode 2**: Tile mode (tile-based graphics)
- [ ] **Mode 3**: Bitmap mode (pixel-perfect graphics)
- [ ] **Mode 4**: Sprite mode (hardware sprites)
- [ ] Understand canvas selection (320x240, 640x480, etc.)
- [ ] Learn plane system (3 fill planes + 3 sprite planes)

**Practice:**
- Study example programs in `/Users/nenaddjordjevic/CProjects/examples/src/`
- Create simple graphics programs
- Experiment with different video modes

### 6.3 PIX Bus Communication (Advanced)
**Learning Objectives:**
- [ ] Understand PIX frame format in detail (32 bits, device/channel/address/value)
- [ ] Learn XRAM broadcasting mechanism
- [ ] Study XREG configuration protocol
- [ ] Understand backchannel communication

---

## Phase 7: RP6502-OS & Application Programming

### 7.1 Operating System API
**Resources:**
- Documentation: https://picocomputer.github.io/os.html
- `/Users/nenaddjordjevic/CProjects/rp6502/src/ria/api/`

**Learning Objectives:**
- [ ] Understand ABI (Application Binary Interface)
  - Fastcall convention
  - XSTACK usage
  - Register passing (A, X, AX, AXSREG)
- [ ] Learn OS call mechanism
  - Setting RIA_OP
  - Polling RIA_BUSY
  - Using RIA_SPIN for blocking calls
- [ ] Study error handling (RIA_ERRNO)

**Key OS Calls to Master:**
- File I/O: `open()`, `read()`, `write()`, `close()`, `lseek()`
- Directory: `opendir()`, `readdir()`, `closedir()`
- Extended registers: `xreg()` for configuring hardware
- Time: `clock()`, `clock_gettime()`, `tzset()`
- Random: `lrand()`

### 7.2 6502 Software Development
**Resources:**
- `/Users/nenaddjordjevic/CProjects/examples/` - Example programs
- `/Users/nenaddjordjevic/CProjects/vscode-cc65/` - CC65 development setup
- `/Users/nenaddjordjevic/CProjects/vscode-llvm-mos/` - LLVM-MOS setup
- `/Users/nenaddjordjevic/CProjects/ehbasic/` - Enhanced BASIC

**Learning Objectives:**
- [ ] Set up development environment (CC65 or LLVM-MOS)
- [ ] Understand memory map usage
- [ ] Learn to use RIA registers from C/assembly
- [ ] Study example programs
- [ ] Create simple applications

**Key Example Files:**
```
examples/src/mode1.c      - Character mode example
examples/src/mode2.c      - Tile mode example
examples/src/mode3.c      - Bitmap mode example
examples/src/paint.c      - Drawing program
examples/src/gamepad.c    - Gamepad input
examples/src/rtc.c        - Real-time clock
```

### 7.3 Programming Patterns
**Learning Objectives:**
- [ ] XRAM access patterns (RW0/RW1 with STEP)
- [ ] Video mode programming
- [ ] Input handling (keyboard, mouse, gamepad)
- [ ] Audio programming (PSG, OPL2)
- [ ] File I/O and storage
- [ ] Interrupt handling (VSYNC interrupts)

---

## Phase 8: Advanced Topics

### 8.1 PIO Programming
**Resources:**
- `/Users/nenaddjordjevic/CProjects/rp6502/src/ria/ria.pio`
- `/Users/nenaddjordjevic/CProjects/rp6502/src/vga/vga.pio`
- Pico SDK PIO documentation

**Learning Objectives:**
- [ ] Understand PIO state machines
- [ ] Study RIA PIO program (bus interface)
- [ ] Study VGA PIO program (video generation)
- [ ] Learn to write custom PIO programs

### 8.2 Networking & Communication
**Resources:**
- `/Users/nenaddjordjevic/CProjects/rp6502/src/ria/net/`

**Learning Objectives:**
- [ ] WiFi configuration and usage
- [ ] Bluetooth LE setup
- [ ] Hayes modem emulation
- [ ] NTP time synchronization

### 8.3 Audio Systems
**Resources:**
- `/Users/nenaddjordjevic/CProjects/rp6502/src/ria/aud/`
- `/Users/nenaddjordjevic/CProjects/rp6502/src/emu8950/`

**Learning Objectives:**
- [ ] PSG (Programmable Sound Generator) programming
- [ ] OPL2 FM synthesis
- [ ] Audio mixing and effects

### 8.4 Custom Hardware Development
**Learning Objectives:**
- [ ] Design custom PIX bus devices
- [ ] Create new video modes
- [ ] Add custom OS calls
- [ ] Hardware expansion via VIA

---

## Recommended Learning Path

### Phase 0: Understanding Workspace Projects (30 minutes - 1 hour)
1. Read `notes/WORKSPACE_PROJECTS.md` to understand all 12 projects
2. Identify which projects are core RP6502 vs reference materials
3. Understand when to use each project during learning
4. Familiarize yourself with project locations and purposes

### Phase 1: High-Level Overview (1-2 days)
1. Study the schematic and identify all chips
2. Understand what each chip does at a high level
3. Review the memory map
4. Read hardware documentation overview

### Phase 2: Assembly (1-2 days)
1. Prepare workspace and verify all parts
2. Assemble the computer following instructions
3. Install firmware on both Pico modules
4. Double-check all connections

### Phase 3: Testing (1 day)
1. Power on and verify basic functionality
2. Access console/monitor
3. Test basic commands
4. Troubleshoot any issues

### Phase 4: Deep Dive - Chip Interactions (1-2 weeks)
1. Study 6502 bus architecture in detail
2. Understand RIA bus interface
3. Learn memory system (System RAM vs XRAM)
4. Study PIX bus communication
5. Understand power distribution

### Phase 5: RIA Firmware (1-2 weeks)
1. Study RIA source code structure
2. Understand bus interface implementation
3. Learn OS call mechanism
4. Experiment with RIA features

### Phase 6: VGA Firmware (1 week)
1. Study VGA source code
2. Understand video modes
3. Learn PIX bus communication details
4. Create simple graphics programs

### Phase 7: Application Development (2+ weeks)
1. Set up development environment
2. Study example programs
3. Write simple applications
4. Experiment with different features

### Phase 8: Advanced Topics (ongoing)
1. Deep dive into specific areas of interest
2. Custom hardware/software development
3. Optimization and performance tuning
4. Contributing to the project

---

## Key Resources

### Documentation
- Main docs: https://picocomputer.github.io/
- Hardware: https://picocomputer.github.io/hardware.html
- RIA: https://picocomputer.github.io/ria.html
- VGA: https://picocomputer.github.io/vga.html
- OS: https://picocomputer.github.io/os.html

### Code Repositories
- Firmware: `/Users/nenaddjordjevic/CProjects/rp6502/`
- Examples: `/Users/nenaddjordjevic/CProjects/examples/`
- Development templates: `/Users/nenaddjordjevic/CProjects/vscode-cc65/`, `/Users/nenaddjordjevic/CProjects/vscode-llvm-mos/`

### Community
- GitHub: https://github.com/picocomputer
- Forums: https://github.com/picocomputer/community/discussions
- Discord: https://discord.gg/TC6X8kTr6d
- YouTube: https://youtube.com/playlist?list=PLvCRDUYedILfHDoD57Yj8BAXNmNJLVM2r

### Reference Materials
- Pico SDK: `/Users/nenaddjordjevic/CProjects/pico-sdk/`
- Pico Examples: `/Users/nenaddjordjevic/CProjects/pico-examples/`
- Pico Extras: `/Users/nenaddjordjevic/CProjects/pico-extras/`

---

## Practice Projects

### Beginner
1. **Hello World**: Display text on VGA console
2. **Blink LED**: Control RIA LED from 6502
3. **Keyboard Input**: Read and display keyboard input
4. **File Reader**: Read and display a text file

### Intermediate
1. **Simple Game**: Create a basic game using tile mode
2. **Audio Player**: Play sounds using PSG
3. **Image Viewer**: Display bitmap images
4. **Terminal Program**: Create a simple terminal application

### Advanced
1. **Custom Video Mode**: Implement a new video mode
2. **Network Application**: Create a network-enabled program
3. **Audio Synthesizer**: Build a music synthesizer
4. **Operating System**: Create a simple OS for the 6502

---

## Tips for Learning

1. **Start with the schematic**: Understanding the hardware is crucial
2. **Read the source code**: The firmware is well-organized and documented
3. **Experiment**: Try modifying example programs
4. **Use the monitor**: The RP6502 monitor is a great debugging tool
5. **Join the community**: The forums and Discord are very helpful
6. **Watch the videos**: The YouTube series shows the development process
7. **Study examples**: The example programs demonstrate best practices

---

## Next Steps

1. **Start with Phase 0** - Understanding workspace projects
   - Read `notes/WORKSPACE_PROJECTS.md` to get familiar with all projects
   - Understand what each project contains and when to use it
   - This will help you navigate the workspace effectively

2. **Move to Phase 1** - High-level chip overview
   - Study the schematic and identify all 8 chips
   - Understand what each chip does (don't worry about details yet)
   - Review the memory map at a high level
   
3. **Move to Phase 2** - Assembly
   - Verify you have all parts
   - Follow assembly instructions carefully
   - Install firmware on both Pico modules
   
4. **Phase 3** - Testing
   - Power on and verify it works
   - Access the monitor/console
   - Test basic functionality
   
5. **Phase 4+** - Deep dive
   - Once the computer is working, dive into details
   - Study chip interactions
   - Learn firmware internals
   - Start programming

6. **Get help when needed**
   - Ask questions in forums or Discord
   - Review documentation
   - Study example code

Good luck with your RP6502 learning journey! 🚀
