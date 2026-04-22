---
description: File a Polaris Issue — a lightweight bug / incident report with What / When / Reproduction / Impact, optionally linked to an active Goal. Writes to polaris/issues/open/.
---

# /issue

You are executing the Polaris `/issue` slash command. Issues are operational reports — observations of bugs, incidents, or user-reported problems. They are **not** strategic layers. There is no PASS/WEAK/FAIL interrogation; the template structure is the quality gate. Follow the procedure below exactly.

## Step 1 — Preconditions

Check that `polaris/mission.md` exists in the current working directory.

- If it does not exist, halt. Tell the user: "This repository isn't set up for Polaris yet. Run `/init` first."
- If it exists, proceed.

Also ensure `polaris/issues/open/` exists. If it does not (e.g., the repo was initialized before Issues were added), create the directory with a `.gitkeep` file and also create `polaris/issues/closed/.gitkeep`.

## Step 2 — Collect the four sections

Ask the user each of the following, one at a time. Do not score — just collect. Accept "N/A" for Reproduction if the Issue is informational or unreproducible.

### I1 — What
"What was observed or reported? Describe the concrete symptom or user-facing behavior."

### I2 — When
"When did this happen or become visible? An approximate answer is fine — 'since last deploy', 'around 2026-04-20', 'once today'."

### I3 — Reproduction
"Steps to reproduce? If not reproducible or not applicable (e.g., one-time incident, environmental), just say 'N/A' or describe the condition under which it appeared."

### I4 — Impact
"Who is affected, and what is blocked or degraded? Plain description — no severity tags or labels."

## Step 3 — Reporter

Ask: "Who reported this? (your name if you observed it yourself — I'll use 'self'; otherwise the name of the user or source)"

Default to `self` if the user does not answer.

## Step 4 — Optional Goal linkage

List files in `polaris/goals/active/` (excluding `.gitkeep`).

- If the list is empty, skip this step (no `related_goal` value).
- If active Goals exist, present them as a numbered list:

  > Does this Issue relate to a specific active Goal? (optional)
  > 1. {slug-1} — {one-line summary from G1}
  > 2. {slug-2} — {one-line summary from G1}
  > ...
  > Or: "none" to skip.

- If the user picks a number, record the corresponding `{goal-timestamp}-{goal-slug}` as `related_goal`.
- If the user says "none" or similar, leave `related_goal` blank in frontmatter.

Do not ask about related Specs at Issue-creation time. Spec-side is the canonical direction for the Spec↔Issue link — when a Spec is later created that addresses this Issue, `/spec` will record the link in the Spec's frontmatter.

## Step 5 — Write the Issue

Generate:
- `timestamp` — Unix epoch seconds, from `date +%s`.
- `slug` — kebab-case, 2–5 words capturing the observation, derived from I1.

File path: `polaris/issues/open/{timestamp}-{slug}.md`

(New Issues always land in `open/`. When closed — whether resolved by a Spec, declared wontfix, duplicate, or obsolete — `git mv` the file to `polaris/issues/closed/` and fill the `Resolution notes` section. The directory IS the status.)

Read the Issue template at `${CLAUDE_PLUGIN_ROOT}/templates/issue.md`. Fill it:
- Frontmatter:
  - `id: {timestamp}`
  - `reporter: {reporter or 'self'}`
  - `related_goal: {goal-id-slug or blank}`
  - `created: {YYYY-MM-DD today}`
- Prose sections I1–I4 — use the user's final answers.
- Leave `Resolution notes` empty — it is filled only when the Issue moves to `closed/`.

Write the file.

## Step 6 — Commit

Stage and commit only the new Issue file on the current branch:

```bash
git add polaris/issues/open/{timestamp}-{slug}.md
git commit -m "issue: {slug}"
```

Do not create a branch. Issues are not PR-sized units; they accumulate on whatever branch the user is on. If the user is in the middle of Spec work on a feature branch, surface this — the Issue will travel with the branch.

## Step 7 — Confirm

Tell the user:

> Issue filed: `polaris/issues/open/{timestamp}-{slug}.md`
> Reporter: `{reporter}`
> Related Goal: `{goal-slug or "none"}`
>
> When a Spec addresses this Issue, `/spec` will let you link it — the Spec's `related_issues:` frontmatter is the canonical source. When the Issue is resolved (Spec merged), declared wontfix, duplicate, or obsolete, move the file to `polaris/issues/closed/` (`git mv`) and fill `Resolution notes`.
