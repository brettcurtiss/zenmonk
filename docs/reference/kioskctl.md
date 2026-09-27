---
icon: lucide/terminal
---

# `kioskctl` reference

`kioskctl` is the CLI for the ZenFleet control plane. Authenticate with `kioskctl login` (SSO). Your role decides which commands you can run.

| Role | Can do |
| --- | --- |
| `fleet-viewer` | `status`, `logs`, `fleet status`, `queue status`, `release list/health` |
| `fleet-operator` | Everything `fleet-viewer` can, plus `app restart`, `app mode`, `touch *`, `cache clear`, `reboot`, `net failover` |
| `release-manager` | Everything `fleet-operator` can, plus `release promote/rollback/block`, `feature set` |

## Common commands

| Command | What it does | Disruptive? |
| --- | --- | --- |
| `kioskctl status <id> [--verbose]` | Health summary for one kiosk | No |
| `kioskctl fleet status [--site S] [--unhealthy] [--group-by F]` | Fleet or site overview | No |
| `kioskctl logs <id> --unit U --since D` | Stream logs (`kiosk-ui`, `agent`, `kernel`) | No |
| `kioskctl touch events <id> --duration D` | Stream raw touch events | No |
| `kioskctl touch calibrate <id>` | Reapply the calibration profile | No |
| `kioskctl touch reset <id>` | Power-cycle the USB touch controller | Brief |
| `kioskctl app restart <id> [--when idle]` | Restart Chromium / the React app | ~10 s |
| `kioskctl app mode <id> normal\|maintenance` | Show or clear the "Out of order" screen | Yes |
| `kioskctl cache clear <id> --scope service-worker` | Clear cached app assets | Needs restart |
| `kioskctl queue status\|export <id>` | Inspect or export the offline order queue | No |
| `kioskctl reboot <id> --reason R` | Reboot the device | ~90 s |
| `kioskctl net failover <id> --enable` | Force the LTE failover route | Brief |
| `kioskctl release list\|health\|promote\|rollback\|block` | Manage web releases | Yes |
| `kioskctl feature set <flag>=<value> --ttl D` | Set a fleet feature flag temporarily | Yes |

!!! info "Targets"
    Most commands take a single kiosk ID, or one of `--site <code>`, `--ring <ring>` or `--all`. Commands that target more than 25 kiosks ask for confirmation unless you pass `--yes`.
