# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Polaris is a Claude Code plugin, not an application. The deliverable is the set of markdown, JSON, and template files that get distributed to other repositories as strategy-as-code scaffolding. There is no build step and no runtime — only plugin validation.

## Validation

```bash
claude plugin validate .
```

Run this after any change to `.claude-plugin/*.json`, command frontmatter, or file paths referenced by commands. This is the only automated check in the repo.

## Key file roles (easy to confuse)

- `.claude-plugin/plugin.json` — plugin manifest; version lives here.
- `.claude-plugin/marketplace.json` — makes this repo installable as a self-hosted marketplace (`/plugin marketplace add retemper/polaris`). The `source: "./"` points the plugin at the repo root.
- `commands/*.md` — slash-command definitions (frontmatter + procedure).
- `templates/*.md` — files written into **target repos** when commands execute (e.g. `templates/mission.md` → `polaris/mission.md` in the consumer repo).
- `CLAUDE.md.snippet` — the rule block injected into the **target repo's** CLAUDE.md. This is not the CLAUDE.md you're reading now. Edits to this file change AI behavior in every repository that installs Polaris.

## Path conventions inside commands

Command files reference the plugin root via `${CLAUDE_PLUGIN_ROOT}`:

- `${CLAUDE_PLUGIN_ROOT}/templates/spec.md`
- `${CLAUDE_PLUGIN_ROOT}/templates/mission.md`
- `${CLAUDE_PLUGIN_ROOT}/templates/philosophy.md`
- `${CLAUDE_PLUGIN_ROOT}/templates/goal.md`
- `${CLAUDE_PLUGIN_ROOT}/templates/issue.md`
- `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md.snippet`

Never hardcode absolute paths. Never assume the plugin is cloned to a specific directory — it could be installed anywhere by the marketplace mechanism.

## Load-bearing design principles

These are not style preferences; they are the mental model the plugin teaches. Violating them silently breaks how Polaris is used in consumer repos:

- **Directory-as-status.** A Spec's lifecycle state is the directory it lives in (`planned/`, `in-progress/`, `done/`, `canceled/`). Do not introduce a `status:` frontmatter field. Do not add a JSON index. `ls` is the query interface.
- **Filesystem over metadata.** When the plugin tells AI "scan in-progress Specs", it should mean an `ls` or glob, never parsing a registry file. Registries drift from reality; directories cannot.
- **Trigger-action rule structure in `CLAUDE.md.snippet`.** Rules use "Before / When / If / Never" headings. AI agents follow trigger-action better than descriptive prose. Keep new rules in the same form.
- **PASS / WEAK / FAIL rubrics in Clarity Gate.** Each S1–S6 question has a rubric. The `weak_dimensions` frontmatter field records which scored WEAK/FAIL so retros can later find systemically weak Specs. Do not remove rubrics or soften scoring language without understanding this feedback loop.
- **v0.1.0 intentional minimality.** Deferred features (Initiative layer, `/retro`, `/archaeology`, programmatic anti-strategy hooks, installer script) are listed in the README under "What's NOT in v0.1.0". If a requested change expands beyond the current scope, surface that to the user before implementing.
- **Five commands, one bootstrap.** `/init` is the only command that creates `polaris/`. `/philosophy`, `/goal`, `/spec`, and `/issue` all require `polaris/mission.md` to exist and halt with instructions to run `/init` otherwise. Keep this invariant — it is what makes "is this repo set up?" an unambiguous question.
- **Every Spec has a parent Goal.** The `goal:` frontmatter field on each Spec is required, not optional. `/spec` enforces this by running `/goal` inline when no active Goals exist. Do not loosen this constraint without understanding that it is the mechanism by which Specs stay convergent on Mission-level outcomes.
- **Philosophy is invariant by design.** `polaris/philosophy.md` has no state directories (no `active/` / `archived/`) and no lifecycle. Principles are identity-level commitments that constrain every Goal and Spec. If a principle needs to change, that is a strategic amendment, not a routine update. Do not add lifecycle mechanics to Philosophy — the invariance IS the point.
- **Philosophy is optional at the consumer layer.** `polaris/philosophy.md` may not exist in a user's repo (e.g., if they ran `/init` before v0.1.0 and haven't run `/philosophy` yet, or if they skipped Part C). `/goal` and `/spec` Philosophy-alignment checks must handle the absent-file case by skipping, never by halting.
- **Issues intentionally break the Clarity-Gate-everywhere pattern.** Mission, Philosophy, Goals, and Specs are strategic commitments, so each runs through a PASS/WEAK/FAIL rubric (B-, P-, G-, S-). Issues are *operational reports* — observations of reality, not commitments about the future. They collect What / When / Reproduction / Impact with no scoring; the template structure is the quality gate. Do not add a scoring rubric to `/issue` or `templates/issue.md` without understanding that the no-scoring design is the line between "strategic artifact" and "operational log".
- **Spec → Issue is one-way canonical.** The Spec's `related_issues:` frontmatter is where Spec↔Issue links live. Issues do not carry a `related_specs:` field. This avoids bi-directional drift — the Spec edits itself when linking, nothing has to edit the Issue. Resist any proposal to add `related_specs:` to Issues "for symmetry"; grep-from-Spec-side is the query.

## When editing commands

A command defines a procedure that Claude Code will execute in a user's repo. Two recurring pitfalls:

- **Filesystem reality vs. what the user says.** The command should always prefer verifying state from disk (e.g. "does `polaris/mission.md` exist?") over trusting prior assertions. This mirrors the directory-as-status principle at runtime.
- **Init side-effects stay in init.** Repo bootstrapping (creating `polaris/`, writing `mission.md`, injecting `CLAUDE.md`) lives in the init subroutine of the command that triggered it. Do not sprinkle bootstrap logic into other commands — that makes "is this repo set up?" ambiguous.

## Common edit flow

1. Edit the command, template, or snippet.
2. Run `claude plugin validate .`.
3. If command logic changed, dry-run it in a scratch target repo to confirm the procedure still produces the expected files and Git state.
4. Commit on a feature branch; PR to `main`.

<!-- POLARIS-START (do not edit between markers; managed by Polaris) -->
## Polaris

This repository uses [Polaris](https://github.com/retemper/polaris) — strategy-as-code for AI agents. The rules below govern your behavior in this repository.

**Before proposing any non-trivial code change:**
- Read `polaris/mission.md`. Internalize the anti-strategy items, the current phase, and the riskiest strategic assumption.
- Read `polaris/philosophy.md` if present. These principles are invariant across Goal changes — every proposal must respect them.
- List `polaris/goals/active/` and read the frontmatter of each active Goal. These are the outcomes the repo is currently converging on.
- Scan `polaris/specs/in-progress/` and `polaris/specs/planned/` for a Spec that covers the proposed work.

**When no Spec covers the proposed work:**
- Run `/spec` to create one. Do not write implementation code until the Spec exists on disk.
- One-line fixes and obvious chores can bypass Spec creation, but you must say so explicitly in chat before acting.

**When the user reports a bug, incident, or observation that isn't itself a Spec-scoped change:**
- Run `/issue` to file it under `polaris/issues/open/`. Issues are lightweight operational reports, not strategic layers — no Clarity Gate scoring applies.
- When a later Spec addresses the Issue, `/spec` records the link on the Spec side via `related_issues:`. The Issue file does not store its Spec.

**When a Spec exists for this work:**
- Stay within the scope declared in S1 (what changes) and S3 (out of scope).
- The Spec's `goal:` frontmatter field names its parent Goal in `polaris/goals/active/`. Verify the work still advances that Goal's G1 target outcome — if it drifts, raise it.
- If the Spec's `related_issues:` lists any Issues, they live in `polaris/issues/open/`. Resolving the Spec closes those Issues (`git mv` to `polaris/issues/closed/` and fill `Resolution notes`).
- If the work requires expanding scope, stop and ask the user to amend the Spec.

**If the proposed change conflicts with an anti-strategy item in `polaris/mission.md` or a principle in `polaris/philosophy.md`:**
- Halt. Name the specific item or principle being violated and raise the conflict with the user.
- Do not silently proceed. The user must either cancel the change or explicitly amend the conflicting file.

**Never:**
- Trust metadata over filesystem reality. The directory a Spec, Goal, or Issue lives in IS its status — `planned/` `in-progress/` `done/` `canceled/` for Specs, `active/` `achieved/` `abandoned/` for Goals, `open/` `closed/` for Issues. No `status:` field overrides this.
- Move Spec, Goal, or Issue files across status directories, or rewrite `polaris/mission.md` or `polaris/philosophy.md`, without the user's explicit request.
- Create a Spec without a parent Goal. Every Spec must link to an active Goal via its `goal:` frontmatter field.
- Treat Philosophy principles as aspirational. If a principle exists in `polaris/philosophy.md`, violating it is a strategic decision that requires explicit user amendment, not silent deviation.
- Store Spec references on Issues. The Spec's `related_issues:` frontmatter is the canonical direction for the Spec↔Issue link. Issues do not carry a `related_specs:` field.

**Directory layout (strategic context):**
- `polaris/mission.md` — mission, anti-strategy, current phase, riskiest strategic assumption
- `polaris/philosophy.md` — principles invariant across Goal changes (identity-level commitments); may not exist if the user hasn't defined any
- `polaris/goals/active/` — outcomes currently being pursued
- `polaris/goals/achieved/` — outcomes that have been observed (historical reference; `Outcome notes` section filled)
- `polaris/goals/abandoned/` — outcomes no longer being pursued, with reason recorded
- `polaris/specs/planned/` — Specs not yet started
- `polaris/specs/in-progress/` — active work
- `polaris/specs/done/` — completed (historical reference)
- `polaris/specs/canceled/` — canceled with reason recorded in the Spec
- `polaris/issues/open/` — bug / incident / observation reports not yet resolved
- `polaris/issues/closed/` — resolved, wontfix, duplicate, or obsolete Issues with `Resolution notes` filled

**Slash commands (from the Polaris plugin):**
- `/init` — one-time setup: interview for mission, anti-strategy, phase, philosophy, and 2-3 initial Goals
- `/philosophy` — add a new Philosophy principle (P1–P3 interrogation)
- `/goal` — add a new Goal (G1–G4 interrogation)
- `/spec` — create a new Spec under a parent Goal (Clarity Gate S1–S6)
- `/issue` — file a bug / incident / observation report (structural, no scoring)
<!-- POLARIS-END -->
