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

### 1.2 Key Connections (High-Level)
**Learning Objectives:**
- [x] **6502 Bus**: Address (A0-A15), Data (D0-D7), Control (RWB, PHI2, RESB)
- [x] **PIX Bus**: 5 wires connecting RIA to VGA (PHI2 + PIX0-3)
- [x] **Power**: Two 3.3V rails (+3V3A, +3V3B) for different sections
- [x] **VGA Output**: Analog RGB signals via resistor ladders
- [x] **Audio Output**: Stereo audio via PWM from RIA

---

## Phase 2: Assembly & Hardware Setup

**Goal**: Build the computer and get it working

### 2.1 Pre-Assembly Checklist
**Resources:**
- Documentation: https://picocomputer.github.io/hardware.html
- Assembly guide in hardware docs
- YouTube videos: https://youtube.com/playlist?list=PLvCRDUYedILfHDoD57Yj8BAXNmNJLVM2r

**Learning Objectives:**
- [ ] Verify you have all ICs (check Active Parts List)
- [x] Review assembly instructions
- [x] Understand IC orientation (pin 1 indicators, notch/dot orientation)
- [ ] Set up anti-static mat and wrist strap at bench and check that they are properly grounded
- [ ] Tools: multimeter, logic analyzer, chip tester, microscope

### 2.1.1 Pre-IC Testing & Inspection (Pre-Soldered Board)
**Goal**: Test and inspect the board before inserting expensive ICs to catch any manufacturing defects

**Visual Inspection:**
- [ ] Check for solder bridges between adjacent pads/pins
- [ ] Verify all passive components are installed (resistors, capacitors)
- [ ] Check for cold solder joints (dull, grainy appearance)
- [ ] Verify component orientation matches silkscreen markings
- [ ] Check for missing components (compare to BOM)
- [ ] Inspect IC sockets for proper installation and pin alignment
- [ ] Verify connectors (VGA, audio, GPIO headers) are properly soldered
- [ ] Check for any physical damage (cracks, lifted pads, scratches)

**Continuity Testing (Multimeter):**
- [ ] **Power Rails**: Test continuity on +3V3A and +3V3B rails (should be isolated from each other)
- [ ] **Ground Plane**: Verify all GND connections have continuity
- [ ] **Socket Power Pins**: Check VCC pins on each IC socket connect to appropriate power rail
- [ ] **Socket Ground Pins**: Check GND pins on each socket connect to ground
- [ ] **No Shorts**: Verify no continuity between power rails and ground (with no power applied)
- [ ] **PIX Bus**: Check continuity on PIX bus connections (PHI2, PIX0-3) between RIA and VGA sockets
- [ ] **6502 Bus**: Verify address/data bus traces have continuity (check a few representative pins)

**Resistance Checks:**
- [ ] Verify resistor values match BOM (measure a few key ones)
- [ ] Check decoupling capacitors don't show short circuits
- [ ] Verify pull-up/pull-down resistors are correct values

**Socket Inspection:**
- [ ] Verify each socket pin makes good contact (gently probe with multimeter)
- [ ] Check socket orientation matches silkscreen (pin 1 indicators)
- [ ] Verify socket pin numbering matches expected IC pinout
- [ ] Test socket pin continuity to PCB traces

**Power Supply Testing (CAUTION - Only if safe):**
- [ ] **DO NOT** apply power yet if you haven't verified no shorts
- [ ] If continuity tests pass, consider brief power test with current-limited supply
- [ ] Check voltage levels on power rails (should be 3.3V)
- [ ] Verify no excessive current draw (indicates short circuit)
- [ ] **Stop immediately** if you see smoke, excessive heat, or unexpected voltages

**Tools Needed:**
- Digital multimeter (continuity, resistance, voltage modes)
- Good lighting and magnification (for visual inspection)
- Anti-static mat and wrist strap (when handling ICs later)

**Safety Notes:**
- Always test with power OFF for continuity/resistance checks
- Never apply power if you find shorts between power and ground
- Use current-limited power supply if testing voltages
- Keep ICs in anti-static bags until ready to install

### 2.2 Assembly Steps (Pre-Soldered Board)
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
- [ ] **Install Raspberry Pi Pico modules**:
  - [ ] U2 - Pico 2 W (RIA) - goes in socket (note orientation)
  - [ ] U4 - Pico 2 (VGA) - goes in socket (note orientation)
- [ ] Double-check all IC orientations (pin 1 matches socket pin 1)
- [ ] Verify all ICs are fully seated in sockets

### 2.3 Firmware Installation
**Resources:**
- Firmware releases: https://github.com/picocomputer/rp6502/releases
- Documentation: https://picocomputer.github.io/hardware.html#step-5-pi-pico-firmware

**Learning Objectives:**
- [ ] Download latest RIA firmware (.uf2 file)
- [ ] Download latest VGA firmware (.uf2 file)
- [ ] Load RIA firmware:
  - [ ] Hold BOOTSEL button on Pico 2 W
  - [ ] Connect USB to computer
  - [ ] Copy RIA .uf2 file to mounted drive
  - [ ] Wait for LED to turn on
- [ ] Load VGA firmware:
  - [ ] Hold BOOTSEL button on Pico 2
  - [ ] Connect USB to computer
  - [ ] Copy VGA .uf2 file to mounted drive
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
