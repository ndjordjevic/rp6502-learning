# UART on the Raspberry Pi Pico (RP2040) and RIA

Comprehensive guide to UART for learning the RIA UART registers ($FFE0–$FFE2).  
**Sources:** Quadros D. "Knowing the RP2040" (2022), "Programming The Raspberry Pi Pico/W In C, Third Edition", "RP2040 Assembly Language Programming", RP2040 Datasheet (Section 4.2), Pico C SDK Reference (Section 5.1.30), picocomputer.github.io/ria.html.

---

## 1. What is UART?

**UART** = **Universal Asynchronous Receiver/Transmitter**. It is one of the oldest forms of serial communication. The key word is **asynchronous**: there is no shared clock wire between sender and receiver. Both sides agree on a **baud rate** (bits per second) in advance, and the data is framed with start/stop bits so the receiver can synchronize.

> "Asynchronous serial communication is one of the oldest form of serial communication. Bits are sent serially (one after the other) over a wire with no common clock signal to synchronize the receiver to the transmitter." — *Knowing the RP2040*, p.168

### Physical wiring

A UART link needs **two data wires** (plus ground):

- **TX** (Transmit) on device A → **RX** (Receive) on device B  
- **RX** on device A → **TX** on device B  

**Rule:** TX of one side connects to RX of the other.

---

## 2. UART Framing (How Bytes Travel on the Wire)

When the line is idle, it sits **HIGH** (logic 1). A single character is sent as a **frame**:

```
Idle ──┐    ┌──┬──┬──┬──┬──┬──┬──┬──┬──┐   ┌── Idle
       │    │D0│D1│D2│D3│D4│D5│D6│D7│  │   │
       └────┘                          └───┘
       Start  Data bits (LSB first)    Parity Stop
       bit                             (opt)  bit
```

- **Start bit:** Line goes LOW for one bit period. Tells the receiver "a frame is starting."
- **Data bits:** 5, 6, 7, or **8 bits** (8 is standard). **LSB first** (least significant bit sent first).
- **Parity bit (optional):** Error detection. Even parity = total number of 1s (data + parity) is even. Odd parity = total is odd. Most systems use **no parity**.
- **Stop bit(s):** Line returns HIGH for 1 or 2 bit periods, signaling end of frame. Modern: **1 stop bit**.
- **Break:** Special condition where the line is held LOW for an entire frame or more.

**Simple 8N1 example — sending `'A'` (0x41 = 65):**  
Binary 0100 0001, LSB first on wire → data bits: `1 0 0 0 0 0 1 0`. One full frame:

```
Line:  ...1  0  1 0 0 0 0 0 1 0  1  ...
           │  └───── 8 data ─────┘  │
           │     (LSB first)        │
        start                    stop
```

So: start (0) + 8 data bits + stop (1) = 10 bits per character.

> "Individual words (typically a byte) are sent as a 'frame' composed of: start bit, data bits, parity bit, stop bit(s)." — *Knowing the RP2040*, p.173–174

### Common shorthand: 8N1

**8N1** = 8 data bits, No parity, 1 stop bit. This is the default for nearly everything, including the RP6502 RIA (115200 8N1).

---

## 3. Baud Rate

**Baud rate** = number of bits per second on the wire. Common values:

| Baud Rate | Use Case |
|-----------|----------|
| 9600 | Slow, legacy devices |
| 115200 | Default for Pico, RP6502, most modern embedded |
| 921600 | High-speed |

Both sides **must** use the same baud rate.

### How the RP2040 generates baud rate

The RP2040 uses a **fractional baud rate divider**: 16-bit integer + 6-bit fractional part (22 bits total). It divides `clk_peri` (typically 125 MHz) to produce an internal **Baud16** clock at 16× the baud rate. Sampling at 16× lets the receiver find the center of each bit.

**Example** (*Knowing the RP2040*, p.175): 9600 baud with `clk_peri` = 125 MHz:

- Divisor = 125,000,000 / (16 × 9600) = **813.802**
- Integer part (IBRD) = 813
- Fractional part = int(0.802 × 64 + 0.5) = **51**
- Actual baud = 125,000,000 / (16 × 813.796875) = **9600.06** (tiny error)

---

## 4. RP2040 UART Hardware

The RP2040 has **two UARTs**: `uart0` and `uart1`. They are based on the **ARM PL011** design.

### Features (*Knowing the RP2040*, p.170)

- **32-entry FIFOs** for transmit and receive
- 5–8 data bits, 1–2 stop bits, parity none/even/odd
- Hardware flow control (RTS/CTS)
- Break detection and generation
- Interrupt and DMA support
- Programmable baud rate generator

### Key registers (RP2040 Datasheet, Section 4.2)

| Register | Offset | Purpose |
|----------|--------|---------|
| UARTDR | 0x000 | **Data register** — write to transmit, read to receive |
| UARTFR | 0x018 | **Flag register** — status bits (TXFF, RXFE, BUSY, etc.) |
| UARTIBRD | 0x024 | Integer baud rate divisor |
| UARTFBRD | 0x028 | Fractional baud rate divisor |
| UARTLCR_H | 0x02C | Line control (word length, parity, stop bits, FIFO enable) |
| UARTCR | 0x030 | Control register (enable UART, TX, RX) |
| UARTIMSC | 0x038 | Interrupt mask |
| UARTRIS | 0x03C | Raw interrupt status |
| UARTMIS | 0x040 | Masked interrupt status |
| UARTICR | 0x044 | Interrupt clear |
| UARTDMACR | 0x048 | DMA control |

### Flag register (UARTFR) — status bits

| Bit | Name | Meaning |
|-----|------|---------|
| 7 | TXFE | TX FIFO empty |
| 6 | RXFF | RX FIFO full |
| 5 | TXFF | TX FIFO full |
| 4 | RXFE | RX FIFO empty |
| 3 | BUSY | UART is transmitting |

This is what the RIA **READY** register ($FFE0) emulates: bit 7 = "TX not full" (OK to send), bit 6 = "RX has data."

---

## 5. Default Pico UART Configuration

*Programming The Raspberry Pi Pico/W In C*, p.317; Pico C SDK Reference, p.398.

- **UART0** on **GPIO 0** (TX) and **GPIO 1** (RX)
- **115200** baud
- 8N1

```c
#define PICO_DEFAULT_UART          0
#define PICO_DEFAULT_UART_TX_PIN   0
#define PICO_DEFAULT_UART_RX_PIN   1
#define PICO_DEFAULT_UART_BAUD_RATE 115200
```

---

## 6. Using UART with the Pico C SDK

### Initialization

```c
#include "hardware/uart.h"
#include "hardware/gpio.h"

#define UART_ID    uart0
#define BAUD_RATE  115200
#define UART_TX_PIN 0
#define UART_RX_PIN 1

uart_init(UART_ID, BAUD_RATE);
gpio_set_function(UART_TX_PIN, GPIO_FUNC_UART);
gpio_set_function(UART_RX_PIN, GPIO_FUNC_UART);
```

`uart_init()` (RP2040 Datasheet, p.428): deassert reset, enable `clk_peri`, set control bits, enable FIFOs, set baud divisors, set format (8N1).

### Sending data

```c
uart_putc(UART_ID, 'H');
uart_puts(UART_ID, "Hello!\r\n");
uart_putc_raw(UART_ID, byte);
```

### Receiving data

```c
if (uart_is_readable(UART_ID)) {
    char ch = uart_getc(UART_ID);
}
```

### Configuration

```c
uart_set_baudrate(UART_ID, 9600);
uart_set_format(UART_ID, 8, 1, UART_PARITY_NONE);   // 8N1
uart_set_hw_flow(UART_ID, false, false);
uart_set_fifo_enabled(UART_ID, true);
```

---

## 7. UART at the Assembly Level

*RP2040 Assembly Language Programming*, pp.275–276. Direct register access:

```asm
@ Init UART0 at 115200 baud
LDR R1, uart0base
MOV R0, #67
STR R0, [R1, #UART_UARTIBRD_OFFSET]
MOV R0, #52
STR R0, [R1, #UART_UARTFBRD_OFFSET]
MOV R0, #(UART_UARTLCR_H_WLEN_BITS + UART_UARTLCR_H_FEN_BITS)
STR R0, [R1, #UART_UARTLCR_H_OFFSET]
MOV R0, #3
LSL R0, #UART_UARTCR_TXE_LSB
ADD R0, #UART_UARTCR_UARTEN_BITS
STR R0, [R1, #UART_UARTCR_OFFSET]

@ Read: poll UARTFR until RXFE clear, then read UARTDR
@ Write: poll UARTFR until TXFF clear, then write UARTDR
```

---

## 8. Interrupts

RP2040 Datasheet, Section 4.2.6. The UART has **11 maskable interrupts**, including:

- RX interrupt (data in RX FIFO)
- TX interrupt (TX FIFO needs data, watermark)
- RX timeout
- Framing, parity, break, overrun errors

FIFO watermarks: 1/8, 1/4, 1/2, 3/4, 7/8 full.

```c
uart_set_irq_enables(UART_ID, true, false);
irq_set_exclusive_handler(UART0_IRQ, uart_handler);
irq_set_enabled(UART0_IRQ, true);
```

---

## 9. RIA UART Registers ($FFE0–$FFE2)

The RP6502 RIA runs on a Raspberry Pi Pico. Its UART at $FFE0–$FFE2 is a **software view** of the same UART idea: status + data.

| RIA Register | Address | Meaning |
|--------------|---------|---------|
| **READY** | $FFE0 | Bit 7 = TX not full (OK to send). Bit 6 = RX has data. |
| **TX** | $FFE1 | Write byte to send (UART TX). |
| **RX** | $FFE2 | Read byte received (UART RX). |

> "Easy and direct access to the UART RX/TX pins of the RP6502-RIA is available from $FFE0–$FFE2. The ready flags on bits 6–7 enable testing with the BIT operator." — [picocomputer.github.io/ria.html](https://picocomputer.github.io/ria.html)

RIA UART: **115200 8N1**.

### 6502 usage pattern

1. Read $FFE0; use **BIT** to test bit 7 (N flag) → TX ready?
2. If set: write byte to $FFE1 (TX).
3. Read $FFE0; use **BIT** to test bit 6 (V flag) → RX has data?
4. If set: read byte from $FFE2 (RX).

Same idea as bare-metal RP2040: poll flag, then read/write data.

---

## 10. Summary: RP2040 vs RIA

| Concept | RP2040 hardware | RIA ($FFE0–$FFE2) |
|---------|-----------------|-------------------|
| TX ready | UARTFR bit 5 (TXFF) clear | READY bit 7 set |
| RX ready | UARTFR bit 4 (RXFE) clear | READY bit 6 set |
| Send byte | Write UARTDR | Write TX ($FFE1) |
| Receive byte | Read UARTDR | Read RX ($FFE2) |
| Baud rate | Fractional divider from clk_peri | 115200 (RIA firmware) |
| FIFO depth | 32 entries | RIA firmware / emulator buffer |
| Framing | 5–8 bits, parity, 1–2 stop | 8N1 |

Understanding UART on the Pico makes it clear that RIA’s $FFE0–$FFE2 are a minimal register view of the same UART: status flags + data, with the Pico (or emulator) doing the real serial protocol.

---

## 11. RIA Firmware Source: Where UART Is Implemented

The real RIA runs on a Raspberry Pi Pico; its firmware lives in the **rp6502** repo under `src/ria/`. These are the files that implement UART for the 6502.

### 11.1 Files to study (priority order)

| Order | File | Role |
|-------|------|------|
| 1 | **`src/ria/sys/com.h`** | UART config (pins, baud, buffer declarations, inline helpers) |
| 2 | **`src/ria/sys/com.c`** | UART hardware init, TX/RX circular buffers, stdio, break detection |
| 3 | **`src/ria/sys/ria.c`** | 6502 register handlers: $FFE0 (READY), $FFE1 (TX), $FFE2 (RX) |
| 4 | **`src/ria/sys/mem.h`** | Register mapping: `REGS(addr)`, `regs[]` for $FFE0–$FFFF |
| 5 | **`src/ria/ria.pio`** | PIO bus interface (6502 bus protocol); optional for UART logic |

### 11.2 What each file does

**`com.h`**
- Defines: `COM_UART` = uart1, `COM_UART_BAUD_RATE` = 115200, `COM_UART_TX_PIN` = 4, `COM_UART_RX_PIN` = 5
- TX buffer: `com_tx_buf[32]` with head/tail
- RX: `com_rx_char` (single-byte queue, -1 = empty)
- Inline helpers: `com_tx_writable()`, `com_tx_write()`, `com_putchar_ready()`

**`com.c`**
- **`com_init()`** — `uart_init(COM_UART, COM_UART_BAUD_RATE)`, `gpio_set_function()` for TX/RX pins (4 and 5), 115200 baud on UART1
- **`com_tx_task()`** — Drains TX circular buffer into RP2040 UART hardware
- **`com_rx_task()`** — Reads from UART hardware (and keyboard) into RX buffer / `com_rx_char`
- 32-byte circular buffers for TX and RX
- UART break detection; stdio driver integration

**`ria.c`**
- **`CASE_READ(0xFFE0)`** — READY: bit 7 = TX has space (`com_tx_writable()`), bit 6 = RX has data (`com_rx_char != -1`)
- **`CASE_WRITE(0xFFE1)`** — TX: byte from 6502 → `com_tx_write()` → TX buffer → later sent by `com_tx_task()`
- **`CASE_READ(0xFFE2)`** — RX: returns `com_rx_char` (consumed from RX path filled by `com_rx_task()`)
- Action loop processes these register accesses when the 6502 hits $FFE0–$FFE2

**`mem.h`**
- `regs[]` — 32-byte array for RIA space $FFE0–$FFFF
- `REGS(addr)` — maps 6502 address to register index: `addr & 0x1F` (so $FFE0→0, $FFE1→1, $FFE2→2, …)

**`ria.pio`**
- PIO state machines for 6502 bus: chip select, PHI2, read/write. How the RP2040 physically talks to the 6502. Not required to understand UART register logic.

### 11.3 Register address mapping (RIA firmware)

| Address | Name | Access | Meaning |
|---------|------|--------|---------|
| $FFE0 | READY | Read | Bit 7 = TX ready (buffer has space). Bit 6 = RX has data. |
| $FFE1 | TX | Write | Write byte to send. |
| $FFE2 | RX | Read | Read received byte. |

### 11.4 Data flow

**6502 → wire (TX)**  
6502 writes $FFE1 (TX) → PIO/ria.c sees write → `CASE_WRITE(0xFFE1)` puts byte in `com_tx_buf[]` → `com_tx_task()` drains buffer to UART1 hardware → byte goes out on GPIO 4 (TX pin).

**Wire → 6502 (RX)**  
Byte arrives on GPIO 5 (RX pin) → UART1 hardware → `com_rx_task()` reads into buffer / `com_rx_char` → 6502 reads $FFE2 (RX) → `CASE_READ(0xFFE2)` returns `com_rx_char` to 6502.

**6502 reads READY ($FFE0)**  
`CASE_READ(0xFFE0)` builds byte: bit 7 = `com_tx_writable()`, bit 6 = (com_rx_char != -1).

### 11.5 Buffers in the firmware

- **TX:** 32-byte circular buffer (`com_tx_buf[]`); 6502 writes go there; `com_tx_task()` sends from buffer to UART hardware.
- **RX:** 32-byte circular buffer plus single-byte `com_rx_char` for the action loop; `com_rx_task()` fills from UART; 6502 reads from the single-byte queue ($FFE2).
- RP2040 UART hardware also has its own FIFOs (e.g. 32 entries); the firmware adds another layer of buffering on top.
