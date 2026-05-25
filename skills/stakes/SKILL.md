---
name: stakes
description: Articulate cost-of-error in each direction — what's at risk if we do this, what's at risk if we don't, what's reversible and what isn't, and the opportunity cost in each direction. ALWAYS invoke this skill when the situation has high or asymmetric stakes — irreversible action, identity-touching decision, significant resource commitment, reputation impact — or when the user is paralyzed because both options feel costly. Produces a Stakes artifact — cost-of-error analysis for action and inaction, reversibility model, opportunity-cost analysis, and the asymmetry diagnosis (where one side's cost compounds while the other can be recovered). Skip when stakes are genuinely low, when the decision is easily reversible, or when the user has already analyzed stakes elsewhere.
# description-style: directive + negative constraint (Seleznov)
# rationale: Stakes articulation is one of the most-skipped frame operations. Users typically articulate the upside of action ("if I do this, X happens") while implicitly understating the cost of inaction ("if I don't, nothing changes"). Directive style forces symmetric articulation. The asymmetry diagnosis is the most valuable output — it tells the user *which side of the decision compounds* if wrong.
---

<!--
ACTIVATION TESTS (for /skill:validate --bench):
1. SHOULD activate (high stakes, paralysis): "I'm trying to decide whether to take this 6-month client engagement — both paths feel costly"
2. SHOULD activate (irreversible action): "Should I publicly position myself as an AI-transformation specialist? Once I'm known for it I can't really walk back."
3. SHOULD NOT activate (low stakes): "Should I use this library or that one for a hobby project"
4. BOUNDARY: "Help me think about whether to take this job" (could be stakes alone, could be composer with stakes + reframe + jtbd)
-->

# stakes

Cost-of-error articulation — symmetric. What's at risk if we *do* this, what's at risk if we *don't*. Reversibility, opportunity cost, and the asymmetry diagnosis: which side of the decision compounds if wrong.

> See [PRINCIPLES.md](../../PRINCIPLES.md) for shared rules — particularly **§10 question-first**.

## When to Use

- High-stakes decision (resource commitment, reputation, identity, irreversibility).
- Asymmetric situation where the user has thought about one side (usually the upside of action) but not the other (usually the cost of inaction).
- User is paralyzed because both options feel costly.
- Irreversible or hard-to-reverse action under consideration.
- Composer-invoked when intake reveals high or asymmetric stakes.

## When NOT to Use

- Stakes are genuinely low and the cost of analysis exceeds the cost of getting the decision wrong.
- The decision is easily reversible — articulating stakes adds friction without payoff.
- The user has already analyzed stakes carefully and is presenting the analysis — capture in `frame:problem-statement` (field 9), don't redo.

## Process

The skill outputs a **Stakes artifact** — four sections: cost-of-action analysis, cost-of-inaction analysis, reversibility model, opportunity-cost analysis, plus the asymmetry diagnosis.

Question-first per [PRINCIPLES.md §10](../../PRINCIPLES.md#10-question-first--new-to-frame): the skill leads with four pairs of questions.

### 1. Cost-of-action analysis

*"If we do this, what's at risk?"*

For each risk:
- What goes wrong (specific scenario, not "things fail")
- Probability (qualitative — high / medium / low / unknown)
- Severity if it happens (qualitative — recoverable / costly / catastrophic)
- Domain of impact (financial, reputational, relational, identity, time, technical, legal)

### 2. Cost-of-inaction analysis

*"If we DON'T do this, what's at risk?"*

This is the asymmetric move most users skip. Cost of inaction includes:
- Opportunity cost (what we couldn't do because we didn't do this)
- Position drift (what naturally happens if we don't act — markets move, relationships fade, alternatives close)
- Identity cost (what it means for us to be the kind of person/agency who didn't do this)
- Compound cost (the longer we don't act, the more expensive acting later becomes)

Apply the same per-risk schema: scenario, probability, severity, domain.

### 3. Reversibility model

For each action under consideration:
- **Reversible**: cleanly undone if it goes wrong, no residual cost
- **Hard-to-reverse**: undoable but at meaningful cost (storno, public retraction, financial loss)
- **Irreversible**: cannot be undone in any practical sense

For hard-to-reverse and irreversible actions, document the *reversal path* (if any) and its cost.

### 4. Opportunity-cost analysis

*"What's the opportunity cost of each direction?"*

For each path (act / not act, or for each alternative if there are multiple options):
- What we *can't do* if we choose this path (because resources / attention / commitments are taken)
- What we *make possible* by choosing this path (downstream options opened)

Opportunity cost is bidirectional — every choice forecloses some options and opens others.

### 5. Asymmetry diagnosis

The most valuable output. Looking across the four prior sections, identify:

- **Which direction's costs compound** (over time, with delay, with public visibility)?
- **Which direction's costs are recoverable** (even if painful)?
- **Which direction's costs are asymmetric** in domain (e.g., one direction risks identity, the other risks money — those don't trade off cleanly)?
- **What's the worst credible scenario** in each direction, and how recoverable is it?

The asymmetry diagnosis gives the user a *direction* (not a decision — that's still theirs) on which side they should be more cautious about, given that one side's cost is harder to recover.

### 6. Output

Write to:
- **Within composer**: `<contracts-root>/frame-<slug>/stakes.md`
- **Standalone**: `<contracts-root>/stakes-<slug>.md`

Format:

```markdown
# Stakes: <slug>

## Decision under consideration
<one-sentence summary of the choice>

## Questions this artifact answers
- If we do this, what's at risk?
- If we DON'T do this, what's at risk?
- What's reversible? What isn't?
- What's the opportunity cost in each direction?
- Which direction's costs compound if wrong — which is recoverable?

## Cost of action ("if we do this")

| # | Scenario | Probability | Severity | Domain |
|---|---|---|---|---|
| 1 | <specific scenario> | <high/med/low/?> | <recoverable/costly/catastrophic> | <financial/reputational/relational/identity/time/technical/legal> |
| 2 | ... | ... | ... | ... |

## Cost of inaction ("if we DON'T do this")

| # | Scenario | Probability | Severity | Domain |
|---|---|---|---|---|
| 1 | <opportunity cost: ...> | ... | ... | ... |
| 2 | <position drift: ...> | ... | ... | ... |
| 3 | <identity cost: ...> | ... | ... | ... |
| 4 | <compound cost: ...> | ... | ... | ... |

## Reversibility

### Action 1: <name>
- Reversibility: <reversible | hard-to-reverse | irreversible>
- Reversal path (if any): <description>
- Reversal cost: <what it costs to undo>

[repeat per action under consideration]

## Opportunity cost

### Path A: <act>
- Forecloses: <what we can't do if we take this path>
- Opens: <what downstream options become possible>

### Path B: <not act>
- Forecloses: <what we can't do if we don't act>
- Opens: <what downstream options become possible>

[repeat per path if more than 2]

## Asymmetry diagnosis

- Which direction's costs compound: <action | inaction | neither | both — and why>
- Which direction is recoverable if wrong: <action | inaction | neither>
- Cross-domain asymmetry: <are the risks in different domains, making them hard to compare cleanly?>
- Worst credible scenario in each direction: <action scenario + recoverability> / <inaction scenario + recoverability>
- **Headline diagnosis**: <one-sentence read on which side the user should weight more cautiously, with rationale>

## Implications for the Problem Statement
- <how stakes affect Polya 9 fields, especially #9 Stakes and #6 Constraints (reversibility)>

## Extraction-confidence
<low | medium | high>

## Assumptions
<inferences during stakes articulation, or "none">
```

### 7. Stop

Skill is framing-only. The Stakes artifact informs the decision; it does not make the decision. The user decides; this skill makes the decision more honest.

## Anti-Patterns

- **Cost-of-action only.** Skipping cost-of-inaction is the most common skip and the most expensive one. Symmetric articulation is the discipline.
- **Generic stakes ("things could go wrong").** Each scenario must be specific enough to be falsifiable.
- **Probability without uncertainty marker.** "Low probability" without "or unknown" hides what the framer actually knows. Mark `?` when probability is genuinely unknown.
- **Severity without domain.** "High severity" is meaningless without saying high in WHAT (financial, reputational, identity, etc.).
- **Reversibility hand-waved.** "Mostly reversible" or "kind of irreversible" — neither lands. Pick the category and document the reversal path explicitly.
- **No asymmetry diagnosis.** The four data sections are means; the asymmetry diagnosis is the end. Skipping it makes the artifact a data dump rather than an analysis.
- **Asymmetry diagnosis as recommendation.** The diagnosis names *which side compounds if wrong*; it doesn't say what the user should do. Confusing the two crosses into approach-space, which violates frame-only.
- **Confusing low-probability-high-severity with "unlikely so safe."** Black-swan risks compound exactly because their probability is hard to estimate; mark `?` and treat high severity as a high-attention item regardless.

## Rules

- The skill outputs a single Stakes artifact (markdown file); never executes, never decides.
- All five sections (cost-of-action, cost-of-inaction, reversibility, opportunity-cost, asymmetry diagnosis) are populated.
- Asymmetry diagnosis must include the "headline diagnosis" one-sentence read.
- Probability marked as `?` when genuinely unknown — don't pretend to know.
- Severity must include domain.
- Reversibility must include reversal path (or "none" with explanation) for hard-to-reverse and irreversible actions.
- When invoked from `frame:composer`, artifact lives at `<contracts-root>/frame-<slug>/stakes.md`.
- When invoked standalone, artifact lives at `<contracts-root>/stakes-<slug>.md`.
- `Extraction-confidence` and `Assumptions` fields are non-optional per PRINCIPLES.md §3.
- Question-first (PRINCIPLES.md §10): the artifact structure follows from the symmetric pair of questions.
- When a sub-Agent is the worker, the contract MUST specify `model: opus` per PRINCIPLES.md §2.

## Key Files

- Output (standalone): `<contracts-root>/stakes-<slug>.md`
- Output (composer): `<contracts-root>/frame-<slug>/stakes.md`
- Sibling frame skills (all under `frame` plugin, all live): `frame:composer`, `frame:probe`, `frame:problem-statement`, `frame:from-corpus`, `frame:jtbd`, `frame:lens`, `frame:scope`, `frame:reframe`.
- Theoretical grounding: Bezos's "one-way door vs two-way door" reversibility heuristic; Taleb on asymmetric risk; standard decision-theory literature on cost-of-action vs cost-of-inaction symmetry.
