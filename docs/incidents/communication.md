---
icon: lucide/megaphone
---

# Customer communication

The **Comms lead** owns these updates. Use plain language, name the symptom customers actually see, and never guess at an ETA.

## Status page templates

=== "Investigating"

    ```text
    Title: Self-service kiosks unavailable at some locations

    We are investigating reports that self-service kiosks at some locations
    are not responding. Staff-assisted checkout is available at the counter.
    Next update in 30 minutes.
    ```

=== "Identified"

    ```text
    We have identified the cause of the kiosk issue and are applying a fix.
    Kiosks may restart automatically in the next few minutes. Staff-assisted
    checkout remains available. Next update in 30 minutes.
    ```

=== "Monitoring"

    ```text
    A fix has been applied and kiosks are recovering. We are monitoring to
    confirm all locations are back to normal. Orders placed while kiosks were
    in offline mode are being synchronized.
    ```

=== "Resolved"

    ```text
    The issue affecting self-service kiosks has been resolved. All kiosks are
    operating normally. We apologize for the inconvenience. A summary will be
    shared with affected customers.
    ```

## Internal update template

Post this in the incident channel at the cadence your [severity](severity.md) requires:

```markdown
**Status:** Investigating | Identified | Monitoring | Resolved
**Severity:** SEV-2
**Impact:** 38 kiosks at 12 sites showing white screen; counter checkout OK
**Current action:** Rolling back web release 2026.09.3 → 2026.09.2 (ring 2)
**Next update:** 14:30 PT
```

!!! warning "Do not share"
    Keep internal hostnames, stack traces, customer names and security details out of customer-facing updates.
