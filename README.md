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

### PDF

The site is also published as a single PDF using [prodockit](https://prodockit.org/) (Pandoc + WeasyPrint).

```bash
brew install pango                     # macOS, once (Linux: libpango-1.0-0 libpangoft2-1.0-0 libharfbuzz-subset0)
uv run zensical build --clean          # the PDF is made from the built site
uv run pdk pdf                         # writes docs/ and site/zenmonk-kiosk-runbooks.pdf
```

On first run `pdk pdf` downloads verified Pandoc, fonts and a Mermaid renderer into `.prodockit/` (git-ignored).
PDF layout lives in `pdk-pdf.toml`; print-only CSS in `docs/stylesheets/print.css`.
Mark content with `{ .web-only }` / `.pdf-only` classes to show it in only one output.

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
├── pdk-pdf.toml            # PDF settings (prodockit)
├── pdf-requirements.txt    # PDF-only Python deps policy (read by prodockit)
├── pyproject.toml / uv.lock
└── .github/workflows/docs.yml   # build site + PDF on push/PR; deploy Pages from main
```

## Branching

- `dev` — integration branch for documentation changes
- `main` — published; every push deploys the site (with the PDF) to GitHub Pages

Every run on `main`, `dev` or a pull request also attaches the PDF as the
`zenmonk-kiosk-runbooks-pdf` artifact on the workflow run.

To enable publishing, set **Settings → Pages → Source** to **GitHub Actions** in the repository.
