---
icon: lucide/pointer
tags: [kiosk, hardware, touch]
---

# Touch input unresponsive

| | |
| --- | --- |
| **Owner** | Kiosk Platform |
| **Typical severity** | SEV-3 (SEV-2 if all kiosks at a site are affected) |
| **Alert(s)** | `KioskNoTouchEvents` — no touch events for 30 min during site hours |
| **Last reviewed** | 2026-09-27 by Kiosk Platform |
| **Est. time to mitigate** | 10 min |

## Symptoms

- The UI looks normal (the attract screen animates), but taps do nothing.
- Taps register **in the wrong place**, usually offset or mirrored.
- Taps work only on part of the screen.
- Idle-watchdog resets show up in the logs, but no touch events do.

## Impact

Customers cannot use this kiosk and are sent to the counter. It usually affects one kiosk.

## Before you start

- [ ] Ticket or incident opened with the kiosk ID (`KSK-####`)
- [ ] You have `kioskctl` role `fleet-operator`

## Diagnose

1. **Check the kiosk's status.** Replace `KSK-0421` with the affected kiosk.

    ```bash
    kioskctl status KSK-0421
    ```

    ```text title="Example: touch controller missing"
    Kiosk      KSK-0421  (site: PDX-014 "Pearl District")
    Heartbeat  12s ago            ✔
    App        2026.09.3 running  ✔
    Display    1920x1080 @60Hz    ✔
    Touch      NOT DETECTED       ✘   # (1)!
    Uptime     19d 04h
    ```

    1.  `NOT DETECTED` means the OS cannot see the USB touch controller. Go to [Mitigate → step 2](#mitigate). If it shows `detected`, the hardware is present and the problem is calibration or the app.

2. **Check whether the OS is receiving touch events.** This streams raw input for 15 seconds. Ask someone on site to tap the screen while it runs.

    ```bash
    kioskctl touch events KSK-0421 --duration 15s
    ```

    - **No events at all:** hardware, cable or controller problem.
    - **Events arrive, but the app ignores them:** app problem. Go to [White screen / app crash](white-screen.md) and treat it as a frozen UI.
    - **Events arrive with wrong coordinates:** calibration problem.

3. **Look for USB errors in the kiosk logs.**

    ```bash
    kioskctl logs KSK-0421 --unit kernel --since 2h | grep -iE "usb|hid|touch"
    ```

    Repeated `usb disconnect` / `new full-speed USB device` lines point to a loose cable or failing controller.

## Mitigate

1. **Recalibrate** (wrong coordinates only):

    ```bash
    kioskctl touch calibrate KSK-0421 --profile default
    ```

    This reapplies the calibration profile for the panel model. It takes effect immediately, with no restart needed.

2. **Reset the USB touch controller** (controller not detected, or no events):

    ```bash
    kioskctl touch reset KSK-0421
    ```

    This power-cycles the USB port. Wait 20 seconds, then repeat [Diagnose step 1](#diagnose).

3. **Reboot the kiosk** if the controller still isn't detected:

    !!! danger "Customer-visible"
        The kiosk shows a black screen for about 90 seconds. Check that no customer is mid-transaction. `kioskctl` warns you if a session is active.

    ```bash
    kioskctl reboot KSK-0421 --reason "touch controller not detected"
    ```

4. **Dispatch Field Ops** if the controller still isn't detected after a reboot. Include the kiosk ID, the panel model from `kioskctl status --verbose`, and the log excerpt from Diagnose step 3. While you wait, put the kiosk in maintenance mode so customers see an "Out of order" screen instead of a frozen one:

    ```bash
    kioskctl app mode KSK-0421 maintenance --message "Please see the counter"
    ```

## Verify

- [ ] `kioskctl status` shows `Touch detected ✔`
- [ ] Someone on site taps through the whole flow: attract screen → browse → cart → (cancel)
- [ ] Taps land in all four corners of the screen
- [ ] Maintenance mode cleared: `kioskctl app mode KSK-0421 normal`

## Escalate

- **Several kiosks at one site:** suspect a site-wide cause such as a power event or a bad firmware push. Raise to SEV-2 and page the Kiosk Platform on-call.
- **Same kiosk more than twice in 30 days:** open a hardware RMA with PanelCo. See [Contacts](../overview/contacts.md#vendors).

## Related

- [White screen / app crash](white-screen.md) — for a frozen UI where touch events do arrive
- [`kioskctl` reference](../reference/kioskctl.md)
