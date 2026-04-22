---
description: Show current Polaris state (phase, active Goals, in-progress Specs, open Issues) and suggest 1-3 concrete next moves. Use when disoriented — this is the compass.
---

# /polaris

You are executing the Polaris `/polaris` slash command. This is a **compass** — you read the filesystem state and orient the user toward concrete next moves. The output is the product. `/polaris` is read-only — do not create or modify files.

## Step 1 — Precondition

Check whether `polaris/mission.md` exists in the current working directory.

- If it does not exist, output:
  > No Polaris setup detected in this repository. Run `/init` to set up mission, philosophy, and initial Goals.
  Halt. Do not proceed to later steps.
- If it exists, proceed.

## Step 2 — Capture user context (optional)

If the user passed extra text after `/polaris` (e.g. `/polaris 테스트 실패 중`, `/polaris 어디까지 했지`), keep that raw text as `context` — you will use it in Step 6.

If there is no extra text, `context` is empty (passive mode).

## Step 3 — Read state (read-only)

Read the following. Exclude `.gitkeep` entries from all listings.

- `polaris/mission.md` — extract:
  - `phase:` from frontmatter
  - Mission statement (first paragraph after `# Mission`)
  - Riskiest strategic assumption (paragraph after `# Riskiest strategic assumption`)
- `polaris/philosophy.md` — if the file exists, count principles (number of `## ` H2 sections after the intro).
- `polaris/goals/active/` — list files, read each file's H1 title and the first non-comment line of its G1 section.
- `polaris/specs/in-progress/` — list files, read each file's H1 title and `goal:` frontmatter.
- `polaris/specs/planned/` — list files, read each file's H1 title and `goal:` frontmatter.
- `polaris/issues/open/` — list files, read each file's H1 title.

If any directory is missing, treat as empty (count 0). Do not create directories.

## Step 4 — Passive output (state summary)

Print a compact summary in this shape. Keep it scannable — one screen if possible. Truncate long lines to ~100 chars with `…`.

```
Polaris — phase: {phase}
Mission: {one-line mission statement, truncated}
Riskiest assumption: {one-line, truncated}

Active Goals ({N}):
  - {slug}: {G1 first line, truncated}
  - ...

In-progress Specs ({N}):
  - {slug} (goal: {goal-slug})
  - ...

Planned Specs ({N}):
  - {slug} (goal: {goal-slug})
  - ...

Open Issues ({N}):
  - {slug}
  - ...

Philosophy: {N principles} (or "not defined" if polaris/philosophy.md absent)
```

For any category with 0 items, print `- (none)` instead of bullets.

## Step 5 — Next-move suggestions

Derive 1–3 concrete next moves based on current state. Each suggestion **must cite an exact command** (`/spec`, `/goal`, `/issue`, `/philosophy`) **or exact shell action** (`ls polaris/specs/in-progress/`, `git mv polaris/specs/planned/X.md polaris/specs/in-progress/`). Do not suggest abstract next steps.

Apply these rules in order, stopping once you have 3 suggestions:

1. If no active Goals → "Create your first Goal for this phase: `/goal`."
2. If active Goals exist but no in-progress Specs AND no planned Specs → "Pick a Goal and start a Spec: `/spec`."
3. If in-progress Specs exist → "Continue active work. Read the Spec for scope: `ls polaris/specs/in-progress/`, then open the file."
4. If planned Specs exist and no in-progress Spec → "Start a planned Spec: `git mv polaris/specs/planned/{slug}.md polaris/specs/in-progress/`."
5. If open Issues exist → "Review open Issues: `ls polaris/issues/open/`. Address one by creating a Spec via `/spec` (link it in `related_issues:`)."
6. If `polaris/philosophy.md` does not exist → "No Philosophy yet. When an invariant commitment crystallizes, run `/philosophy`."

Print as:

```
Suggested next moves:
- {suggestion 1}
- {suggestion 2}
- ...
```

## Step 6 — Interactive extension (conditional)

If Step 2 captured non-empty `context`, add an **Interactive** section after Step 5's suggestions:

```
Interactive (context: "{context}"):
{1–3 sentences of specific advice grounded in compass state + user context. Cite specific Spec/Goal/Issue slugs when relevant.}
```

Grounding rules:
- Only reference Specs/Goals/Issues you read in Step 3. Do not invent slugs.
- If the context sounds like an operational observation (bug, unexpected behavior, degradation) and no Spec covers it, suggest `/issue`.
- If the context sounds scoped to an in-progress Spec, name the Spec and point at its S1 (what changes) and S3 (out of scope) for scope check.
- If the context asks "what should I prioritize" and multiple directions exist, state the tradeoff plainly — do not fabricate a priority.

If Step 2's `context` is empty, skip this step entirely.

## Step 7 — Halt

Output is complete. Do not auto-execute any suggestion. Do not modify files. The user decides what to do next.
