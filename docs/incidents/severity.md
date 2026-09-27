---
icon: lucide/gauge
---

# Severity levels

| Severity | Definition | Examples | Response | Updates |
| --- | --- | --- | --- | --- |
| <span class="sev sev-1">SEV-1</span> | Fleet-wide or multi-site outage. Customers cannot complete transactions. | All kiosks show a white screen after a release. Checkout API is down. | Page immediately, 24 × 7. IC required. | Every 30 min |
| <span class="sev sev-2">SEV-2</span> | Major degradation, or a full outage at a high-value site. | Checkout P95 above 5 s. Every kiosk at one flagship site is offline. | Page immediately, 24 × 7. IC required. | Every 60 min |
| <span class="sev sev-3">SEV-3</span> | Some kiosks affected, with a workaround available. | One kiosk with dead touch input. A site running in offline mode. | Business hours, or next on-call shift | Daily |
| <span class="sev sev-4">SEV-4</span> | Cosmetic issue or no customer impact. | A typo on a screen. One noisy alert. | Normal backlog | — |

## Upgrade and downgrade rules

- **Upgrade** when impact spreads, for example when a SEV-3 on one kiosk turns out to affect a whole site.
- **Upgrade** a SEV-3 to SEV-2 if it has no workaround and lasts more than 4 hours during site opening hours.
- **Downgrade** only once impact is mitigated. The IC must post the change in the incident channel.

!!! question "Unsure which severity?"
    Pick the higher one. Downgrading costs nothing, but under-reacting costs customers.
