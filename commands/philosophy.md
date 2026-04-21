---
description: Create or append a Polaris Philosophy principle — runs P1–P3 interrogation and appends to polaris/philosophy.md.
---

# /philosophy

You are executing the Polaris `/philosophy` slash command. Philosophy principles are invariant across Goal changes — they describe *how* the codebase operates and *what it believes*, not *what it's building* (Mission) or *what outcome is currently targeted* (Goal). Follow the procedure below exactly.

## Step 1 — Preconditions

Check that `polaris/mission.md` exists.

- If it does not exist, halt. Tell the user: "This repository isn't set up for Polaris yet. Run `/init` first."
- If it exists, proceed.

Read `polaris/mission.md`. You will reference its Mission statement and anti-strategy during Step 3's P3 check.

If `polaris/philosophy.md` already exists, read it and list existing principle slugs to the user so they don't propose a duplicate or near-duplicate.

## Step 2 — Principle count advisory (soft limit)

Count existing principles in `polaris/philosophy.md` (the number of `##` headings under `# Philosophy`). If the file does not yet exist, count is 0.

- If fewer than 5, proceed silently.
- If 5 or more, tell the user: "You already have {N} Philosophy principles. Beyond 5, individual principles become harder to hold in mind and tend to lose enforcement weight. Continue anyway, or cancel this command and reconsider whether this is really a variation of an existing principle?"

Advisory, not blocking.

## Step 3 — P1–P3 interrogation

Ask the user for a principle statement — a positive declaration of what this codebase commits to (not a negation; anti-strategy covers negations).

Then ask P1–P3 one at a time. After each answer, score it using the rubric and reflect your scoring back. If the score is WEAK or FAIL, explain why and offer the user a chance to refine. Accept whatever they land on.

Track which dimensions end up WEAK or FAIL — you will record these in the principle's HTML comment annotation.

### P1 — Does it actually change decisions?
Ask: "Name a specific decision this principle changes. If this principle were removed, what choice would flip?"

Rubric:
- **PASS**: names a concrete past or plausible near-future decision that this principle would change.
- **WEAK**: gestures at decisions ("helps us prioritize") without naming one concretely.
- **FAIL**: cannot name any decision it changes — it is a slogan.

### P2 — Does it have teeth under temptation?
Ask: "Describe the situation where violating this would be tempting. When that situation comes, what concretely tells you the principle has been violated?"

Rubric:
- **PASS**: a specific tempting scenario plus a concrete violation marker.
- **WEAK**: names a temptation but the violation marker would be arguable.
- **FAIL**: "no one would ever want to violate this" — probably too weak to be a principle, or not yet contested enough to matter.

### P3 — Does it align with Mission/Goals?
Ask: "Does this principle contradict the Mission statement, anti-strategy, or any currently-active Goal?"

You also verify independently:
- Read `polaris/mission.md` — does the principle conflict with the Mission statement or any anti-strategy item?
- List `polaris/goals/active/` — does any active Goal require violating this principle to achieve its G1 target?

Rubric:
- **PASS**: no contradiction surfaces.
- **WEAK**: partial tension with a Goal's strategy (can coexist but creates friction).
- **FAIL**: direct contradiction with Mission statement, anti-strategy item, or an active Goal's G1 target.

If P3 scores FAIL, halt. Tell the user which layer conflicts and how. The user must (a) drop the principle, (b) revise it, or (c) revise the conflicting Mission/Goal element before re-running `/philosophy`.

## Step 4 — Write or append

Generate:
- `slug` — kebab-case, 2–4 words capturing the principle, derived from the Statement.

### 4a. If `polaris/philosophy.md` does not exist

- Read `${CLAUDE_PLUGIN_ROOT}/templates/philosophy.md`.
- Fill frontmatter: `owner` (from `polaris/mission.md` frontmatter), `created` (today's date, YYYY-MM-DD).
- Replace the `## <principle-slug>` example section with the new principle (slug as heading, filled Statement / Why it matters / Violation trigger).
- Include the `<!-- weak_dimensions: [...] -->` comment only if any P-dimension scored WEAK or FAIL. Omit the comment if all PASS.
- Write to `polaris/philosophy.md`.

### 4b. If `polaris/philosophy.md` exists

- Append a new `## {slug}` section at the end of the file with Statement / Why it matters / Violation trigger.
- Do NOT rewrite the frontmatter or existing principles.
- Include the `<!-- weak_dimensions: [...] -->` HTML comment on the line below the principle only if any P-dimension scored WEAK or FAIL.

## Step 5 — Commit

Stage and commit only `polaris/philosophy.md` on the current branch:

```bash
git add polaris/philosophy.md
git commit -m "philosophy: {slug}"
```

Do not create a branch. Philosophy updates are not PR-sized units; they accumulate on whatever branch the user is on.

## Step 6 — Confirm

Tell the user:

> Philosophy principle added: `{slug}`
> Weak dimensions: `{list or "none"}`
> Total principles: `{N}`
>
> This principle will be checked on every future `/spec` and `/goal`. If any new Spec or Goal conflicts with it, Polaris will halt and raise the conflict.

## Note on inline use from /init

When `/init` invokes this flow inline during its Philosophy part, skip Step 1 (preconditions are handled by `/init`; `polaris/mission.md` is drafted in memory, not yet on disk). Skip Step 5 (commit) — `/init` will write and commit `polaris/philosophy.md` together with the rest of the first commit. Return to `/init` after Step 3 with the principle slug and its weak dimensions.
