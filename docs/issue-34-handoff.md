# Issue #34 Handoff: Ruff Lint

## Goal

Issue #34 asks for a minimal Ruff configuration using the default rules plus import sorting, a lint step in CI, and the local lint command in `CONTRIBUTING.md`. Existing findings must be fixed or narrowly ignored.

## Current Changes

- `pyproject.toml` extends Ruff's default lint rules with `I` for import sorting.
- `.github/workflows/ci.yml` installs Ruff and runs `ruff check .` in the Python 3.12 CI job.
- `CONTRIBUTING.md` documents `ruff check .` for contributors.

Ruff is intentionally not a runtime requirement. Avoid blanket ignores; inspect and fix findings first, adding only justified narrow ignores if needed.

## Branch And Verification

- Working branch: `chore/ruff-lint-ci`, created from `main`.
- The branch is local and has not been pushed.
- `git diff --check` passed.
- `ruff check .` has not yet returned a final result. The host has no Python/Ruff installed. A Docker attempt initially failed while Docker Desktop was updating; a retry pulled `python:3.12-slim`, but no final pip/Ruff output was received before handoff.
- The pytest suite has not been run.

## Resume Steps

From the repository root, rerun the lint in a disposable Python 3.12 container:

```powershell
docker run --rm -v "${PWD}:/workspace" -w /workspace python:3.12-slim sh -c "pip install --disable-pip-version-check ruff && ruff check ."
```

Review every reported finding, fix real issues or add narrowly scoped documented ignores, then rerun `ruff check .`. Run `pytest` if Python dependencies are available. Before opening the PR, summarize the config, CI gate, documentation, findings addressed, and validation results.