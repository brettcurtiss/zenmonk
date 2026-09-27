---
icon: lucide/book-open-check
---

# Runbooks

Find the runbook by the **symptom** you are seeing. Each runbook follows the same structure: *Symptoms → Impact → Diagnose → Mitigate → Verify → Escalate*.

| Runbook | Symptom | Typical severity | Est. time to mitigate |
| --- | --- | --- | --- |
| [Touch input unresponsive](touch-unresponsive.md) | Screen displays normally but taps do nothing or land in the wrong place | <span class="sev sev-3">SEV-3</span> | 10 min |
| [White screen / app crash](white-screen.md) | Blank white screen, "Something went wrong" screen, or a crash loop | <span class="sev sev-2">SEV-2</span> | 15 min |
| [Kiosk offline](kiosk-offline.md) | Offline banner, heartbeat missing, orders queued | <span class="sev sev-3">SEV-3</span> | 20 min |
| [Slow UI / API latency](slow-ui.md) | Spinners, slow screen transitions, timeouts at checkout | <span class="sev sev-2">SEV-2</span> | 30 min |
| [Deploy & rollback](deploy-rollback.md) | Rolling out a web release, or reverting a bad one | <span class="sev sev-2">SEV-2</span> | 10 min |

!!! tip "No runbook fits?"
    Follow the general [incident process](../incidents/index.md), then write the missing runbook from the [template](../contributing/runbook-template.md) once the incident is over.

## Quick triage

```mermaid
flowchart TD
  S{"What does the<br/>kiosk show?"} -->|"Normal UI, taps ignored"| T["Touch input unresponsive"]
  S -->|"White / error screen"| W["White screen / app crash"]
  S -->|"Offline banner"| O["Kiosk offline"]
  S -->|"UI works but slow"| L["Slow UI / API latency"]
  S -->|"Black screen / no power"| F["Field Ops dispatch"]
  W --> R{"Started right<br/>after a release?"}
  R -->|Yes| D["Deploy & rollback"]
  click T "touch-unresponsive/"
  click W "white-screen/"
  click O "kiosk-offline/"
  click L "slow-ui/"
  click D "deploy-rollback/"
```
