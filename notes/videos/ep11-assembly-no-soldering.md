# RP6502 Ep11 — Assembly Without Soldering

**Video:** Part two of how to build a Picocomputer — **no soldering**. Use pre-assembled boards (e.g. PCBWay), install firmware on the Picos, plug in ICs, optional acrylic case. Sponsor: PCBWay (single-unit assembly).

---

## Getting a Picocomputer

- **Open source:** All design files are released; everything needed to have one manufactured is in the documentation. Videos supplement that.
- **Manufacturing:** Send design files to a factory, say how many you want, wait for the product. Some have minimum quantity; **PCBWay** can assemble **a single unit** (video sponsor).
- **Cost (at time of video):** Assembly for **one** = **$30**; for **two** = **$15 each**. Author ordered two; the second is a viewer giveaway (see end).

---

## What you need

- **Silicon:** From **Mouser** (ships worldwide).
- **Acrylic:** Common size used for photographs; easy to find. Author uses **100×150×3 mm** from eBay; can be bought pre-cut (you drill the 3 mm holes).
- **Screws and standoffs:** Nothing special. Standoffs in the video are 3D-printed; commercial ones work too.
- **Tools:** Knife (open packages), screwdriver (case), something to **straighten IC pins** (pliers, table, or a dedicated tool). No soldering iron.

---

## Unboxing and inspection

- **Visual inspection first:** Quick check that nothing was damaged in transit.
- **VGA Pi Pico:** Install the one used for VGA. This one came with **header pins already installed** (no soldering). Still need to **load firmware**: hold **BOOTSEL** while plugging into a computer; it appears as a USB drive. Copy the **VGA .uf2** to the drive; done when the **LED turns on**.
- **RIA Pi Pico:** Same process with the **RIA .uf2** file. Then install it on the board.
- **ICs:** Plug in the rest of the ICs. **Part numbers are on the back of the board.** New ICs often need **pins straightened** or they won’t line up with the sockets (pliers, table, or pin-straightener tool). **Orientation matters:** notches on ICs match notches on sockets match notches on the circuit board.

---

## Case (optional)

- **No case:** Stick **rubber feet** on the bottom and you’re done.
- **Acrylic case:** Stack the 100×150×3 mm sheets (with holes drilled), align, and **snug up the bolts**. “It’s easier to show” — follow the video for the stacking order.

---

## Power-up and a mishap

- **Power up:** Plug in keyboard, monitor, and power. Console should appear.
- **Wrong chip:** Author installed the wrong chip by mistake; had to fix it **without full disassembly** (pliers under the acrylic). Emphasizes: double-check parts before closing the case.

---

## Design for manufacturing

- **If you design for manufacturing and then send the design to be manufactured, it really is this easy.** Building electronics this way has been done for 50 years. Even surface-mount assembly is easy when you send it out.
- **Making the design files is hard** — but the author has done that, so you only need to send them off to be made. Same for programming and documentation; everything is **open source**. If you build one, consider **Patreon** or **Super Thanks** on YouTube.

---

## Giveaway

- **Second assembled board:** Given away to a viewer. Random comment chosen in a couple of weeks; **for every 100 comments**, more winners get an **empty circuit board**. Details in the video description.

---

## Related

- **Credits on the machine:** In the monitor, **`about`** shows project and component credits (Picocomputer 6502 © Rumbledethumps, Pi Pico SDKs, Tiny printf, TinyUSB, FatFs, littlefs, and Patrons).
- **Ep10:** Full assembly *with* soldering (PCB + parts from Mouser); same bring-up idea (VGA Pico → RIA Pico → ICs) and firmware flow.

---

## Source

- Transcript: user-provided (RP6502 Ep11 — assembly without soldering).
- Documentation and BOM: https://picocomputer.github.io/
