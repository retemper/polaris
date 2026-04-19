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
- `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md.snippet`

Never hardcode absolute paths. Never assume the plugin is cloned to a specific directory — it could be installed anywhere by the marketplace mechanism.

## Load-bearing design principles

These are not style preferences; they are the mental model the plugin teaches. Violating them silently breaks how Polaris is used in consumer repos:

- **Directory-as-status.** A Spec's lifecycle state is the directory it lives in (`planned/`, `in-progress/`, `done/`, `canceled/`). Do not introduce a `status:` frontmatter field. Do not add a JSON index. `ls` is the query interface.
- **Filesystem over metadata.** When the plugin tells AI "scan in-progress Specs", it should mean an `ls` or glob, never parsing a registry file. Registries drift from reality; directories cannot.
- **Trigger-action rule structure in `CLAUDE.md.snippet`.** Rules use "Before / When / If / Never" headings. AI agents follow trigger-action better than descriptive prose. Keep new rules in the same form.
- **PASS / WEAK / FAIL rubrics in Clarity Gate.** Each S1–S6 question has a rubric. The `weak_dimensions` frontmatter field records which scored WEAK/FAIL so retros can later find systemically weak Specs. Do not remove rubrics or soften scoring language without understanding this feedback loop.
- **v0.1.0 intentional minimality.** Deferred features (Initiative layer, `/retro`, `/archaeology`, programmatic anti-strategy hooks, installer script) are listed in the README under "What's NOT in v0.1.0". If a requested change expands beyond the current scope, surface that to the user before implementing.
- **Three commands, one bootstrap.** `/init` is the only command that creates `polaris/`. `/goal` and `/spec` both require `polaris/mission.md` to exist and halt with instructions to run `/init` otherwise. Keep this invariant — it is what makes "is this repo set up?" an unambiguous question.
- **Every Spec has a parent Goal.** The `goal:` frontmatter field on each Spec is required, not optional. `/spec` enforces this by running `/goal` inline when no active Goals exist. Do not loosen this constraint without understanding that it is the mechanism by which Specs stay convergent on Mission-level outcomes.

## When editing commands

A command defines a procedure that Claude Code will execute in a user's repo. Two recurring pitfalls:

- **Filesystem reality vs. what the user says.** The command should always prefer verifying state from disk (e.g. "does `polaris/mission.md` exist?") over trusting prior assertions. This mirrors the directory-as-status principle at runtime.
- **Init side-effects stay in init.** Repo bootstrapping (creating `polaris/`, writing `mission.md`, injecting `CLAUDE.md`) lives in the init subroutine of the command that triggered it. Do not sprinkle bootstrap logic into other commands — that makes "is this repo set up?" ambiguous.

## Common edit flow

1. Edit the command, template, or snippet.
2. Run `claude plugin validate .`.
3. If command logic changed, dry-run it in a scratch target repo to confirm the procedure still produces the expected files and Git state.
4. Commit on a feature branch; PR to `main`.
