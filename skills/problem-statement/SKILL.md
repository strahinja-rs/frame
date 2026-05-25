---
name: problem-statement
description: Articulate a situation as a Polya 9-target structured Problem Statement — the canonical artifact that feeds /approach:composer. ALWAYS invoke this skill when the user wants to capture a problem in its canonical articulated form (after frame work has clarified seeing, OR when the problem is already well-articulated and they just want the structured artifact). Produces a Problem Statement document with 9 targets: desired end state, current state, gap, deliverable shape, verification criterion, constraints, known/unknown, user role, stakes. Plus Assumptions + extraction-confidence. Universal terminator: every frame:composer composition ends with this skill. Does not execute. Skip when problem is genuinely unclear (run frame:probe first), when user wants execution not articulation (use /approach), or when articulation isn't the bottleneck (a probe or reframe is).
# description-style: directive + negative constraint (Seleznov)
# rationale: problem-statement is the universal terminator of every frame composition. Its 9 targets map to Polya's How To Solve It (1945) — the deepest grounding for structured problem articulation in the literature. Directive style forces the canonical structure even when the user is tempted to skip ahead to action.
---

<!--
ACTIVATION TESTS (for /skill:validate --bench):
1. SHOULD activate (terminator of composer): invoked at end of every frame:composer run
2. SHOULD activate (standalone, well-articulated): "Capture this in our canonical Problem Statement format" / "I know what I'm trying to do, give me the structured version"
3. SHOULD NOT activate (problem unclear): "I don't know what's going on" → run frame:probe first
4. SHOULD NOT activate (user in execution mode): "Refactor the auth module" → straight to /approach
5. BOUNDARY: "Write a one-pager on what I'm trying to do" (could be problem-statement, could be a memo task — clarify which structure)
-->

# problem-statement

The canonical articulation skill: takes a situation (already explored, partially framed, or fully clarified) and produces a Polya 9-target structured Problem Statement. This is the **universal terminator** of every `frame:composer` composition — the artifact that feeds `/approach:composer`.

> See [PRINCIPLES.md](../../PRINCIPLES.md) for shared rules — particularly **§13 universal terminator**, **§3 Assumptions + extraction-confidence**, **§10 question-first**.
>
> See [ARCHITECTURE.md](../../ARCHITECTURE.md) for the cascade and hand-off protocol.

## When to Use

- Closing every `frame:composer` composition (mandatory per PRINCIPLES.md §13).
- Standalone, when the user already has a clear problem and wants it captured in canonical form for downstream use.
- Hand-off prep — about to invoke `/approach:composer` and need a structured Problem Statement to feed it.
- Documentation — capturing the problem statement at the start of a project / engagement / sprint for later reference.

## When NOT to Use

- Problem is genuinely unclear ("I don't know what's going on") → run `frame:probe` first.
- User wants execution not articulation → straight to `/approach`.
- Articulation isn't the bottleneck (the user already wrote a problem statement and wants to challenge it) → use `frame:reframe`.
- The artifact wanted is a *memo* or *narrative*, not a structured problem statement → not this skill.

## Process

The skill outputs a **Problem Statement artifact** — a structured markdown document with 9 named fields plus metadata. Question-first per [PRINCIPLES.md §10](../../PRINCIPLES.md#10-question-first--new-to-frame): each field is articulated by answering a specific question, surfaced to the user (or to upstream frame artifacts when invoked from composer).

### 1. Determine extraction mode

- **From upstream frame artifacts** (when invoked from `frame:composer` after other skills ran): read the existing frame artifacts (probe, lens, reframe, etc.) and synthesize the Polya 9 from them. No new user questions unless gaps remain.
- **From user dialogue** (standalone with no prior frame work): ask the 9 questions one cluster at a time (cluster Q1-Q3, Q4-Q5, Q6-Q9). Hard cap on dialogue rounds: 3.
- **Quick-extract** (when the user's prompt explicitly states all 9 targets): write directly, mark `extraction-confidence: high`, list Assumptions for any inferred fields.

### 2. Fill the 9 targets

Each target answers a specific question. Question-first:

1. **Desired end state** — *"When this is done, what's true that isn't true now?"* Concrete, observable. Not aspirational language; specific outcome.
2. **Current state** — *"What's actually true now?"* (Often differs from user's framing — surface honest gaps.)
3. **Gap** — *"What's missing / broken / needed to bridge current → end?"*
4. **Deliverable shape** — *"What's produced when this is done?"* Artifact / decision / behavior change / knowledge / communication.
5. **Verification criterion** — *"How do we know it's done?"* Deterministic (tests pass, file exists)? Judgment-bound (LLM critic, human review)? Human-only (taste, business decision)?
6. **Constraints** — *"What boundaries does the solution have to respect?"* Time, cost, quality floor, scope, reversibility, dependencies, blast radius.
7. **Known vs unknown** — *"What do we know? What don't we know? Which unknowns are blockers?"*
8. **User role** — *"Where does the user want gates / decisions? Where autonomy?"*
9. **Stakes** — *"Why does this matter?"* Reputational, financial, identity, relational, technical-debt-shaped.

### 3. Mark extraction-confidence

Per [PRINCIPLES.md §3](../../PRINCIPLES.md#3-assumptions--extraction-confidence--surface-uncertainty-dont-hide-it):

- **high** — every required field was stated or directly inferable; no significant inference.
- **medium** — some inference, no blocking unknowns.
- **low** — significant inference; multiple Assumptions; downstream tools should treat as provisional.

The marker is honest, not aspirational. Low-confidence Problem Statements still get written — they're useful artifacts — but downstream consumers (notably `/approach:composer`) will surface the confidence to the user.

### 4. List Assumptions

Every field where the framer inferred rather than the user stated gets an Assumption. Format:

```
Assumptions:
- [field]: <what was inferred + what the user actually stated, if anything>
- ...
```

If `none`, write `Assumptions: none`. Empty field is not allowed (per [PRINCIPLES.md §3](../../PRINCIPLES.md#3-assumptions--extraction-confidence--surface-uncertainty-dont-hide-it)).

### 5. Output

Write to:
- **Standalone**: `<contracts-root>/problem-statement-<slug>.md`
- **Within composer**: `<contracts-root>/frame-<slug>/problem-statement.md`

Format:

```markdown
# Problem Statement: <slug>

## Situation (one-paragraph distillation)
<a single paragraph capturing the situation in the user's voice or close to it>

## 1. Desired end state
<concrete, observable; "when this is done, what's true">

## 2. Current state
<what's actually true now; surface honest gaps from the user's framing>

## 3. Gap
<what's missing / broken / needed>

## 4. Deliverable shape
<artifact / decision / behavior change / knowledge / communication>

## 5. Verification criterion
<deterministic | judgment-bound | human-only — and the specific check>

## 6. Constraints
- Time: <cap or "open">
- Cost: <budget or "open">
- Quality floor: <minimum standard>
- Scope boundary: <what's in / out>
- Reversibility: <reversible | hard-to-reverse | irreversible>
- Dependencies: <other commitments / artifacts / approvals>
- Blast radius: <scope of impact>

## 7. Known vs unknown
- Known: <what we have evidence for>
- Unknown: <what we don't know>
- Blocking unknowns: <the unknowns that prevent moving forward, or "none">

## 8. User role
- Gates: <where the user wants explicit approval>
- Autonomy: <where the user wants the system to act without checking>

## 9. Stakes
<why this matters, what's at risk if we get it wrong>

## Extraction-confidence
<low | medium | high>

## Assumptions
<list per inferred field, or "none">

## Source frame artifacts (if produced via composer)
- composition.md
- probe.md (if ran)
- jtbd.md (if ran)
- lens.md (if ran)
- scope.md (if ran)
- stakes.md (if ran)
- reframe.md (if ran)

## Ready for hand-off
`/approach:composer <path-to-this-file>`
```

### 6. Stop

Skill is framing-only. Do not invoke `/approach:composer`. Do not start downstream work. The user (or the composer that wrapped this skill) decides next step.

## Anti-Patterns

- **Skipping a field with "TBD" or empty.** Every field is filled, even if low-confidence with Assumptions. "TBD" defers articulation to the reader, which is exactly what this skill is supposed to prevent.
- **Aspirational end state.** "Become a more strategic organization" is not a Polya end state. It's a wish. Concrete, observable: "Strategy doc circulated and signed off by leadership team by Q3."
- **Confusing user's framing with current state.** Field 2 is what's *actually* true, not what the user *believes* is true. Surface the gap if there is one.
- **Hiding inference.** If the framer inferred a verification criterion or stake from context, that goes in Assumptions, not silently in the body.
- **Inflating extraction-confidence.** "high" should be reserved for genuinely high-confidence extractions. Marking everything "high" defeats the marker.
- **Generic verification.** "It works" is not a verification criterion. Be specific about *how we'd know*.
- **Skipping the question-first discipline.** Each field answers a specific question. If the skill is producing structure without the user (or upstream artifacts) having genuinely answered the question, it's filling in templates, not articulating.

## Rules

- The skill outputs a single Problem Statement artifact (markdown file); it never executes downstream work.
- All 9 fields must be populated. No "TBD" placeholders.
- `Extraction-confidence` is non-optional (low | medium | high) per PRINCIPLES.md §3.
- `Assumptions` is non-optional; if empty, write `none`.
- When invoked from `frame:composer`, the artifact lives at `<contracts-root>/frame-<slug>/problem-statement.md`.
- When invoked standalone, the artifact lives at `<contracts-root>/problem-statement-<slug>.md`.
- The artifact MUST end with the `Ready for hand-off` line naming the path to feed `/approach:composer`.
- When a worker is a Claude sub-Agent (for extracting from upstream artifacts), the contract MUST specify `model: opus` per PRINCIPLES.md §2.
- If the problem is too unclear to articulate (multiple targets at extraction-confidence low and the user can't disambiguate), recommend `frame:probe` and stop.

## Key Files

- Output (standalone): `<contracts-root>/problem-statement-<slug>.md`
- Output (composer): `<contracts-root>/frame-<slug>/problem-statement.md`
- Sibling frame skills (all under `frame` plugin, all live): `frame:composer`, `frame:probe`, `frame:from-corpus`, `frame:jtbd`, `frame:lens`, `frame:scope`, `frame:stakes`, `frame:reframe`.
- Downstream consumer: `/approach:composer` — reads this artifact as input to its Phase 1 (problem extraction).
- Theoretical grounding: Polya, *How to Solve It* (1945) — the 9 targets are an extension of Polya's 4-step problem-solving heuristic with stakes/role/uncertainty axes added for modern AI-mediated work.
