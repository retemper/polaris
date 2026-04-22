---
description: Create a Polaris Goal — runs G1-G4 interrogation and writes a Goal file to polaris/goals/active/.
---

# /goal

You are executing the Polaris `/goal` slash command. Follow the procedure below exactly.

## Step 1 — Preconditions

Check that `polaris/mission.md` exists.

- If it does not exist, halt. Tell the user: "This repository isn't set up for Polaris yet. Run `/init` first."
- If it exists, proceed.

Read `polaris/mission.md`. You will reference its anti-strategy section during Step 4.

## Step 2 — Active Goal advisory (soft limit)

List files in `polaris/goals/active/` (excluding `.gitkeep`).

- If there are fewer than 5, proceed silently.
- If there are 5 or more, tell the user: "You already have {N} active Goals. Holding too many in flight dilutes focus. Continue anyway, or cancel this command and reassess existing Goals first?"

This is advisory, not blocking. If the user says continue, proceed.

## Step 3 — G1–G4 interrogation

Ask the user each of the following questions, one at a time. After each answer, score it using the rubric and reflect your scoring back to the user. If the score is WEAK or FAIL, explain why and offer the user a chance to refine. Accept whatever they land on.

Track which dimensions end up WEAK or FAIL — you'll record these in the Goal's frontmatter.

### G1 — Target outcome
Ask: "What observable state of the world tells you this Goal is achieved? Translate any abstract outcome into a concrete, independently-verifiable signal — e.g., 'users are happier' becomes 'NPS from our in-app survey crosses 50, measured across 200+ responses'."

Rubric:
- **PASS**: a check someone else could independently run and verify (a metric crossing a named threshold, a user flow functioning end-to-end, a product shipped and in use by a named customer segment).
- **WEAK**: self-reported or subjective ("I feel we're there", "the team thinks it's working").
- **FAIL**: cannot name any observable signal, or the signal is only verifiable by the author.

### G2 — Connection to Mission
Ask: "Which element of `polaris/mission.md` does this Goal advance? Quote the specific line or phrase."

Rubric:
- **PASS**: directly quotes or references a specific part of the Mission and explains the link.
- **WEAK**: gestures at Mission broadly ("it aligns with our mission").
- **FAIL**: cannot articulate a link, or the link requires multiple inferential hops.

### G3 — Not this
Ask: "Name one thing related to this Goal that you are explicitly NOT promising. Something that could plausibly be assumed to be in scope, but isn't."

Rubric:
- **PASS**: a concrete boundary that could plausibly have been in scope.
- **WEAK**: tautological ("won't do unrelated things").
- **FAIL**: "nothing is out of scope."

### G4 — Riskiest strategic bet
Ask: "What single belief, if wrong, makes pursuing this Goal a waste of effort?"

Rubric:
- **PASS**: a falsifiable belief with real stakes — naming it exposes a real risk.
- **WEAK**: names a risk but not a falsifiable belief ("things could go wrong").
- **FAIL**: "nothing could invalidate this Goal."

## Step 4 — Anti-strategy check

Read the anti-strategy section of `polaris/mission.md`. For each item, ask yourself whether this Goal violates it.

If you find a violation:
- Halt. Tell the user exactly which anti-strategy item conflicts with this Goal and how.
- The user must either (a) cancel the Goal, or (b) explicitly amend `polaris/mission.md` to remove/modify the anti-strategy item. Amending is a strategic decision — do not do it silently.

If no violation, proceed.

## Step 5 — Philosophy alignment check

If `polaris/philosophy.md` exists, read it. For each principle, verify:

- Achieving this Goal's G1 target outcome does not require violating the principle.
- This Goal's G3 excluded territory does not directly contradict the principle.

If you find a conflict:
- Halt. Tell the user exactly which Philosophy principle conflicts with this Goal and how (e.g., "achieving G1 would require {action} which violates principle '{slug}'").
- The user must either (a) cancel the Goal, (b) revise G1/G3 so it no longer conflicts, or (c) revise the conflicting principle via `/philosophy` (or by editing `polaris/philosophy.md` directly). Amending is a strategic decision — do not do it silently.

If no violation, proceed. If `polaris/philosophy.md` does not exist, skip this step — Philosophy is optional and may not have been defined yet.

## Step 6 — Write the Goal

Generate:
- `timestamp` — Unix epoch seconds, from `date +%s`.
- `slug` — kebab-case, 2–5 words capturing the Goal's subject, derived from G1.

File path: `polaris/goals/active/{timestamp}-{slug}.md`

(New Goals always land in `active/`. Move to `achieved/` when the G1 signal is observed, or to `abandoned/` if the direction is no longer right. The directory IS the status.)

Read the Goal template at `${CLAUDE_PLUGIN_ROOT}/templates/goal.md`. Fill it:
- Frontmatter:
  - `id: {timestamp}`
  - `created: {YYYY-MM-DD today}`
  - `weak_dimensions: [...]` — list the G-dimensions that scored WEAK or FAIL (e.g., `[G1, G3]`). Empty list if all PASS.
- Prose sections G1–G4 — use the user's final answers.
- Leave `Outcome notes` empty — it is filled only when the Goal moves to `achieved/` or `abandoned/`.

Write the file.

## Step 7 — Commit

Stage and commit only the new Goal file on the current branch:

```bash
git add polaris/goals/active/{timestamp}-{slug}.md
git commit -m "goal: {slug}"
```

Do not create a branch. Goals are not PR-sized units; they accumulate on whatever branch the user is on. If the user is in the middle of Spec work on a feature branch, the Goal will travel with that branch — surface this to the user if it matters.

## Step 8 — Confirm

Tell the user:

> Goal created: `polaris/goals/active/{timestamp}-{slug}.md`
> Weak dimensions: `{list or "none"}`
>
> When you run `/spec`, you can link a Spec to this Goal. When the G1 signal is observed, move the file to `polaris/goals/achieved/` (`git mv`) and fill the `Outcome notes` section. If the direction is no longer right, move it to `abandoned/` instead.

## Note on inline use from /spec

When `/spec` invokes this command inline (because no active Goal exists or none fit), skip Step 1's halt — `/spec` has already verified the precondition. Skip Step 7's commit — `/spec` will stage and commit the Goal together with the new Spec. Return to `/spec` after Step 6 with the new Goal's `{timestamp}-{slug}` identifier.
