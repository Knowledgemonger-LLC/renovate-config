# renovate-config

Org-wide **base [Renovate](https://docs.renovatebot.com/) preset** for
**Knowledgemonger-LLC**. The single canonical source for how dependency updates behave across
every repo, so update policy is defined once and inherited everywhere.

## What it is

[`default.json`](default.json) is the shared base config. It:

- extends `config:recommended` (Renovate's sane defaults),
- enables the **dependency dashboard** (`:dependencyDashboard`) and **semantic commits**
  (`:semanticCommits`),
- runs on a **weekly** schedule (`before 9am on monday`),
- labels update PRs `dependencies`,
- **automerges devDependencies** minor/patch updates.

## How repos consume it

`template-base` scaffolds a thin `renovate.json` into every repo that extends this preset:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>Knowledgemonger-LLC/renovate-config"]
}
```

> **Why the file must be named `default.json` at the repo root:** a bare
> `github>Knowledgemonger-LLC/renovate-config` reference (no path after the repo name) resolves
> to `default.json` at the root. Renaming or moving it breaks every consumer.

Per-repo customization (extra `packageRules`, a different `schedule`, additional presets) goes
in each repo's **own** `renovate.json` alongside the `extends` — never here. `template-base`'s
drift check enforces only that the `extends` still contains this preset (managed-keys rule); the
rest is repo-owned.

## This repo must stay public

Renovate resolves `github>` presets over the public GitHub API. If this repo is private,
Renovate cannot read the preset and **errors in every consuming repo**. Keep it public.
