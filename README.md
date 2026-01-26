# RP6502 Learning Repository

This repository contains learning materials, documentation, and notes for understanding and working with the **RP6502 (Picocomputer 6502)** - a modern single-board computer built around the classic WDC W65C02S 8-bit microprocessor.

## What is RP6502?

The RP6502 is a tribute to the processor that launched a computing revolution. It features:
- **64KB System RAM** + **64KB Extended RAM**
- **VGA and HD Output** (via RP6502-VGA module)
- **Yamaha OPL2 FM Sound Generator**
- **Protected Operating System** (RP6502-OS)
- **USB** support for Keyboard, Mouse, Gamepads, and Storage
- **Bluetooth LE** for Keyboard, Mouse, and Gamepads
- **WiFi** with Hayes modem emulation
- **Real Time Clock** with DST and NTP

## Repository Contents

### 📚 Documentation
- **`notes/LEARNING_PLAN.md`** - Comprehensive learning plan with hardware-first approach
- **`notes/WORKSPACE_PROJECTS.md`** - Guide to all projects in the RP6502 workspace

### ⚙️ Configuration
- **`.cursor/rules/rp6502-documentation.mdc`** - Cursor AI rules for documentation resources

## Learning Approach

This repository follows a **hardware-first** learning approach:

1. **Phase 0**: Understand workspace projects
2. **Phase 1**: High-level chip overview and assembly
3. **Phase 2**: System testing and verification
4. **Phase 3**: Deep dive into chip interactions and firmware

## Resources

### Official Documentation
- **Picocomputer Documentation**: https://picocomputer.github.io/index.html
- **GitHub Organization**: https://github.com/picocomputer

### Key Components
- **U1 - W65C02S**: Main 8-bit CPU
- **U2 - RP6502-RIA (Pico 2 W)**: Interface adapter
- **U3 - AS6C1008**: 128KB SRAM
- **U4 - RP6502-VGA (Pico 2)**: Video adapter (optional)
- **U5 - W65C22S**: VIA chip for classic I/O

## Getting Started

1. Review the [Learning Plan](notes/LEARNING_PLAN.md)
2. Familiarize yourself with [Workspace Projects](notes/WORKSPACE_PROJECTS.md)
3. Follow the phases in order, checking off items as you complete them

## License

This repository contains learning materials and notes. Refer to individual project licenses for code and firmware.

## Links

- [Picocomputer Official Site](https://picocomputer.github.io/)
- [GitHub Discussions](https://github.com/picocomputer/community/discussions)
- [Discord Community](https://discord.gg/TC6X8kTr6d)
- [YouTube Tutorials](https://youtube.com/playlist?list=PLvCRDUYedILfHDoD57Yj8BAXNmNJLVM2r)
