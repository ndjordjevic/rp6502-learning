# Intel HEX format — what it is and why it’s used

A short guide to “hex line records” (Intel HEX): what they are, why they exist, and how to read them. No prior knowledge assumed.

---

## The problem: getting code into the machine

You write a program on your PC. The 6502 runs on the RP6502 and only understands bytes in memory. You need to:

- **Transfer** those bytes from PC to the board.
- **Put them in the right places** (e.g. code at `$C000`, reset vector at `$FFFC`).
- **Do it in a way that’s robust** (no random corruption, clear “this is data for address X”).

Raw binary (a single `.bin` file) is just a stream of bytes. It has **no addresses inside it**. So you have to remember “this file is for address $C000” and tell every tool and every document. If you load it at the wrong address, the program does the wrong thing and it’s hard to see why.

**Hex line records** solve this by **embedding the address (and other info) in the same file as the data**. Each line is one “record”: “here are N bytes, put them at this address.” Tools (and humans) can read the file and know exactly where every byte goes.

---

## What is “Intel HEX”?

**Intel HEX** (often just “hex file” or “.hex”) is a **text file format** that describes memory contents. The file is made of lines of **hex digits** (and a colon). Each line is one **record**.

- **Text, not binary** — You can open it in a text editor, paste it in a terminal, or send it over a serial connection. No need for a “binary mode.”
- **Self-describing** — Each line says how many bytes and at which address; you don’t need a separate “load address” for the whole file.
- **Checksummed** — Every line has a checksum so the loader can detect corrupted or truncated data.
- **Standard** — Many assemblers, compilers, and flash/monitor tools produce or accept it.

So “hex line records” = “those text lines that describe memory in Intel HEX format.”

---

## How one line is built

Every record is a single line and looks like this:

```
:10200000A901850060000000000000000000000000E3
```

Break it into **pairs of hex digits** (each pair = one byte in hex):

| Part        | Example | Meaning |
|------------|--------|--------|
| Start      | `:`     | Every line starts with a colon. |
| Byte count | `10`    | How many **data** bytes follow (here 16). |
| Address    | `2000`  | 16‑bit address where this data goes (here $2000). |
| Record type| `00`    | 0 = data; 1 = end of file; 2/3/4/5 = special (see below). |
| Data       | `A901850060...` | The bytes (2 hex digits per byte). |
| Checksum   | `E3`    | One byte: such that (byte_count + addr + type + data + checksum) & 0xFF = 0. |

So this line means: “Load these 16 bytes at address $2000.” The loader (or the RP6502 monitor) parses the line, checks the checksum, and writes those bytes to RAM (or flash) at the given address.

---

## Record types (what the “type” byte means)

The **record type** (two hex digits after the address) tells the loader what to do with the rest of the line.

| Type | Name        | Meaning |
|------|-------------|--------|
| `00` | Data        | “Here are N bytes; store them at the given address.” This is the normal “load data” record. |
| `01` | End of file | “No more records.” Often the last line in the file. |
| `02` | Extended segment address | Changes how following addresses are interpreted (segment base). Used for addresses above 64K in a segment-based way. |
| `03` | Start segment address | “When you’re done loading, the 16‑bit start (e.g. reset) address is in this record.” Some tools use this for the 6502 reset vector. |
| `04` | Extended linear address | Upper 16 bits of a 32‑bit address for following data records. Used for large memories. |
| `05` | Start linear address | 32‑bit start address. Can be used similarly to type 03. |

For simple 6502 use you mostly see **type 00** (data) and **type 01** (EOF). Types 02/03/04/05 matter when you have more than 64K or when the toolchain encodes “start address” or “reset vector” in the hex file.

---

## Why use HEX instead of raw binary?

| Aspect | Raw binary (.bin) | Intel HEX (.hex) |
|--------|-------------------|------------------|
| Addresses | Not in the file; you must remember or pass them separately. | In every line; file is self-describing. |
| Transfer | Needs “binary” mode (no newline conversion, no stripping). | Plain text; safe over terminal, email, serial. |
| Partial load | Awkward (you must track offset and length). | Natural: each line is one block; you can have gaps. |
| Corruption | One flipped bit is invisible. | Checksum per line can detect many errors. |
| Debugging | “What’s at $C000?” — you have to correlate with a listing. | You can open the .hex and see “this line is for $C000.” |

So hex line records are **useful** because they combine **data**, **address**, and **sanity check** in one text format that’s easy to generate (assembler/compiler), move (copy/paste, serial), and load (monitor, flash tool) without extra “load at 0xC000” configuration.

---

## Where you see them

- **Assemblers / compilers** — Often have an option like “output Intel HEX” (e.g. `-f intel-hex` or “Intel HEX” in the IDE). The build produces a `.hex` file.
- **Flash / programmer tools** — Many load `.hex` and program microcontrollers or ROM; the format is standard.
- **RP6502 monitor** — You can paste hex lines (with a `]` prefix) and the monitor parses them and writes the bytes to the given addresses. So “hex line records” are directly useful for getting code from your PC into the 6502’s memory.

---

## Small example end-to-end

**1. Program (conceptually)**  
“Put at $C000: LDA #1, STA $00, RTS. Set reset vector at $FFFC to $C000.”

**2. As bytes**  
- At $C000: `A9 01 85 00 60` (and maybe padding).  
- At $FFFC: `00 C0` (low byte, high byte of $C000).

**3. As Intel HEX (two records)**  
```text
:05C00000A90185006028
:02FFFC0000C0B9
```

- First line: 5 bytes, address $C000, type 00, data `A9 01 85 00 60`, then checksum.  
- Second line: 2 bytes, address $FFFC, type 00, data `00 C0`, then checksum.

**4. In the monitor**  
You’d paste something like:  
`]:05C00000A90185006028`  
`]:02FFFC0000C0B9`  
(With the `]` the monitor expects.) The monitor writes the bytes and (for the RP6502) can use type 03/05 if present to set the reset vector.

So: **hex line records are useful** because they let one text file (or a few pasted lines) describe exactly what bytes go where, with checksums and no separate “load address” guesswork.

---

## Summary

- **Hex line records** = lines in **Intel HEX** format: text lines starting with `:`, each saying “here are N bytes at this address” (plus type and checksum).
- **Why they’re useful**: addresses are in the file, transfer is text-safe, partial loads and gaps are natural, and checksums help catch errors.
- **Where they come from**: assemblers, compilers, and sometimes hand-written or script-generated.
- **Where they’re used**: flash tools, programmers, and the RP6502 monitor — so you can move code from your PC to the board in a standard, readable way.

For more detail on how the RP6502 monitor accepts these lines, see **MONITOR.md** (section “Intel HEX records”).
