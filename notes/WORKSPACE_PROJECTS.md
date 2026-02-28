# RP6502 Workspace Projects Overview

This document briefly describes each project in the RP6502 workspace and their purpose in the learning journey.

## Core RP6502 Projects

### 1. **rp6502** - Main Firmware Repository
**Path**: `/Users/nenaddjordjevic/CProjects/rp6502/`

**Purpose**: Source code for the RP6502 firmware modules
- **RP6502-RIA**: Interface adapter firmware (runs on Pico 2 W)
- **RP6502-RIA-W**: Wireless features variant
- **RP6502-VGA**: Video graphics adapter firmware (runs on Pico 2)

**Key Contents**:
- `src/ria/` - RIA firmware (bus interface, USB, audio, networking, storage)
- `src/vga/` - VGA firmware (video modes, scanvideo, terminal)
- `src/emu8950/` - OPL2 FM synthesis emulation
- `src/fatfs/` - FAT32 filesystem implementation

**Use For**: Understanding how the firmware works, building custom firmware, studying PIO programs, bus interfaces

---

### 2. **examples** - Example Programs
**Path**: `/Users/nenaddjordjevic/CProjects/examples/`

**Purpose**: Example 6502 programs demonstrating RP6502 features

**Key Examples**:
- `mode1.c`, `mode2.c`, `mode3.c` - Video mode demonstrations
- `paint.c` - Drawing program
- `gamepad.c` - Gamepad input handling
- `rtc.c` - Real-time clock usage
- `mandelbrot.c` - Graphics demos
- `ezpsg.c` - Audio examples

**Use For**: Learning how to program the RP6502, understanding API usage, starting your own projects

---

### 3. **schematic** - Hardware Design Files
**Path**: `/Users/nenaddjordjevic/CProjects/schematic/`

**Purpose**: KiCad schematic and PCB design files for the RP6502

**Key Files**:
- `rp6502.kicad_sch` - Main schematic
- `rp6502.kicad_pcb` - PCB layout
- `rp6502.assembly/` - Assembly instructions and photos
- `rp6502.acrylic/` - Case design files (OpenSCAD)

**Use For**: Understanding hardware design, component placement, signal routing, building the computer

---

### 4. **picocomputer.github.io** - RP6502 Documentation Source
**Path**: `/Users/nenaddjordjevic/CProjects/picocomputer.github.io/`

**Purpose**: Source repository for the official Picocomputer 6502 documentation website (https://picocomputer.github.io/)

**Key Contents**:
- `docs/source/` - Sphinx/RST source files (index.rst, hardware.rst, ria.rst, ria_w.rst, vga.rst, os.rst)
- `docs/source/_static/` - Static assets (schematic PDF, gerbers, BOM CSVs, assembly zip; Rev B uses `2026-01-26-rp6502.pdf`)

**Use For**: Reading documentation source, offline access to RP6502 docs, contributing to documentation, accessing schematic PDF and BOM files locally

---

## Development Tools & Templates

### 5. **vscode-cc65** - CC65 Development Template
**Path**: `/Users/nenaddjordjevic/CProjects/vscode-cc65/`

**Purpose**: VS Code project template for developing 6502 software using CC65 compiler

**Features**:
- Pre-configured CMake build system
- VS Code launch configurations
- Example C and assembly code
- Integration with rp6502.py upload tool

**Use For**: Starting new 6502 projects, learning CC65 development workflow

---

### 6. **vscode-llvm-mos** - LLVM-MOS Development Template
**Path**: `/Users/nenaddjordjevic/CProjects/vscode-llvm-mos/`

**Purpose**: VS Code project template for developing 6502 software using LLVM-MOS compiler

**Features**:
- Pre-configured CMake build system
- VS Code launch configurations
- Example C code
- LLVM-MOS toolchain integration

**Use For**: Starting new 6502 projects with modern LLVM toolchain

---

### 7. **ehbasic** - Enhanced 6502 BASIC
**Path**: `/Users/nenaddjordjevic/CProjects/ehbasic/`

**Purpose**: Enhanced 6502 BASIC interpreter ported to RP6502

**Features**:
- Full BASIC interpreter
- RP6502-specific extensions
- Can be loaded as a ROM

**Use For**: Running BASIC programs on RP6502, learning BASIC programming for 6502

---

### 8. **adventure** - Colossal Cave Adventure
**Path**: `/Users/nenaddjordjevic/CProjects/adventure/`

**Purpose**: Classic Colossal Cave Adventure (Will Crowther / Don Woods) ported to RP6502

**Key Contents**:
- `src/` - Game source
- `tools/` - Build/development tools
- CMake build; produces `advent4` binary (and optional `advent4.txt` etc.) for loading from USB
- The `.txt` files (`advent1.txt`–`advent4.txt`) are **game data** (cave descriptions, vocabulary, objects). The `advent4` binary loads `advent4.txt` at runtime; the others are alternate datasets from the classic Adventure family.

**Use For**: Running a full game from USB (`load advent4` in monitor), building from source with cc65/CMake; see notes/videos/ep10-assembly-on-circuit-board.md (program from disk)

---

## Compiler & SDK Projects

### 9. **llvm-mos-sdk** - LLVM-MOS SDK
**Path**: `/Users/nenaddjordjevic/CProjects/llvm-mos-sdk/`

**Purpose**: LLVM-based compiler toolchain and platform libraries for 6502 systems

**Key Features**:
- Modern C/C++ compiler for 6502
- Platform support for RP6502 (in `mos-platform/rp6502/`)
- Standard library implementations
- Examples for various 6502 platforms

**Use For**: Using modern C/C++ features, cross-platform 6502 development, advanced compiler features

---

### 10. **cc65** - CC65 Compiler and Libraries
**Path**: `/Users/nenaddjordjevic/CProjects/cc65/`

**Purpose**: C and assembly cross-compiler and runtime library for 6502/65C02 systems

**Documentation**: https://cc65.github.io/doc/rp6502.html — Picocomputer 6502–specific cc65 info (binary format, memory layout, `rp6502.h` / `rp6502.inc`)

**Key Features**:
- C compiler (cc65), assembler (ca65), linker (ld65)
- C runtime library and platform-specific libraries
- RP6502 support (used by vscode-cc65 template and ehbasic)
- Long-standing 6502 toolchain

**Use For**: Compiling C and assembly for RP6502, reference for cc65-specific code, building ehbasic and CC65-based examples

---

## Raspberry Pi Pico Reference Projects

### 11. **pico-sdk** - Official Pico SDK
**Path**: `/Users/nenaddjordjevic/CProjects/pico-sdk/`

**Purpose**: Official Raspberry Pi Pico SDK - C/C++ libraries and APIs for RP2040

**Key Contents**:
- Hardware abstraction layer (HAL)
- PIO (Programmable I/O) support
- USB, GPIO, timers, DMA APIs
- Documentation and examples

**Use For**: Understanding Pico SDK APIs, low-level hardware access, PIO programming, reference documentation

---

### 12. **pico-examples** - Official Pico Examples
**Path**: `/Users/nenaddjordjevic/CProjects/pico-examples/`

**Purpose**: Official example programs for Raspberry Pi Pico

**Key Examples**:
- GPIO, ADC, PWM, UART, SPI, I2C
- USB device and host examples
- Multicore programming
- PIO examples
- WiFi and Bluetooth (Pico W)

**Use For**: Learning Pico SDK usage, understanding hardware interfaces, reference implementations

---

### 13. **pico-extras** - Additional Pico Libraries
**Path**: `/Users/nenaddjordjevic/CProjects/pico-extras/`

**Purpose**: Additional libraries not yet in the main SDK

**Key Libraries**:
- `pico_scanvideo` - Video output (used by RP6502-VGA!)
- `pico_audio` - Audio output
- `pico_sd_card` - SD card interface
- Various other experimental libraries

**Use For**: Understanding video generation (scanvideo), audio processing, advanced Pico features

---

### 14. **Hunter-Adams-RP2040-Demos** - Educational Demos
**Path**: `/Users/nenaddjordjevic/CProjects/Hunter-Adams-RP2040-Demos/`

**Purpose**: Collection of RP2040 examples from Cornell ECE4760 course

**Key Demos**:
- VGA graphics examples
- Audio synthesis
- Networking (WiFi, Bluetooth)
- Sensor interfaces
- Control systems (PID)
- Signal processing

**Use For**: Learning advanced Pico techniques, VGA programming, audio synthesis, educational examples

---

### 15. **documentation** - Raspberry Pi Documentation
**Path**: `/Users/nenaddjordjevic/CProjects/documentation/`

**Purpose**: Source for Raspberry Pi official documentation website

**Use For**: Reference documentation, understanding official documentation structure

---

## Learning Path Integration

### Phase 1-2: Hardware & Assembly
- **picocomputer.github.io/** - Read official docs (hardware, schematic PDF)
- **schematic/** - Study the hardware design
- **rp6502/** - Understand firmware requirements

### Phase 3: Testing
- **examples/** - Try example programs
- **adventure/** - Build and run Colossal Cave from USB (`load advent4`)
- **ehbasic/** - Experiment with BASIC

### Phase 4-5: Deep Dive
- **rp6502/** - Study firmware source code
- **pico-sdk/** - Understand underlying Pico APIs
- **pico-extras/** - Learn about scanvideo (used by VGA)

### Phase 6-7: Development
- **vscode-cc65/** or **vscode-llvm-mos/** - Start your own projects
- **examples/** - Reference implementations
- **pico-examples/** - Learn Pico SDK patterns

### Phase 8: Advanced
- **Hunter-Adams-RP2040-Demos/** - Advanced techniques
- **llvm-mos-sdk/** - Modern compiler features
- **rp6502/** - Custom firmware development

---

## Quick Reference

| Project | Primary Use | When to Study |
|---------|------------|---------------|
| **rp6502** | Firmware source | Phase 4-5, 8 |
| **examples** | Learning to program | Phase 3, 6-7 |
| **adventure** | Colossal Cave (load from USB) | Phase 3 |
| **picocomputer.github.io** | RP6502 docs source | Phase 1-2 |
| **schematic** | Hardware understanding | Phase 1-2 |
| **vscode-cc65** | Development setup | Phase 6-7 |
| **vscode-llvm-mos** | Development setup | Phase 6-7 |
| **ehbasic** | BASIC programming | Phase 3, 6-7 |
| **llvm-mos-sdk** | Modern compiler | Phase 7-8 |
| **cc65** | C/asm compiler | Phase 6-7 |
| **pico-sdk** | Pico API reference | Phase 4-5 |
| **pico-examples** | Pico learning | Phase 4-5 |
| **pico-extras** | Video/audio | Phase 5 |
| **documentation** | Raspberry Pi docs source | Reference |
| **Hunter-Adams** | Advanced techniques | Phase 8 |

---

## Notes

- Most projects are git repositories - check for updates regularly
- Some projects have submodules - run `git submodule update --init` when needed
- The **rp6502** project is the most important for understanding the system
- **examples** is the best starting point for learning to program
- **pico-sdk** and **pico-examples** are essential references for Pico development
