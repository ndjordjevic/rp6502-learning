# FPGA learning goal (future)

**Reminder:** One day I’d like to learn FPGA and maybe create a Picocomputer in FPGA.

- **Why it makes sense:** The Picocomputer’s glue logic (address decode, RIA/VIA/RAM selects) is already designed in Digital and could be exported to Verilog; the 6502 core has open-source implementations (e.g. in Verilog/VHDL). Putting both on an FPGA would give a single-chip “Picocomputer” without discrete 74xx and a physical 6502 — good learning and a neat project.

## Do I need to simulate the RP Pico in the FPGA?

**No.** The 6502 only sees RIA and VGA as a register map and bus timing. You don’t simulate the RP2040; you implement logic that has the **same behavior at the 6502 bus** (same chip selects, read/write timing, register layout). Simulating a full RP Pico (dual M0+, PIO, USB, etc.) in Verilog would be huge and there’s no official open-source RTL — and it’s unnecessary.

## Practical paths

1. **Hybrid first:** 6502 core + RAM + VIA + glue in the FPGA; keep **real** Pi Picos for RIA and VGA. No Pico simulation at all.
2. **Full FPGA later (optional):** Replace RIA/VGA with FPGA blocks that expose the **same register set** — e.g. a soft CPU (RISC‑V, small core) + HDL peripherals, or pure state machines. Still no “Pico emulator”; just compatible bus/register behavior.

- **Links when ready:** Digital’s Verilog export, Logisim-Evolution’s FPGA flow, open 6502 cores, cheap boards (e.g. ICE40, Lattice).

*(Stub — expand when I start.)*
