---
name: scope
description: Boundary clarification — what's in scope, what's out, what's adjacent. ALWAYS invoke this skill when the situation has fuzzy boundaries, when the user is unsure where the work begins and ends, or when "this might also touch X" creep is starting. Produces a Scope artifact — explicit in/out/adjacent lists with rationale per boundary, plus boundary-drift flags (where the boundary is structurally weak and likely to move during execution). Skip when scope is genuinely clear and stated, when work is too small to warrant boundary articulation, or when the situation isn't bounded by the user's choice (a recurring deliverable's scope is set by the contract, not by a frame skill).
# description-style: directive + negative constraint (Seleznov)
# rationale: Scope clarity is one of the most under-articulated dimensions of framing — most problems "creep" because boundaries weren't named. Directive style forces explicit in/out/adjacent statements. The boundary-drift flagging is the under-treated valuable output: knowing *where* a boundary is structurally weak lets the user pre-decide how to handle the drift.
---

<!--
ACTIVATION TESTS (for /skill:validate --bench):
1. SHOULD activate (fuzzy boundaries): "I want to redo the brief→PPT pipeline, but that touches creative, project management, design — how do I bound this?"
2. SHOULD activate (creep risk): "The client says it's a 'small' redesign but every conversation suggests it's actually a full re-platforming"
3. SHOULD NOT activate (scope clear): "Refactor the auth module" (scoped to the auth module — articulate, don't re-scope)
4. BOUNDARY: "Help me figure out what this consulting engagement actually covers" (could be scope, could be frame:probe — composer should disambiguate based on whether user wants boundary articulation specifically)
-->

# scope

Boundary clarification. Surfaces what is **in scope**, what is **out**, and what is **adjacent** (related but explicitly excluded). Also flags **boundary-drift risks** — places where the boundary is structurally weak and likely to move during execution.

> See [PRINCIPLES.md](../../PRINCIPLES.md) for shared rules — particularly **§10 question-first**.

## When to Use

- Situation has fuzzy boundaries — user isn't sure where the work begins or ends.
- Risk of scope creep — early conversations suggest the work touches more than initially named.
- Multi-area situation where the user needs to decide whether the work is one of those areas or spans multiple.
- High-stakes engagement where scope ambiguity would cost real time/money (consulting engagements, contract work, multi-month projects).
- Composer-invoked when intake reveals scope ambiguity.

## When NOT to Use

- Scope is genuinely clear and stated — articulate in `frame:problem-statement` instead.
- Work is small enough that boundary articulation overhead exceeds the work.
- The scope is fixed by external contract (a recurring retainer deliverable, a regulatory mandate) — `frame:scope` can't change those boundaries.
- The thing the user actually wants is a *decision about which scope to pick* — that's `frame:reframe` (alternative scopes as alternative frames).

## Process

The skill outputs a **Scope artifact** with three explicit lists (in / out / adjacent) plus boundary-drift flags.

Question-first per [PRINCIPLES.md §10](../../PRINCIPLES.md#10-question-first--new-to-frame): the skill leads with three questions —

- *"What's definitely IN this work?"*
- *"What's definitely OUT?"*
- *"What's ADJACENT — related, tempting to include, but explicitly out?"*

### 1. Enumerate the in-scope items

For each item:
- What it is (specific, not categorical)
- Why it's in (rationale citing the situation)
- Acceptance signal (how we'll know this in-scope item is done)

Bias toward specificity. "The brief→PPT pipeline" is too general; "the debrief synthesis stage of the brief→PPT pipeline" is specific.

### 2. Enumerate the out-of-scope items

Out-of-scope items are things that could plausibly be assumed in scope but explicitly are NOT. For each:
- What it is
- Why it's out (rationale — usually one of: too big for this engagement, owned by someone else, not within user's authority, low ROI, deferred to later)
- What signal would change this (the condition under which it would move into scope — useful for renegotiation later)

Out-of-scope is the most underused part of scope articulation. Most "scope" docs name only what's in. Out-of-scope makes the boundary visible.

### 3. Enumerate the adjacent items

Adjacent items are the dangerous zone — closely related to in-scope work, often touched during execution, but explicitly *not* the deliverable. For each:
- What it is
- Why it's adjacent rather than in (the line between "touches it" and "delivers it")
- Risk of drift (how likely the work creeps into this during execution)

Adjacent items are where most scope creep happens. Naming them in advance lets the user pre-decide how to handle the drift when it appears.

### 4. Flag boundary-drift risks

For each boundary (in/out, in/adjacent, especially adjacent/out), flag where the boundary is structurally weak:

- *Stakeholder pressure*: a stakeholder is likely to push for adjacent → in
- *Dependency*: in-scope item can't ship without an adjacent item also being done → adjacent gets pulled in
- *Information emergence*: discovering something during execution that reveals scope was misset
- *Authority gradient*: scope is set by user but external party can override

For each flag, suggest a **pre-decided response** (what the user will do when the drift appears). Pre-deciding is the deliverable; without it, scope is theater.

### 5. Output

Write to:
- **Within composer**: `<contracts-root>/frame-<slug>/scope.md`
- **Standalone**: `<contracts-root>/scope-<slug>.md`

Format:

```markdown
# Scope: <slug>

## Situation
<one-paragraph distillation>

## Questions this artifact answers
- What's definitely IN this work?
- What's definitely OUT?
- What's ADJACENT — related, tempting to include, but explicitly out?
- Where is the boundary structurally weak, and what's the pre-decided response when drift appears?

## In scope

| # | Item | Why in | Acceptance signal |
|---|---|---|---|
| 1 | <specific item> | <rationale> | <how we know it's done> |
| 2 | ... | ... | ... |

## Out of scope

| # | Item | Why out | Signal that would change this |
|---|---|---|---|
| 1 | <specific item> | <rationale> | <condition for moving in> |
| 2 | ... | ... | ... |

## Adjacent (closely related, explicitly out)

| # | Item | Why adjacent (not in) | Drift risk | Pre-decided response |
|---|---|---|---|---|
| 1 | <specific item> | <where the line falls> | <high/medium/low> | <what user does if drift appears> |

## Boundary-drift flags

### Flag 1: <name>
- Boundary: <which boundary is weak>
- Pressure source: <stakeholder pressure / dependency / information / authority>
- Pre-decided response: <what user does when this drift appears>

### Flag 2: <name>
[same structure]

## Implications for the Problem Statement
- <how scope affects end state, deliverable, constraints>
- <especially: "Scope boundary" field of Polya 9>

## Extraction-confidence
<low | medium | high>

## Assumptions
<inferences during scope articulation, or "none">
```

### 6. Stop

Skill is framing-only. Scope articulation produces a record; the user uses it to negotiate, communicate, and make decisions during execution.

## Anti-Patterns

- **Only naming what's in scope.** Most "scope" docs do this. Without out-of-scope and adjacent, the boundary isn't visible — it's just a wish list of in-scope items.
- **Generic out-of-scope ("anything not listed above").** Useless. Out-of-scope items must be *specific* — the things that could plausibly be assumed in scope but explicitly aren't.
- **No adjacent section.** Adjacent is where most scope creep happens. Skipping it loses the most operationally valuable output of this skill.
- **No drift flags or no pre-decided responses.** Flagging the boundary as weak without naming what the user will do when the drift appears defers the work. Pre-decision is the deliverable.
- **Categorical instead of specific items.** "Design work" is categorical; "the visual design of the homepage hero section" is specific. Categorical scope items are scope theater.
- **Skipping the rationale columns.** Why an item is in / out / adjacent is the audit trail. Without it, the scope is opaque to anyone who reads it later (including the user themselves in 3 months).
- **Scope that contradicts the Problem Statement.** If `frame:scope` runs after `frame:problem-statement`, the scope must be consistent with the Polya 9 constraints field. If contradictions emerge, flag them as iteration triggers (per PRINCIPLES.md §11).

## Rules

- The skill outputs a single Scope artifact (markdown file); never executes.
- All three lists (in / out / adjacent) are populated. "Empty" lists must have explicit rationale (e.g., "No adjacent items — the work is contained").
- Drift flags include pre-decided responses, not just identification of the risk.
- When invoked from `frame:composer`, artifact lives at `<contracts-root>/frame-<slug>/scope.md`.
- When invoked standalone, artifact lives at `<contracts-root>/scope-<slug>.md`.
- `Extraction-confidence` and `Assumptions` fields are non-optional per PRINCIPLES.md §3.
- Question-first (PRINCIPLES.md §10): the artifact structure follows from the three "what's in / out / adjacent" questions.
- When a sub-Agent is the worker, the contract MUST specify `model: opus` per PRINCIPLES.md §2.

## Key Files

- Output (standalone): `<contracts-root>/scope-<slug>.md`
- Output (composer): `<contracts-root>/frame-<slug>/scope.md`
- Sibling frame skills (all under `frame` plugin, all live): `frame:composer`, `frame:probe`, `frame:problem-statement`, `frame:from-corpus`, `frame:jtbd`, `frame:lens`, `frame:stakes`, `frame:reframe`.
- Theoretical grounding: project-management scope-management literature; product-management "MoSCoW" (Must / Should / Could / Won't) prioritization; design-thinking "Define" diamond.
