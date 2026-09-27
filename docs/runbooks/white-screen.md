---
icon: lucide/monitor-x
tags: [kiosk, frontend, react]
---

# White screen / app crash

| | |
| --- | --- |
| **Owner** | Kiosk Platform |
| **Typical severity** | SEV-2 (SEV-1 if fleet-wide) |
| **Alert(s)** | `KioskUIHealthFailing`, `FrontendErrorRateHigh` |
| **Last reviewed** | 2026-09-27 by Kiosk Platform |
| **Est. time to mitigate** | 15 min |

## Symptoms

- A **blank white screen** with no attract screen.
- The React error boundary screen: *"Something went wrong — tap to restart"*.
- The UI loads, freezes, and reloads over and over (a crash loop).
- A spike in errors in the frontend error tracker, often a `ChunkLoadError` or `TypeError`.

## Impact

Customers cannot use the affected kiosks. When many kiosks are hit at once, the cause is almost always a **release** or a **CDN** problem rather than the devices.

## Diagnose

1. **Work out the blast radius first.**

    ```bash
    kioskctl fleet status --check ui-health --group-by app_version
    ```

    ```text title="Example: failures concentrated on one version"
    APP VERSION   KIOSKS   UI HEALTHY   UI FAILING
    2026.09.3        412          21          391   ✘
    2026.09.2        790         788            2
    ```

    If failures cluster on the newest version, **go straight to [Deploy & rollback](deploy-rollback.md#roll-back)**. Don't debug individual kiosks.

2. **Check the frontend error tracker** at `https://errors.example.com/zenmonk-kiosk` and filter by `release:<version>`. The most common errors:

    | Error | Likely cause | Next step |
    | --- | --- | --- |
    | `ChunkLoadError: Loading chunk 412 failed` | The service worker is serving a stale `index.html` that points at chunks the CDN has purged | [Clear the SW cache](#clear-service-worker-cache) |
    | `TypeError: Cannot read properties of undefined` in the render path | A code bug, often an API contract change | Roll back |
    | `Failed to fetch /config.json` | CDN or config outage | Check CDN status, then [Kiosk offline](kiosk-offline.md) |
    | `QuotaExceededError` | IndexedDB is full, usually a large offline queue | [Kiosk offline](kiosk-offline.md#offline-queue-is-large) |

3. **On a single kiosk,** pull the Chromium console log:

    ```bash
    kioskctl logs KSK-0421 --unit kiosk-ui --since 30m
    ```

## Mitigate

### Restart the app

This is the least disruptive fix. It restarts Chromium but leaves the device running (about 10 seconds):

```bash
kioskctl app restart KSK-0421
```

For every failing kiosk at a site:

```bash
kioskctl app restart --site PDX-014 --only-unhealthy
```

### Clear service worker cache

Use this for `ChunkLoadError`, or when the UI is stuck on an old version after a deploy:

```bash
kioskctl cache clear KSK-0421 --scope service-worker
kioskctl app restart KSK-0421
```

!!! warning "Keep the offline queue"
    `--scope service-worker` clears only cached assets. **Never** use `--scope all` while the kiosk has unsynced orders. Check first:

    ```bash
    kioskctl queue status KSK-0421
    ```

### Roll back the release

If failures follow a release, use the rollback procedure in [Deploy & rollback](deploy-rollback.md#roll-back).

## Verify

- [ ] `kioskctl fleet status --check ui-health` is back to its normal baseline (under 0.5 % failing)
- [ ] The error rate in the error tracker has returned to baseline
- [ ] A kiosk on site shows the attract screen and a test order can be started

## Escalate

- **More than 5 % of the fleet failing:** SEV-1. Page the Kiosk Platform on-call and the engineering manager.
- **Errors mention API responses** (`4xx`/`5xx` in the stack): bring in the Backend API on-call.

## Related

- [Deploy & rollback](deploy-rollback.md)
- [Kiosk offline](kiosk-offline.md)
- [Architecture → Frontend](../overview/architecture.md#frontend-react-spa)
