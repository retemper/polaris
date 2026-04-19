# Polaris

> Strategy-as-code for Claude Code. AI agents should know *why* — not just what and how.

**v0.0.1 — minimum viable core, delivered as a Claude Code plugin.**

## What it does

Polaris makes a repository's strategy (mission, anti-strategy, current phase) into versioned files that Claude Code reads before proposing any code change.

- `polaris/mission.md` — what the codebase exists to do, what it won't do, what phase it's in
- `polaris/specs/` — strategic work units, created via the `/spec` command with Clarity Gate interrogation

A `CLAUDE.md` rule (auto-injected on first use) ensures Claude Code consults `polaris/mission.md` before any change and requires a Spec before non-trivial work.

## Install (dev / local)

For now, install via `--plugin-dir` (session-scoped):

```bash
claude --plugin-dir /path/to/polaris
```

Or clone and enable persistently through your settings (see [Claude Code plugin docs](https://code.claude.com/docs/en/plugins-reference) for marketplace installation once Polaris is published).

## Use

In any repository, in Claude Code:

```
/spec
```

(Namespaced as `/polaris:spec` if another plugin defines `/spec`.)

Flow:
1. If `polaris/mission.md` doesn't exist in the current repo, four setup questions create it (mission, anti-strategy items, current phase, owner) and the Polaris section is added to `CLAUDE.md`.
2. Six Clarity Gate questions (S1–S6) interrogate the proposed work. Each answer is scored PASS / WEAK / FAIL against a rubric.
3. Anti-strategy check runs — halts if the Spec conflicts with any anti-strategy item.
4. On pass: writes `polaris/specs/planned/{unix-timestamp}-{slug}.md` and checks out branch `feat/{unix-timestamp}`. Move the file to `in-progress/`, `done/`, or `canceled/` as state changes — the directory IS the status.

## Layout

```
polaris/
├── .claude-plugin/
│   └── plugin.json               plugin manifest
├── commands/
│   └── spec.md                   /spec command definition
├── templates/
│   ├── mission.md                mission.md template
│   └── spec.md                   spec file template
├── CLAUDE.md.snippet             injected into target repo's CLAUDE.md
├── LICENSE
└── README.md
```

## What's NOT in v0.0.1

- Goal and Initiative layers (deferred — Spec is the only execution unit for now)
- Other slash commands (`/brief`, `/move`, `/validate`, `/retro`, `/archaeology`) — all deferred
- Programmatic anti-strategy enforcement (Claude Code is *instructed* to check via `CLAUDE.md`; no hook-based guard yet)
- Marketplace publishing
- Installer script

These return in later versions once Spec-level usage is validated.

## License

MIT
