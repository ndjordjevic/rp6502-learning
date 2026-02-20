# Raspberry Pi Debug Probe

**Product**: Raspberry Pi Debug Probe  
**Manufacturer**: Raspberry Pi Ltd  
**Product page**: https://www.raspberrypi.com/products/debug-probe/  
**Documentation**: https://www.raspberrypi.com/documentation/microcontrollers/debug-probe.html  
**Product brief (PDF)**: https://datasheets.raspberrypi.com/debug/raspberry-pi-debug-probe-product-brief.pdf  

---

## Overview

The Raspberry Pi Debug Probe is an all-in-one **USB-to-debug kit** that provides solderless, plug-and-play debugging for Raspberry Pi Pico, RP2040, and other targets. It exposes:

- **Serial Wire Debug (SWD)** — processor debug interface (Arm SWD by default; other interfaces can be supported)
- **UART** — industry-standard serial interface

Both use the **Raspberry Pi 3-pin debug connector** (see [Raspberry Pi 3-pin Debug Connector Specification](https://rptl.io/debug-spec)). The probe appears as standard **CMSIS-DAP** and **UART** over USB, so it works with OpenOCD and other CMSIS-DAP–capable tools on Windows, Mac, and Linux.

While aimed at Raspberry Pi products, it can be used with other processors or as a USB-to-UART cable. It is based on **Raspberry Pi Pico** hardware and runs the open-source **Picoprobe** firmware ([github.com/raspberrypi/picoprobe](https://github.com/raspberrypi/picoprobe)). Firmware is updated like standard Pico firmware (BOOTSEL + UF2).

---

## Specification

| Parameter | Value |
|-----------|--------|
| **Form factor** | 22 mm × 32 mm |
| **Nominal I/O voltage** | 3.3 V |
| **Operating temperature** | -20°C to +70°C |
| **Production lifetime** | At least January 2028 |

### Features

- USB to two-wire serial debug bridge (Arm Serial Wire Debug by default)
- USB to UART bridge
- CMSIS-DAP compatible
- Works with OpenOCD and other CMSIS-DAP tools
- Compliant with Raspberry Pi 3-pin Debug Connector Specification

### Included in kit

- Debug Probe unit in plastic case
- USB cable
- Three debug cables:
  - 3-pin JST to 3-pin JST
  - 3-pin JST to 0.1" header (female)
  - 3-pin JST to 0.1" header (male)

---

## Use with RP6502 / Picocomputer

The Debug Probe can be used to:

- **Debug the RIA (Pico 2 W)** or **VGA Pico (Pico 2)** if the board exposes or can be adapted to the 3-pin debug connector (SWD + UART).
- **Flash or debug RP2040-based firmware** (e.g. custom RIA/VGA builds) with OpenOCD or other CMSIS-DAP tools.
- **Use the UART side only** as a USB–serial adapter for console or logging.

Check the RP6502 hardware docs and schematic for presence of debug headers and pinout; you may need an adapter or flying leads to the 3-pin connector.

---

## Safety and handling (from product brief)

- Operate in a well-ventilated environment; if used in a case, do not cover the case.
- Keep the unit firmly secured or on a stable, flat, non-conductive surface; avoid contact with conductive items.
- Connecting incompatible devices can affect compliance, damage the unit, and invalidate the warranty.
- Do not expose to water/moisture or place on a conductive surface while in operation.
- Avoid heat sources; store in a cool, dry place.
- When powered, handle only by the edges to reduce ESD risk.

---

## References

- [Raspberry Pi Debug Probe — product page](https://www.raspberrypi.com/products/debug-probe/)
- [Debug Probe documentation](https://www.raspberrypi.com/documentation/microcontrollers/debug-probe.html)
- [Raspberry Pi Debug Probe product brief (PDF)](https://datasheets.raspberrypi.com/debug/raspberry-pi-debug-probe-product-brief.pdf)
- [Picoprobe firmware (GitHub)](https://github.com/raspberrypi/picoprobe)
- [Raspberry Pi 3-pin Debug Connector Specification](https://rptl.io/debug-spec)
