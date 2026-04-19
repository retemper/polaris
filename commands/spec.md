---
description: Create a Polaris Spec — initializes polaris/ directory on first use, then runs the Clarity Gate interrogation (S1–S6) and writes a Spec file on a new feature branch.
---

# /spec

You are executing the Polaris `/spec` slash command. Follow the procedure below exactly.

## Step 1 — Check initialization

Check whether `polaris/mission.md` exists in the current working directory.

- If it exists, skip to Step 3.
- If it does not exist, proceed to Step 2.

## Step 2 — Initialize the repository (first-time setup)

Tell the user: "This repository doesn't have Polaris set up yet. I'll ask four questions to create `polaris/mission.md`, then we'll proceed with the Spec."

Ask these four questions, one at a time, waiting for each answer:

1. **Mission** — "In one sentence: what does this codebase exist to do?"
2. **Anti-strategy** — "Name 2–4 things this codebase will explicitly never do, even when tempting. Be concrete (e.g., 'never add a billing module', not 'never compromise quality')."
3. **Current phase** — "What phase is this repo in right now: Discovery, Build, Scale, or Sunset?"
4. **Strategic owner** — "Who is responsible for strategic decisions here? (one human name)"

Read the mission template at `${CLAUDE_PLUGIN_ROOT}/templates/mission.md` and fill it with the user's answers. Write the result to `polaris/mission.md` in the current repo.

Create the spec state directories — each an empty `.gitkeep` to hold the directory in git:

- `polaris/specs/planned/.gitkeep`
- `polaris/specs/in-progress/.gitkeep`
- `polaris/specs/done/.gitkeep`
- `polaris/specs/canceled/.gitkeep`

The directory a Spec lives in *is* its status. There is no `status:` frontmatter field.

Inject the Polaris CLAUDE.md snippet:

- If `CLAUDE.md` does not exist at repo root, create it and write the contents of `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md.snippet`.
- If `CLAUDE.md` exists, check whether it contains the line `<!-- POLARIS-START`. If not, append the contents of `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md.snippet` to the end of the file (with a blank line separator). If it does contain the marker, leave `CLAUDE.md` unchanged.

Confirm to the user: "Polaris initialized. Continuing with Spec creation."

## Step 3 — Read the mission

Read `polaris/mission.md`. You will reference the anti-strategy section during Step 5.

## Step 4 — Clarity Gate (S1–S6)

Ask the user each of the following questions, one at a time. After each answer, score it using the rubric and reflect your scoring back to the user. If the score is WEAK or FAIL, explain why and offer the user a chance to refine their answer. Accept whatever they land on (refinement is optional).

Track which dimensions end up as WEAK or FAIL — you'll record these in the Spec's frontmatter.

### S1 — What changes
Ask: "When this PR merges, what specific file or observable behavior changes?"

Rubric:
- **PASS**: names concrete file paths, functions, endpoints, or user-visible behaviors.
- **WEAK**: names a module or area but no specific artifacts.
- **FAIL**: abstractions like "improve X" or "refactor Y" with no concrete artifact.

### S2 — Done criteria
Ask: "What testable acceptance criteria tell you this is done? Translate any abstract outcome into a concrete, independently-verifiable check — e.g., 'revenue improves' becomes 'Free → Pro upgrade flow passes E2E test and Stripe dashboard shows a test charge'."

Rubric:
- **PASS**: a check someone else could independently run and verify (a passing test, an endpoint returning a specific value, a CI step green, a user flow completed end-to-end, a metric crossing a named threshold).
- **WEAK**: self-reported or subjective ("I'm happy with it", "code looks clean", "feels better").
- **FAIL**: cannot name any observable signal, or the signal is not verifiable by anyone other than the author.

### S3 — Out of scope
Ask: "Name one thing related to this work that this PR will explicitly NOT do."

Rubric:
- **PASS**: a concrete boundary that could plausibly have been in scope but is cut.
- **WEAK**: tautological ("won't change unrelated code").
- **FAIL**: "nothing else is excluded" / refuses to draw a boundary.

### S4 — Why now
Ask: "Why does this happen now rather than next week or next month?"

Rubric:
- **PASS**: unblocks something, deadline, degrading condition, or directly advances the repo's current phase per `polaris/mission.md`.
- **WEAK**: "it's convenient."
- **FAIL**: no reason beyond preference.

### S5 — User / consumer
Ask: "Who benefits when this merges? (Can be 'the codebase itself' for infra work — but say so.)"

Rubric:
- **PASS**: names a persona, user segment, or specific downstream consumer.
- **WEAK**: generic ("users").
- **FAIL**: cannot identify any beneficiary.

### S6 — Riskiest assumption
Ask: "What single assumption, if wrong, would make this PR pointless?"

Rubric:
- **PASS**: names a falsifiable assumption with real stakes.
- **WEAK**: names a risk but not a falsifiable assumption.
- **FAIL**: "nothing could go wrong."

## Step 5 — Anti-strategy check

For each item in the anti-strategy section of `polaris/mission.md`, ask yourself whether this Spec violates it. If you find a violation:

- Halt. Tell the user exactly which anti-strategy item conflicts with this Spec and how.
- The user must either (a) cancel the Spec, or (b) explicitly amend `polaris/mission.md` to remove/modify the anti-strategy item. Amending is a strategic decision — do not do it silently.

If no violation, proceed.

## Step 6 — Write the Spec

Generate:
- `timestamp` — Unix epoch seconds, from `date +%s`.
- `slug` — kebab-case, 2–5 words capturing the Spec's subject, derived from S1.

File path: `polaris/specs/planned/{timestamp}-{slug}.md`

(New Specs always land in `planned/`. When the user starts implementation they move the file to `in-progress/`, and to `done/` or `canceled/` when finished. The directory IS the status.)

Read the Spec template at `${CLAUDE_PLUGIN_ROOT}/templates/spec.md`. Fill it:
- Frontmatter:
  - `id: {timestamp}`
  - `branch: feat/{timestamp}`
  - `created: {YYYY-MM-DD today}`
  - `weak_dimensions: [...]` — list the S-dimensions that scored WEAK or FAIL (e.g., `[S3, S5]`). Empty list if all PASS.
- Prose sections S1–S6 — use the user's final answers.

Write the file.

## Step 7 — Create the branch

Run:

```bash
git checkout -b feat/{timestamp}
git add polaris/specs/planned/{timestamp}-{slug}.md
git commit -m "spec: {slug}"
```

(If Step 2 ran, also stage `polaris/mission.md`, the four `polaris/specs/{planned,in-progress,done,canceled}/.gitkeep` files, and `CLAUDE.md` changes, and include them in the first commit on the new branch.)

## Step 8 — Confirm

Tell the user:

> Spec created: `polaris/specs/planned/{timestamp}-{slug}.md`
> Branch: `feat/{timestamp}` (checked out)
> Weak dimensions: `{list or "none"}`
>
> You can start implementing now. Claude Code will see the Spec via `CLAUDE.md` and stay scoped to it. When you begin implementation, move the file to `polaris/specs/in-progress/` (`git mv`). When merged or canceled, move to `done/` or `canceled/`.
