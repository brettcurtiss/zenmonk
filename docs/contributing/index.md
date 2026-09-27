---
icon: lucide/git-pull-request
---

# Contributing

This site is built with [Zensical](https://zensical.org) and managed with [uv](https://docs.astral.sh/uv/).

## Local setup

```bash
uv sync                      # install Zensical + dev tools into .venv
uv run pre-commit install    # enable lint hooks on commit
uv run zensical serve        # live preview at http://localhost:8000
```

## Workflow

1. Branch from `dev`, for example `docs/runbook-badge-reader`.
2. Add or edit pages under `docs/`. Start new runbooks from the [runbook template](runbook-template.md).
3. Add any new page to `nav` in `zensical.toml`.
4. Run the checks locally:

    ```bash
    uv run pre-commit run --all-files
    uv run zensical build --clean --strict
    ```

5. Open a pull request into `dev`. Merging `dev` into `main` publishes the site to GitHub Pages.

## Style

- Write in the second person, imperative mood: "Restart the app", not "The app should be restarted".
- Use admonitions deliberately: `tip` for shortcuts, `warning` for gotchas, `danger` for destructive or customer-visible actions.
- Use `KSK-0421` and `PDX-014` as example IDs, and tell the reader to substitute their own.
- Add abbreviations to `includes/abbreviations.md`. They get tooltips on every page automatically.
