# RP6502 Ep10 — Assembly on a Circuit Board

**Video:** Tenth episode; full walkthrough of assembling the Picocomputer on a **PCB** — ordering parts (Mouser), soldering order, three-part bring-up (VGA Pico → RIA Pico → ICs), and running a program from USB. This is the **current production assembly**; use official docs and the latest BOM/CSV for today’s build.

---

## Intro: “Expensive hobby” and why build your own

- **Retro computers:** Classic Commodores that used to be ~$50 now sell for hundreds; Commander X16 was supposed to be $50, now ~$500.
- **Picocomputer:** “What would happen if different choices were made” — author spent a year designing his own 6502 computer.
- **Prototype:** Many have seen the Picocomputer on a breadboard; **no loss of functionality** on a breadboard, runs at **full 8 MHz** even **without bypass capacitors** (video/audio noisier but it works).
- **Community:** Four other people had built Picocomputers at the time; forum photos shown.
- **This episode:** How easy it is to assemble the project on a **circuit board**.

## Ordering parts

- **Documentation:** Grab the **latest CSV** from the project documentation.
- **Mouser:** Go to mouser.com and **upload the CSV** to a shopping cart; when prompted, map columns — **quantity** and **Mouser part number** are all that’s required. If the header row is treated as data, delete it.
- **Cost (at time of video):** **$59.51 plus shipping** for all parts **except the circuit board**.
- Unpack and organize the order before starting.

## Tools (no fancy gear)

- **Basic:** Eye protection, soldering iron, wire cutters, multimeter.
- **Optional but normal:** Magnification, extra flux, work holder — author skips them in the video to show you don’t need fancy tools.

## Assembly order (by height)

- **Shortest first** so the iron can move around easily.
- **1. Axial capacitors** — 0.1 µF axial; value of every part is **silkscreened on the back** of the board. No step-by-step checklist: **solder everything according to the silkscreen**. If there are still holes when you’re done, you missed a part.
- **2. Resistors** — Longer row = video circuit, shorter row = audio circuit. Do in stages so loading too many at once doesn’t block the iron.
- **3. Sockets** — **Orientation matters:** the **notch on the socket** must match the **notch on the silkscreen**. Don’t install ICs yet, only sockets. Sockets and ICs are the only parts you can install backwards — be careful.
- **4. Pi Pico sockets** — Two parts per socket. Solder one pin, straighten by eye so they’re perpendicular, then solder the rest.
- **5. Three-pin socket** — Not at Mouser; take an extra **20-pin socket** and **cut it down** with wire cutters (use eye protection).
- **6. Connectors and button**
- **7. 47 µF capacitors** — **Not electrolytic, not polarized.** Do these last so they don’t get bent while working on other parts.
- **Pi Pico header pins:** Push into sockets for alignment, **solder a few pins quickly** on each side (or you’ll melt the sockets). Once a few are secure, remove the Pi Pico and finish the rest. (Author normally uses a sacrificial breadboard; this method avoids buying extra gear.)

## Board marking

- **“Founders Edition”** — Surprise for patrons who sponsored the video.

## Pre-power check (no ICs, no Picos installed)

- **Multimeter (ohms):** Check that **no capacitors are shorted**. C9–C12 are in the audio circuit — **low resistance is normal**, but they must **not** be shorted. If you find a short, clean flux and track it down.
- **Resistors:** Test all; each should match the value on the silkscreen.
- **Then** bring up the board in **three parts** — still **don’t install the ICs** until part three.

## Bring-up Part 1: VGA Pico

- **Load firmware before installing** the Pico on the board.
- **Loading firmware:** Hold **BOOTSEL**, plug into a computer; the Pi Pico appears as a disk. Copy the **VGA** UF2 to it; when the **LED comes on**, you’re done. Unplug and install on the main board.
- **Power:** Picocomputer uses about **half a watt**. USB supply for standalone, or plug into a computer if you want serial console over USB.
- **Power switch:** Optional; one that “looks like this” (inline USB switch).
- **VGA → HDMI:** Inexpensive adapter; the **VGA jack can power the adapter** (~0.25 W); total still under 1 W.
- **Success:** **Working video** = **blinking cursor**. If not, suspect bad Pi Pico or bad soldering.

## Bring-up Part 2: RIA Pico

- Load **RIA** firmware the same way (BOOTSEL, copy **RIA** UF2 — not the VGA file), then install on the board.
- **USB on the RIA** is programmed to **accept a hub**. You need an **OTG adapter cable** for a normal hub, or a hub built for OTG.
- **Turn power off** when installing parts.
- Plug in an **ordinary USB keyboard**.
- **Power up** — you should get a **startup message**; keyboard works; you can browse **help**. Accessing 6502 memory will **time out** until the rest of the parts are installed.

## Bring-up Part 3: Rest of the parts (ICs)

- **Straighten IC pins** before inserting (e.g. pin straightener, or push against a table — careful with orientation).
- **Don’t install backwards:** Notch on IC = notch on socket = notch on silkscreen.
- **Turn power off** when installing.
- **Power up:** **Read RAM** works; **write to RAM** works. **Success.**  
- **Timeout or verify error** → bad soldering or bad chip.

## After assembly

- Clean the board; add **rubber feet** or put it in a **case** — your choice.

## 8 MHz and SET PHI2

- **Default:** Pi Pico runs the 6502 at **4 MHz**.
- **If you bought the correct parts,** it will run at **8 MHz**. Command: **`SET PHI2 8000`**. This is saved to the **Pi Pico EEPROM** — no DIP switches on a Picocomputer.

## Program from disk (USB, no SD)

- **No SD card slot** — storage is **USB**.
- **`status`** shows a drive mounted; list files.
- **`LOAD ADVENT4`** — runs. (Program was written on a minicomputer, ported to microcomputers, now running on a Picocomputer.)

## Links and community

- **Description:** Links in the video description, including a **playlist** going back to the beginning of development.
- **Sales:** Author has no store at the time of the video; if there’s enough interest, something can be set up. **Patreon** or **Twitter** for announcements.

---

## Takeaways for your build

- **BOM/CSV:** Always use the **latest CSV** from the documentation for current part numbers and quantities.
- **Silkscreen:** Back of board has every part value; no separate checklist — “solder everything; if there are holes left, you missed a part.”
- **Orientation:** Sockets and ICs — notch = socket = silkscreen; only parts that can go in backwards.
- **Three-part bring-up:** (1) VGA Pico → video; (2) RIA Pico → keyboard/console; (3) ICs → RAM and full system. Reduces risk and isolates faults.
- **Firmware:** Load **VGA** and **RIA** UF2 **before** installing each Pico; BOOTSEL + copy file + LED = done.
- **USB:** RIA accepts a hub with an OTG adapter or OTG hub; keyboard (and storage) plug into that.
- **8 MHz:** `SET PHI2 8000` stored in EEPROM; correct parts required for 8 MHz capable operation.

---

## Source

- Transcript: user-provided (RP6502 Ep10 — assembly on circuit board).
- Author’s documentation and BOM: project docs linked in the video description; https://picocomputer.github.io/ for current hardware and assembly info.
