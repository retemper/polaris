# Polaris

> Strategy-as-code for Claude Code. AI agents should know *why* — not just what and how.

**v0.1.0 — Mission + Goals + Specs, delivered as a Claude Code plugin.**

## What it does

Polaris makes a repository's strategy (mission, anti-strategy, phase, current outcomes) into versioned files that Claude Code reads before proposing any code change.

- `polaris/mission.md` — what the codebase exists to do, what it won't do, current phase, riskiest strategic assumption
- `polaris/goals/active/` — outcomes the repo is currently converging on (2–3 recommended, up to ~5 before focus dilutes)
- `polaris/specs/` — PR-sized units of work, each linked to a parent Goal, created via Clarity Gate interrogation

A `CLAUDE.md` rule (auto-injected on first use) ensures Claude Code consults all three layers before any non-trivial change and requires a Spec-under-a-Goal before implementation.

## Install (dev / local)

For now, install via `--plugin-dir` (session-scoped):

```bash
claude --plugin-dir /path/to/polaris
```

Or clone and enable persistently through your settings. See [Claude Code plugin docs](https://code.claude.com/docs/en/plugins-reference) for marketplace installation.

## Use

In any repository, in Claude Code:

```
/init   # first time only — sets up mission + initial goals
/goal   # add a new goal later
/spec   # create a spec under an active goal
```

(Namespaced as `/polaris:init`, `/polaris:goal`, `/polaris:spec` if another plugin defines the same names.)

### First-time flow

1. `/init` runs a 20–40 minute interview in three parts:
   - **Context** (owner, codebase, users, phase, constraints) — open questions, no scoring
   - **Mission** (statement, anti-strategy, riskiest assumption) — scored PASS / WEAK / FAIL against rubrics
   - **Initial Goals** — 2 to 3 Goals, each interrogated across G1–G4 (target outcome, mission link, not-this, riskiest bet)
2. On completion: writes `polaris/mission.md`, `polaris/goals/active/*.md`, state directories, injects the Polaris section into `CLAUDE.md`, makes the first commit.

### Creating a Spec

1. `/spec` lists your active Goals and asks which one this Spec advances. If none fit, it runs `/goal` inline first.
2. Clarity Gate interrogation: S1–S6 (what changes, done criteria, out of scope, why now, user, riskiest assumption) with PASS/WEAK/FAIL scoring.
3. Goal–Spec alignment check: verifies the Spec converges on the Goal's G1 and doesn't fall inside its G3 exclusion.
4. Anti-strategy check against the Mission.
5. On pass: writes `polaris/specs/planned/{unix-timestamp}-{slug}.md` with a `goal:` frontmatter link, checks out `feat/{unix-timestamp}`. Move the file to `in-progress/`, `done/`, or `canceled/` as state changes — the directory IS the status.

### Life cycle

- **Goals:** `active/` → `achieved/` (target outcome observed, fill `Outcome notes`) or `abandoned/` (direction no longer right).
- **Specs:** `planned/` → `in-progress/` → `done/` (fill `Implementation notes`) or `canceled/`.

All transitions are `git mv` — there is no `status:` field to update. The filesystem is the source of truth.

## Layout (this repo)

```
polaris/
├── .claude-plugin/
│   ├── plugin.json               plugin manifest
│   └── marketplace.json          self-hosted marketplace catalog
├── commands/
│   ├── init.md                   /init — setup interview
│   ├── goal.md                   /goal — add a Goal
│   └── spec.md                   /spec — create a Spec under a Goal
├── templates/
│   ├── mission.md                mission template
│   ├── goal.md                   goal template
│   └── spec.md                   spec template
├── CLAUDE.md.snippet             injected into target repo's CLAUDE.md
├── CLAUDE.md                     guidance for Claude Code working on this plugin
├── LICENSE
└── README.md
```

## What's NOT in v0.1.0

- Initiative layer (deferred — if Goals prove to need a middle aggregator, we'll add it)
- `/retro` — done-Spec lessons-learned ritual that feeds back into mission.md (planned next)
- `/archaeology` — search historical Specs/Goals (deferred)
- Programmatic anti-strategy enforcement (Claude Code is *instructed* to halt via `CLAUDE.md`; no hook-based guard yet)
- Installer script

These return in later versions once Goal-Spec usage is validated.

## License

MIT
