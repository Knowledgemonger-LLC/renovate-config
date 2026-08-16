# renovate-config

Org-wide **base [Renovate](https://docs.renovatebot.com/) preset** for
**Knowledgemonger-LLC**. The single canonical source for how dependency updates behave across
every repo, so update policy is defined once and inherited everywhere.

## What it is

[`default.json`](default.json) is the shared base config. It:

- extends `config:recommended` (Renovate's sane defaults),
- enables the **dependency dashboard** (`:dependencyDashboard`) and **semantic commits**
  (`:semanticCommits`),
- runs on a **weekly** schedule (`before 9am on monday`, `America/New_York`),
- labels update PRs `dependencies`,
- **automerges devDependencies** minor/patch updates, after a **7-day cooldown**.

## Why devDependencies automerge has a cooldown

Automerge means no human reads the change before it reaches a default branch. Renovate waits
on status checks, but only ones that **exist** — most repos in this org have no CI at all, so
on those there is nothing between a published release and `main`.

`minimumReleaseAge: "7 days"` is what stands in for that missing review. Package compromises
are typically caught and yanked within days of publication, so the delay means the incident is
public before the merge happens. The cost is that legitimate devDependency patches land a week
late, which for tooling is not a cost worth paying attention to.

Do not remove it without giving the repos that inherit it something else — a required status
check, or `automerge: false`.

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
