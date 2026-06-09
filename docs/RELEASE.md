# Release playbook — npm packages

This repo ships two public npm packages from `tools/`:

| Package | Directory | Release tag |
|---------|-----------|-------------|
| `@cobusgreyling/loop-audit` | `tools/loop-audit` | `loop-audit-v*` |
| `@cobusgreyling/loop-init` | `tools/loop-init` | `loop-init-v*` |

## One-time setup

1. Create an npm org/user scope `@cobusgreyling` on [npmjs.com](https://www.npmjs.com/).
2. Generate an npm **Automation** or **Publish** token.
3. Add it to the repo as **`NPM_TOKEN`** (Settings → Secrets → Actions).

## Version bump

Edit `version` in the package `package.json`, update that package's `CHANGELOG.md` if present, and commit to `main` via PR.

## Publish

Tag pushes trigger the release workflows:

```bash
# loop-audit (runs tests before publish)
git tag loop-audit-v1.3.0
git push origin loop-audit-v1.3.0

# loop-init (bundles starters/templates, runs smoke tests)
git tag loop-init-v1.1.0
git push origin loop-init-v1.1.0
```

Workflows: `.github/workflows/release-loop-audit.yml`, `.github/workflows/release-loop-init.yml`.

## Verify after publish

```bash
npx @cobusgreyling/loop-audit --help
npx @cobusgreyling/loop-init --help

mkdir /tmp/loop-init-test && cd /tmp/loop-init-test
npx @cobusgreyling/loop-init . --pattern daily-triage --tool grok --dry-run
```

## Before npm is live (local / monorepo)

```bash
cd tools/loop-audit && npm ci && npm test && node dist/cli.js ../.. --suggest
cd tools/loop-init && npm ci && npm test && node dist/cli.js /tmp/target --pattern daily-triage --dry-run
```