---
name: jtbd
description: Articulate a situation as Jobs-to-be-Done statements, one per stakeholder. ALWAYS invoke this skill when the situation involves multiple stakeholders with potentially different goals — clients, team members, founders, partners, users, family — or when a decision affects parties whose perspectives aren't all in the room. Produces a JTBD artifact — per-stakeholder statements in the form "When [situation], I want to [motivation], so I can [outcome]" plus structural-tension flags where JTBDs conflict. Skip when the situation has a single stakeholder, when the user has already done the JTBD work, or when the situation isn't progress-shaped (some problems aren't well-served by JTBD framing — use a different lens or skip this skill).
# description-style: directive + negative constraint (Seleznov)
# rationale: JTBD is a recognized articulation pattern (Christensen, Ulwick) for surfacing the underlying progress different parties are seeking. Directive style forces explicit per-stakeholder statements rather than collapsing multi-stakeholder dynamics into a single "what the user wants." The structural-tension flagging is the part most JTBD treatments skip but is the most valuable output for frame work.
---

<!--
ACTIVATION TESTS (for /skill:validate --bench):
1. SHOULD activate (multi-stakeholder consulting): "I'm consulting for an agency where the founder, the COO, and the team all want different things from this transformation"
2. SHOULD activate (product / service decision): "Customer A wants X, customer B wants Y, my team wants Z"
3. SHOULD NOT activate (single stakeholder): "What's the right tool for ME to use for this task" (no others involved)
4. BOUNDARY: "Help me figure out what my client actually needs" (could be JTBD if multi-stakeholder at the client, or just probe if single contact)
-->

# jtbd

Articulates a situation as **Jobs-to-be-Done statements**, one per stakeholder. The JTBD pattern (Christensen, Ulwick) surfaces *what progress each party is trying to make* — separated by functional, emotional, and social dimensions when relevant. Particularly valuable for multi-stakeholder situations where stakeholders have potentially conflicting goals that the user's framing has collapsed.

> See [PRINCIPLES.md](../../PRINCIPLES.md) for shared rules — particularly **§10 question-first**, **§7 knowledge-work primacy**.

## When to Use

- Multi-stakeholder situation (clients, team, partners, users, family, etc.).
- The user's framing has implicitly foregrounded one stakeholder; surfacing others' JTBDs reveals structural tensions.
- Consulting / agency / product / service work where different parties (client decision-maker, end user, team executor) have different jobs.
- Decision that affects parties whose perspectives aren't all in the room.
- Composer-invoked when intake reveals multi-stakeholder dynamics.

## When NOT to Use

- Single-stakeholder situation — the JTBD frame doesn't add much value over `frame:problem-statement`.
- User has already done the JTBD work and is presenting one — capture in `frame:problem-statement`, don't redo.
- Situation isn't progress-shaped — some problems (e.g., aesthetic / craft decisions, philosophical questions) don't map cleanly to "what job is being hired." Use a different lens or skip.
- High-stakes decision where the JTBD pattern would prematurely constrain articulation — sometimes a more open `frame:probe` is better first.

## Process

The skill outputs a **JTBD artifact** — per-stakeholder JTBD statements in canonical form, plus structural-tension flags where multiple JTBDs conflict.

Question-first per [PRINCIPLES.md §10](../../PRINCIPLES.md#10-question-first--new-to-frame): the skill is built around the questions "who is involved?" and "what progress is each one trying to make?"

### 1. Enumerate stakeholders

Generate a complete list of stakeholders the situation involves, including parties not necessarily in the user's framing:

- Decision-makers (who signs off)
- Implementers (who does the work)
- End users (who consumes the output)
- Funders / sponsors
- Approvers / regulators
- Affected parties (family, partners, downstream consumers)
- The user themselves (always include)

When in doubt, include rather than exclude — the structural-tension analysis (step 4) will identify which JTBDs matter most.

### 2. Per-stakeholder JTBD statement

For each stakeholder, articulate one (or sometimes more) JTBD statement in canonical form:

> **When [situation / triggering circumstance], I want to [motivation / desired progress], so I can [outcome / why it matters].**

Examples:
- *"When my client asks me to lead their AI transformation, I want to feel like I'm building something durable, so I can stay in this work for years without burning out."*
- *"When my partner asks how the next 6 months look, I want to be able to describe a sustainable rhythm, so I can keep the relationship healthy."*
- *"When the client's executive team reviews progress, they want to see concrete operational change, so they can justify the budget internally."*

Each JTBD should:
- Be **specific** (not "I want this to go well")
- Be **stakeholder-true** (in the stakeholder's voice, not the user's projection)
- Surface **functional + emotional + social** dimensions when present (Ulwick's three-axis split)

Where the user's framing genuinely lacks information about a stakeholder's JTBD, mark it `[inferred]` and add to Assumptions.

### 3. Identify structural tensions

Compare JTBDs pairwise. Mark places where two or more JTBDs **conflict structurally**:

- Stakeholder A wants speed; Stakeholder B wants thoroughness → tension
- Stakeholder A wants visibility; Stakeholder B wants quiet execution → tension
- Stakeholder A wants the work; Stakeholder B wants the *credit* for the work → tension

Structural tension is the most valuable output of this skill. It's the thing the user's framing typically collapsed — and naming it is what changes the downstream approach selection.

For each tension, state:
- Which JTBDs conflict
- What the conflict is about (substance / timing / visibility / authority / etc.)
- Whether it's a hard conflict (one JTBD's satisfaction precludes the other) or a soft conflict (both can be satisfied with care)

### 4. Output

Write to:
- **Within composer**: `<contracts-root>/frame-<slug>/jtbd.md`
- **Standalone**: `<contracts-root>/jtbd-<slug>.md`

Format:

```markdown
# JTBD: <slug>

## Questions this artifact answers
- Who is involved in this situation (every stakeholder, including the ones not in the room)?
- What progress is each one trying to make — what job are they hiring this work to do?
- Where do JTBDs conflict structurally?

## Stakeholders
1. <stakeholder name + role>
2. <stakeholder name + role>
...

## Jobs-to-be-Done

### <Stakeholder 1>
- **When**: <triggering circumstance>
- **I want to**: <motivation / progress sought>
- **So I can**: <outcome / why it matters>
- Dimensions: functional / emotional / social (mark which are present)
- Source: <stated by user | inferred from context | inferred [low-confidence]>

### <Stakeholder 2>
[same structure]

...

## Structural tensions

### Tension 1: <name>
- JTBDs involved: <stakeholder A's JTBD> vs <stakeholder B's JTBD>
- About: <substance / timing / visibility / authority / resources>
- Type: <hard conflict | soft conflict>
- Implication for framing: <what this surfaces about the problem>

### Tension 2: <name>
[same structure]

## Implications for the Problem Statement
- <how the multi-stakeholder JTBDs should shape end state, verification, constraints, stakes>

## Extraction-confidence
<low | medium | high>

## Assumptions
- <inferred JTBDs or stakeholder roles>
- <or "none" if all sourced>
```

### 5. Stop

Skill is framing-only. Do not resolve the tensions. Do not pick a side. Do not invoke `/approach`. The artifact surfaces the multi-stakeholder structure; the resolution (or the choice to live with the tension) is downstream work.

## Anti-Patterns

- **Collapsing multi-stakeholder dynamics into a single user JTBD.** The whole point of this skill is to surface that the user's framing flattened multi-party dynamics.
- **JTBDs in the user's voice instead of the stakeholder's voice.** "What I think my client wants" is the user's framing of the client's JTBD; the JTBD itself should be in the *client's* voice (with `[inferred]` mark when the user is projecting).
- **Generic JTBDs ("I want this to succeed").** Useless. Each JTBD should be specific enough to be falsifiable.
- **Skipping structural tensions.** If multiple JTBDs are surfaced, *some* will tension. Identifying them is the highest-value output. A JTBD artifact with no tensions identified is either a single-stakeholder situation (wrong skill) or skipped the analysis.
- **Three-axis (functional/emotional/social) treated as required for every JTBD.** Some are purely functional; some primarily emotional. Mark which dimensions are present; don't manufacture dimensions to fill the template.
- **Inferred JTBDs not marked.** When the framer is guessing what a stakeholder wants (because the user didn't say), mark `[inferred]` and add to Assumptions.

## Rules

- The skill outputs a single JTBD artifact (markdown file); it never resolves tensions or invokes downstream skills.
- Every stakeholder gets at least one JTBD; complex stakeholders (the user, the client decision-maker) may have multiple.
- Structural tensions are mandatory when 2+ stakeholders are present. "No tensions identified" requires explicit justification.
- When invoked from `frame:composer`, the artifact lives at `<contracts-root>/frame-<slug>/jtbd.md`.
- When invoked standalone, the artifact lives at `<contracts-root>/jtbd-<slug>.md`.
- `Extraction-confidence` and `Assumptions` fields are non-optional per PRINCIPLES.md §3.
- Inferred JTBDs (where the user didn't state the stakeholder's perspective) are marked `[inferred]` and listed in Assumptions.
- Question-first (PRINCIPLES.md §10): the artifact's structure follows from "who is involved? what progress is each one trying to make?", not from a template.
- When a sub-Agent is the worker, the contract MUST specify `model: opus` per PRINCIPLES.md §2.

## Key Files

- Output (standalone): `<contracts-root>/jtbd-<slug>.md`
- Output (composer): `<contracts-root>/frame-<slug>/jtbd.md`
- Sibling frame skills (all under `frame` plugin, all live): `frame:composer`, `frame:probe`, `frame:problem-statement`, `frame:from-corpus`, `frame:lens`, `frame:scope`, `frame:stakes`, `frame:reframe`.
- Theoretical grounding: Christensen, *The Innovator's Solution* (2003); Ulwick, *Jobs to be Done: Theory to Practice* (2016). Three-axis (functional/emotional/social) framing from Ulwick's outcome-driven innovation literature.
