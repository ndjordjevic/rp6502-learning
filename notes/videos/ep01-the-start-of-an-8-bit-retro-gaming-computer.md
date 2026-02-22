# RP6502 Ep1 — The start of an 8-bit Retro Gaming Computer

**Video:** First episode in the author's series on building the Picocomputer.  
**Context:** This describes an **early prototype** (breadboard, two Picos, different part count). The current Picocomputer (PCB, RIA + VGA, W65C02S, AS6C1008, VIA, glue logic) has evolved from this — use official docs and schematic for today's design.

---

## Why a Pico + 6502?

- **Raspberry Pi Pico** (original, $4): no Linux/desktop; has **OTG USB** and can generate a **clean VGA signal**.
- A few parts away from a full computer comparable to 1980s machines.
- Idea: attach it to a **retro processor (6502)** and use the Pico to replace a lot of discrete logic.

## What the Pico Replaces (Early Design)

With a Pi Pico in the design, you no longer need:

- Clock generator  
- Reset timers  
- Address logic  
- GPIO chips  
- ROMs  
- Chip programmers  

**Programming the Pico:** UF2 = copy a file over USB; no special software, just the OS copy command.

**Why keep the 6502?** Without it you wouldn't have a **6502 expansion bus** — the expansion bus is a core part of the design.

## Early Hardware (Breadboard Prototype)

- **Parts:** 1× Pi Pico, 1× 6502, **64K RAM**, **12 glue chips**.
- All through-hole on the video breadboard; parts also available in SMT.
- **Two Picos in the video:** one is the main board; the second is a **debug probe + console port** (not strictly necessary), connected to the dev machine over USB (which also powers everything).
- **VGA:** End of a VGA-to-HDMI converter; Pico provides accurate VGA timing so HDMI has no added frame buffer lag, even on cheap adapters.

## Features (Target / Early Vision)

- VGA video  
- **PWM audio** — audio from a Pico GPIO pin driven with pulse-width modulation (duty cycle = sample value); a simple low-pass filter turns it into a voltage for speaker/headphone. The current Picocomputer uses PSG/OPL2 in firmware instead.  
- **Expansion slots**  
- USB: keyboards, mice, joysticks, mass storage  

## USB Host

- **“Real gem”:** USB host controller — no PS/2 keyboards or SD cards; plug ordinary USB devices into a hub.
- Hub is nothing special; may need an adapter cable to fit the Pico.
- Tests shown: keyboard (recognized, typing works), mouse (events to stdout: left/right click, movement), PlayStation 4 controller (analog data 8-bit, easy for 6502), USB flash drive (SD card reader seen too; file reading not tried yet but expected to work).

## Monitor (Firmware / Serial)

- **Monitor:** Classic concept — software to inspect and modify memory (replaced panels of lights and switches).
- **Difference:** On this design the monitor runs on the **DMA controller** (Pico), not the main CPU.
- **Serial port** on the Pico gives access to the monitor at all times (e.g. in **minicom**).
- Primary function: inspect and modify memory; useful while bringing up the DMA controller.
- Typing an address will show memory contents (“it will later” — not fully working in the episode).

## Two Ways to Access the Monitor

Both the Monitor (command interpreter) and the built-in terminal are written by the author as part of the Picocomputer firmware; the Pico does not provide them out of the box.

1. **Serial port** (minicom, etc.).  
2. **USB keyboard + VGA display** — terminal emulator on the Pico (claimed as first color ANSI terminal emulator for the Pico).  
- **Scroll Lock** toggles this built-in terminal on/off.  
- This terminal is **not** the main computer’s primary I/O; it’s a convenient built-in terminal.

## Video System (Early)

- **Mockup shown:** Low-res **40-column mode** with Commodore “Pensky” font.
- **Planned:** Graphics, sprites, parallax scrolling; character mode used to keep things visually interesting during development.
- **Resolutions:** High-res **640×480**, low-res **320×240**; either can be letterboxed to 16:9 and output as **720p**.
- Works with old (e.g. IBM 8513) or modern widescreen displays.  
- **No TV support** (could be added but not planned; other 6502 retro video hardware already covers TVs — “that’s what the expansion slots are for”).

## Memory Layout (Early)

- **Video memory:** Its own **64K**, separate from the 6502’s 64K.
- **DMA (future):** Intended to move data between USB storage, video memory, and 6502 memory.
- At the time of the video, DMA was not working; video memory could be **manually modified via the monitor** (address and data worked out for “Hello world”).
- Same technique could be used to load a 6502 program into RAM.

## Takeaways for Later Design (Current Picocomputer)

- **Evolution:** Current design uses **two Picos** (RIA + VGA) on a PCB, **W65C02S**, **AS6C1008** (128K SRAM), **VIA (W65C22S)**, and fewer glue chips (e.g. 74AC00, 74AC02, 74HC30). No “12 glue chips” in the same form.
- **RIA** = Interface Adapter (replaces “DMA controller” role, provides USB host, UART, OS, XRAM, etc.).
- **VGA Pico** = dedicated video (and later PIX bus to RIA).
- **Monitor** today: on RIA, available over serial (UART) or USB CDC; same idea — inspect/modify memory, load programs.
- **Expansion:** Still a goal; current board has **VIA** at $FFD0–$FFDF for classic 6502 expansion.

---

## Source

- Transcript: user-provided (RP6502 Ep1 — “The start of an 8-bit Retro Gaming Computer”).
- Author’s design notes: GitHub repository linked in the video description.
- For current hardware and firmware: https://picocomputer.github.io/ and the `rp6502` / `picocomputer.github.io` repos.
