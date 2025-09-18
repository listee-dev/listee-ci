# Repository Guidelines

## Project Structure and Module Organization
- This repository serves as an aggregation of reusable GitHub Actions. It does not contain source code for applications or libraries.
- Common workflows are located in `.github/workflows/`: `lint.yml` / `test.yml` / `typecheck.yml` / `release.yml`.
- `.github/actions/setup-bun/` contains a composite action that installs Bun and runs `bun install --frozen-lockfile`.

## Build, Test, and Development Commands
- No build artifact is produced here; validate changes locally with `act`.
- Example usage (Apple Silicon: add `--container-architecture linux/amd64`):
  - `act -j lint -W .github/workflows/lint.yml --container-architecture linux/amd64`
  - `act -j test -W .github/workflows/test.yml --container-architecture linux/amd64`
  - `act -j typecheck -W .github/workflows/typecheck.yml --container-architecture linux/amd64`
- Optional static analysis: `actionlint`.
- Pin verification: run `pinact run --check` locally or via the `pinact.yml` workflow.

## Local act Configuration
- Copy `actrc.example` to your OS‑specific config to omit the architecture flag:
  - macOS: `$HOME/Library/Application Support/act/actrc`
  - Linux: `$HOME/.actrc`
- The example sets `--container-architecture linux/amd64` and a default image mapping.

## Coding Standards and Naming Conventions
- YAML uses 2‑space indentation; filenames are `kebab-case.yml`.
- Job IDs are lowercase and concise (e.g., `lint`, `test`); step `name` is human‑readable.
- External actions should use stable tags (e.g., `actions/checkout@v5`). The internal composite action is referenced relatively: `./.github/actions/setup-bun` so it works both with `act` and on GitHub.

## Testing Guidelines
- For changes, verify basic cases using `act` and optionally run tests in a dedicated sandbox repository.
- Example call syntax for downstream repositories:
  ```yaml
  jobs:
    lint:      { uses: listee-dev/listee-ci/.github/workflows/lint.yml@v1 }
    test:      { uses: listee-dev/listee-ci/.github/workflows/test.yml@v1 }
    typecheck: { uses: listee-dev/listee-ci/.github/workflows/typecheck.yml@v1 }
  ```

## Committing and Pull Request Process
- Conventional Commits format is recommended (e.g., `feat(lint): Enhance CI for Biome`).
- PRs should include purpose, key changes, impact scope, and verification instructions (`act` command examples). Link relevant issues.

## Security and Configuration Notes
- `release.yml` requires the `NPM_TOKEN` to be provided by the caller. Permissions are minimized to `contents` and `pull-requests`.
- Do not log sensitive information. Using stable tags helps reduce supply chain risks; `pinact` enforcement ensures commits stay pinned.
