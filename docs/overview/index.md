---
icon: lucide/info
---

# Service overview

**ZenMonk Kiosk** is a customer-facing, touch-first web application that runs full-screen on self-service kiosks at customer sites (retail pickup counters, clinic check-in, venue ticketing).

## At a glance

| | |
| --- | --- |
| **Service owner** | Kiosk Platform team |
| **Business criticality** | Tier 1 — customer facing, revenue impacting |
| **Users** | End customers at ~1,200 kiosks across ~340 sites |
| **Hours of operation** | 24 × 7 (site hours vary by customer) |
| **Frontend** | React 18 + TypeScript SPA, built with Vite, installed as a PWA |
| **Runtime on device** | Chromium in kiosk mode on ZenOS (hardened Linux) |
| **Backend** | Kiosk API (Kubernetes), PostgreSQL, Redis |
| **Fleet management** | ZenFleet agent + `kioskctl` CLI |

## Service level objectives

| SLO | Target | Measured by |
| --- | --- | --- |
| Kiosk availability (app interactive) | 99.5 % per site, monthly | Agent heartbeat + UI health ping |
| Tap-to-response latency | P95 < 300 ms | Real-user monitoring (RUM) |
| Checkout API success rate | 99.9 % | API gateway metrics |
| Offline transaction sync | 99 % synced within 5 min of reconnect | Sync queue metrics |

!!! tip "Error budget"
    When a site has burned more than 50 % of its monthly error budget, non-urgent releases to that site's ring are paused. See [Deploy & rollback](../runbooks/deploy-rollback.md).

## Key dashboards

- **Fleet health** — `https://grafana.example.com/d/kiosk-fleet`
- **Kiosk API** — `https://grafana.example.com/d/kiosk-api`
- **Frontend errors** — `https://errors.example.com/zenmonk-kiosk`
- **Status page (customer facing)** — `https://status.example.com`
