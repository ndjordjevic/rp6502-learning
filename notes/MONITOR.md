# RP6502 Monitor Reference

The Monitor is the command shell built into the RIA (Raspberry Pi Interface Adapter — the RP2040/RP2350 Pico that controls the 6502). It runs exclusively on the RIA; the 6502 is held in reset (RESB low) while the monitor is active. Everything you see at the `]` prompt is produced by the RIA, not the 6502.

## Source Layout

```
rp6502/src/ria/
├── mon/
│   ├── mon.c / mon.h       — main command dispatch loop, response/pager system
│   ├── hlp.c / hlp.h       — HELP command, topic lookup
│   ├── set.c / set.h       — SET command (system configuration)
│   ├── ram.c / ram.h       — memory read/write (address commands, BINARY)
│   ├── rom.c / rom.h       — ROM file management (LOAD, INFO, INSTALL, REMOVE)
│   ├── fil.c / fil.h       — filesystem commands (LS, CD, MKDIR, UPLOAD, UNLINK)
│   └── vip.c / vip.h       — VIP/credits list (HELP ABOUT)
├── str/
│   ├── str.inc             — API-stable command keywords, prompts, SET response formats
│   ├── str_en.inc          — English locale: all help text, error messages
│   ├── str.h / str.c       — X-macro expansion, string parse utilities
│   └── rln.c / rln.h       — readline line editor (history, ANSI, word navigation)
└── sys/sys.c               — STATUS, REBOOT, RESET command handlers
```

All string literals are placed in flash using `__in_flash` via the X-macro pattern in `str.c`. `str.inc` is part of the API guarantee (never changes); `str_en.inc` is the localizable part.

---

## How the Monitor Works

**Entry point:** `mon_task()` in `mon.c` calls `rln_read_line()` to collect a line using the readline editor, then dispatches via `mon_command_lookup()`.

**Command lookup order** (before hitting the command table):
1. Is the first token a hex address? → memory read/write (`ram.c`)
2. Is the first token a drive specifier (`0:`–`7:` or `USB0:`–`USB7:`)? → change drive
3. Is the first token an installed ROM name? → load from LittleFS flash
4. Otherwise → scan the `MON_COMMANDS[]` table (case-insensitive)

**Prompt:**
```
]
```
Sent as `STR_MON_PROMPT` = `\030\033[0m\033[?25h]` — that's Ctrl-X (terminal sync), ANSI reset attributes, show cursor, then `]`. After a break the newline variant `STR_MON_PROMPT_NEWLINE` is used.

**Response system:** Non-blocking and paginated. Large outputs (directory listings, help text) are queued as callback chains. After `rows_max` lines (24/30/32 depending on VGA mode) output pauses with `--More--`; any key continues, `q`/`Q`/Ctrl-C aborts.

**Error messages** all start with `?`, e.g.:
```
?Unknown command
?Invalid argument
?No file
?Verify failed at $FFFC
```

**Readline features** (from `rln.c`):
- 3-entry history (Up/Down arrows)
- Home (Ctrl-A), End (Ctrl-E), Delete, Backspace
- Word navigation: Alt-B (back word), Alt-F (forward word)
- ANSI/VT100 escape sequence state machine
- Max line length: 254 characters

**Return to monitor from running program:** CTRL-ALT-DEL (handled in `kbd.c`, calls `main_break()`). Also: sending a UART break on the serial port.

---

## Command Reference

### HELP / H / ?

```
]HELP
]HELP <command>
]HELP SET <attr>
]HELP ABOUT
]HELP CREDITS
]HELP SYSTEM
]HELP 0000
]HELP 0:
```

No argument — prints the full command list followed by installed ROMs.  
With a topic — prints expanded help for that command or SET attribute.  
`HELP ABOUT` / `HELP CREDITS` — copyright notice + shuffled VIP/supporter list.  
`HELP SYSTEM` — tutorial on memory commands and general monitor usage.  
`HELP 0000` / `HELP 0:` — help for memory commands and drive switching.

**Code:** `mon/hlp.c`

---

### STATUS

```
]STATUS
```

Shows everything connected to the RIA, in this order:
- Firmware name and version (e.g. `RP6502`, `RIA Version X.Y` or `RIA W`)
- VGA Pico status: version/display type, or "VGA Searching" / "VGA Not Found" / "VGA Connection Lost"
- WiFi (Pico W only): state line (`WiFi: ...`), then MAC (`MAC : xx:xx:...`), then IPv4 if connected (`IPv4: ...`)
- NTP (Pico W only): one line `NTP : ...`
- Time: one line `Time: ...` from RTC (always shown; may show error if RTC unavailable)
- BLE (Pico W only): one line `BLE : ...` (device counts or "Disabled" / "RF Off")
- USB: one line `USB : N keyboards, N mice, N gamepads, N storage, N serial`
- MSC drives: one line per registered/mounted volume, e.g. `MSC0: 1.5 GB VendorID ProductID rev 1.00` or `MSC0: (no media) ...`
- VCP serial ports: one line per mounted CDC port, e.g. `com0: vendor product`

**Code:** `sys/sys.c` → `sys_mon_status()` (adds response strings and response fns; actual text from `sys/vga.c`, `net/wfi.c`, `net/ntp.c`, `api/clk.c`, `net/ble.c`, `usb/usb.c`, `usb/msc.c`, `usb/vcp.c`).

---

### SET

```
]SET
]SET <attr>
]SET <attr> <value>
```

Without arguments — dumps all current settings.  
With attribute only — shows current value for that attribute.  
With attribute and value — changes and saves the setting.

Settings are stored in `CONFIG.SYS` on the RIA's internal LittleFS flash. If no `CONFIG.SYS` exists (or a line is missing), each attribute uses the default below.

| Attribute | Range / Values | Description | Default |
|-----------|----------------|-------------|---------|
| `PHI2` | 100–8000 (kHz) | 6502 clock speed. Next highest available frequency chosen if exact not available. | 8000 |
| `BOOT` | rom name or `-` | ROM auto-loaded on cold start/reboot. `-` = boot to monitor. | (none) |
| `TZ` | city name or POSIX string | Time zone. E.g. `SET TZ LOS_ANGELES` or `SET TZ PST8PDT,M3.2.0/2,M11.1.0/2`. | Etc/UTC |
| `KB` | layout name (e.g. `US`, `DE`, `DK`) | Keyboard layout. | US |
| `CP` | 437, 737, 771, 775, 850, 852, 855, 857, 860, 861, 862, 863, 864, 865, 866, 869 | OEM code page for text output. | 437 |
| `VGA` | 0, 1, 2 | Display type: 0=640×480 (4:3), 1=640×480+1280×720 (16:9), 2=1280×1024 (5:4). | 0 |
| `RF` | 0 or 1 | (Pico W) Disable/enable all radios. | 1 |
| `RFCC` | country code or `-` | (Pico W) WiFi country code. `-` = worldwide. | `-` (worldwide) |
| `SSID` | text or `-` | (Pico W) WiFi SSID. `-` = disable WiFi. | empty |
| `PASS` | text or `-` | (Pico W) WiFi password. | empty |
| `BLE` | 0, 1, 2, or 86 | (Pico W) 0=disable, 1=enable, 2=enable+pair, 86=clear all bonds. | 1 |

**Examples:**
```
]SET PHI2 4000
PHI2: 4000 (kHz)

]SET KB US
KB  : US (US English)

]SET BOOT EHBASIC
BOOT: EHBASIC

]SET BOOT -
BOOT: (none)
```

**Code:** `mon/set.c`

---

### Memory Commands (address read/write)

No keyword required — the monitor detects hex addresses automatically.

#### Read 16 bytes at an address

```
]0200
0200  DA DA DA DA DA DA DA DA  DA DA DA DA DA DA DA DA  |................|
```

#### Read a range

```
]0200-020F
]10000-100FF
```

Range: repeat reading 16 bytes at a time with `--More--` paging.

#### Write bytes at an address

```
]FFFC 00 02
]C000 A9 01 85 00 60
```

First line writes `00 02` at `$FFFC`. Second writes `A9 01 85 00 60` starting at `$C000` and verifies after write (`?Verify failed` if mismatch).

#### Intel HEX records

You can paste standard Intel HEX lines (as produced by many assemblers and tools). Each line starts with `:` and has: byte count, address, record type, data bytes, and checksum — all in hex.

**Example — one line decoded:**

```
]:02FFFC00000201
```
- `02` = 2 bytes of data  
- `FFFC` = address (6502 reset vector)  
- `00` = record type 0 (data)  
- `0002` = the two bytes written: `$00`, `$02` (low, high of reset address = $0200)  
- `01` = checksum  

So this line writes the 6502 reset vector to jump to `$0200`.

**Another example:**

```
]:10C00000A90185006000000000000000000000000047
```
- `10` = 16 bytes of data at address `C000`, type 0 (data). The first 5 data bytes are `A9 01 85 00 60` (LDA #1, STA $00, RTS); the rest are padding. Last two hex digits are the record checksum.

**Supported record types:** 0 = data, 1 = EOF, 2 = extended segment address, 3/5 = start address (e.g. sets reset vector), 4 = extended linear address.

#### Address ranges

- `$0000`–`$FFFF` — 6502 RAM (written via RIA, verified after)
- `$10000`–`$1FFFF` — XRAM (extended 64KB RAM on PIX bus, direct)

**Code:** `mon/ram.c`

---

### BINARY addr len crc

```
]BINARY C000 256 0xDEADBEEF
```

Fastest bulk write from a host system. After the command, send exactly `len` bytes of raw binary. The RIA verifies the CRC-32 (same algorithm as zip) before writing. Maximum 1024 bytes per call. Returns `]` on success, `?CRC error` or `?Verify failed` on error.

Used by development tools (cc65, llvm-mos) to push compiled code/data directly to 6502 RAM without the overhead of Intel HEX encoding.

**Code:** `mon/ram.c` → `ram_mon_binary()`

---

### LS / DIR

```
]LS
]LS mydir
]DIR
```

Lists directory contents of the current or specified directory. Subdirectories shown as `<DIR>`. File sizes shown in bytes (up to 999999) or with `K`/`M`/`G`/`T` suffix. Hidden and system files are skipped.

**Code:** `mon/fil.c` → `fil_mon_ls()`

---

### CD / CHDIR

```
]CD
]CD mydir
]CD ..
]CHDIR /games
```

Without argument — prints current working directory.  
With argument — changes directory.

**Code:** `mon/fil.c` → `fil_mon_chdir()`

---

### 0: through 9: / MSC0:–MSC9: (drive switching)

```
]0:
]1:
]MSC0:
]MSC3:
]9:
```

Changes the active USB mass storage device. Up to 10 drives are supported (0–9). **`STATUS`** labels them **`MSC0:`**–**`MSC9:`** (FatFs volume names). At the prompt use **`0:`**–**`9:`** (short form) or **`MSC0:`**–**`MSC9:`** — the same drives. **Not** `USB0:` / `usb0` (unknown / invalid).

**Code:** `mon/fil.c` → `fil_mon_chdrive()`

---

### MKDIR dir

```
]MKDIR games
]MKDIR /projects/demo
```

Creates a new directory on the current USB drive.  
Use `UNLINK` to remove empty directories.

**Code:** `mon/fil.c` → `fil_mon_mkdir()`

---

### UNLINK file|dir

```
]UNLINK oldgame.rp6502
]UNLINK emptydir
```

Deletes a file or empty directory. Primarily intended for use in scripted automation from a host system.

**Code:** `mon/fil.c` → `fil_mon_unlink()`

---

### LOAD file

```
]LOAD mygame.rp6502
]LOAD ehbasic.rp6502
```

Loads a ROM file from the current USB drive into 6502 RAM. If the file contains data for the reset vector (`$FFFC`–`$FFFD`), the 6502 is started automatically.

ROM file format:
```
#!RP6502
# Cool Game V1.0 by Awesome Dev
# Press F1 for help.
$C000 1024 0x0C0FFEE0
<1024 bytes of binary data>
$FFFC 2 0xDEADBEEF
<2 bytes: lo, hi of start address>
```

Line 1 must be `#!RP6502`. Lines starting with `# ` are help/info text (shown by `INFO`). Data chunks: `addr len crc32` followed by binary. Max 1024 bytes per chunk. CRC-32 uses zip algorithm.

**Code:** `mon/rom.c` → `rom_mon_load()`

---

### INFO file

```
]INFO mygame.rp6502
```

Displays only the `# ` comment lines from a ROM file — the embedded help/description — without loading or executing it.

**Code:** `mon/rom.c` → `rom_mon_info()`

---

### INSTALL file

```
]INSTALL ehbasic.rp6502
```

Copies a ROM file from USB drive to the RIA's internal LittleFS flash. Once installed, the ROM name appears in `HELP` and can be loaded by simply typing its name (e.g. `]EHBASIC`).

Rules for the ROM name (derived from filename, extension `.rp6502` stripped):
- Must start with a letter (A–Z)
- May only contain letters and digits (A–Z, 0–9)
- Max 16 characters
- Must not conflict with any system command
- Name is stored uppercased

The ROM file must contain a reset vector (`$FFFC`–`$FFFD`) to be installable.

**Code:** `mon/rom.c` → `rom_mon_install()`

---

### romname (run installed ROM)

```
]EHBASIC
]ADVENT
]MYGAME
```

Types the ROM name as a bare command — loads the ROM from RIA flash and starts the 6502. Same as `LOAD` but reads from internal flash instead of USB.

**Code:** `mon/rom.c` → `rom_load_installed()`

---

### REMOVE rom

```
]REMOVE EHBASIC
]REMOVE MYGAME
```

Deletes an installed ROM from RIA flash. If it was the boot ROM, the boot setting is also cleared.

**Code:** `mon/rom.c` → `rom_mon_remove()`

---

### REBOOT

```
]REBOOT
```

Hardware reboot of the RIA via watchdog (`watchdog_reboot(0,0,0)`). Same as pressing the RUN button or cutting power. If a boot ROM is configured (`SET BOOT`), it loads automatically.

**Code:** `sys/sys.c` → `sys_mon_reboot()`

---

### RESET

```
]RESET
```

Releases the 6502 RESB line (brings it high). The 6502 starts executing from whatever address is in the reset vector `$FFFC`–`$FFFD`. The monitor suspends until the 6502 stops (CTRL-ALT-DEL, UART break, or 6502 program calls `exit()`).

**Code:** `sys/sys.c` → `sys_mon_reset()`

---

### UPLOAD file

```
]UPLOAD mygame.rp6502
}$400 $0C0FFEE0
<binary data>
}END
]
```

Receives a file from a host system via the serial port. The monitor emits `}` prompts; the host sends chunks of `len crc32` followed by binary data, then `END` or blank line to finish.

- Max chunk size: 1024 bytes
- CRC-32 (zip algorithm)
- No retry — not intended for lossy connections
- Any error returns `?` and aborts; only `]` means success
- Overwrites existing file of same name

**Code:** `mon/fil.c` → `fil_mon_upload()`

---

## Breakout Keys

| Key / Signal | Effect |
|---|---|
| **CTRL-ALT-DEL** | Break — stops running 6502 program, returns to `]` prompt |
| **UART Break** | Same as CTRL-ALT-DEL (from serial connection) |
| **REBOOT command** | Hardware reset of RIA via watchdog |
| **q / Q / Ctrl-C** | Abort `--More--` pager |

CTRL-ALT-DEL is handled in `rp6502/src/ria/hid/kbd.c` — when `key_ctrl && key_alt && keycode == DELETE` on initial press, it calls `main_break()` in `main.c`, which transitions the main state machine from running → stopping → stopped → monitor prompt.

---

## CONFIG.SYS

Settings are stored in `CONFIG.SYS` on the RIA's LittleFS (internal Pico flash, not USB). Format is a text file with lines like:

```
+P4000
+BUS
+KDE
+TZPacific/Auckland
```

Each line is a tagged setting. The `+P` prefix is PHI2, `+B` is BOOT, `+K` is KB layout, `+TZ` is time zone, etc. You never edit this directly — use `SET`.

---

## Tips

- **Check available keyboard layouts:** `HELP SET KB` — shows layout list
- **Check available code pages:** `HELP SET CP`
- **Check display options:** `HELP SET VGA`
- **See all installed ROMs:** just type `HELP`
- **Get verbose help for a specific command:** `HELP LOAD`, `HELP BINARY`, etc.
- **Monitor tutorial:** `HELP SYSTEM`
- **Set reset vector manually:** `]FFFC 00 C0` sets reset vector to `$C000`
- **Read extended RAM:** `]10000` reads XRAM at offset 0
- **Change clock speed:** `SET PHI2 8000` (max 8 MHz), `SET PHI2 1000` (1 MHz)
