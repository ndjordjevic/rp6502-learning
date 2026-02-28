# RP6502 Ep4 — Hello, World! and monitor under the hood

**Video:** Demo time: schematic released for the “8-Bit Expeditionary Force”; RAM and 6522 connected; using the Picocomputer from a serial terminal; Hello World in 6502 assembly; how the monitor works despite the 6502 being “shut down” (self-modifying fast load, PIO, DMA).  
**Context:** Breadboard prototype era. VGA Pico interconnects not yet wired (16-bit color, sprites, scrolling planned). Monitor and built-in ANSI terminal are in firmware; same behavior over serial (e.g. minicom) or USB keyboard + VGA. Use current docs/schematic for today’s PCB and addresses.

---

## Where things stood (post–Ep3)

- **RAM and 6522** are connected; “happy blinking light” left running for stability testing.
- **Not yet wired:** Pi Pico interconnects for **VGA** (future: 16-bit color, sprites, scrolling).
- **This episode:** Use the Picocomputer from a **serial terminal** (author uses **minicom** on Linux). Same functionality with **USB keyboard + VGA/HDMI** via the built-in color ANSI terminal (active unless a program switches to graphics mode).

---

## What is the “monitor”?

- In 6502 land, **monitor** usually means **software** that lets you **prepare memory and launch programs** — not the physical display.
- You type an **address** → it shows **memory contents**. You type an **address and data** → it **sets** memory. Same idea as front-panel switches, but from a terminal.
- On the Picocomputer, the monitor runs on the **Pi Pico** (not on the 6502). It’s the command prompt you see in minicom or on the VGA terminal.

---

## Memory map (quick recap for Hello World)

- **No ROM** — full **64K is RAM**. Anything you’d put in ROM can be loaded into RAM (no chip programmer needed).
- **Program placement:** Author puts the program at **$0200** (avoids zero page and stack; “don’t fight the expectations of the 6502”).
- **I/O at $FF00:** All non-RAM devices live here, **including the Pi Pico**.
- **Important addresses for this episode:**
  - **$FFEE** — write a byte here → **sent out the serial port** (USB to the host). This is how “Hello, World!” appears in the terminal.
  - **$FFEF** — write here → **ask the Pi Pico to shut down the 6502** and return control to the monitor (no 6502 halt instruction; this is the clean way to exit the program).

*(A memory map diagram in the repo — e.g. `sch/mem.tldr` — shows RAM, zero page, stack, 6522 VIA, expansion bus, and Pi Pico RIA with NMI/RESB/IRQ vectors at $FFFA–$FFFF.)*

---

## Hello, World! in 6502 assembly

- Author uses a **web assembler** (e.g. [mass:werk 6502 Assembler](https://www.masswerk.at/6502/assembler.html)) for “instant gratification” — no local toolchain for this demo.

### Directives and conventions

- **`* = $0200`** — start address; no machine code, assembler directive only.
- **`$` = hex**, no `$` = decimal. **`#` = immediate** (this is a value, not an address). Traditional 6502 syntax (pre-C style).

### The eight opcodes used

| Opcode | Meaning | Use in this program |
|--------|--------|----------------------|
| **LDX #$00** | Load X with immediate 0 | Loop index = 0 |
| **LDA text,X** | Load A from address `text + X` | A = text[X] (one character) |
| **STA $FFEE** | Store A at $FFEE | UART TX → character out serial |
| **INX** | X = X + 1 | Next character |
| **CMP #$00** | Compare A with 0 | Set flags for “end of string” |
| **BNE loop** | Branch if not equal | If A ≠ 0, continue loop |
| **STA $FFEF** | Store A at $FFEF | Shut down 6502, back to monitor |
| **NOP** / **BRA** | No-op; branch always | Used in the *fast load* template later |

### Data

- **`.ASCII "Hello, World!"`** — the string.
- **`.BYTE $0D $0A $00`** — CR, LF, and **null terminator**. The loop stops when it loads the null (A = 0).

### Program flow

1. **X = 0** (index into `text`).
2. **loop:** Load **text[X]** into A; **STA $FFEE** (send byte to serial); **INX**; **CMP #$00**; **BNE loop** if A ≠ 0.
3. When A = 0 (null): **STA $FFEF** (shut down 6502). Monitor has control again.

After pasting the assembled bytes into the monitor (e.g. at $0200) and running **JMP $0200**, “Hello, World!” appears in the terminal. The author then demonstrates **speed 8000** (8 MHz) and **speed 1** (1 kHz) and runs the program again at both speeds.

---

## How can the monitor work when the 6502 is “shut down”?

- The **6502 has no halt instruction**. The Pi Pico can **stop the 6502 clock** (“shut down the 6502”).
- The Pi Pico is only connected to **five address lines** (for the 32-byte register space), so it **cannot directly address all 64K** of 6502 RAM.
- **Mechanism:** The Pi Pico **starts the 6502** and runs a **short program** (on the order of **10 bytes**) that **copies memory**. That program runs in the Pico-exposed “register” space; the Pico uses it to read/write 6502 RAM via a carefully choreographed protocol.
- **Performance:** Copying across this “barrier” is **faster** than the 6502 copying within its own RAM — but the implementation is complex: **three CPUs, two PIOs, and a bunch of DMA** working in parallel for a single byte write. Execution is seamless from the user’s perspective.

---

## Under the hood: action loop and PIO

- A **third PIO program** was added to **trigger actions when certain 6502 addresses are accessed**. That PIO sends **messages** into a FIFO.
- **`ria_action_loop()`** (in `ria.c`) **consumes an entire ARM CPU** to meet **hard real-time** requirements. It runs a tight loop:
  - If the **action PIO’s RX FIFO** is non-empty, it reads a **message** (address + data).
  - It dispatches on an **action code** (derived from the address bits):
    - **Serial output:** e.g. action **0x0E** — write `data` to **UART0** (this is **$FFEE** → “sent out the serial port”).
    - **Shut down 6502:** action **0x0F** — call **`ria_halt()`** (this is **$FFEF**).
    - **RAM read/write:** e.g. **0x16** → **`ria_action_ram_write()`**, **0x17** → **`ria_action_ram_read()`**.
- So: when the 6502 does **STA $FFEE** or **STA $FFEF**, the PIO sees the access, sends a message, and the action loop does the UART write or halt. No 6502 code needed after that.

---

## Self-modifying “fast load” program (RAM write)

- To **write** 6502 RAM, the Pi Pico installs a **tiny 6502 program** in its **32-byte register space**. The 6502 runs this program; the program’s **instructions are modified** by the Pi Pico for each byte written.
- **Template idea:** A program that **stores one value into one address, then branches back** to repeat. The value and address are **replaced** by the Pi Pico for each byte; on the **last** byte, the **branch is short-circuited** so the 6502 stops and control returns.
- **Instructions used in the template:**
  - **LDA #$00** — load immediate (value will be replaced with `buf[i]`).
  - **STA $0000** — store absolute (address will be replaced with target address).
  - **BRA $FFF0** — branch back to start (on last iteration this is changed so the loop exits).
  - **NOP** — alignment so the program counter is in a **known place** before shutdown (important for deterministic 6502 startup later).
- **In C:** `ria_ram_write()` sets **REGS(0xFFF0)** … **REGS(0xFFF9)** with the right opcodes and operands; **REGS(0xFFF1)** = current byte to write, **REGS(0xFFF3)/REGS(0xFFF4)** = target address. When the 6502 **reads** the byte at the branch offset (e.g. 0xFFF6), the PIO triggers an action; **`ria_action_ram_write()`** runs: it updates the value and address in the template, and on the **last** byte sets the branch to **0x00** (short-circuit) and transitions state (e.g. `ria_state = done`, then **`ria_reset()`**).
- **Timing:** This must happen in **under ~200 ns** at 8 MHz (nanoseconds, not ms or µs). The author says the timing part “worked as expected.”

---

## 6502 startup “garbage” and deterministic shutdown

- **Datasheet omission:** For the **first seven clock cycles** after reset, the 6502 can output **garbage on the address bus**. That caused intermittent bugs.
- **Fix:** Ensure the 6502 is **shut down in a deterministic way**. The **NOP used for alignment** in the fast-load program is there so the **program counter is in a known location** before the Pi Pico stops the clock. That way, on next start, behavior is predictable.
- **Debugging notes:** The bug was intermittent and buried in parallelism; you can’t “step through” it. Only one or two things could be instrumented per run. The author also **missed a `volatile`**, so the compiler optimized away some instrumentation. **Noise on the breadboard ground** was another factor and took time to track down.

---

## Packaging and cases (author’s ask)

- The author is thinking about **how to package** the Picocomputer. A **fully 3D-printed case** is a last resort; something like an **extrusion with 3D-printed end caps** would be ideal. He hadn’t found anything nice and inexpensive for a board that size and asked viewers for suggestions (and whether they’d even want a case).

---

## Screenshots / visuals (from author or workspace)

All referenced images are in **[ep04-assets/](ep04-assets/)**.

- **Monitor in minicom:** HID mount (keyboard/mouse), memory view at an address (e.g. `8000`), then `8000 1 2 3` to set bytes, then `8000` again to show `01 02 03 00...`.
- **Memory map:** e.g. `sch/mem.tldr` — RAM (zero page, stack), I/O page with 6522 VIA, expansion bus, Pi Pico RIA and vectors at $FFFA–$FFFF.
- **Web assembler:** mass:werk 6502 Assembler — source panel with Hello World assembly; object code / listing after Assemble.
- **Hello World in monitor:** Memory dump at $0200 with the program bytes and “Hello, World!” ASCII at $0210; `jmp $200`; output “Hello, World!”; then `speed 8000` / `speed 1` and reruns.
- **PIO:** `ria.pio` — program `ria_action`; comments that it sends a FIFO message for every write and for hard-coded/variable reads.
- **ria.c:** `ria_action_loop()` — FIFO check, `addr`/`data` extraction, `switch` on action with cases for UART write (0x0E), halt (0x0F), ram_write (0x16), ram_read (0x17). Also `ria_ram_write()` with the self-modifying fast-load REGS setup and the commented 6502 template; and `ria_action_ram_write()` updating REGS(0xFFF1), address bytes, and branch on last byte.

---

## Summary table (addresses)

| Address   | Function |
|----------|----------|
| **$0200** | Suggested program start (e.g. Hello World). |
| **$FFEE** | Write byte → UART TX (serial out to host). |
| **$FFEF** | Write → Pi Pico shuts down 6502, monitor regains control. |
| **$FF00–…** | I/O space (6522 VIA, expansion, Pi Pico RIA). |

---

*Next (author): VGA working for the following video.*
