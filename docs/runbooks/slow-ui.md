---
icon: lucide/timer
tags: [backend, api, performance]
---

# Slow UI / API latency

| | |
| --- | --- |
| **Owner** | Backend API (with Kiosk Platform) |
| **Typical severity** | SEV-2 |
| **Alert(s)** | `KioskAPILatencyP95High` (> 1 s for 10 min), `TapToResponseSLOBurn` |
| **Last reviewed** | 2026-09-27 by Backend API |
| **Est. time to mitigate** | 30 min |

## Symptoms

- Spinners on screen transitions, and the catalog takes a long time to load.
- Checkout times out with *"This is taking longer than usual"*.
- The RUM dashboard shows tap-to-response P95 above 300 ms.
- The Kiosk API dashboard shows P95 latency or 5xx errors climbing.

## Impact

Customers give up on transactions and queues form at the counter. This usually affects **many sites at once**.

## Diagnose

1. **Is it the frontend or the backend?** On the RUM dashboard, compare *tap-to-response* with *API time*.

    - Both are high: backend. Keep going with this runbook.
    - Only tap-to-response is high: the device or the frontend (for example, a new release with a heavy render). Check [Deploy & rollback](deploy-rollback.md).

2. **Look at the Kiosk API golden signals** on `https://grafana.example.com/d/kiosk-api`:

    | Signal | Normal | What it suggests if high |
    | --- | --- | --- |
    | Request rate | 800–1,500 rps | Traffic spike or retry storm |
    | P95 latency | < 250 ms | See below |
    | 5xx rate | < 0.1 % | Errors, pod crashes |
    | Pod CPU | < 60 % | Needs scaling |
    | DB connections | < 70 % of pool | Pool exhaustion or slow queries |

3. **Check the pods:**

    ```bash
    kubectl -n kiosk get pods -l app=kiosk-api
    kubectl -n kiosk top pods -l app=kiosk-api
    ```

4. **Check the dependencies:**

    === "PostgreSQL"

        ```sql
        -- Longest running queries
        SELECT pid, now() - query_start AS duration, state, left(query, 80)
        FROM pg_stat_activity
        WHERE state <> 'idle'
        ORDER BY duration DESC
        LIMIT 10;
        ```

    === "Redis"

        ```bash
        redis-cli -h kiosk-cache.internal INFO stats | grep -E "hits|misses|evicted"
        ```

        A cache hit ratio below 90 % means the cache is cold or evicting keys, so more load falls on PostgreSQL.

    === "Payments"

        Check `https://status.payflow.example`. Slow payment calls affect **checkout only**. Browsing stays fast.

## Mitigate

1. **Scale out the API** if CPU-bound:

    ```bash
    kubectl -n kiosk scale deploy/kiosk-api --replicas=12
    ```

2. **Retry storm** (request rate jumps while latency rises): turn on server-side load shedding so kiosks back off.

    ```bash
    kioskctl feature set api.load_shedding=on --ttl 2h
    ```

3. **Slow query or lock:** cancel the offending query with `SELECT pg_cancel_backend(<pid>);`, then open a follow-up ticket.

4. **Payments provider degraded:** switch kiosks to *pay at counter* until the provider recovers.

    ```bash
    kioskctl feature set checkout.card_payments=off --ttl 1h
    ```

5. **Started with a backend deploy:** roll back the API using the standard backend deploy pipeline.

## Verify

- [ ] Kiosk API P95 is under 250 ms for 15 minutes
- [ ] Tap-to-response P95 is under 300 ms
- [ ] Temporary feature flags (`load_shedding`, `card_payments`) are reverted, or their TTL has expired

## Escalate

- **P95 still above 1 s after 30 min:** page the Backend API secondary and the engineering manager.
- **Database suspected:** bring in the DBA on-call.

## Related

- [Kiosk offline](kiosk-offline.md) — kiosks switch to offline mode when API calls keep timing out
- [Service overview → SLOs](../overview/index.md#service-level-objectives)
