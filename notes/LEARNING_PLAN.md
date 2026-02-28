# RP6502 Learning Plan

## Overview
The RP6502 (Picocomputer 6502) is a modern single-board computer built around the classic WDC W65C02S 8-bit microprocessor, enhanced with two Raspberry Pi Pico 2 modules acting as intelligent coprocessors. This plan guides you through understanding the system from hardware to software.

---

> **Unified Strategy:** The RP6502 emulator project (`rp6502-emulator/notes/IMPLEMENTATION.md`) is the primary hands-on learning vehicle for Phases 4–8. Each emulator milestone requires studying the relevant firmware and documentation first, then implementing and testing — which proves understanding. This plan serves as the **reference checklist** of what to understand; the emulator milestones define **how you learn it** through building. Hardware assembly (Phases 2–3) runs as a parallel track whenever you're at the bench.

---

## Phase 0: Understanding Projects in RP6502 Workspace

**Goal**: Get familiar with the workspace structure and understand what each project contains

**Resources:**
- Detailed guide: `notes/WORKSPACE_PROJECTS.md`

**Workspace Projects Checklist:**
- [✔] **rp6502/** - Main firmware source code (RIA and VGA)
- [✔] **examples/** - Example 6502 programs to learn from
- [✔] **schematic/** - Hardware design files (KiCad)
- [✔] **picocomputer.github.io/** - Official RP6502 documentation source
- [✔] **vscode-cc65/** - CC65 development template
- [✔] **vscode-llvm-mos/** - LLVM-MOS development template
- [✔] **ehbasic/** - Enhanced 6502 BASIC interpreter
- [✔] **llvm-mos-sdk/** - LLVM-MOS compiler and SDK
- [✔] **cc65/** - CC65 C/assembly compiler and libraries
- [✔] **pico-sdk/** - Official Raspberry Pi Pico SDK
- [✔] **pico-examples/** - Official Pico example programs
- [✔] **pico-extras/** - Additional Pico libraries (scanvideo, audio)
- [✔] **Hunter-Adams-RP2040-Demos/** - Educational Pico demos
- [✔] **documentation/** - Raspberry Pi documentation source

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
- [✔] **U1 - W65C02S**: Main 8-bit CPU - executes your programs
- [✔] **U2 - RP6502-RIA (Pico 2 W)**: Interface adapter - controls CPU, provides modern I/O
- [✔] **U3 - AS6C1008**: 128KB SRAM - main system memory
- [✔] **U4 - RP6502-VGA (Pico 2)**: Video adapter - generates VGA/HD output (optional)
- [✔] **U5 - W65C22S**: VIA chip - classic 6502 I/O chip (GPIO, timers)
- [✔] **U6 - 74AC00**: Quad NAND gates - address decoding logic
- [✔] **U7 - 74AC02**: Quad NOR gates - address decoding logic
- [✔] **U8 - 74HC30**: 8-input NAND gate - address decoding logic

---

## Phase 2: Assembly & Hardware Setup

**Goal**: Build the computer and get it working

### 2.1 Pre-Assembly Checklist
**Resources:**
- Documentation: https://picocomputer.github.io/hardware.html
- Assembly guide in hardware docs
- YouTube videos: https://youtube.com/playlist?list=PLvCRDUYedILfHDoD57Yj8BAXNmNJLVM2r

**Learning Objectives:**
- [✔] Verify you have all ICs (check Active Parts List)
- [✔] Review assembly instructions
- [✔] Understand IC orientation (pin 1 indicators, notch/dot orientation)
- [✔] Set up anti-static mat and wrist strap at bench and verify grounding (wrist strap should show 1-2M ohm to ground - this is correct, the resistor protects you from shock)
- [✔] Tools: multimeters, logic analyzer, logic probes, chip tester, oscilloscope, power supply, LCR meter, USB power meter, microscope
  - [✔] Chip Tester Pro V2 — [notes/tools/CHIP_TESTER.md](notes/tools/CHIP_TESTER.md)
  - [✔] Extech EX350 multimeter — [notes/tools/MULTIMETER_EX350.md](notes/tools/MULTIMETER_EX350.md)
  - [✔] FNIRSI DMT-99 multimeter — [notes/tools/MULTIMETER_DMT99.md](notes/tools/MULTIMETER_DMT99.md)
  - [✔] FNIRSI DSO-TC3 (oscilloscope / component tester / signal generator) — [notes/tools/DSO_TC3.md](notes/tools/DSO_TC3.md)
  - [✔] FNIRSI LCR-ST1 (LCR tweezer meter) — [notes/tools/LCR_ST1.md](notes/tools/LCR_ST1.md)
  - [✔] FNIRSI DPS-150 (DC power supply) — [notes/tools/DPS_150.md](notes/tools/DPS_150.md)
  - [✔] FNIRSI SG-003A (signal generator / process calibrator) — [notes/tools/SG_003A.md](notes/tools/SG_003A.md)
  - [✔] DSLogic Plus (logic analyzer) — [notes/tools/DSLOGIC_PLUS.md](notes/tools/DSLOGIC_PLUS.md)
  - [✔] Logic Probe LP-1 — [notes/tools/LOGIC_PROBE_LP1.md](notes/tools/LOGIC_PROBE_LP1.md)
  - [✔] Logic Probe Model 610B — [notes/tools/LOGIC_PROBE_610B.md](notes/tools/LOGIC_PROBE_610B.md)
  - [✔] USB Power Meter (Aideepen USB volt-ammeter) — [notes/tools/USB_POWER_METER.md](notes/tools/USB_POWER_METER.md)
  - [✔] Raspberry Pi Debug Probe (SWD + UART for Pico/RP2040) — [notes/tools/RASPBERRY_PI_DEBUG_PROBE.md](notes/tools/RASPBERRY_PI_DEBUG_PROBE.md)

### 2.1.1 Pre-IC Testing & Inspection (Pre-Soldered Board)
**Goal**: Test and inspect the board before inserting expensive ICs to catch any manufacturing defects

**Test all ICs with chip tester** (see [notes/tools/CHIP_TESTER.md](notes/tools/CHIP_TESTER.md)):
- [✔] U1 — W65C02S (40-pin, Other Series/Mfr → WDC → W65C02)
- [✔] U3 — AS6C1008 SRAM (32-pin, RAM → 32-pin Static → 628128)
- [✔] U5 — W65C22S VIA (40-pin, Other Series/Mfr → WDC → W65C22)
- [✔] U6 — 74AC00 Quad NAND (14-pin, 74HC CMOS → 7400)
- [✔] U7 — 74AC02 Quad NOR (14-pin, 74HC CMOS → 7402)
- [✔] U8 — 74HC30 8-input NAND (14-pin, 74HC CMOS → 7430)

**Visual Inspection:**
- [✔] Check for solder bridges between adjacent pads/pins
- [✔] Verify all passive components are installed (resistors, capacitors)
- [✔] Check for cold solder joints (dull, grainy appearance)
- [✔] Verify component orientation matches silkscreen markings
- [✔] Check for missing components (compare to BOM)
- [✔] Inspect IC sockets for proper installation and pin alignment
- [✔] Verify connectors (VGA, audio, GPIO headers) are properly soldered
- [✔] Check for any physical damage (cracks, lifted pads, scratches)

*How to do it (magnifier + microscope):* Use the **magnifier** first for a full-board pass: check that nothing is obviously missing, no big bridges, and connectors/sockets look straight. Then use the **microscope** on high-risk areas: **VGA and audio connector pins** (tight pitch, most likely bridges), **IC socket rows** (especially 40-pin U1/U5 and 32-pin U3), and any joint that looks dull or odd under the magnifier. Good joints are shiny and concave; cold joints are dull/grainy; bridges are blobs between two pins. For **BOM check**, use the assembly BOM (e.g. `picocomputer.github.io` repo → `docs/source/_static/rp6502-reva-assembly/bom.csv`): 10× 0.1µF caps (C1–C9, C11), 2× 47µF (C10, C12), 24 resistors (R1–R24), J1 (2×12), J2 (2×6), J3 VGA, J4 audio, REBOOT button, sockets U1, U3, U5 (40/32/40-pin), U6–U8 (14-pin), U2/U4 (20-pin header sockets). Match silkscreen designators and count; note polarity only where it matters (47µF caps, REBOOT button).

**Continuity Testing (Multimeter) - Essential Checks:**
- [✔] **Power Rails Isolation**: Verify +3V3A and +3V3B rails are isolated from each other (no continuity between them)
- [✔] **No Shorts - +3V3A**: Verify NO continuity between +3V3A and GND (with no power applied) — critical safety check
- [✔] **No Shorts - +3V3B**: Verify NO continuity between +3V3B and GND (with no power applied) — critical safety check
- [✔] **Ground Plane**: Verify all GND connections have continuity (check 2-3 GND points like connector shells, socket GND pins)

*Note: Detailed socket pin mapping and bus continuity checks can be deferred unless troubleshooting issues.*

**Resistance Checks:**
- [✔] Verify resistor values match BOM (measure a few key ones):
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
- [✔] Verify capacitor values match BOM (spot-check with capacitance meter):
  - C1–C9, C11: 0.1 µF (C1: ~570 nF confirms C1+C2+C3+C6+C7+C8; C4: ~190 nF confirms C4+C5; C9/C11: resistance confirms presence)
  - C10, C12: 47 µF (resistance confirms presence)
 
**Socket Inspection:**
- [✔] Check socket orientation matches silkscreen (pin 1 indicators) — verified visually
- [✔] Verify socket pins are straight and properly seated — verified during visual inspection

*Note: Detailed pin-by-pin continuity testing can be deferred unless troubleshooting issues. Factory-soldered boards typically have reliable socket connections.*

**Power Supply Testing (CAUTION - Only if safe):**
- [✔] **Pre-check**: DO NOT apply power unless you already verified **no shorts** (+3V3A↔GND, +3V3B↔GND, +3V3A↔+3V3B) with the multimeter
- [✔] **Set bench supply** (DPS-150): **3.3 V**, current limit **0.10 A** (100 mA) to start
- [✔] **Power +3V3A rail (brief test)**:
  - [✔] Connect supply **+** to **U1 socket pin 6** (+3V3A connection point)
  - [✔] Connect supply **-** to **U1 socket pin 21** (GND connection point)
  - [✔] Turn on supply and observe:
    - [✔] Voltage on +3V3A is **3.3 V** (measured at U3 pin 32 and C4 GND)
    - [✔] Supply current is **0 mA** (very low, normal for board with only passives — no shorts detected)
- [✔] **Power +3V3B rail (brief test)**:
  - [✔] Connect supply **+** to **C4 rail-side pad** (+3V3B connection point)
  - [✔] Connect supply **-** to **U1 socket pin 21** (GND connection point)
  - [✔] Turn on supply and observe:
    - [✔] Voltage on +3V3B is **3.3 V** (measured at U3 pins 32 and 16)
    - [✔] Supply current is **0 mA** (very low, normal for board with only passives — no shorts detected)
- [✔] **Stop immediately** if you see: current hitting the limit / rapidly rising current, voltage collapsing, smoke/odor, or anything heating up

### 2.2 First Step: VGA Pico and Display Check
**Goal:** Load VGA firmware on a Pico 2, install it in the socket (U4), connect the VGA output to the display via a VGA-to-HDMI adapter, and power the VGA Pico. A blinking cursor should appear in the top-left corner of the screen, as expected (as shown in the author's video).

**Resources:**
- Firmware releases: https://github.com/picocomputer/rp6502/releases
- Documentation: https://picocomputer.github.io/hardware.html#step-5-pi-pico-firmware

**Learning Objectives:**
- [✔] Download latest VGA firmware (.uf2 file)
- [✔] Load VGA firmware on Pico 2 (hold BOOTSEL → connect USB → copy .uf2 to mounted drive → wait for LED)
- [✔] Install Pico 2 (VGA) in U4 socket (note orientation)
- [✔] Connect VGA output to display via VGA-to-HDMI adapter (e.g. 9.7" portable Mini-HDMI display; power adapter from USB if needed)
- [✔] Power the VGA Pico; observe blinking cursor in top-left corner (as in author's video)

### 2.3 Second Step: RIA Pico, Keyboard, Display, and HELP Check
**Goal:** Load RIA firmware on a second Pi Pico (standalone), connect a keyboard, power it up, and verify that typing and running the **HELP** command works.

**Resources:**
- Firmware releases: https://github.com/picocomputer/rp6502/releases (RIA .uf2 for Pico 2 W)
- Documentation: https://picocomputer.github.io/hardware.html#step-5-pi-pico-firmware, https://picocomputer.github.io/ria.html

**Learning Objectives:**
- [✔] Download latest RIA firmware (.uf2 file) from the releases page
- [✔] Load RIA firmware on a second Pico 2 W (hold BOOTSEL → connect USB → copy RIA .uf2 to mounted drive → wait for LED)
- [✔] Connect the RIA Pico to the computer via USB (power + serial console); or power via USB and connect a USB keyboard using a Micro USB to USB-A OTG adapter
- [✔] Power up the RIA Pico; type **HELP** and press Enter; verify that the HELP command runs and displays help output

*Once this works, the same RIA Pico can later be installed in the board socket (U2); keyboard and serial behavior apply there too (with VGA for display).*

### 2.4 Assembly Steps (Pre-Soldered Board) — Next Steps
**Goal:** Install the logic chips (U6, U7, U8) in sockets, install the main ICs (U1, U3, U5) in sockets, and double-check all IC orientations.

**Learning Objectives:**
- [✔] **Install logic chips** (U6, U7, U8) in sockets:
  - [✔] U6 - 74AC00 Quad NAND (14-pin, note orientation)
  - [✔] U7 - 74AC02 Quad NOR (14-pin, note orientation)
  - [✔] U8 - 74HC30 8-input NAND (14-pin, note orientation)
- [✔] **Install main ICs** in sockets:
  - [✔] U1 - W65C02S (40-pin, note orientation - pin 1 indicator)
  - [✔] U3 - AS6C1008 SRAM (32-pin, note orientation)
  - [✔] U5 - W65C22S VIA (40-pin, note orientation)
- [✔] Double-check all IC orientations (pin 1 matches socket pin 1)
- [✔] Verify all ICs are fully seated in sockets

---

## Phase 3: Testing & Verification

**Goal**: Verify the computer works correctly

### 3.1 Initial Power-On Test (Done)
**What I did:** Connected power to the **VGA Pico** via the Pico power switch (CanaKit PiSwitch); connected the **Raspberry Pi keyboard with built-in hub** to the **RIA Pico**; connected the board’s VGA output to the **9.7" portable screen** via the VGA-to-HDMI adapter. Everything works as expected.

**Learning Objectives:**
- [✔] Connect power (e.g. to VGA Pico via USB and power switch, or to RIA via USB)
- [✔] Check LED indicators (RIA and VGA should have LEDs)
- [✔] Verify no smoke or overheating
- [✔] Check that both Picos are running (LEDs on)

**Quirk (power via switch):** If `status` doesn’t show VGA on cold power-up (screen and keyboard work; one reboot fixes it), see **3.3 Troubleshooting**.

### 3.2 Basic Functionality Tests
**Learning Objectives:**
- [✔] **Console Access**: VGA monitor + USB keyboard (tested for now)
- [✔] **Monitor Boot**: Should see RP6502 monitor prompt
- [✔] **VGA Test**: If VGA installed, should see console on monitor
- [✔] **Keyboard Test**: Type in monitor, should see characters
- [✔] **Basic Commands**: Try `help` command in monitor

### 3.3 Troubleshooting
**Common Issues:**
- [✔] **`status` doesn’t show VGA when powering with or without the switch** — On cold power-up, `status` omits the VGA Pico even though the screen and keyboard work; one reboot (CTRL-ALT-DEL or power cycle) fixes it. **Cause (author):** Noise on the UART backchannel; he suspects something changed (e.g. power supply or board sourcing). **Fix (v0.17):** In `vga_connect()` in `rp6502/src/ria/sys/vga.c`: `busy_wait_ms(5)`. Build from source: see SETUP_NOTES.md.

---

## Phase 3.4: Hands-On Exploration (Computer Working)

**Goal**: Once the hardware is fully functional — explore it as a user, run existing programs, set up a development environment, write simple apps, build intuition for how the system works, and explore how to extend the computer with custom hardware (add-on boards or circuits) before diving into firmware internals.

**Prerequisite**: All ICs installed, RIA + VGA firmware loaded, console access working (USB-C adapter arrived, monitor prompt visible).

> **Why this phase?** Phases 4–8 go deep into firmware and chip internals. But first you should *use* the computer — see what it does, what programs exist, how development works. This builds context that makes the deep dives far more productive. Think of it as "user mode" before "kernel mode."

### 3.4.1 Explore the Monitor
**Goal**: Get comfortable with the built-in monitor (command shell).

**Tasks:**
- [ ] Connect to the Picocomputer (USB CDC serial or VGA + USB keyboard)
- [ ] Type `help` — read and understand each command
- [ ] Try `status` — note PHI2 speed, firmware version, memory info
- [ ] Try `set phi2 <value>` — change clock speed, observe effect
- [ ] Try memory commands: read address (`0200`), write address (`0200:A9`)
- [ ] Try `reboot` — observe boot sequence
- [ ] Try CTRL-ALT-DEL (break) if a program is running

### 3.4.2 Run Existing Programs
**Goal**: See what the Picocomputer can do out of the box.

**Resources:**
- Example programs: `/Users/nenaddjordjevic/CProjects/examples/`
- ehBASIC: `/Users/nenaddjordjevic/CProjects/ehbasic/`
- Pre-built .rp6502 files from releases: https://github.com/picocomputer/rp6502/releases

**Tasks:**
- [ ] Copy example .rp6502 files to a USB drive (FAT32 formatted)
- [ ] Plug USB drive into the Picocomputer's USB-A port
- [ ] Use `ls` in monitor to see files
- [ ] `load` and run each example, observe behavior:
  - [ ] Text/console examples (hello world, echo)
  - [ ] Graphics examples (mode1, mode2, mode3 — character, tile, bitmap)
  - [ ] Input examples (keyboard, gamepad)
  - [ ] Audio examples (if available)
- [ ] Load and run **ehBASIC** — type a simple BASIC program (`10 PRINT "HELLO"`, `20 GOTO 10`, `RUN`)
- [ ] Note what works, what's interesting, what you want to understand deeper

### 3.4.3 Set Up Development Environment
**Goal**: Be able to compile and load your own 6502 programs.

**Resources:**
- CC65 template: `/Users/nenaddjordjevic/CProjects/vscode-cc65/`
- LLVM-MOS template: `/Users/nenaddjordjevic/CProjects/vscode-llvm-mos/`
- CC65 RP6502 docs: https://cc65.github.io/doc/rp6502.html

**Tasks:**
- [ ] **Choose toolchain**: CC65 (C + assembly, well-documented for RP6502) or LLVM-MOS (modern C/C++, better optimization)
- [ ] Install chosen toolchain on your Mac
- [ ] Clone/copy the development template (vscode-cc65 or vscode-llvm-mos)
- [ ] Build the template's default program — verify it produces a .rp6502 file
- [ ] Load the built program onto the Picocomputer (USB drive or UART upload)
- [ ] Verify it runs — you've completed the "hello world" of RP6502 development

### 3.4.4 Write Simple Programs
**Goal**: Get hands-on with 6502 programming on the Picocomputer.

**Tasks:**
- [ ] **Hello World**: Print text to the console via UART ($FFE0/$FFE1)
- [ ] **Echo**: Read keyboard input, echo back to console (poll READY, read RX, write TX)
- [ ] **Memory Explorer**: Read and display a range of memory addresses
- [ ] **XRAM Test**: Write a pattern to XRAM via portals ($FFE4–$FFEB), read it back, verify
- [ ] **Simple Graphics** (if VGA): Set a video mode via xreg, draw something to XRAM, see it on screen
- [ ] **File I/O**: Open a file from USB drive, read contents, print to console

### 3.4.5 Observe and Probe (with tools)
**Goal**: Use your test equipment to see what's happening on the real hardware.

**Tasks:**
- [ ] **Logic analyzer**: Capture PHI2, address bus, data bus during a simple program — see the 6502 fetch-execute cycle in real time
- [ ] **Logic probes**: Check chip select signals (CS for SRAM, VIA, RIA) during reads/writes
- [ ] **Oscilloscope**: Measure PHI2 frequency, verify it matches `status` output
- [ ] **Compare with emulator**: Run the same program in the emulator and on hardware — do the register reads/writes match?
- [ ] Document observations and any differences between emulator and real hardware

### 3.4.6 Understand the User-Level Architecture
**Goal**: Build a mental model of how the system works from a programmer's perspective.

**Tasks:**
- [ ] Draw/sketch the data flow: 6502 → bus → SRAM / RIA / VIA — what happens on each STA/LDA
- [ ] Understand the memory map from a user's perspective: where code goes, where stack is, where I/O registers are
- [ ] Understand the OS call flow: push args to XSTACK → write OP → spin on BUSY → read result
- [ ] Understand how video works from a user's perspective: xreg to configure mode → write pixel/tile data to XRAM → VGA Pico reads it via PIX bus
- [ ] Understand file I/O from a user's perspective: filenames on XSTACK, OS calls for open/read/write/close
- [ ] Review the example programs' source code and trace the logic

### 3.4.7 Explore Hardware Extension Possibilities
**Goal**: Understand how the Picocomputer can be extended with custom hardware so you can eventually design and build your own add-ons.

**Why here?** Once the computer works, it's natural to ask "what can I add?" — a second goal of this learning journey is to explore creating hardware extensions (add-on boards or circuits) that extend the computer's capabilities.

**Tasks:**
- [ ] Read the schematic and docs for **expansion points**:
  - [ ] **VIA (U5, $FFD0–$FFDF)**: 16 GPIO lines, timers, shift register — the classic 6502 expansion chip; any add-on that needs pins or timing can use it
  - [ ] **GPIO / connector headers**: Check schematic for any exposed bus lines or spare pins (e.g. J1, J2) that could drive an expansion board
  - [ ] **PIX bus**: RIA ↔ VGA link; advanced extensions could add PIX-compatible devices (see Phase 8.4)
- [ ] Identify one or two extension ideas (e.g. extra LEDs, buttons, serial port, SD card slot, custom display, sensor board) that would use VIA or a small glue circuit
- [ ] Study the VIA register map (W65C22S datasheet) — which registers you'd use for your first extension
- [ ] Optional: Browse the community (forums, Discord) for others' hardware extension projects

**Later (Phase 8.4):** Design and build a real hardware extension — schematic, PCB or breadboard, 6502 driver code.

### 3.5 Other Console Access Options
**Goal**: Try the other ways to get a console besides VGA + USB keyboard.

**Tasks:**
- [ ] **USB CDC (serial)** — if VGA is connected, connect the RIA Pico to the computer via USB and use a serial terminal (e.g. minicom, PuTTY) at the correct port and baud rate to get a second console or to access the monitor without a display
- [ ] **UART pins on RIA** — use the RIA’s UART pins (115200 8N1) with a USB–serial adapter or similar to get console access (see hardware docs for pinout)

### 3.6 Learning and Exploring Debug Probe
**Goal**: Learn to use the Raspberry Pi Debug Probe when the Pico (RIA or VGA) won’t respond or firmware load fails.

**Tasks:**
- [ ] **Use Raspberry Pi Debug Probe** — Connect the Debug Probe (SWD + UART) to the RIA or VGA Pico’s debug header (if present on the board) to reflash firmware, run OpenOCD, or capture serial output. See [notes/tools/RASPBERRY_PI_DEBUG_PROBE.md](notes/tools/RASPBERRY_PI_DEBUG_PROBE.md). Check schematic/docs for 3-pin debug connector availability and pinout.

---

## Phase 4: Deep Dive into Chip Interactions

**Goal**: Understand how chips communicate and work together

> **Emulator path:** These topics are learned hands-on via emulator milestones 1.2 (Memory Bus), 1.4 (XRAM Portals). See `rp6502-emulator/notes/IMPLEMENTATION.md`.

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

> **Emulator path:** These topics are learned hands-on via emulator milestones 1.3 (Echo Terminal / UART), 1.4 (XRAM), 1.5 (OS Calls), 1.8 (Monitor). Each milestone lists the exact firmware files to study first. See `rp6502-emulator/notes/IMPLEMENTATION.md`.

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

> **Emulator path:** Covered by emulator Phase 2 (VGA). See `rp6502-emulator/notes/IMPLEMENTATION.md`.

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

> **Emulator path:** These topics are learned hands-on via emulator milestones 1.5 (OS Calls), 1.6 (ROM Loading), 1.7 (File I/O), 1.9 (Run Real Programs). See `rp6502-emulator/notes/IMPLEMENTATION.md`.

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

> **Emulator path:** PIO is studied when reading RIA/VGA firmware but not emulated directly (PIO is Pico-specific). Audio is covered by emulator Phase 2. See `rp6502-emulator/notes/IMPLEMENTATION.md`.

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

### 8.4 Hardware Extensions — Design & Build Add-Ons
**Goal**: Design and build hardware extensions that plug into or connect to the Picocomputer to extend its capabilities (your own add-on boards or circuits).

**Learning Objectives:**
- [ ] **VIA-based expansion** ($FFD0–$FFDF): Use the W65C22S GPIO, timers, and shift register to drive custom circuits (LEDs, buttons, relays, sensors, extra serial, etc.). Study VIA datasheet; write 6502 code to talk to your hardware.
- [ ] **Expansion connectors**: If the board exposes bus or GPIO headers, design a small expansion board (schematic + PCB or breadboard) that plugs in and decodes addresses for your device.
- [ ] **PIX bus devices** (advanced): Design a device that speaks the PIX protocol (e.g. another Pico or CPLD) to add new "virtual" hardware the RIA/VGA firmware can talk to.
- [ ] **New video modes / OS calls** (software + firmware): Add custom video modes or OS calls in the firmware to support your extension; requires building custom RIA/VGA firmware.
- [ ] **End-to-end project**: Choose one extension (e.g. LED matrix, keypad, SD card via SPI on VIA), design the circuit, build it, and write 6502 driver code that uses it.

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

### Phase 3.4: Hands-On Exploration (1-2 weeks, once hardware is working)
1. Explore the monitor — learn all commands
2. Run existing programs and ehBASIC
3. Set up dev environment (CC65 or LLVM-MOS)
4. Write simple programs (hello, echo, XRAM test, graphics)
5. Probe with logic analyzer and oscilloscope
6. Build mental model of user-level architecture
7. Explore hardware extension possibilities (VIA, expansion headers, PIX bus) — goal: eventually design and build add-on hardware

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
   
5. **Phase 3.4** - Hands-on exploration (once hardware works)
   - Explore the monitor, run existing programs, set up dev environment
   - Write simple programs, probe with tools
   - Build user-level mental model before diving into firmware

6. **Phase 4+** - Deep dive
   - Dive into chip interactions, firmware internals
   - Emulator milestones drive this learning (see IMPLEMENTATION.md)

7. **Get help when needed**
   - Ask questions in forums or Discord
   - Review documentation
   - Study example code

Good luck with your RP6502 learning journey! 🚀
