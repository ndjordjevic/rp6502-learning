# Num Lock at Boot — RPi Keyboard and RP6502 Firmware

Deep research on the Num Lock-on-at-boot issue with the official Raspberry Pi Keyboard and Hub when used with RP6502 RIA firmware v0.18+, and the recommended **PID/VID exception** solution.

**Related:** [KEYBOARD_RASPBERRY_PI_HUB.md](KEYBOARD_RASPBERRY_PI_HUB.md) (keyboard product and connection), [LEARNING_PLAN.md](../LEARNING_PLAN.md) §3.3 (troubleshooting).

**Terms:** **HID** = Human Interface Device (USB device class for keyboards, mice, gamepads). **VID** = Vendor ID (16-bit, identifies manufacturer). **PID** = Product ID (16-bit, identifies the product). VID:PID (e.g. `04d9:0006`) identifies a specific device.

---

## 1. The Problem

After cold start or reboot with **RP6502 RIA firmware v0.18**, Num Lock is on by default. On keyboards **without a separate numeric keypad** (e.g. the official Raspberry Pi Keyboard and Hub), the right-hand letter keys then act as an embedded numpad:

- U→4, I→5, O→6, J→1, K→2, L→3, M→0, etc.

Typing produces numbers instead of letters until the user presses Num Lock once to turn it off.

**Workaround (no firmware change):** Press Num Lock once after boot; typing then works normally.

---

## 2. Why This Happens in v0.18

In v0.18, the RIA correctly sets Num Lock at boot and **sends the HID LED report in time**:

- `rp6502/src/ria/hid/kbd.c`: `kbd_init()` sets `kbd_hid_leds = KBD_LED_NUMLOCK` and calls `kbd_send_leds()`.
- TinyUSB now sends the LED report to the keyboard when it is enumerated, so the keyboard actually applies Num Lock.

In earlier firmware (e.g. v0.17), a typo and cold-boot timing often meant the LED wasn’t sent in time, so many keyboards defaulted to their own power-on state (often Num Lock off). The “fix” in v0.18 made Num Lock at boot reliable — which is correct for standard keyboards but wrong for the RPi keyboard’s design.

---

## 3. Author’s Position (Picocomputer)

The Picocomputer author’s view:

- **Windows boots with Num Lock on, Linux often off. Linux is wrong.** Num Lock on at boot has been standard since the IBM AT (1984) introduced dedicated cursor/numpad keys.
- **The RPi keyboard ignores how HID keyboards are supposed to work** and is effectively “Linux-only” in this respect — it uses the HID Num Lock LED to enable an embedded numpad overlay instead of following the usual contract.
- **Adding a configuration layer** (e.g. “Num Lock on/off at boot”) would add more UX complexity to an already non-standard situation; not a good solution.
- **A PID/VID exception** for the RPi keyboard is the right approach: detect that specific device and don’t force Num Lock for it.

---

## 4. Your Keyboard’s USB Identity

The **Raspberry Pi Keyboard and Hub** (official, 79-key, with built-in USB hub) uses:

| Field | Value | Source |
|-------|--------|--------|
| **VID** | `0x04d9` | Holtek Semiconductor, Inc. |
| **PID** | `0x0006` | “Wired Keyboard (78/79 key)” / “RPI Wired Keyboard 5” |

References: [USB ID Repository](https://usb-ids.gowdy.us/read/UD/04d9) (Holtek 04d9, device 0006), Raspberry Pi forum posts, `lsusb` reports.

**Note:** Newer Pi 500 / Pi 500+ keyboards use different VID:PID (`2e8a:0010`, `2e8a:0011`) and different firmware; they are a different product and may not need this exception.

---

## 5. How the Firmware Handles LEDs

### Boot

1. `main.c`: `usb_init()` then `kbd_init()`.
2. `kbd_init()` (`kbd.c`): `kbd_hid_leds = KBD_LED_NUMLOCK`; `kbd_send_leds()`.
3. `kbd_send_leds()` → `usb_set_hid_leds(kbd_hid_leds)` → sets `usb_hid_leds` and calls `usb_hid_leds_restart()`.

### LED broadcast (`usb.c`, `usb_task()`)

A loop iterates over device addresses (1 … `CFG_TUH_DEVICE_MAX`) and HID interface indices. For each interface with protocol `HID_ITF_PROTOCOL_KEYBOARD`, it sends the 1-byte `usb_hid_leds` report via `tuh_hid_set_report(..., HID_REPORT_TYPE_OUTPUT, &usb_hid_leds, ...)`.

VID/PID are **not** currently used for keyboards; they are only passed to `pad_mount()` for gamepads. `tuh_vid_pid_get(dev_addr, &vendor_id, &product_id)` is already called in `tuh_hid_mount_cb()`.

### Constants (`kbd.c`)

- `KBD_LED_NUMLOCK  (1 << 0)`
- `KBD_LED_CAPSLOCK (1 << 1)`
- `KBD_LED_SCROLLLOCK (1 << 2)`

---

## 6. Recommended Solution: PID/VID Exception in `usb.c`

**Idea:** In the LED broadcast loop, before sending the LED report to a keyboard interface, read the device’s VID/PID. If it is the RPi keyboard (`0x04d9`, `0x0006`), send the same report **with the Num Lock bit cleared** for that device only. All other keyboards still get Num Lock on at boot.

**Where:** `rp6502/src/ria/usb/usb.c`, inside the `while (usb_hid_leds_dev)` loop, where `tuh_hid_set_report` is called for keyboard interfaces.

**Needed:**

1. Define RPi keyboard VID/PID (e.g. `RPI_KBD_VID 0x04D9`, `RPI_KBD_PID 0x0006`).
2. Before `tuh_hid_set_report`, call `tuh_vid_pid_get(usb_hid_leds_dev, &vid, &pid)`.
3. If `vid == RPI_KBD_VID && pid == RPI_KBD_PID`, use a local byte `leds = usb_hid_leds & ~KBD_LED_NUMLOCK`; otherwise `leds = usb_hid_leds`.
4. Call `tuh_hid_set_report(..., &leds, sizeof(leds))` (so only this device sees Num Lock cleared).
5. Include `hid/kbd.h` in `usb.c` to use `KBD_LED_NUMLOCK`, or use the literal `(1 << 0)`.

**Why this is the right fix:**

- **Minimal:** One file, a small number of lines.
- **Targeted:** Only `04d9:0006`; standard keyboards unchanged.
- **No configuration:** No menus or user settings.
- **Correct semantics:** RIA’s internal state (`kbd_hid_leds`) is unchanged; only the report sent to this keyboard has Num Lock cleared.
- **Num Lock key still works:** Pressing Num Lock toggles `kbd_hid_leds`; the next LED report to the RPi keyboard again has Num Lock cleared, so the embedded numpad stays off.
- **BLE keyboards:** Unaffected (they use `ble_set_hid_leds`, not this USB path).

---

## 7. What to Verify When Testing

1. **Actual VID:PID** — With the keyboard connected via your OTG adapter, confirm (e.g. via `status` or a temporary debug print in `tuh_hid_mount_cb`) that VID=0x04d9, PID=0x0006.
2. **Boot** — After the patch, cold boot: letters should type as letters without pressing Num Lock.
3. **Num Lock key** — Pressing Num Lock should still toggle between letters and embedded numpad on the right-hand keys.
4. **Other keyboards** — Plugging a different keyboard should still get Num Lock on at boot.
5. **Caps/Scroll Lock** — Should be unchanged (only Num Lock is masked for the RPi device).

---

## 8. Alternatives Considered (and Why Not)

| Approach | Why not |
|----------|--------|
| Boot with Num Lock off for *all* keyboards | Breaks standard full-size keyboards where users expect numpad active at boot. |
| Config option “Num Lock on/off at boot” | Adds UX and code for a single device quirk; author rejected this. |
| Detect “no numpad” from HID descriptor | Descriptor does not reliably distinguish embedded vs separate numpad. |
| Do nothing (user presses Num Lock each boot) | Poor UX; v0.18 was intentionally fixing boot LED behavior. |

---

## 9. References

- Picocomputer author (Num Lock, Linux, PID/VID exception).
- RP6502 firmware: `rp6502/src/ria/hid/kbd.c` (KBD_LED_*, kbd_init, kbd_send_leds), `rp6502/src/ria/usb/usb.c` (usb_task LED loop, tuh_hid_mount_cb, tuh_vid_pid_get).
- USB IDs: [usb-ids.gowdy.us — Holtek 04d9 / 0006](https://usb-ids.gowdy.us/read/UD/04d9/0006).
- LEARNING_PLAN.md §3.3 — Troubleshooting “Num Lock on at boot (v0.18)”.
