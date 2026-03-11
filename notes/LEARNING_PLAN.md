# RP6502 Learning Plan

## Overview
The RP6502 (Picocomputer 6502) is a modern single-board computer built around the classic WDC W65C02S 8-bit microprocessor, enhanced with two Raspberry Pi Pico 2 modules acting as intelligent coprocessors. This plan guides you through understanding the system from hardware to software.

---

> **Unified Strategy:** The RP6502 emulator project (`rp6502-emulator/notes/IMPLEMENTATION.md`) is the primary hands-on learning vehicle for Phases 4–8. Each emulator milestone requires studying the relevant firmware and documentation first, then implementing and testing — which proves understanding. This plan serves as the **reference checklist** of what to understand; the emulator milestones define **how you learn it** through building. Hardware assembly (Phases 2–3) runs as a parallel track whenever you're at the bench.

---

## Phase 0: Understanding Projects in RP6502 Workspace

**Goal**: Get familiar with the workspace structure and understand what each project contains

**Workspace projects:** See [WORKSPACE_PROJECTS.md](notes/WORKSPACE_PROJECTS.md) for the full list, descriptions, and when to use each project.

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
- [✔] **RAM read/write**: In the monitor, read memory at an address (e.g. type address to view contents) and write to RAM (address + data); verify no timeout or verify errors
- [✔] **6502 clock speed**: First check current speed with **SET PHI2** (no argument) — should show e.g. `PHI2: 8000 (kHz)` (8 MHz default). Then run **SET PHI2 8000** to set 8 MHz; value is saved to RIA EEPROM.
- [✔] **LOAD ADVENT4**: FAT32 USB plugged in → `status` shows drive, `ls` lists files. Copy release `advent.rp6502` to USB, then **`load advent.rp6502`** (or rename to `advent4` and **`load advent4`**). See notes/videos/ep10-assembly-on-circuit-board.md.

### 3.3 Troubleshooting

**Issue: `status` doesn't show VGA on cold power-up**
On cold boot, `status` omits the VGA Pico even though the screen and keyboard work; one reboot (CTRL-ALT-DEL or power cycle) fixes it.
**Cause:** Noise on the UART backchannel (author suspects power supply or board sourcing).
**Solution:** [✔] Fixed in v0.17 — add `busy_wait_ms(5)` in `vga_connect()` in `rp6502/src/ria/sys/vga.c`. Build from source: see SETUP_NOTES.md. [✔] Flash official v0.18 .uf2 from [releases](https://github.com/picocomputer/rp6502/releases) (RIA and VGA) and verify `status` shows VGA on cold power-up.

- [✔] **Update VGA Pico firmware to v0.18** — RIA already on v0.18; flash the **VGA** Pico with v0.18 .uf2 so both are in sync (com.c fix, term, modes, USB CDC).

---

**Issue: Num Lock on at boot (v0.18)** — On keyboards without a separate numpad (e.g. RPi Keyboard and Hub), letters type as numbers until you press Num Lock. **Workaround:** Press Num Lock once. **Fix:** PID/VID exception in firmware (RPi keyboard only). See [NUM_LOCK_RPI_KEYBOARD.md](peripherals/NUM_LOCK_RPI_KEYBOARD.md) for full research, cause, VID/PID (04d9:0006), and implementation.

- [✔] **Implement PID/VID exception for Num Lock** — [NUM_LOCK_RPI_KEYBOARD.md](peripherals/NUM_LOCK_RPI_KEYBOARD.md).

## Phase 4: Hands-On Exploration (Computer Working)

**Goal**: Once the hardware is fully functional — explore the computer as a user (monitor, serial access, running programs from USB/releases), and build intuition for how the system works from the user's perspective, before diving into firmware internals in later phases.

**Projects to check (user-side exploration):**
- **examples/** — 6502 example programs: text/console (hello, echo), graphics (mode1, mode2, mode3), input (gamepad), audio (furelise, ezpsg), and more. Build to get .rp6502 files or use pre-built from releases.
- **ehbasic/** — Enhanced BASIC interpreter. Load the .rp6502 on USB and run it; type BASIC (e.g. `10 PRINT "HELLO"`, `20 GOTO 10`, `RUN`).
- **Pre-built .rp6502** — https://github.com/picocomputer/rp6502/releases (no build required; copy to USB and load).

### 4.1 Explore the Monitor
**Goal**: Get comfortable with the built-in monitor (command shell) and verify every command. Use [notes/MONITOR.md](notes/MONITOR.md) as the full reference (syntax, code locations, help text source).

**Setup**
- [✔] Connect to the Picocomputer (VGA + USB keyboard). At `]` prompt you are in the monitor (RIA); 6502 is in reset.

**HELP — analyze and test**
- [✔] `help` (or `h` or `?`) — read full command list and installed ROMs; note format matches MONITOR.md.
- [✔] `help system` — read tutorial (memory, RESB, CTRL-ALT-DEL, hex address examples).
- [ ] `help about` / `help credits` — copyright and VIP list.
- [ ] For each command below, run `help <command>` (or `help set <attr>` for SET) and compare to MONITOR.md; then run the command as in the test.

**STATUS**
- [ ] Analyze: `help status`. Test: `status` — note PHI2, firmware version, VGA line, USB device counts, MSC drives.

**SET — analyze and test each attribute**
- [ ] Analyze: `help set` then `help set phi2`. Test: `set`, `set phi2`, `set phi2 4000` (or 1000); confirm value and that it persists (e.g. reboot).
- [ ] Analyze: `help set boot`. Test: `set boot`, `set boot -`, `set boot <rom>` if you have an installed ROM.
- [ ] Analyze: `help set tz`. Test: `set tz`, `set tz <city>` (e.g. `LOS_ANGELES`).
- [ ] Analyze: `help set kb`. Test: `set kb`, `set kb US`; list layouts from help.
- [ ] Analyze: `help set cp`. Test: `set cp`, `set cp 437`.
- [ ] Analyze: `help set vga`. Test: `set vga`, `set vga 0` (or 1/2 if supported).
- [ ] (Pico W only) Analyze: `help set rf`, `help set rfcc`, `help set ssid`, `help set pass`, `help set ble`. Test each if applicable.

**Memory (address commands)**
- [ ] Analyze: `help 0000` or `help 0:` for memory/drive help. Test read: `0200` — 16 bytes hex dump.
- [ ] Test range: `0200-020F` or `10000-1000F` (XRAM); observe `--More--` if multiple lines.
- [ ] Test write: `0200 A9 01 85 00 60` then read `0200` to verify.
- [ ] Test reset vector: `fffc 00 02` (lo/hi of $0200); optional Intel HEX: `]:02FFFC00000201`.

**BINARY**
- [ ] Analyze: `help binary`. Test only if you have a host tool that sends binary (e.g. dev workflow); otherwise note: `binary addr len crc` then raw bytes, max 1024, CRC-32.

**Filesystem (LS, CD, drive, MKDIR, UNLINK)**
- [ ] Analyze: `help dir`. Test: `ls`, `ls <subdir>`, `dir`; note `<DIR>` and file sizes.
- [ ] Test: `cd`, `cd <dir>`, `cd ..`; `0:` … `7:` (or `USB0:` … `USB7:`) — change drive, then `ls`.
- [ ] Analyze: `help mkdir`. Test: `mkdir testdir`, then `ls`; `unlink testdir` (empty dir).
- [ ] Analyze: `help unlink`. Test: create a file (e.g. by UPLOAD or copy on USB), then `unlink <file>`.

**ROM (LOAD, INFO, INSTALL, romname, REMOVE)**
- [ ] Analyze: `help load`. Test: put a .rp6502 on USB, `load <file>`; if file has reset vector, 6502 starts.
- [ ] Analyze: `help install`. Test: `info <file>` (shows `#` lines), then `install <file>`; `help` should list the ROM.
- [ ] Test: run installed ROM by name (e.g. `ehbasic` or `EHBASIC`); confirm it starts.
- [ ] Analyze: `help` (REMOVE in command list). Test: `remove <romname>`; confirm it disappears from `help`.

**REBOOT and RESET**
- [ ] Analyze: `help reboot`, `help reset`. Test: `reset` — 6502 runs from $FFFC; press CTRL-ALT-DEL to return to `]`.
- [ ] Test: `reboot` — observe full RIA boot; if SET BOOT is set, boot ROM loads.

**UPLOAD**
- [ ] Analyze: `help upload`. Test: `upload test.bin`; at `}` send a small chunk (e.g. length + CRC + bytes), then `END` or blank line; verify file with `ls` / `unlink`.

**Break and pager**
- [ ] Run a program (e.g. load a ROM that runs). Press **CTRL-ALT-DEL** — confirm return to `]`.
- [ ] Trigger `--More--` (e.g. `help` or `status` on a tall display); press space to continue, then `q` to abort mid-output.

### 4.2 Three Ways to Access the Monitor (Serial Options) and User-Level View
**Goal**: Use the monitor via serial when you don't have VGA (or as a second console), try all console options besides VGA + USB keyboard, and build a mental model of how the system works from a user's perspective.

**Three ways (official, [Picocomputer RIA](https://picocomputer.github.io/ria.html)):** (1) VGA + USB/Bluetooth keyboard; (2) USB CDC from the VGA Pico when plugged into a host PC; (3) UART RX/TX on the RIA at 115200 8N1 when not using VGA. **4.1** uses method 1. This section covers **method 2** and **method 3**.

**Without a video Pico (method 3 — RIA USB CDC):**
- [ ] Connect only the **RIA Pico** via USB. Find the CDC port (e.g. `/dev/cu.usbmodem*`); use **115200 8N1**. Run minicom (or screen/cu), use monitor; load a text-only program. Send break to return to monitor.

**With VGA Pico (method 2 — CDC from VGA):**
- [ ] When the VGA Pico is connected to the host PC via USB, it presents a second CDC serial device. Use that port at 115200 8N1 for a second console or headless access. With VGA connected, you can also attach the RIA via USB and use a serial terminal (minicom, PuTTY) at 115200 8N1 for a second console.

**RIA UART pins (method 3):**
- [ ] Connect to the RIA's UART RX/TX (115200 8N1) with a USB–serial adapter (see hardware docs for pinout).

**User-level architecture (mental model):**
- [ ] Draw/sketch: 6502 → bus → SRAM / RIA / VIA; where code, stack, and I/O registers live.
- [ ] Console: when RESB low → monitor; when RESB high → 6502 stdio. OS call flow: XSTACK → OP → spin on BUSY → result.
- [ ] Video (user view): xreg to configure mode → write pixel/tile data to XRAM → VGA reads via PIX. File I/O: filenames on XSTACK, OS calls for open/read/write/close.
- [ ] Review example programs' source and trace the logic.

### 4.3 Running Existing Programs from Various Sources
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

---

## Phase 5: Explore Hardware

**Goal**: Use test equipment to observe the hardware and understand how the Picocomputer can be extended with custom add-ons.

### 5.1 Observe and Probe (with tools)
**Goal**: Use your test equipment to see what's happening on the real hardware.

**Tasks:**
- [ ] **Logic analyzer**: Capture PHI2, address bus, data bus during a simple program — see the 6502 fetch-execute cycle in real time
- [ ] **Logic probes**: Check chip select signals (CS for SRAM, VIA, RIA) during reads/writes
- [ ] **Oscilloscope**: Measure PHI2 frequency, verify it matches `status` output
- [ ] **Compare with emulator**: Run the same program in the emulator and on hardware — do the register reads/writes match?
- [ ] Document observations and any differences between emulator and real hardware

### 5.2 Explore Hardware Extension Possibilities
**Goal**: Understand how the Picocomputer can be extended with custom hardware so you can eventually design and build your own add-ons.

**Why here?** Once the computer works, it's natural to ask "what can I add?" — a second goal of this learning journey is to explore creating hardware extensions (add-on boards or circuits) that extend the computer's capabilities.

**Tasks:**
- [ ] Read the schematic and docs for **expansion points**:
  - [ ] **VIA (U5, $FFD0–$FFDF)**: 16 GPIO lines, timers, shift register — the classic 6502 expansion chip; any add-on that needs pins or timing can use it
  - [ ] **GPIO / connector headers**: Check schematic for any exposed bus lines or spare pins (e.g. J1, J2) that could drive an expansion board
  - [ ] **PIX bus**: RIA ↔ VGA link; advanced extensions could add PIX-compatible devices (see Phase 11.4)
- [ ] Identify one or two extension ideas (e.g. extra LEDs, buttons, serial port, SD card slot, custom display, sensor board) that would use VIA or a small glue circuit
- [ ] Study the VIA register map (W65C22S datasheet) — which registers you'd use for your first extension
- [ ] Optional: Browse the community (forums, Discord) for others' hardware extension projects

**Later (Phase 11.4):** Design and build a real hardware extension — schematic, PCB or breadboard, 6502 driver code.

### 5.3 Learning and Exploring Debug Probe
**Goal**: Learn to use the Raspberry Pi Debug Probe when the Pico (RIA or VGA) won't respond or firmware load fails.

**Tasks:**
- [ ] **Use Raspberry Pi Debug Probe** — Connect the Debug Probe (SWD + UART) to the RIA or VGA Pico's debug header (if present) to reflash firmware, run OpenOCD, or capture serial output. See [notes/tools/RASPBERRY_PI_DEBUG_PROBE.md](notes/tools/RASPBERRY_PI_DEBUG_PROBE.md). Check schematic/docs for 3-pin debug connector availability and pinout.

---

## Phase 6: Deep Dive into Chip Interactions

**Goal**: Understand how chips communicate and work together

> **Emulator path:** These topics are learned hands-on via emulator milestones 1.2 (Memory Bus), 1.4 (XRAM Portals). See `rp6502-emulator/notes/IMPLEMENTATION.md`.

### 6.1 6502 Bus Architecture
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

### 6.2 RIA Bus Interface
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

### 6.3 Memory System
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

### 6.4 PIX Bus Communication
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

### 6.5 Power Distribution
**Learning Objectives:**
- [ ] Understand +3V3A and +3V3B rails
- [ ] Learn decoupling capacitor placement
- [ ] Study power sequencing
- [ ] Understand current requirements

---

## Phase 7: RIA Firmware Deep Dive

**Goal**: Understand how the RIA firmware works

> **Emulator path:** These topics are learned hands-on via emulator milestones 1.3 (Echo Terminal / UART), 1.4 (XRAM), 1.5 (OS Calls), 1.8 (Monitor). Each milestone lists the exact firmware files to study first. See `rp6502-emulator/notes/IMPLEMENTATION.md`.

### 7.1 RIA Core Architecture
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

### 7.2 RIA Registers & Memory Map
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

### 7.3 RIA Features & Services
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

## Phase 8: VGA Firmware Deep Dive

> **Emulator path:** Covered by emulator Phase 2 (VGA). See `rp6502-emulator/notes/IMPLEMENTATION.md`.

### 8.1 VGA Core Architecture
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

### 8.2 Video Modes
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

### 8.3 PIX Bus Communication (Advanced)
**Learning Objectives:**
- [ ] Understand PIX frame format in detail (32 bits, device/channel/address/value)
- [ ] Learn XRAM broadcasting mechanism
- [ ] Study XREG configuration protocol
- [ ] Understand backchannel communication

---

## Phase 9: RP6502-OS (Operating System API)

> **Emulator path:** OS calls are learned hands-on via emulator milestones 1.5 (OS Calls), 1.6 (ROM Loading), 1.7 (File I/O). See `rp6502-emulator/notes/IMPLEMENTATION.md`.

### 9.1 Operating System API
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

---

## Phase 10: Application Programming

> **Emulator path:** Application development is reinforced via emulator milestone 1.9 (Run Real Programs). See `rp6502-emulator/notes/IMPLEMENTATION.md`.

### 10.1 Enter a Program Manually Byte by Byte
**Goal**: Enter a minimal 6502 program into RAM completely by hand: type the address, then each byte (in hex) at the monitor prompt. No paste, no Intel HEX — just the "write bytes at an address" syntax. Reinforces addresses and machine code.

**Resources:**
- [notes/MONITOR.md](notes/MONITOR.md) — "Write bytes at an address"

**Tasks:**
- [ ] At the monitor prompt, use the write-bytes form: address then space-separated hex bytes. Example: `]C000 A9 01 85 00 60` writes LDA #1, STA $00, RTS at `$C000`. Enter the reset vector the same way: `]FFFC 00 C0` so reset jumps to `$C000`.
- [ ] Reset (or use the monitor run command) and run the program; confirm by reading `$00` in the monitor afterward.
- [ ] Optional: change the program (e.g. LDA #42) by re-entering the bytes; reset and run again.

### 10.2 Enter a Program Using Intel HEX Lines
**Goal**: Enter a 6502 program into RAM by pasting or typing Intel HEX lines at the monitor. The monitor accepts standard `:...` records; you can use lines you typed, copied from an assembler, or from [INTEL_HEX.md](notes/INTEL_HEX.md).

**Resources:**
- [notes/INTEL_HEX.md](notes/INTEL_HEX.md) — what Intel HEX is and how to read a line
- [notes/MONITOR.md](notes/MONITOR.md) — "Intel HEX records"

**Tasks:**
- [ ] Enter a **data record** that writes bytes at your code address (e.g. `$C000`). Example for LDA #1, STA $00, RTS: `]:05C00000A90185006028` (checksum may vary; see MONITOR.md or compute).
- [ ] Enter a record that sets the **reset vector** at `$FFFC`–`$FFFD` (e.g. low `00`, high `C0` for `$C000`). Example: `]:02FFFC0000C0B9`.
- [ ] Reset or run; confirm the program executes (e.g. read `$00`). Optional: edit the HEX data and re-enter to try another value.

### 10.3 Load a Binary into Memory Using the Monitor
**Goal**: Load a compiled/assembled binary (raw bytes) into RAM using whatever load command the monitor provides — e.g. the **BINARY** command, which accepts address, length, and CRC, then raw bytes from the host. No manual byte entry or HEX encoding.

**Resources:**
- [notes/MONITOR.md](notes/MONITOR.md) — "BINARY addr len crc" (bulk write; host sends raw binary after the command; max 1024 bytes per call, CRC-32 verified)

**Tasks:**
- [ ] Assemble or compile a small program and obtain a raw binary (or use a prebuilt one). Note the load address and size.
- [ ] Use the monitor’s binary-load facility (see MONITOR.md): e.g. issue `BINARY` with address, length, and CRC, then send the exact number of raw bytes from your terminal or tool.
- [ ] Set the reset vector if needed (e.g. via write-bytes or Intel HEX), then run the program and verify it works.

### 10.4 6502 Software Development
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

### 10.5 Programming Patterns
**Learning Objectives:**
- [ ] XRAM access patterns (RW0/RW1 with STEP)
- [ ] Video mode programming
- [ ] Input handling (keyboard, mouse, gamepad)
- [ ] Audio programming (PSG, OPL2)
- [ ] File I/O and storage
- [ ] Interrupt handling (VSYNC interrupts)

---

## Phase 11: Advanced Topics

> **Emulator path:** PIO is studied when reading RIA/VGA firmware but not emulated directly (PIO is Pico-specific). Audio is covered by emulator Phase 2. See `rp6502-emulator/notes/IMPLEMENTATION.md`.

### 11.1 PIO Programming
**Resources:**
- `/Users/nenaddjordjevic/CProjects/rp6502/src/ria/ria.pio`
- `/Users/nenaddjordjevic/CProjects/rp6502/src/vga/vga.pio`
- Pico SDK PIO documentation

**Learning Objectives:**
- [ ] Understand PIO state machines
- [ ] Study RIA PIO program (bus interface)
- [ ] Study VGA PIO program (video generation)
- [ ] Learn to write custom PIO programs

### 11.2 Networking & Communication
**Resources:**
- `/Users/nenaddjordjevic/CProjects/rp6502/src/ria/net/`

**Learning Objectives:**
- [ ] WiFi configuration and usage
- [ ] Bluetooth LE setup
- [ ] Hayes modem emulation
- [ ] NTP time synchronization

### 11.3 Audio Systems
**Resources:**
- `/Users/nenaddjordjevic/CProjects/rp6502/src/ria/aud/`
- `/Users/nenaddjordjevic/CProjects/rp6502/src/emu8950/`

**Learning Objectives:**
- [ ] PSG (Programmable Sound Generator) programming
- [ ] OPL2 FM synthesis
- [ ] Audio mixing and effects

### 11.4 Hardware Extensions — Design & Build Add-Ons
**Goal**: Design and build hardware extensions that plug into or connect to the Picocomputer to extend its capabilities (your own add-on boards or circuits).

**Learning Objectives:**
- [ ] **VIA-based expansion** ($FFD0–$FFDF): Use the W65C22S GPIO, timers, and shift register to drive custom circuits (LEDs, buttons, relays, sensors, extra serial, etc.). Study VIA datasheet; write 6502 code to talk to your hardware.
- [ ] **Expansion connectors**: If the board exposes bus or GPIO headers, design a small expansion board (schematic + PCB or breadboard) that plugs in and decodes addresses for your device.
- [ ] **PIX bus devices** (advanced): Design a device that speaks the PIX protocol (e.g. another Pico or CPLD) to add new "virtual" hardware the RIA/VGA firmware can talk to.
- [ ] **New video modes / OS calls** (software + firmware): Add custom video modes or OS calls in the firmware to support your extension; requires building custom RIA/VGA firmware.
- [ ] **End-to-end project**: Choose one extension (e.g. LED matrix, keypad, SD card via SPI on VIA), design the circuit, build it, and write 6502 driver code that uses it.

---

## Recommended Learning Path

This section is a condensed timeline aligned with the phases above (Phase 0–Phase 11). For full objectives and tasks, see the corresponding phase sections earlier in this document.

### Phase 0: Understanding Projects in RP6502 Workspace (30 min – 1 hour)
1. Read `notes/WORKSPACE_PROJECTS.md` to understand all projects in the workspace
2. Identify which projects are core RP6502 vs reference materials
3. Understand when to use each project during learning
4. Familiarize yourself with project locations and purposes

### Phase 1: High-Level Chip Overview (1–2 days)
1. Study the schematic and identify all chips
2. Understand what each chip does at a high level
3. Review the memory map
4. Read hardware documentation overview

### Phase 2: Assembly & Hardware Setup (1–2 days)
1. Prepare workspace and verify all parts
2. Assemble the computer following instructions
3. Install firmware on both Pico modules
4. Double-check all connections

### Phase 3: Testing & Verification (1 day)
1. Power on and verify basic functionality
2. Access console/monitor
3. Test basic commands
4. Troubleshoot any issues

### Phase 4: Hands-On Exploration — Computer Working (1–2 weeks)
1. Explore the monitor — learn all commands (see Phase 4.1, MONITOR.md)
2. Use all three serial/console access options (VGA+keyboard, CDC, UART)
3. Run existing programs and ehBASIC from USB/releases
4. Build a mental model of user-level architecture (4.2, 4.3)
5. Optional: probe with logic analyzer and oscilloscope

### Phase 5: Explore Hardware (1–2 weeks)
1. Observe and probe with test equipment (PHI2, bus, chip selects)
2. Explore hardware extension possibilities (VIA, expansion headers, PIX bus)
3. Goal: eventually design and build add-on hardware (see Phase 11.4)

### Phase 6: Deep Dive into Chip Interactions (1–2 weeks)
1. Study 6502 bus architecture in detail
2. Understand RIA bus interface and memory system (System RAM vs XRAM)
3. Study PIX bus communication
4. Understand power distribution

### Phase 7: RIA Firmware Deep Dive (1–2 weeks)
1. Study RIA source code structure
2. Understand bus interface implementation
3. Learn OS call mechanism
4. Experiment with RIA features

### Phase 8: VGA Firmware Deep Dive (1 week)
1. Study VGA source code
2. Understand video modes and PIX bus communication
3. Create simple graphics programs

### Phase 9: RP6502-OS (Operating System API) (1–2 weeks)
1. Understand ABI, fastcall, XSTACK, register passing
2. Learn OS call mechanism (RIA_OP, RIA_BUSY, RIA_SPIN)
3. Master key OS calls: file I/O, directory, xreg, time, random
4. Study error handling (RIA_ERRNO)

### Phase 10: Application Programming (2+ weeks)
1. Enter a program manually byte by byte via the monitor (10.1)
2. Enter a program using Intel HEX lines (10.2)
3. Load a binary into memory using the monitor’s load command (e.g. BINARY) (10.3)
4. Set up development environment (CC65 or LLVM-MOS) and study example programs (10.4)
5. Apply programming patterns: XRAM, video, input, audio, file I/O (10.5)

### Phase 11: Advanced Topics (ongoing)
1. PIO programming (RIA/VGA PIO programs)
2. Networking & communication (WiFi, BLE, NTP)
3. Audio systems (PSG, OPL2)
4. Hardware extensions — design and build add-ons (VIA, expansion, PIX)
5. Deep dive into areas of interest; contribute to the project

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

Good luck with your RP6502 learning journey! 🚀
