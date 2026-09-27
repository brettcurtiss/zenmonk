---
icon: lucide/house
hide:
  - navigation
  - toc
---

# ZenMonk Kiosk Runbooks

Operational documentation for **ZenMonk Kiosk**, the React-based touch-screen application our customers use at check-in and self-service kiosks.

!!! warning "Demo content"
    This site is a demonstration. Company names, hostnames, commands and contact details are fictional.

## Start here

<div class="grid cards" markdown>

-   :lucide-siren:{ .lg .middle } **Something is broken right now**

    ---

    Declare the incident, assign an incident commander, and open the runbook that matches the symptom.

    [:octicons-arrow-right-24: Incident response](incidents/index.md)

-   :lucide-book-open-check:{ .lg .middle } **Find a runbook**

    ---

    Step-by-step procedures for the most common kiosk and application failures.

    [:octicons-arrow-right-24: Runbook index](runbooks/index.md)

-   :lucide-network:{ .lg .middle } **Understand the system**

    ---

    What the kiosk app is made of, how it talks to the backend, and what it does when it goes offline.

    [:octicons-arrow-right-24: Architecture](overview/architecture.md)

-   :lucide-phone-call:{ .lg .middle } **Get help**

    ---

    On-call rotations, escalation paths and vendor contacts.

    [:octicons-arrow-right-24: Contacts & escalation](overview/contacts.md)

</div>

## Most-used runbooks

| Symptom seen by the customer | Runbook | Typical severity |
| --- | --- | --- |
| Screen shows content but ignores taps | [Touch input unresponsive](runbooks/touch-unresponsive.md) | SEV-3 |
| Blank white screen or "Something went wrong" | [White screen / app crash](runbooks/white-screen.md) | SEV-2 |
| "Offline mode" banner, orders not syncing | [Kiosk offline](runbooks/kiosk-offline.md) | SEV-3 |
| Spinners, slow screens, timeouts | [Slow UI / API latency](runbooks/slow-ui.md) | SEV-2 |
| Problems right after a release | [Deploy & rollback](runbooks/deploy-rollback.md) | SEV-2 |
