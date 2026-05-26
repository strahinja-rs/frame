---
name: devil
description: Adversarial pressure-test of a committed frame — what would have to be true for this frame to be wrong, what's the strongest case against it, where would a skilled opponent attack first. ALWAYS invoke this skill when the user has converged on a frame and stakes are high enough that one explicit challenge before action is worth the time, when the user wants a steelman of the opposite of their current position, or when frame-blindness risk is real (the user has rehearsed their own framing enough that they can no longer see its weaknesses). Distinct from frame:reframe (which generates alternatives) and frame:probe (which surfaces assumptions) — devil specifically attacks one committed frame. Terminates cleanly: one challenge round, accept the challenge or adopt the alternative — no infinite loop. Skip when the frame isn't actually committed (use frame:reframe to explore alternatives instead), when stakes are low (devil is overhead), or when the user is genuinely uncertain about the frame (probe first).
# description-style: directive + negative constraint (Seleznov)
# rationale: Devil is the v0.2.0 "finish-frame" skill specifically for adversarially pressure-testing a frame before commitment. Distinct cognitive operation from reframe (lateral alternatives) and probe (excavation of assumptions). Directive style forces explicit adversarial framing — common failure mode is producing a polite "things to consider" list instead of a real opponent's attack. Terminates cleanly to avoid procrastination loop.
---

<!--
ACTIVATION TESTS (for /skill:validate --bench):
1. SHOULD activate (high-stakes commitment): "I've decided to take the Žiška Phase 2 as embedded partnership. Before I commit, what's the strongest case I'm wrong?"
2. SHOULD activate (frame-blindness risk): "I've been thinking about this as a capacity decision for two weeks. Pressure-test that."
3. SHOULD NOT activate (low stakes): "Should I use this library or that one for a hobby project" — devil overhead exceeds the cost of being wrong
4. SHOULD NOT activate (frame not committed): "I'm not sure how to frame this" — frame:reframe or frame:probe first
5. BOUNDARY: "What could go wrong with this plan?" (could be devil on the frame, could be frame:stakes on the action — clarify: is the frame itself under challenge, or the cost/benefit of acting on a settled frame?)
-->

# devil

Adversarial pressure-test of a frame the user has committed to (or is about to commit to). Steelman the opposite, name what would have to be true for the frame to be wrong, surface where a skilled opponent would attack first. Terminates cleanly: one challenge round, then the user accepts the challenge (and adopts the alternative or revises) or rejects it (and commits more honestly).

> See [PRINCIPLES.md](../../PRINCIPLES.md) for shared rules — particularly **§10 question-first**, **§11 iteration-as-norm** (devil is the *bounded* iteration — one round, clean termination, *not* perpetual challenge).

## When to Use

- User has converged on a frame and stakes are high enough that one explicit challenge is worth the time.
- Frame-blindness risk is real — the user has rehearsed their own framing enough that they can no longer see its weaknesses.
- High-stakes irreversible action under the current frame is imminent (deploy, send, transmit, publish, commit).
- The frame is influential — others will follow it, so being wrong compounds.
- Composer-invoked when intake reveals high stakes + high certainty + commitment pending.

## When NOT to Use

- Frame isn't actually committed — use `frame:reframe` to explore alternatives, not devil to attack one.
- Stakes are low — devil overhead exceeds the cost of being wrong; just commit and iterate cheaply.
- User is genuinely uncertain about the frame — `frame:probe` first to surface what they're assuming; devil attacks committed frames, not nascent ones.
- The user wants a list of risks / things to consider — that's `frame:stakes`. Devil is adversarial, not cautious.

## Distinct from sibling skills

| | `frame:reframe` | `frame:probe` | `frame:stakes` | `frame:devil` |
|---|---|---|---|---|
| Cognitive move | lateral (alternatives) | excavation (assumptions) | symmetric (cost-of-error each direction) | adversarial (steelman the opposite) |
| Input | a frame to vary | a frame to explore | a decision under stakes | a *committed* frame to attack |
| Output | N alternative frames | surfaced assumptions / foreground / missing | cost-of-error analysis + asymmetry | the strongest case the frame is wrong + what would have to be true |
| Termination | when alternatives are surfaced | when assumptions are mapped | when asymmetry is named | one challenge round — accept or reject, don't iterate |

Devil is *bounded* by design. The point isn't to keep challenging until the frame collapses; it's to give the committed frame one honest opponent's attack and then move on. This prevents devil from becoming a procrastination shape (see PRINCIPLES.md §11 anti-pattern).

## Process

The skill outputs a **Devil artifact** — the strongest case against the committed frame, what would have to be true for it to be wrong, where a skilled opponent attacks, and the user's accept/reject decision (captured after they read it).

Question-first per [PRINCIPLES.md §10](../../PRINCIPLES.md#10-question-first--new-to-frame): the skill leads with three questions —

- *"What is the committed frame?"* (must be named explicitly — devil attacks a specific frame, not a vague situation)
- *"What would have to be true for this frame to be wrong?"* (the steelman question)
- *"Where would a skilled opponent attack first?"* (the surface question)

### 1. Name the committed frame

Devil consumes a committed frame. The frame must be specific:

- Acceptable inputs: a `frame:problem-statement.md` (Polya 9), a `frame:reframe.md` with one alternative explicitly selected, a frame stated explicitly in the user's words with conviction.
- Not acceptable: "I'm thinking about X" — that's not committed yet; run `frame:reframe` to surface alternatives or `frame:probe` to explore assumptions.

If the frame isn't named with conviction, recommend the upstream skill and stop.

### 2. Steelman the opposite

For the committed frame, generate the **strongest version of the opposite**:

- Not a strawman — a steelman. The opposite framed as a sophisticated opponent would frame it, not as the user would caricature it.
- Not "the other side has a point" — a complete, articulate, forceful alternative.
- Specific. If the committed frame is "this is a capacity decision," the opposite isn't "well, capacity is one consideration"; it's "this is an *identity* decision and treating it as capacity is the precise mistake that would cost the most."

### 3. Articulate "what would have to be true"

For the committed frame to be wrong, what conditions would have to hold? List them concretely:

- Hidden facts the framer doesn't have access to
- Assumptions in the frame that, if false, would invalidate it
- Future contingencies that, if they unfold, would reveal the frame was off
- Counterfactual scenarios where the committed frame produces the worst outcome

For each: rate plausibility (low / medium / high) and visibility-when-it-fires (would the framer know quickly, or would the mistake take months to surface?).

### 4. Identify the strongest attack vector

Where would a skilled opponent attack the committed frame first? The single most exposed point. Specifically:

- The assumption the framer is least examining
- The stakeholder whose perspective is most missing from the frame
- The time-horizon the frame treats as irrelevant
- The downside scenario the frame is implicitly betting won't happen

Name the attack vector explicitly. One vector, named with precision, beats five gentle observations.

### 5. The user's accept/reject (captured)

After producing the artifact, the user reads it and decides:

- **Accept the challenge** — the frame was wrong (or wrong in a load-bearing way). Adopt the steelman, or revise the frame to address what devil surfaced.
- **Reject the challenge** — the frame survives the attack. The committed frame holds; the user now commits with full visibility of what they're betting against.
- **Partial accept** — devil surfaced something real that doesn't invalidate the frame but should be added as a *known risk* in the Problem Statement's Stakes / Assumptions fields.

The artifact has a section for capturing which choice the user made + their reasoning. This is the close — devil does not iterate further.

### 6. Output

Write to:
- **Within composer**: `<contracts-root>/frame-<slug>/devil.md`
- **Standalone**: `<contracts-root>/devil-<slug>.md`

Format:

```markdown
# Devil: <slug>

## Committed frame
<one-paragraph statement of the frame being attacked — must be the frame the user has converged on>

## Source
- <link to problem-statement.md / reframe.md / inline statement, ideally with timestamp / context for when the user committed>

## Steelman of the opposite
<the strongest version of the opposite frame — a sophisticated opponent's framing, not a caricature>

## What would have to be true for the committed frame to be wrong

| # | Condition | Plausibility | Visibility-when-it-fires |
|---|---|---|---|
| 1 | <hidden fact, false assumption, future contingency> | <low / medium / high> | <quick to surface / takes months / never surfaces explicitly> |
| 2 | ... | ... | ... |

## Strongest attack vector
<the single most exposed point — the assumption least examined, the stakeholder most missing, the time-horizon treated as irrelevant, the downside scenario being bet against. Named with precision.>

## User decision (captured after reading)
- Choice: <accept | reject | partial accept>
- Reasoning: <user's stated reasoning>
- If accept: <how the frame revises, or which steelman element gets adopted>
- If reject: <the user's commitment is now informed — list what they're explicitly betting against>
- If partial: <which known risks get added to the Problem Statement's Stakes / Assumptions fields>

## Implications for the Problem Statement
- <how devil's output should update Polya 9 — typically Stakes (#9) and Assumptions section, sometimes Constraints (#6) reversibility>

## Extraction-confidence
<low | medium | high>

## Assumptions
- <inferences during devil's generation>
- <or "none">
```

### 7. Stop

Skill terminates after one challenge round. **No iteration.** If the user wants more frame work, they go back to `frame:reframe` (alternatives) or `frame:probe` (deeper assumptions). Devil is a single explicit pressure-test before commitment; it is not a perpetual challenge.

## Anti-Patterns

- **Strawman instead of steelman.** Generating a caricature of the opposite that's easy to dismiss. The point of devil is to produce the *strongest* challenge, not the easiest. If the steelman feels easy to reject, you've strawmanned.
- **Polite "things to consider" list.** Devil is adversarial — the artifact reads like a skilled opponent's attack, not a balanced overview of considerations. "Things to consider" is `frame:stakes`. Devil names the attack.
- **Multiple iterations of devil on the same frame.** Devil is one round. If the user wants more challenge, they go to `frame:reframe` (alternatives) or `frame:probe` (assumptions). Iterating devil is procrastination disguised as rigor.
- **Devil on a non-committed frame.** Attacking a frame the user hasn't actually committed to is empty exercise — they'll just abandon it because it was never load-bearing. Devil requires conviction to push against. If the user is uncertain, run reframe or probe first.
- **No "what would have to be true" specifics.** Vague challenges ("this might be wrong") aren't useful. Specific conditions with plausibility + visibility ratings are.
- **No attack-vector named.** A devil artifact without a named single most-exposed point is too diffuse. The point is to surface the one thing a skilled opponent would attack first.
- **No user-decision capture.** The artifact's value compounds when the user records their accept/reject decision and reasoning. Empty user-decision section means devil ran but the close didn't happen — incomplete.

## Rules

- The skill outputs a single Devil artifact (markdown file); it never executes downstream action.
- One challenge round only — no iteration. Subsequent challenges are different skills (reframe, probe).
- Committed frame must be referenced explicitly (link or inline citation with conviction-signal).
- Steelman must be specific and forceful — caricatures rejected.
- "What would have to be true" entries must include plausibility + visibility-when-it-fires.
- Strongest attack vector is mandatory — one named point, not a list.
- User-decision section is part of the artifact; it gets populated after the user reads the rest. (If user hasn't decided yet, mark as pending.)
- When invoked from `frame:composer`, artifact lives at `<contracts-root>/frame-<slug>/devil.md`.
- When invoked standalone, artifact lives at `<contracts-root>/devil-<slug>.md`.
- `Extraction-confidence` and `Assumptions` fields are non-optional per PRINCIPLES.md §3.
- Question-first (PRINCIPLES.md §10): the artifact structure follows from the three anchor questions.
- When a sub-Agent is the worker, the contract MUST specify `model: opus` per PRINCIPLES.md §2.

## Key Files

- Output (standalone): `<contracts-root>/devil-<slug>.md`
- Output (composer): `<contracts-root>/frame-<slug>/devil.md`
- Sibling frame skills (all under `frame` plugin, all live): `frame:composer`, `frame:probe`, `frame:problem-statement`, `frame:from-corpus`, `frame:jtbd`, `frame:lens`, `frame:scope`, `frame:stakes`, `frame:reframe`, `frame:audience-pivot`.
- Typical upstream: `frame:reframe` (generated alternatives, user picked one) → `frame:devil` (pressure-test the chosen frame before commitment). Or `frame:problem-statement` (canonical Polya 9 written with conviction) → `frame:devil` (one challenge before handoff to /approach).
- Composes well with: `frame:stakes` (devil attacks the frame; stakes maps cost-of-error within the frame — they're complementary on high-stakes decisions).
- Theoretical grounding: pre-mortem methodology (Klein); red-team review practice; Karl Popper on falsifiability — "what would have to be true for this to be wrong" is the operational form of the falsifiability question.
