# RP6502 Ep5 — EhBASIC demo (no narration)

**Video:** Demo only — no talking. The author demonstrates several BASIC programs running on the Picocomputer using **EhBASIC** (Enhanced BASIC). Output was recorded from the VGA display; programs are sent and run by a script.

**Context:** The interpreter is **Lee Davison’s EhBASIC 2.22p5**, built for the RP6502 (see **ehbasic** in the workspace). The demo shows what the system can do with BASIC; the exact program sources shown may not be in the public repos.

---

## What appears in the episode

1. **EhBASIC startup** — Picocomputer 6502 COLOR; HID mount (keyboard, mouse); `basic` → Cold/Warm prompt (`?C`); “Memory size ?” → “52479 Bytes free”; “Enhanced BASIC 2.22p5”; REM lines state the demo is from VGA output and that programs are sent and run by a script.
2. **3D-style plot (asterisks)** — A BASIC program that draws a rounded hill / conical shape with `*`. Uses `WIDTH 80,8`, `DEF FNA(Z)=30*EXP(-Z*Z/100)`, nested `FOR X` / `FOR Y` over a circle (`SQR(900-X*X)`), `Z=INT(25+FNA(...)-.7*Y)`, depth variable `L`, and `PRINT TAB(Z)"*";` with simple hidden-surface logic.
3. **Same plot with ANSI color** — A variant that adds color: “That was nice, but let’s add color.” Uses a color index `CO` (1–7), `PRINT CHR$(27)"[3"CHR$(48+CO)"m";` for foreground color and `CHR$(27)"[0m";` to reset.
4. **Star Trek–style game (code)** — Snippets of a larger BASIC program: STATUS REPORT (KLINGON, STARBASE counts), “TORPEDO, BASE NAV, D/D CALCULATOR”, “DIRECTION/DISTANCE CALCULATOR” with INITIAL/FINAL COORDINATES (X,Y) and direction computation (line numbers in the 7890–8350 range).
5. **Enterprise ASCII art** — Subroutines appending Roman numerals to `G2$`; `RUN` produces ASCII art of the USS Enterprise and the line “THE USS ENTERPRISE --- NCC-1701”.
6. **Star Trek game (mission screen)** — Orders: destroy 26 Klingon warships before stardate 3032, 32 days, 4 starbases; mission starts in quadrant “SIRIUS I”. Galactic quadrant map (ASCII), player `<*>`; status: STARDATE 3000, CONDITION GREEN, QUADRANT/SECTOR, PHOTON TORPEDOES 10, TOTAL ENERGY 3000, SHIELDS 0, KLINGONS REMAINING 26.
7. **Star Trek game (commands)** — `COMMAND? LRS` → Long Range Scan for quadrant 1,5 with result grid; `COMMAND? NAV` → “COURSE (0-9)?” prompt.

---

## Where this lives in the workspace

- **EhBASIC interpreter:** **[ehbasic](https://github.com/picocomputer/ehbasic)** (`/Users/nenaddjordjevic/CProjects/ehbasic/`). Source: `basic.s` (Enhanced BASIC 2.22p5 plus RP6502/cc65 changes), `main.s` (cold/warm entry “6502 EhBASIC [C]old/[W]arm ?”), `min_mon.s` (minimal monitor, load/save vectors). Doc: `doc/help.rp6502`.
- **Running BASIC from a script:** In **ehbasic**, `tools/rp6502.py` has a `basic` command: it opens the serial console, sends `BASIC\r`, waits for `READY`, then uploads a program from a file (line by line) and sends `RUN`. So the “programs are sent and run by a script” line in the demo matches this workflow. The **examples** repo also has `tools/rp6502.py` (RP6502-RIA developer tool) for building and uploading ROMs; it can load and run programs but is not BASIC-specific.
- **Demo program sources:** The specific BASIC listings shown (Gaussian plot, ANSI color plot, Star Trek game) were **not found** in the workspace. They may be in the author’s private scripts or elsewhere. If you have similar .bas or .txt files, you can run them via `python3 tools/rp6502.py basic <file>` from the ehbasic project (with the Picocomputer on the serial port).

---

## Screenshots

7 images from the episode are in **[ep05-assets/](ep05-assets/)**.

---

## Sources for the demo programs (online)

The programs shown in the episode are **classic BASIC games**; the following sources match what appears on screen.

### 3-D Plot (mountain / hill of asterisks)

- **Original:** **3-D Plot** by **Mark Bramhall**, from *BASIC Computer Games* (1978), David H. Ahl / Creative Computing.
- **Code:** `DEF FNA(Z)=30*EXP(-Z*Z/100)`, `FOR X=-30 TO 30 STEP 1.5`, `Y1=5*INT(SQR(900-X*X)/5)`, `Z=INT(25+FNA(SQR(X*X+Y*Y))-.7*Y)`, `PRINT TAB(Z)"*";` with depth variable `L`.
- **BASIC source:**
  - [Vintage BASIC: 3dplot.bas](http://vintage-basic.net/bcg/3dplot.bas) — direct .bas file.
  - [Atari Archives: 3-D Plot](https://www.atariarchives.org/basicgames/showpage.php?page=167) — from *BASIC Computer Games*.
  - [programandala.net: 3D Plot](http://programandala.net/en.program.3d_plot.html) — original listing plus conversions to many languages.
- The episode may use **WIDTH 80,8** (or similar) for the Picocomputer display; that would be an EhBASIC/setup-specific addition.

### Colored 3-D Plot (ANSI)

- The “add color” version (REM “That was nice, but let’s add color.”, `CHR$(27)"[3"…"m"`) is almost certainly a **custom modification** of the 3-D Plot for the Picocomputer’s color ANSI terminal. No canonical public source found; you can replicate it by adding a color index and ANSI SGR sequences around the `PRINT TAB(Z)"*";` loop.

### Star Trek game

- **Original:** **Super Star Trek** (1978), from *BASIC Computer Games*. Based on Mike Mayfield’s 1971 game; modified by Bob Leedom; converted to Microsoft 8K BASIC by John Gorders.
- **Features in the episode:** STATUS REPORT (KLINGON/STARBASE), “TORPEDO, BASE NAV, D/D CALCULATOR”, “DIRECTION/DISTANCE CALCULATOR” with INITIAL/FINAL COORDINATES (X,Y), Enterprise ASCII art (“THE USS ENTERPRISE --- NCC-1701”), mission orders (destroy Klingons, starbases, days), LRS (Long Range Scan), NAV (COURSE 0–9).
- **Full BASIC source:**
  - [GitHub: philspil66/Super-Star-Trek](https://github.com/philspil66/Super-Star-Trek) — repository with [superstartrek.bas](https://github.com/philspil66/Super-Star-Trek/blob/main/superstartrek.bas) (425 lines). This matches the episode’s line numbers and text (e.g. 7890 STATUS REPORT, 8060 TORPEDO/D/D CALCULATOR, 8150 DIRECTION/DISTANCE CALCULATOR, 9230–9260 Roman numerals for quadrant names, Enterprise art at 221–226).
- Other references: [Vintage BASIC games](http://vintage-basic.net/games.html), [Coding Horror / BASIC Computer Games](https://coding-horror.github.io/basic-computer-games/84_Super_Star_Trek/javascript/index.html).

---

## Summary

| Item | Description |
|------|-------------|
| **Interpreter** | EhBASIC 2.22p5 (ehbasic repo) |
| **Demo content** | 3-D Plot (Bramhall 1978), ANSI-colored variant, Super Star Trek (1978) |
| **How programs are run** | Script (e.g. `rp6502.py basic`) sends program to BASIC and RUN |
| **3-D Plot source** | [vintage-basic.net/bcg/3dplot.bas](http://vintage-basic.net/bcg/3dplot.bas), [Atari Archives](https://www.atariarchives.org/basicgames/showpage.php?page=167), [programandala.net](http://programandala.net/en.program.3d_plot.html) |
| **Star Trek source** | [GitHub: philspil66/Super-Star-Trek](https://github.com/philspil66/Super-Star-Trek) (superstartrek.bas) |
| **Program sources in workspace** | Not present; use the links above to download and run via `rp6502.py basic` |

---

## Note (viewer comment)

A viewer mentioned having the **red and yellow Computer Games books** and loving seeing the Star Trek game listing scroll by in the demo — they had spent a long time typing that game in on their **Commodore 64**. That matches the era: *BASIC Computer Games* (1978, Creative Computing) and *More BASIC Computer Games* (or similar sequels) were often typed in by hand from the book on 8-bit machines like the C64; seeing the same Super Star Trek run on a Picocomputer decades later is a direct link to that tradition.

### Which books to look for

- **BASIC Computer Games** — David H. Ahl (ed.), **Creative Computing**, **1978** (Microcomputer Edition). Often shown with a **red**/maroon cover. First computer book to sell a million copies. Contains **Super Star Trek**, **3-D Plot**, Hamurabi, and many others. Originally published by DEC as *101 BASIC Computer Games* (1973); the 1978 edition was adapted for microcomputers and Microsoft BASIC.  
  - **ISBN** (one common printing): 0-916688-07-0.  
  - **Online:** [Atari Archives: BASIC Computer Games](https://www.atariarchives.org/basicgames/) (table of contents and listings).

- **More BASIC Computer Games** — David H. Ahl (ed.), **Creative Computing Press**, **1979**. Sequel with 84 more games; cover colors varied by edition (some **yellow**/tan, some blue, e.g. TRS-80 edition).  
  - **Online:** [Atari Archives: More BASIC Computer Games](https://www.atariarchives.org/morebasicgames/) (listings).

Search by title and “David Ahl” or “Creative Computing”; used copies turn up on eBay, AbeBooks, and Amazon. Cover colors differ by printing and region, so “red and yellow” refers to these two books even if your copy has a different shade.
