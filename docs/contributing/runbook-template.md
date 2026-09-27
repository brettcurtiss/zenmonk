---
icon: lucide/file-plus
---

# Runbook template

Copy the block below into a new file under `docs/runbooks/`, name the file after the symptom in kebab case (for example `badge-reader-failed.md`), and add it to `nav` in `zensical.toml`.

````markdown
---
icon: lucide/<icon-name>
tags: [kiosk, <area>]
---

# <Symptom as the customer or alert describes it>

| | |
| --- | --- |
| **Owner** | <team> |
| **Typical severity** | SEV-<n> |
| **Alert(s)** | `<alert-name>` |
| **Last reviewed** | YYYY-MM-DD by <name> |
| **Est. time to mitigate** | <n> min |

## Symptoms

- What the customer sees on the screen.
- What the alert or dashboard shows.

## Impact

Who is affected and what they cannot do. Is there a workaround?

## Before you start

- [ ] Incident declared and severity assigned (if customer impacting)
- [ ] Access you need: `kioskctl` role `<role>`, dashboard `<name>`

## Diagnose

1. First check — the command, and what good and bad output look like.

    ```bash
    kioskctl status KSK-0000
    ```

2. Next check …

## Mitigate

1. Least disruptive fix first.
2. Escalating fixes after that. Mark destructive steps with a `!!! danger` admonition.

## Verify

- [ ] Customer-visible check (tap through the flow)
- [ ] Metric or alert recovered

## Escalate

When to escalate and to whom (link to [Contacts](../overview/contacts.md)).

## Related

- Links to other runbooks, dashboards, and postmortems.
````

## Writing guidelines

- **Title = symptom, not cause.** On-call engineers search for what they *see*.
- **Every command is copy-pasteable.** Use `KSK-0000` for placeholders and tell the reader to replace it.
- **Show expected output** for any check whose result drives a decision.
- **Least disruptive first.** Order fixes from read-only, to restart, to reboot, to reprovision or roll back.
- **Mark destructive actions** with a `!!! danger` admonition.
- **Keep the metadata current.** Update *Last reviewed* whenever you validate the runbook.
