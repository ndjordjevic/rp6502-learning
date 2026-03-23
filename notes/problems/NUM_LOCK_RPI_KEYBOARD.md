# Num Lock at Boot — RPi Keyboard and RP6502 Firmware

Notes on the Num Lock-on-at-boot issue with the official **Raspberry Pi Keyboard and Hub** on RP6502 RIA firmware v0.18+, how **upstream** fixes it, and why a **PID/VID exception** is still part of the firmware design.

**Resolved in firmware v0.20** — Upgrade RIA to **v0.20 or newer** for the full fix (USB boot window + RPi keyboard exception). v0.19 had the PID/VID logic but **RF-off / slow-USB** setups could still miss the Num Lock clear until the `usb_init()` timer change shipped.

**Upstream fix (summary):** (1) initialize the USB boot-enumeration window in `usb_init()`, so `usb_boot_enumerating()` stays true long enough for slow hub/keyboard setups; (2) keep clearing Num Lock for `0x04D9:0x0006` in `kbd_mount()` when still in that boot window (see §6).

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

For keyboards, VID/PID are used in `kbd_mount()` for the RPi Num Lock exception (`kbd_numlock_off_at_boot`). For gamepads they are passed to `pad_mount()`. `tuh_vid_pid_get` is used where needed (e.g. `tuh_hid_mount_cb` → `kbd_mount`).

### Constants (`kbd.c`)

- `KBD_LED_NUMLOCK  (1 << 0)`
- `KBD_LED_CAPSLOCK (1 << 1)`
- `KBD_LED_SCROLLLOCK (1 << 2)`

---

## 6. How upstream fixes it (`usb.c` + `kbd.c`)

Firmware **v0.20+** (and current `main`) combines two mechanisms. The author reproduced a failure mode where **Wi-Fi / RF disabled** (`RF off`) changed boot timing so USB enumeration finished before the RPi keyboard (a composite device behind an internal hub) was ready — which is why **v0.19 alone** was not enough on some hardware.

### 6.1 Boot enumeration window: `usb_init()`

**File:** `rp6502/src/ria/usb/usb.c`

At the end of `usb_init()`, after `tusb_init` and `tuh_hid_set_default_protocol`:

```c
usb_enum_timeout = make_timeout_time_ms(USB_ENUM_WINDOW_MS);
```

`USB_ENUM_WINDOW_MS` is `(255 + 100)` ms (comment in tree: max `bInterval` plus time for the slowest driver to mount).

**Why it matters:** `usb_boot_enumerating()` uses `usb_enum_timeout` together with TinyUSB events (`ATTACH`, descriptor callbacks, `MOUNT`, etc.) to decide when “initial USB bring-up” is done. If that window was never anchored at host init, **idle timing** could mark boot enumeration finished **before** the slow keyboard path mounted. Debug logs on a bad setup showed something like `READY !!!` at **~854 ms** while the keyboard **HID mount** happened later (**~1.7 s**), so the RPi-specific Num Lock clear in `kbd_mount` never ran (it only runs while `usb_boot_enumerating()` is still true).

Initializing the timeout in `usb_init()` fixes that class of bug for **all** callers of `usb_boot_enumerating()` (monitor, ROM paths, keyboard mount), not only Num Lock.

### 6.2 PID/VID exception: `kbd.c` (not `usb.c` LED loop)

**File:** `rp6502/src/ria/hid/kbd.c`

- Table `kbd_numlock_off_at_boot[]` includes `{0x04D9, 0x0006}` (Raspberry Pi Keyboard).
- In `kbd_mount()`, after the descriptor is parsed, if `conn->valid && usb_boot_enumerating()`, matching VID/PID runs:

  `kbd_hid_leds &= ~KBD_LED_NUMLOCK;` then `kbd_send_leds();`

So the **firmware’s LED state** and the **report sent to the keyboard** stay aligned.

**Why not only patch `usb_task()` in `usb.c`?** Masking the Num Lock bit only in the outgoing `tuh_hid_set_report` byte, without updating `kbd_hid_leds`, can **desync** the host’s idea of Num Lock from the keyboard LED — e.g. first press after boot appears to do nothing. Upstream clears state in `kbd.c` and then sends LEDs through the normal path.

### 6.3 Community debugging (historical)

A closed PR tried a **one-time** Num Lock clear on first mount without `usb_boot_enumerating()` as a workaround when the window ended too early. Upstream’s `usb_init()` timer fix addresses the root cause; the PR is obsolete if you run **v0.20+** / current `main`.

---

## 7. What to Verify When Testing

1. **Actual VID:PID** — With the keyboard connected via your OTG adapter, confirm (e.g. via `status` or a temporary debug print in `tuh_hid_mount_cb`) that VID=0x04d9, PID=0x0006.
2. **Boot** — On **v0.20+**, cold boot: letters should type as letters without pressing Num Lock.
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

- Picocomputer author (Num Lock, Linux, PID/VID exception; `usb_init` boot window in **v0.20**; RF timing).
- RP6502 firmware: `rp6502/src/ria/hid/kbd.c` (`kbd_numlock_off_at_boot`, `kbd_mount`, `kbd_init`, `kbd_send_leds`), `rp6502/src/ria/usb/usb.c` (`usb_init`, `usb_boot_enumerating`, `usb_enum_timeout`, `usb_task` LED loop).
- USB IDs: [usb-ids.gowdy.us — Holtek 04d9 / 0006](https://usb-ids.gowdy.us/read/UD/04d9/0006).
- LEARNING_PLAN.md §3.3 — Troubleshooting “Num Lock on at boot (v0.18)”.
