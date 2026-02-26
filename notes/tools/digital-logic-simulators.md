# Digital Logic Simulators

Survey of digital logic simulation tools relevant to the Picocomputer / rp6502 project, with notes on macOS support and practical fit.

---

## Context: what the rp6502 project needs from a simulator

- Model **74xx glue logic** (74AC00, 74AC02, 74HC30 and similar) at gate level.
- Understand **address decoding** — 16-bit 6502 address → chip select signals for RIA, VIA, RAM.
- **Propagation delay analysis** — the Ep3 breadboard revealed a 50 ns four-gate delay that killed 8 MHz operation; the simulator was used to identify and reason about the signal path.
- **Test harness** — set a specific address, see which device gets selected.
- Bonus: Verilog/VHDL export if the project ever moves to programmable logic.

---

## Tier 1 — Schematic / gate-level simulators

### Digital (H. Neemann) — **recommended for this project**

| | |
|---|---|
| **Type** | Digital-only schematic + simulator |
| **Cost** | Free, open source (GPL-3.0) |
| **Platform** | macOS, Windows, Linux (Java JAR) |
| **Status** | Active — v0.31, September 2024; 5 500+ GitHub stars |
| **Install** | `java -jar Digital.jar` (requires JRE 8+, no installation) |
| **Homepage** | https://github.com/hneemann/Digital |

**Why this tool is used in the rp6502 videos:**

- The author's **RIA.dig** schematic (address-decoding + test harness) opened in Digital. As of 2023 the `sch/` folder was **removed** from the rp6502 repo (commit "sch moved to docs"); it was not moved to another Picocomputer repo. You can recover it from git history: `git show fffccaa^:sch/RIA.dig > sch/RIA.dig`. See [RIA_DIG_GIT_HISTORY.md](RIA_DIG_GIT_HISTORY.md).
- Has the specific features needed: **propagation delays per gate**, **timing/waveform diagrams**, **test vectors** (the test harness in the schematic).
- Designed to fix known weaknesses in Logisim: better oscillation detection, correct master-slave flip-flop behavior, proper inverted inputs on RAM/ROM/counters.
- **Verilog export** — schematic → Verilog netlist in one click, useful if you ever want to implement the glue in a CPLD/FPGA.
- FSM editor with circuit synthesis, K-map / truth table analysis, ROM/RAM data editor.

**Limitations:**

- UI is functional but spartan (Java Swing aesthetic).
- Very large, deeply nested circuits (256+ addressable 32-bit cells) can become unresponsive — not a concern for 74xx glue.
- No analog signal modeling (purely digital/behavioral).
- Wire dragging is less fluid than Logisim; attributes dialog sometimes appears off-screen.
- File format is `.dig` (XML-like), not compatible with Logisim or other tools.
- Last major release 7 months ago (activity slightly lower than logisim-evolution).

**Bottom line:** The right tool for this exact project. Keep using it.

---

### Logisim-Evolution

| | |
|---|---|
| **Type** | Digital-only schematic + simulator |
| **Cost** | Free, open source (GPL-3.0) |
| **Platform** | macOS, Windows, Linux (Java); native packages for Intel + Apple Silicon |
| **Status** | Very active — v3.9.0; 6 800+ GitHub stars, commits ~weekly |
| **Install** | `brew install --cask logisim-evolution` |
| **Homepage** | https://github.com/logisim-evolution/logisim-evolution |

**Strengths over Digital:**

- More actively maintained (higher commit frequency, bigger community).
- **FPGA integration** — synthesize a circuit directly to a real FPGA board (Basys, DE-10, etc.).
- **VHDL and Verilog** integration (both import and export).
- **Timing diagram** generation built in.
- Better UI in some respects: smoother wiring, more polished component library.
- `brew install` makes macOS setup trivial.

**Weaknesses vs Digital:**

- Does **not** read `.dig` files — you would need to recreate the rp6502 schematics from scratch.
- Some known simulation accuracy issues (oscillation investigation, circuit-edit-during-simulation behavior).
- Requires Java 21 for v3.9.0 (more recent requirement).

**Bottom line:** The strongest free alternative and a natural "next step" if you outgrow Digital or want FPGA synthesis from a schematic. Not a drop-in replacement for this project's existing `.dig` files.

---

### Logicly (logic.ly)

| | |
|---|---|
| **Type** | Digital-only, education-oriented |
| **Cost** | Paid — $59 student / $599 classroom (one-time, no subscription) |
| **Platform** | macOS (native), Windows; Linux coming in v2.0 beta |
| **Status** | Active — v1.14 stable; v2.0 public beta (beta 6, May 2025) |
| **Install** | Download from https://logic.ly/purchase/ |
| **Homepage** | https://logic.ly/ |

**Strengths:**

- The most **polished, Mac-native** UI of any desktop digital logic tool.
- Drag-and-drop, clean visuals, real-time signal propagation animation.
- IEC and ANSI/IEEE symbol standards; auto-generated CSV truth tables.
- Step-by-step simulation controls, full undo/redo.
- Good for quickly producing clean circuit diagrams for documentation.

**Weaknesses:**

- **No propagation delay modeling** — signal propagation is conceptual, not timed.
- **No 74xx part library** — you build from primitives (AND, OR, NAND, etc.), not actual 74AC00 components.
- No Verilog/VHDL export.
- Paid (though not expensive for a single student license).
- Cannot open `.dig` files.

**Bottom line:** Best choice if you want to draw clean digital circuit diagrams for teaching or documentation. Not useful for the timing analysis done in the rp6502 videos.

---

### CircuitVerse (browser-based)

| | |
|---|---|
| **Type** | Digital-only schematic + simulator |
| **Cost** | Free |
| **Platform** | Any browser; no install |
| **Status** | Active; 1.3M+ circuits, 314K+ users |
| **Homepage** | https://circuitverse.org/ |

**Strengths:**

- Zero install; works immediately in a browser on macOS.
- Multi-bit buses, subcircuits, timing diagrams, truth table synthesis.
- Share circuits via URL; embed in web pages; educator classroom tools.
- Good for quick experiments and sharing without requiring anyone to install software.

**Weaknesses:**

- No propagation delay modeling.
- No 74xx-level part library.
- Cannot open `.dig` files.
- Depends on browser / internet connection.

**Bottom line:** Good for quick digital experiments and sharing. Not a replacement for Digital in this project.

---

## Tier 2 — Mixed analog + digital simulators

### iCircuit (Mac App Store)

| | |
|---|---|
| **Type** | Mixed analog + digital, real-time |
| **Cost** | Paid — check Mac App Store (approx. $15–20) |
| **Platform** | macOS (native), iOS, iPadOS |
| **Status** | Active; 4.7/5 on Mac App Store (239 ratings) |
| **Install** | Mac App Store |
| **Homepage** | https://icircuitapp.com/ |

**Strengths:**

- Native macOS app with excellent UI — feels like a proper Mac application.
- **Always-on real-time simulation** — change a value, the circuit responds instantly.
- Built-in oscilloscope and multimeter for probing any node.
- **38 7400-series digital parts** in addition to analog components (resistors, capacitors, MOSFETs, op-amps, etc.).
- Great for combined **analog + digital** experiments — e.g. seeing the RC filter on a power rail next to the logic that feeds it.
- iPad version useful for sketching on the go.

**Weaknesses:**

- No precise propagation delay modeling in the nanosecond range — simulates behavior, not exact timing.
- Not designed for pure gate-level address decoding work.
- No Verilog/VHDL export.
- Cannot open `.dig` files.
- Critics note lack of SPICE parametrized models compared to LTspice.

**Bottom line:** Best pick if you want the most **polished Mac-native experience** and occasionally work with analog circuits alongside digital. For pure 74xx timing analysis, Digital is more appropriate.

---

### LTspice (Analog Devices)

| | |
|---|---|
| **Type** | Full SPICE-level analog + digital behavioral simulator |
| **Cost** | Free (freeware, no node/component limits) |
| **Platform** | macOS 10.15+, Windows |
| **Status** | Active — maintained by Analog Devices |
| **Install** | Download from https://www.analog.com/en/resources/design-tools/ltspice-simulator.html |
| **Homepage** | https://www.analog.com/en/resources/design-tools/ltspice-simulator.html |

**Strengths:**

- Industry-standard SPICE simulator — used by professional engineers.
- **Built-in 74HC gate symbols** (74HC00, 74HC02, 74HC04, etc.) — can simulate actual 74xx timing with SPICE transistor models.
- **True signal integrity simulation** — you can model supply noise, cross-talk, transmission line effects.
- Useful if you want to understand *why* the 74HC logic was slow vs. 74AC at transistor/physics level.
- Free, no restrictions.

**Weaknesses:**

- Steep learning curve; SPICE netlists, not schematic-first like Digital.
- Importing vendor SPICE models (e.g. TI SN74HC240) on macOS is fiddly.
- Very slow for large digital-only simulations (it simulates transistors, not logic levels).
- Overkill for address decoding logic testing.
- No Verilog/VHDL.

**Bottom line:** Pull this out when you need to understand a **real analog signal** issue (supply ringing, edge rates, input thresholds). Not the right tool for "does this address select the right chip?"

---

## Tier 3 — HDL-based (text, code-as-design)

These require writing **Verilog** or **VHDL** rather than drawing schematics, but they are powerful and free on macOS.

### Icarus Verilog + GTKWave

| | |
|---|---|
| **Type** | Verilog simulator + waveform viewer |
| **Cost** | Free, open source |
| **Platform** | macOS (Homebrew), Windows, Linux |
| **Install** | `brew install icarus-verilog && brew install --cask gtkwave` |
| **Homepage** | https://steveicarus.github.io/iverilog/ / https://gtkwave.sourceforge.net/ |

- Write the glue logic as Verilog, write a testbench, compile with `iverilog`, simulate with `vvp`, view waveforms in GTKWave.
- **Exact propagation delays** via `#delay` expressions.
- Can import FPGA synthesis results and standard cell timing models.
- Significant step up in workflow complexity — not schematic-based.
- **Good path from Digital:** Digital can export a Verilog netlist of your schematic, which you can then simulate or synthesize.

### GHDL + GTKWave

Same idea as above but for **VHDL**. Also available via Homebrew: `brew install ghdl`.

---

## Quick comparison table

| Tool | Cost | macOS | Analog | Timing delays | 74xx parts | Verilog/VHDL | Open `.dig` | Best for |
|---|---|---|---|---|---|---|---|---|
| **Digital** | Free | ✓ (Java) | ✗ | ✓ ns-level | ✓ | Export only | ✓ native | rp6502 glue, address decode |
| **Logisim-Evolution** | Free | ✓ native | ✗ | Limited | Partial | ✓ import+export | ✗ | Larger CPU projects, FPGA |
| **Logicly** | $59+ | ✓ native | ✗ | ✗ | ✗ (primitives) | ✗ | ✗ | Clean diagrams, teaching |
| **CircuitVerse** | Free | Browser | ✗ | ✗ | Partial | ✗ | ✗ | Quick experiments, sharing |
| **iCircuit** | ~$15 | ✓ native | ✓ | ✗ | 38 types | ✗ | ✗ | Mixed analog+digital, Mac feel |
| **LTspice** | Free | ✓ native | ✓ | ✓ SPICE | 74HC built-in | ✗ | ✗ | Signal integrity, analog |
| **Icarus Verilog** | Free | ✓ Homebrew | ✗ | ✓ | Via models | ✓ Verilog | ✗ | HDL design, FPGA flow |

---

## Recommendation for this project

1. **Primary:** Keep using **Digital** — it opens the rp6502 author's `.dig` files directly, supports the timing analysis shown in the video, and is the right tool for 74xx gate-level work.

2. **Second tool (optional):** Install **Logisim-Evolution** (`brew install --cask logisim-evolution`) for when you want to model a larger design (CPU, bus controller) with FPGA synthesis or VHDL output.

3. **If you ever want to see the analog side** (supply noise on the clock, RC on the reset line, 74HC vs. 74AC rise times): **LTspice** is free and the most rigorous option.

4. **When you want to go beyond schematic drawing** into real HDL: start with Digital's Verilog export to get a working netlist, then refine in **Icarus Verilog + GTKWave**.

---

## Source

- H. Neemann Digital: https://github.com/hneemann/Digital
- Logisim-Evolution: https://github.com/logisim-evolution/logisim-evolution
- Logicly: https://logic.ly/
- CircuitVerse: https://circuitverse.org/
- iCircuit: https://icircuitapp.com/
- LTspice: https://www.analog.com/en/resources/design-tools/ltspice-simulator.html
- Icarus Verilog: https://steveicarus.github.io/iverilog/
- GTKWave: https://gtkwave.sourceforge.net/
- LibHunt comparison Digital vs logisim-evolution: https://www.libhunt.com/compare-Digital-vs-logisim-evolution
