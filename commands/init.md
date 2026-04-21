---
description: Initialize Polaris in this repository. Runs a setup interview covering context, mission, 3-5 philosophy principles, and 2-3 initial Goals, writes polaris/, injects CLAUDE.md rules, and makes the first commit.
---

# /init

You are executing the Polaris `/init` slash command. This is a setup interview — expect 40-60 minutes of dialogue. Do not shortcut it.

## Step 1 — Precondition

Check whether `polaris/mission.md` exists in the current working directory.

- If it exists, halt. Tell the user: "Polaris is already initialized in this repository. To add a Goal, run `/goal`. To create a Spec, run `/spec`."
- If it does not exist, proceed.

Tell the user:

> This will initialize Polaris in this repository. I'll interview you in four parts:
> 1. **Context** — your role, the codebase, its phase (5-7 open questions, no scoring)
> 2. **Mission** — what this codebase exists to do, anti-strategy, riskiest assumption (scored against rubrics)
> 3. **Philosophy** — 3 to 5 principles that stay invariant across Goal changes (scored across P1–P3)
> 4. **Goals** — 2 to 3 initial Goals for the current phase (each scored across G1–G4)
>
> At any question, if your answer is vague I'll reflect that back and give you a chance to sharpen it. You're never forced — you can accept a WEAK answer if that's where your thinking is. We'll record which dimensions scored weak so you can revisit them later.
>
> Philosophy is the slowest part because principles need real reflection. If you can only name 2 strong principles in this session, you can add more later with `/philosophy` — don't force-fill.
>
> Ready to start?

Wait for affirmative. If the user declines or wants to defer, exit without writing anything.

## Step 2 — Part A: Context (open questions, no scoring)

Ask each of the following, one at a time. Take notes mentally (or in a scratch store) — these answers shape later writing but are not persisted as separate fields.

- **A1. Owner.** "Who is the one human responsible for strategic decisions on this codebase? (name)"
- **A2. Your relationship.** "What's your relationship to this codebase — are you the owner, a contributor, or someone onboarding?"
- **A3. Codebase in one sentence.** "Before we refine it: in one rough sentence, what does this codebase do today? (I'll help sharpen this into the Mission in Part B.)"
- **A4. Consumer.** "Who uses what this codebase produces? Name a specific persona, segment, or downstream system. ('The codebase itself' is OK for infra work — but say so.)"
- **A5. Phase.** "What phase is this repo in right now: Discovery, Build, Scale, or Sunset?" Then probe: "What evidence tells you it's this phase and not the next one?"
- **A6. Constraint.** (Optional — skip if user seems impatient) "What's the biggest constraint on this work right now? Time, team size, a dependency, a tech limit?"
- **A7. Decision authority.** (Optional) "If there's a strategic disagreement, who decides?"

## Step 3 — Part B: Mission (scored)

Now derive the Mission. Ask each of the following, one at a time. Score each answer with a rubric, reflect back, offer refinement if WEAK or FAIL.

Track weak dimensions — but Mission weakness doesn't get recorded in frontmatter (that's Spec/Goal scope). Instead, if B1, B2, or B3 end up WEAK or FAIL, tell the user at the end of Part B: "Your Mission has weak spots in [list]. I'll still write it, but you may want to revisit after running a few Specs."

### B1 — Mission statement
Use the user's A3 rough sentence as a starting point. Ask: "Let's tighten this. In one sentence: what does this codebase exist to do? Name a specific outcome or capability, not a category."

Rubric:
- **PASS**: names a specific outcome, user, or capability. "Expose X to Y so they can Z."
- **WEAK**: vague category ("build software for teams"). Missing the outcome or the user.
- **FAIL**: tautological ("this codebase exists to be a codebase") or aspirational with no actor ("change the world").

### B2 — Anti-strategy
Ask: "Name 2 to 4 things this codebase will explicitly never do, even when tempting. Be concrete — 'never add a billing module' is concrete; 'never compromise quality' is a platitude."

Rubric (apply per item, then overall):
- **PASS**: each item names a specific tempting direction that is ruled out.
- **WEAK**: generic quality statements ("never ship bugs"), or items that nobody would actually propose.
- **FAIL**: fewer than 2 items, or all items are platitudes.

### B3 — Riskiest strategic assumption
Ask: "What's the single belief underneath this Mission that, if wrong, makes the entire project misguided? A falsifiable claim — not a worry."

Rubric:
- **PASS**: a specific, falsifiable belief with real stakes. "We assume X; if X turns out to be false, this codebase shouldn't exist."
- **WEAK**: names a risk but not a falsifiable belief ("things could change").
- **FAIL**: "nothing could invalidate this."

## Step 4 — Part C: Philosophy (scored, 3–5 principles)

Tell the user:

> Now 3 to 5 Philosophy principles. Unlike Goals (which come and go), Philosophy is what stays invariant even as Goals change — the "how we operate" and "what we believe" beneath the Mission's "what and why". These are identity-level commitments that will constrain every Goal and Spec from now on.
>
> Each principle is a positive statement (not a negation — anti-strategy covers negations). I'll interrogate each one across three dimensions (P1–P3). Minimum 2, target 3, soft cap 5.

Run the following loop.

### Per-principle loop

Ask: "State one principle this codebase commits to. A positive declaration — what you believe, how you operate — not what you won't do."

Then run P1–P3 exactly as in the `/philosophy` command. See `${CLAUDE_PLUGIN_ROOT}/commands/philosophy.md` for the full interrogation — do not re-paraphrase, reuse the question text and rubrics verbatim:

- **P1** — Does it actually change decisions? (names a concrete decision this would flip)
- **P2** — Does it have teeth under temptation? (specific tempting scenario + concrete violation marker)
- **P3** — Does it align with Mission? (no contradiction with Mission statement or anti-strategy)

For P3 during `/init`, only check against Mission (B1 + B2) — no active Goals exist yet. Goals will be checked against Philosophy separately in Step 6.

Track WEAK and FAIL dimensions per principle — these go into that principle's `<!-- weak_dimensions -->` annotation in `polaris/philosophy.md`.

### After each principle

Tell the user: "Principle recorded. You now have {count} principles drafted. Continue?"
- If count < 2: "I recommend at least one more — a single principle doesn't paint much of an identity picture. Continue with another?"
- If count >= 2 and < 5: "Add another, or stop here?"
- If count == 5: "Five is a comfortable ceiling. I recommend stopping — more principles dilute individual enforcement weight. Continue anyway, or stop here?"

If the user struggles to produce a second principle after meaningful effort, accept 1–2 and tell them: "You can add more later with `/philosophy` as patterns crystallize."

## Step 5 — Part D: Initial Goals (iterative, scored)

Tell the user:

> Now 2 to 3 initial Goals. A Goal is a desired outcome for the current phase — more specific than the Mission, broader than a single PR. For each Goal, I'll ask four questions (G1–G4). You can add more later with `/goal`.

Run the following loop. Minimum 2 Goals, target 3, soft cap at 5.

### Per-Goal loop

Ask: "Name one outcome you want to see in the current phase. I'll then interrogate it across four dimensions."

Then run G1–G4 exactly as in the `/goal` command (same questions, same rubrics). See `${CLAUDE_PLUGIN_ROOT}/commands/goal.md` for the full interrogation — do not re-paraphrase, reuse the question text and rubrics verbatim:

- **G1** — Target outcome (independently verifiable signal)
- **G2** — Connection to Mission (quotes or references a specific element from Part B)
- **G3** — Not this (concrete boundary)
- **G4** — Riskiest strategic bet (falsifiable belief)

Track WEAK and FAIL dimensions per Goal — these go into that Goal's `weak_dimensions` frontmatter.

### After each Goal

Tell the user: "Goal recorded. You now have {count} Goals drafted. Continue?"
- If count < 2: "I recommend at least one more — a single-Goal repo can't show portfolio trade-offs. Continue with another?"
- If count >= 2 and < 5: "Add another, or stop here?"
- If count == 5: "Five is a lot. I recommend stopping — more Goals dilute focus. Continue anyway, or stop here?"

## Step 6 — Anti-strategy + Philosophy consistency check

For each drafted Goal, run two checks:

1. **Anti-strategy check.** Verify the Goal does not violate any item in Mission's anti-strategy (from B2).
2. **Philosophy alignment check.** Verify achieving this Goal's G1 target does not require violating any Philosophy principle from Part C, and that G3's excluded territory does not directly contradict a principle.

If any violation surfaces:
- Halt at that Goal. Tell the user specifically: "Goal '{slug}' conflicts with {anti-strategy item '{item}' / Philosophy principle '{slug}'} because {how}."
- The user must (a) drop the Goal, (b) revise it so it doesn't conflict, or (c) revise the conflicting Mission/Philosophy element. Amending either is a strategic decision — do not do it silently.

## Step 7 — Write files

Generate for each drafted Goal:
- `timestamp` — Unix epoch seconds, `date +%s` (separate timestamp per Goal to avoid collisions; if running close together, increment by 1)
- `slug` — kebab-case, 2–5 words

For each drafted Philosophy principle:
- `slug` — kebab-case, 2–4 words derived from the Statement

Read templates and write:

1. **Mission** — read `${CLAUDE_PLUGIN_ROOT}/templates/mission.md`, fill:
   - Frontmatter: `owner: {A1}`, `phase: {A5}`, `created: {YYYY-MM-DD today}`
   - Mission section: final B1 answer
   - Anti-strategy items: final B2 items
   - Current phase paragraph: 1-2 sentences describing what the chosen phase means for this codebase (derive from A5 probe + A6 if given)
   - Riskiest strategic assumption: final B3 answer
   - Write to `polaris/mission.md`

2. **Philosophy** — read `${CLAUDE_PLUGIN_ROOT}/templates/philosophy.md`, fill:
   - Frontmatter: `owner: {A1}`, `created: {YYYY-MM-DD today}`
   - Remove the `## <principle-slug>` example section.
   - Append one `## {slug}` section per drafted principle, each with Statement / Why it matters / Violation trigger filled.
   - For any principle with WEAK/FAIL dimensions, add `<!-- weak_dimensions: [...] -->` on the line below the principle's three fields. Omit the comment for all-PASS principles.
   - Write to `polaris/philosophy.md`.

3. **State directories** — create empty files:
   - `polaris/specs/planned/.gitkeep`
   - `polaris/specs/in-progress/.gitkeep`
   - `polaris/specs/done/.gitkeep`
   - `polaris/specs/canceled/.gitkeep`
   - `polaris/goals/active/.gitkeep`
   - `polaris/goals/achieved/.gitkeep`
   - `polaris/goals/abandoned/.gitkeep`

4. **Each Goal** — read `${CLAUDE_PLUGIN_ROOT}/templates/goal.md`, fill with G1–G4 answers and `weak_dimensions`, write to `polaris/goals/active/{timestamp}-{slug}.md`.

5. **CLAUDE.md injection:**
   - If `CLAUDE.md` does not exist at repo root, create it and write the contents of `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md.snippet`.
   - If `CLAUDE.md` exists and does not contain `<!-- POLARIS-START`, append the snippet (with a blank line separator).
   - If it already contains the marker, leave `CLAUDE.md` unchanged.

## Step 8 — First commit

Stage and commit everything on the current branch:

```bash
git add polaris/ CLAUDE.md
git commit -m "polaris: initialize mission, {P} philosophy principles, {N} goals, CLAUDE.md rules"
```

No new branch is created — `/init` is repo-wide bootstrap, not feature work. Subsequent `/spec` runs will create their own feature branches.

## Step 9 — Confirm

Tell the user:

> Polaris initialized.
>
> **Written:**
> - `polaris/mission.md` — Mission, anti-strategy ({N_anti} items), phase ({phase}), riskiest assumption
> - `polaris/philosophy.md` — {P} principles: {list of principle slugs}
> - `polaris/goals/active/` — {N} Goals: {list of goal slugs}
> - State directories for specs/ and goals/
> - `CLAUDE.md` — rules for AI agents working in this repo
>
> **Weak dimensions flagged:**
> - Mission: {list or "none"}
> - Philosophy: {per-principle list, e.g. "filesystem-single-source: [P2]"}
> - Goals: {per-goal list, e.g. "onboarding-under-60s: [G2, G4]"}
>
> **Next steps:**
> - Run `/spec` to create your first Spec. You'll be asked which Goal it advances and the Spec will be checked against Philosophy and anti-strategy.
> - Run `/goal` later to add more Goals, or `/philosophy` to add principles as patterns crystallize.
> - When a Goal's target outcome is observed, move its file to `polaris/goals/achieved/` (`git mv`) and fill the `Outcome notes` section.
