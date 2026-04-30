---
description: Create a Polaris Spec — selects a parent Goal, runs the Clarity Gate interrogation (S1–S6), and writes a Spec file on a new feature branch.
---

# /spec

You are executing the Polaris `/spec` slash command. Follow the procedure below exactly.

## Step 1 — Preconditions

Check that `polaris/mission.md` exists in the current working directory.

- If it does not exist, halt. Tell the user: "This repository isn't set up for Polaris yet. Run `/init` first."
- If it exists, proceed.

Read `polaris/mission.md`. You will reference its anti-strategy section during Step 7.

## Step 2 — Goal selection (required)

Every Spec must declare which Goal it advances. List files in `polaris/goals/active/` (excluding `.gitkeep`).

### 2a. No active Goals

If the list is empty, tell the user:

> No active Goals. Every Spec must link to a Goal so we can tell if work is converging on a target outcome. Let's create a Goal first.

Run the `/goal` command's Step 2 through Step 6 inline — see `${CLAUDE_PLUGIN_ROOT}/commands/goal.md`. Skip `/goal`'s Step 1 (precondition already verified) and Step 7 (commit) — you will commit the Goal together with the Spec in Step 10 below. After the Goal is written, record its `{timestamp}-{slug}` identifier and continue to Step 3.

### 2b. Active Goals exist

Read the frontmatter and Goal title of each file in `polaris/goals/active/`. Present them to the user as a numbered list:

> Which Goal does this Spec advance?
> 1. {slug-1} — {one-line summary from G1}
> 2. {slug-2} — {one-line summary from G1}
> ...
> Or: "none fit" to create a new Goal first.

Wait for the user's choice.

- If user picks a number: record the corresponding `{timestamp}-{slug}` as the parent Goal. Proceed to Step 3.
- If user says "none fit" or similar: run the inline `/goal` flow from 2a, then proceed.

## Step 3 — Issue linkage (optional)

Related Issues are recorded on the Spec side — Issues do not store their Specs. List files in `polaris/issues/open/` (excluding `.gitkeep`).

### 3a. No open Issues or directory missing

If the directory does not exist or is empty, skip this step (leave `related_issues` empty).

### 3b. Open Issues exist

Read the frontmatter and Issue title of each file in `polaris/issues/open/`. Present them to the user as a numbered list:

> Does this Spec address any open Issue(s)? (optional, multi-select)
> 1. {slug-1} — {one-line summary from I1}
> 2. {slug-2} — {one-line summary from I1}
> ...
> Or: "none" to skip.

Wait for the user's response.

- If the user picks one or more numbers (e.g. `1, 3` or `1 and 3`): record the corresponding `{timestamp}-{slug}` identifiers as `related_issues`.
- If the user says "none" or similar: leave `related_issues` empty.

Proceed to Step 4.

## Step 4 — Parent Spec selection (optional)

A Spec can be a child of a larger umbrella Spec. Hierarchy is expressed in the **filesystem only** — the parent's full slug becomes a group folder under `polaris/specs/{status}/`, and child Specs live inside as sibling files. There is no `parent_spec:` frontmatter field; `git mv` is the only attachment mechanism. When child statuses diverge from the parent, the same group folder appears under multiple status directories (e.g., `polaris/specs/in-progress/{parent-slug}/` for the parent and in-flight children, `polaris/specs/done/{parent-slug}/` for completed children).

### 4a. Enumerate candidates

List every entry directly under `polaris/specs/planned/` and `polaris/specs/in-progress/` (excluding `.gitkeep`). Skip `done/` and `canceled/` — adding children to a finished parent is exceptional and should be done manually if needed.

For each entry:
- A flat `.md` file (e.g., `polaris/specs/in-progress/1776869476-polaris-compass.md`): candidate parent. Identifier = filename without `.md` (e.g., `1776869476-polaris-compass`). Status = the directory it lives in.
- A directory (e.g., `polaris/specs/in-progress/1776869476-polaris-compass/`): existing parent group. The parent file inside is `{dir-basename}/{dir-basename}.md`. Identifier = directory basename. Status = the directory the group folder lives in.

For each candidate, read its `## What changes (S1)` section and extract a one-line summary.

### 4b. Ask the user

If the candidate list is empty, skip to Step 5 — this is necessarily a flat Spec.

Otherwise, present:

> Is this Spec part of a larger Spec? (optional)
> 1. {parent-full-slug-1} — {one-line S1 summary}
> 2. {parent-full-slug-2} — {one-line S1 summary}
> ...
> Or: "no" to keep it flat.

Wait for the user's response.

- "no" / "none" / similar: record `parent = null`. Proceed to Step 5.
- A number: record `parent = {parent-full-slug}` and `parent_status` = the status directory the parent currently lives in (`planned` or `in-progress`). Also record whether the parent is currently flat (`.md` file) or already a group folder. Proceed to Step 5.

## Step 5 — Clarity Gate (S1–S6)

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
Ask: "Why does this happen now rather than next week or next month? How does it advance the parent Goal specifically?"

Rubric:
- **PASS**: unblocks something, deadline, degrading condition, or names a specific way it advances the parent Goal's G1 target outcome.
- **WEAK**: "it's convenient" or gestures at the Goal without explaining the link.
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

## Step 6 — Goal-Spec alignment check

Re-read the parent Goal's G1 target outcome and G3 ("not this") sections. Verify:

- S1 (what changes) plausibly contributes to the Goal's G1 target outcome.
- S1 does not fall inside the Goal's G3 excluded territory.

If either check fails:
- Tell the user exactly which alignment is broken.
- Offer: (a) revise the Spec to fit the Goal, (b) pick a different Goal, or (c) create a new Goal.
- Do not silently proceed.

## Step 7 — Anti-strategy check

For each item in the anti-strategy section of `polaris/mission.md`, ask yourself whether this Spec violates it. If you find a violation:

- Halt. Tell the user exactly which anti-strategy item conflicts with this Spec and how.
- The user must either (a) cancel the Spec, or (b) explicitly amend `polaris/mission.md` to remove/modify the anti-strategy item. Amending is a strategic decision — do not do it silently.

If no violation, proceed.

## Step 8 — Philosophy alignment check

If `polaris/philosophy.md` exists, read each principle. For each principle, ask yourself whether this Spec — specifically its S1 (what changes) — would violate the principle.

If you find a conflict:
- Halt. Tell the user exactly which Philosophy principle the Spec violates and how (e.g., "the file-layout change in S1 would require {action} which violates principle '{slug}': {statement}").
- The user must either (a) cancel the Spec, (b) revise S1/S3 so it no longer conflicts, or (c) revise the conflicting principle via `/philosophy` (or by editing `polaris/philosophy.md` directly). Amending is a strategic decision — do not do it silently.

If no violation, proceed. If `polaris/philosophy.md` does not exist, skip this step — Philosophy is optional and may not have been defined yet.

## Step 9 — Write the Spec

Generate:
- `timestamp` — Unix epoch seconds, from `date +%s`.
- `slug` — kebab-case, 2–5 words capturing the Spec's subject, derived from S1.

Determine the file path based on whether a parent was selected in Step 4:

**No parent selected:** `polaris/specs/planned/{timestamp}-{slug}.md` — the existing flat path.

**Parent selected:** `polaris/specs/planned/{parent-full-slug}/{timestamp}-{slug}.md`. Before writing, set up the group folder:

1. If the parent is currently a flat `.md` file (not yet a group folder), migrate it so the parent and its children share the group folder:

   ```bash
   mkdir -p polaris/specs/{parent_status}/{parent-full-slug}
   git mv polaris/specs/{parent_status}/{parent-full-slug}.md polaris/specs/{parent_status}/{parent-full-slug}/{parent-full-slug}.md
   ```

   (`{parent_status}` is `planned` or `in-progress` per Step 4. The `git mv` stages the rename; no separate `git add` for it later.)

2. Ensure the child's group folder exists under `planned/`:

   ```bash
   mkdir -p polaris/specs/planned/{parent-full-slug}
   ```

(New Specs always land in `planned/` — flat or under a group folder. When the user starts implementation they move the file to `in-progress/` (or `in-progress/{parent-full-slug}/` for grouped Specs), and to `done/` or `canceled/` when finished. The directory IS the status, applied per-Spec.)

Read the Spec template at `${CLAUDE_PLUGIN_ROOT}/templates/spec.md`. Fill it:
- Frontmatter:
  - `id: {timestamp}`
  - `goal: {parent-goal-timestamp-slug}` — required; the identifier from Step 2
  - `related_issues: [...]` — list the `{issue-timestamp}-{issue-slug}` identifiers selected in Step 3. Empty list if none.
  - `branch: feat/{timestamp}`
  - `created: {YYYY-MM-DD today}`
  - `weak_dimensions: [...]` — list the S-dimensions that scored WEAK or FAIL (e.g., `[S3, S5]`). Empty list if all PASS.
- Prose sections S1–S6 — use the user's final answers.

Write the file.

## Step 10 — Create the branch

Run:

```bash
git checkout -b feat/{timestamp}
git add {child-spec-path}
```

`{child-spec-path}` is the path determined in Step 9 — either `polaris/specs/planned/{timestamp}-{slug}.md` or `polaris/specs/planned/{parent-full-slug}/{timestamp}-{slug}.md`. If Step 9 ran a `git mv` to migrate a flat parent into a group folder, that rename is already staged from the `git mv` and does not need a separate `git add`.

If Step 2a ran (a new Goal was created inline), also stage it:

```bash
git add polaris/goals/active/{goal-timestamp}-{goal-slug}.md
```

Then commit:

```bash
git commit -m "spec: {slug}"
```

(If a new Goal was created, the commit message is: `spec: {slug} (+goal: {goal-slug})`.)

## Step 11 — Confirm

Tell the user:

> Spec created: `{child-spec-path}`
> Parent Goal: `{goal-slug}`
> Parent Spec: `{parent-full-slug or "none"}`
> Branch: `feat/{timestamp}` (checked out)
> Weak dimensions: `{list or "none"}`
>
> You can start implementing now. Claude Code will see the Spec via `CLAUDE.md` and stay scoped to it. When you begin implementation, move the file to `in-progress/` via `git mv` (preserving any group folder, e.g., `polaris/specs/in-progress/{parent-full-slug}/{timestamp}-{slug}.md`). When merged or canceled, move to `done/` or `canceled/`.
