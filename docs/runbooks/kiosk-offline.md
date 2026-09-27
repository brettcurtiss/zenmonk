---
icon: lucide/wifi-off
tags: [kiosk, network, offline]
---

# Kiosk offline

| | |
| --- | --- |
| **Owner** | Kiosk Platform (network: Field Ops) |
| **Typical severity** | SEV-3 (SEV-2 if a whole site is offline for more than 30 min) |
| **Alert(s)** | `KioskHeartbeatMissing`, `KioskOfflineModeActive`, `OfflineQueueBacklog` |
| **Last reviewed** | 2026-09-27 by Kiosk Platform |
| **Est. time to mitigate** | 20 min |

## Symptoms

- The kiosk shows a yellow **"Offline mode — pay at counter"** banner.
- There's been no heartbeat in ZenFleet for more than 3 minutes.
- Orders are building up in the kiosk's offline queue.

## Impact

Customers can still browse and place **pay-at-counter** orders, but card payment is unavailable. Queued orders reach the store's systems only after the kiosk reconnects.

!!! info "Two kinds of offline"
    - **Offline mode on, heartbeat OK:** the device is online, but the *app* can't reach the Kiosk API. Suspect the API, the gateway or DNS.
    - **No heartbeat:** the whole device is offline. Suspect the site network, power or the device itself.

## Diagnose

1. **Find out whether it's one kiosk or the whole site.**

    ```bash
    kioskctl fleet status --site PDX-014
    ```

    - **All kiosks at the site offline:** site network or power. Go to step 3.
    - **One kiosk offline:** the device or its cable. Go to step 2.
    - **Many sites in offline mode, heartbeats OK:** backend. Go to [Slow UI / API latency](slow-ui.md).

2. **For a single kiosk, check the last known network state:**

    ```bash
    kioskctl status KSK-0421 --verbose | grep -A6 Network
    ```

    ```text
    Network
      Primary   eth0  DOWN  (link lost 2026-09-27T14:02:11Z)
      Failover  wwan0 UP    LTE signal -97 dBm (weak)
      API reach FAIL  (timeout api.kiosk.example.com:443)
      Last seen 6m ago
    ```

3. **For a whole site,** check the site router in the NetEdge portal and confirm with site staff whether the store's own systems are also down.

## Mitigate

1. **Device online but app offline:** restart the app to force it to reconnect.

    ```bash
    kioskctl app restart KSK-0421
    ```

2. **Primary link down, LTE failover available:** make sure the failover route is active.

    ```bash
    kioskctl net failover KSK-0421 --enable
    ```

3. **No path back to the device:** ask site staff to check the Ethernet cable and the switch port, or to power-cycle the kiosk using the switch behind the service panel.

4. **Site-wide outage:** open a ticket with NetEdge (see [Vendors](../overview/contacts.md#vendors)) and tell the customer success manager for that site.

### Offline queue is large

When a kiosk has been offline for a long time, check the queue once it reconnects:

```bash
kioskctl queue status KSK-0421
```

```text
Pending orders   148
Oldest           2026-09-27T09:12:40Z
Last sync error  429 Too Many Requests
```

If syncing is being rate limited, let it drain on its own. The queue backs off and retries. **Do not clear the queue**, because that permanently deletes customer orders.

!!! danger "Never run `kioskctl queue clear` without approval"
    Clearing the queue deletes unsynced customer orders. It needs IC approval and an export first:
    `kioskctl queue export KSK-0421 > KSK-0421-queue.json`

## Verify

- [ ] Heartbeat is current in ZenFleet
- [ ] The offline banner is gone and card payment is offered again
- [ ] `kioskctl queue status` shows `Pending orders 0` (allow up to 5 minutes)

## Escalate

- **Whole site offline for more than 30 min during opening hours:** SEV-2. Notify Field Ops and the customer success manager.
- **Queue not draining after 15 min online:** page the Backend API on-call.

## Related

- [Architecture → request flow](../overview/architecture.md#request-flow-customer-checkout)
- [Slow UI / API latency](slow-ui.md)
