# zenmonk

IT operations runbooks for the **ZenMonk Kiosk** — a React-based touch-screen application — published with [Zensical](https://zensical.org).

> **Note:** All content is demo material. Names, hosts and commands are fictional.

## Quick start

Requires [uv](https://docs.astral.sh/uv/).

```bash
uv sync                    # create .venv and install dependencies
uv run zensical serve      # preview at http://localhost:8000
uv run zensical build      # build static site into ./site
```

Enable the lint hooks (markdownlint, whitespace, strict build):

```bash
uv run pre-commit install
uv run pre-commit run --all-files
```

## Layout

```text
.
├── docs/                   # Markdown sources
│   ├── overview/           # service overview, architecture, contacts
│   ├── incidents/          # incident process, severity, comms
│   ├── runbooks/           # symptom-based runbooks
│   ├── reference/          # health checks, kioskctl, glossary
│   └── contributing/       # contributor guide + runbook template
├── includes/               # snippets auto-appended to every page (abbreviations)
├── zensical.toml           # site configuration and navigation
├── pyproject.toml / uv.lock
└── .github/workflows/docs.yml   # build + deploy to GitHub Pages on push to main
```

## Branching

- `dev` — integration branch for documentation changes
- `main` — published; every push deploys to GitHub Pages

To enable publishing, set **Settings → Pages → Source** to **GitHub Actions** in the repository.
