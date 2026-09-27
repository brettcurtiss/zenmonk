---
icon: lucide/heart-pulse
---

# Health checks

| Check | Source | Interval | Healthy when | Alert |
| --- | --- | --- | --- | --- |
| **Heartbeat** | ZenFleet agent | 60 s | Last seen < 3 min | `KioskHeartbeatMissing` |
| **UI health** | App posts `/health/ui` after first render + every 5 min | 5 min | React root mounted, no error boundary, SW active | `KioskUIHealthFailing` |
| **Touch events** | Agent counts HID events | 5 min | > 0 events in 30 min during site hours | `KioskNoTouchEvents` |
| **Offline queue** | App reports queue depth | 5 min | < 20 pending and oldest < 15 min | `OfflineQueueBacklog` |
| **API reachability** | App → `GET /v1/ping` | 60 s | 2xx in < 1 s | `KioskOfflineModeActive` |
| **Kiosk API latency** | Gateway metrics | 1 min | P95 < 250 ms | `KioskAPILatencyP95High` |

## UI health payload

The React app sends this after the first successful render, and every 5 minutes after that:

```json title="POST /health/ui"
{
  "kioskId": "KSK-0421",
  "appVersion": "2026.09.3",
  "swState": "activated",
  "errorBoundaryTripped": false,
  "offlineQueueDepth": 0,
  "lastTouchAt": "2026-09-27T14:21:03Z",
  "memoryMB": 212
}
```

!!! tip
    When `memoryMB` climbs steadily across reports, that points to a memory leak in the SPA. The nightly 03:00 app restart hides it, so compare the value against uptime since the last restart.
