# Issue #34 Handoff: Ruff Lint

## Goal

Issue #34 asks for a minimal Ruff configuration using the default rules plus import sorting, a lint step in CI, and the local lint command in `CONTRIBUTING.md`. Existing findings must be fixed or narrowly ignored.

## Current Changes

- `pyproject.toml` extends Ruff's default lint rules with `I` for import sorting.
- `.github/workflows/ci.yml` installs Ruff and runs `ruff check .` in the Python 3.12 CI job.
- `CONTRIBUTING.md` documents `ruff check .` for contributors.

Ruff is intentionally not a runtime requirement. Avoid blanket ignores; inspect and fix findings first, adding only justified narrow ignores if needed.

## Owner Guidance

- The owner approved AI-assisted development. Review and understand every change, run the relevant checks, and disclose AI use in the PR description.
- Keep the Ruff configuration to default rules plus `I`.
- Do not run repo-wide `ruff format` in this PR. If formatting-only changes become necessary and large, separate them from real fixes in another commit or PR.
- Keep the CI and contributor documentation changes requested in the issue.

## Branch And Verification

- Working branch: `chore/ruff-lint-ci`, created from `main`.
- Base implementation commit `90d4ad1` is pushed to `origin/chore/ruff-lint-ci`.
- Import-only commit `3240751` is on the working branch; it sorted 80 `I001` findings across Python files.
- The reviewed Ruff cleanup spans 58 files: safe fixes, deterministic finding corrections, line-level suppressions, and the handoff update.
- Ruff 0.17.0 ran in Docker. A Windows bind mount reported 64 false-positive `EXE002` findings because it exposed tracked `100644` Python files as executable. Re-running against a temporary copy with Python-file modes normalized to `644` removed those platform artifacts.
- The CI-like `ruff check .` now passes. Ruff's current defaults produced 857 findings initially; 640 safe fixes were applied, deterministic findings were corrected, and 52 intentional exception boundaries have line-level `BLE001` suppressions. No rule group is disabled globally.
- `LLM_API_KEY=test-key pytest -q` in Python 3.12 Docker: 88 passed, 1 skipped, 8 deselected, 1 warning after the final Ruff fixes.
- `git diff --check` passes.
- No repo-wide `ruff format` was run.
- No manual UI test is needed; this change does not alter UI behavior.

## Resume Steps

From the repository root, Ruff can be run in a disposable Python 3.12 container. On Windows, normalize Python file modes in a temporary copy before treating `EXE002` as a project finding; CI checks out the tracked `100644` modes correctly.

```powershell
docker run --rm -v "${PWD}:/workspace" -w /workspace python:3.12-slim sh -c "pip install --disable-pip-version-check ruff && ruff check ."
```

Before opening the PR, review the final commit range and CI result. Summarize the config, CI gate, documentation, findings addressed, validation results, and disclose AI assistance as requested by the owner.