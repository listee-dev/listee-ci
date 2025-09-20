# listee-ci

[![actionlint](https://github.com/listee-dev/listee-ci/actions/workflows/actionlint.yml/badge.svg?branch=main)](https://github.com/listee-dev/listee-ci/actions/workflows/actionlint.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Reusable GitHub Actions workflows for the listee-dev organization — Bun, Biome, Vitest, and Changesets out of the box.

## Workflows
- `lint.yml`: Run Biome (`bun x biome ci .`).
- `test.yml`: Run Vitest with coverage.
- `typecheck.yml`: Run TypeScript project references (fallback to `--noEmit`).
- `release.yml`: Changesets release (opens PR or publishes to npm).
- `pinact.yml`: Validate that reusable workflows reference full-length commit SHAs.

## Usage (in a consumer repository)
```yaml
name: ci
on: [push, pull_request]
jobs:
  lint:      { uses: listee-dev/listee-ci/.github/workflows/lint.yml@v1 }
  test:      { uses: listee-dev/listee-ci/.github/workflows/test.yml@v1 }
  typecheck: { uses: listee-dev/listee-ci/.github/workflows/typecheck.yml@v1 }
  # Release via Changesets (requires npm token in caller repo)
  release:
    uses: listee-dev/listee-ci/.github/workflows/release.yml@v1
    with:
      environment: production
```

Notes
- Runners: `ubuntu-latest` recommended. External actions are pinned to full-length commit SHAs via `pinact` to mitigate tag rewrite attacks.
- npm releases use Trusted Publishing (OIDC); ensure you whitelist the workflow + environment in npm and no `NPM_TOKEN` secret is required.
- The internal Bun setup is packaged as a composite action and referenced relatively for portability.

## Local Development
- Validate workflows with `act` (Apple Silicon often needs amd64):
  - `act -j lint -W .github/workflows/lint.yml --container-architecture linux/amd64`
  - `act -j test -W .github/workflows/test.yml --container-architecture linux/amd64`
  - `act -j typecheck -W .github/workflows/typecheck.yml --container-architecture linux/amd64`
  - `act -j pinact -W .github/workflows/pinact.yml --container-architecture linux/amd64`
- Optional: copy `actrc.example` to your system config to avoid repeating flags.

## Changesets Release Flow
- In the consumer repo, add a changeset per meaningful change: `bunx changeset` (select bump type and packages).
- Commit the generated file under `.changeset/` and open a PR.
- After merge to the default branch, the `release.yml` job creates a “Version Packages” PR.
- Merge that PR to publish to npm. Configure npm Trusted Publishing for the repository/environment instead of providing `NPM_TOKEN`.
- Local preview: `bunx changeset status`. Manual flows: `bunx changeset version` then `bunx changeset publish`.

## Contributing
- Conventional Commits are encouraged (e.g., `feat(lint): strengthen Biome CI`).
- PRs should include purpose, key changes, impact, and local verification steps.
- Static checks run on PRs via `actionlint`.

## License
MIT License — see `LICENSE`.
