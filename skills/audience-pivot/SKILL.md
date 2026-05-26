---
name: audience-pivot
description: Take one frame (or one Problem Statement) and translate it for N different audiences — board, team, client, public, juniors, regulators — producing a frame×audience matrix showing what each audience needs to hear, what gets foregrounded vs backgrounded per audience, and what's deliberately *not said* to each. ALWAYS invoke this skill when the same situation needs to land for multiple stakeholder groups, when public-launch comms span internal + external audiences, when a strategic decision needs to be communicated up + across + down with different emphasis, or when "how do I position this for X" recurs across multiple X's. Distinct from frame:reframe (which generates alternative frames of the situation itself) — audience-pivot keeps the frame, varies the audience. Skip when there's only one audience, when the audience set isn't enumerable yet (use frame:probe first), or when audiences would receive the same message (then audience-pivot adds friction without value).
# description-style: directive + negative constraint (Seleznov)
# rationale: Audience-pivot is the v0.2.0 "finish-frame" skill specifically for multi-audience communication situations. Distinct cognitive operation from reframe (one frame, N audiences) vs (N frames, situation). Directive style forces the explicit matrix output — common failure mode is producing one polished version + hand-waving "and adapt as needed" for the others.
---

<!--
ACTIVATION TESTS (for /skill:validate --bench):
1. SHOULD activate (multi-audience launch): "We're launching this internally — how should it land for the team, for leadership, for clients?"
2. SHOULD activate (consulting communication): "I need to write up these findings — board version, team version, client version"
3. SHOULD NOT activate (single audience): "What should I tell my client about the schedule slip?" — single audience, no pivot needed
4. SHOULD NOT activate (no frame yet): "I'm not sure how to think about this" — frame:probe or frame:composer first
5. BOUNDARY: "How do I talk about this engagement at the meetup?" (could be audience-pivot if multiple audiences involved, could be one-shot if just one talk to one audience — clarify audience count)
-->

# audience-pivot

Take one frame (or Problem Statement) and translate it for N distinct audiences. Output is a **frame×audience matrix** — same frame, different foreground / background / emphasis / omissions per audience. Particularly valuable for public-launch comms, strategic decisions that need to be communicated up + across + down, and consulting deliverables that span multiple stakeholder groups.

> See [PRINCIPLES.md](../../PRINCIPLES.md) for shared rules — particularly **§10 question-first**, **§7 knowledge-work primacy**.

## When to Use

- Same situation needs to land for multiple distinct audiences (board / team / client / public / juniors / regulators).
- Public-launch comms span internal + external audiences with different stakes / vocabularies / risk profiles.
- Strategic decision needs explicit communication up (board), across (peer departments), down (team) — each with different emphasis.
- Consulting deliverable that produces multiple artifacts from one analysis (executive summary + technical appendix + client-facing brief).
- Composer-invoked when intake reveals "this needs to land for multiple audiences."

## When NOT to Use

- Single audience — no pivot needed; just write the one thing.
- Audience set isn't enumerable yet — `frame:probe` or `frame:jtbd` first to surface who the audiences are.
- Audiences would receive identical messages — pivot adds friction without value (rare but possible for purely operational comms).
- The frame itself is unclear — `frame:reframe` or `frame:problem-statement` first; audience-pivot consumes a frame, doesn't generate one.

## Distinct from `frame:reframe`

| | `frame:reframe` | `frame:audience-pivot` |
|---|---|---|
| What varies | The *frame* (lateral alternatives to current framing) | The *audience* (same frame translated for different recipients) |
| Output | N alternative framings of the situation | 1 frame × N audience translations (a matrix) |
| Use when | Stuck in a frame, want to see other angles | Have a frame, need to communicate it to multiple groups |

The two compose: run `frame:reframe` first to pick the right frame, then `frame:audience-pivot` to translate the chosen frame across audiences.

## Process

The skill outputs an **Audience-Pivot artifact** — a matrix with one row per audience, columns for the per-audience foreground / background / vocabulary / omissions / first-line / stakes-as-they-see-it. Plus a cross-audience consistency check (the frame must remain *the same frame* — pivot translates, doesn't substitute).

Question-first per [PRINCIPLES.md §10](../../PRINCIPLES.md#10-question-first--new-to-frame): the skill leads with three questions —

- *"What is the frame being translated?"* (must be named explicitly)
- *"Who are the audiences?"* (enumerated; each is a distinct group with distinct stakes and vocabulary)
- *"What does each audience need to hear vs not hear?"* (the actual translation work)

### 1. Confirm the frame is well-formed

Audience-pivot consumes a frame. The frame must be at least loosely structured before pivoting:

- Acceptable inputs: a `frame:problem-statement.md` (Polya 9), a `frame:reframe.md` (with one alternative selected), a frame stated explicitly in the user's words.
- Not acceptable: "I'm thinking about X" with no structure — run `frame:probe` or `frame:reframe` first.

If the frame is unclear, recommend the upstream skill and stop.

### 2. Enumerate audiences

List the audiences this frame will be translated for. Typical audience sets:

- **Vertical**: leadership / direct-report / peer / cross-functional
- **Internal vs external**: team / executive / board / client / public / regulator
- **Stake gradient**: high-stake decision-makers / medium-stake influencers / low-stake informed parties
- **Domain-specific**: technical / business / legal / creative / financial

When in doubt, include rather than exclude. The matrix can have empty cells if some audiences don't actually need the translation — that's information.

For each audience, capture briefly:
- Who they are (role / archetype, not name)
- What they bring (prior knowledge, prior emotional state, prior commitments)
- What they decide / do with the message (downstream action)

### 3. Per-audience translation

For each audience, produce:

- **Foreground for this audience**: what gets prominent placement / time / emphasis
- **Background for this audience**: what's true but de-emphasized (not hidden, just not centered)
- **Omissions for this audience**: what's deliberately *not said* (and why — usually because it's noise for this audience, or premature, or would create friction without payoff)
- **Vocabulary shift**: what words this audience uses; what jargon to translate / drop / preserve
- **First-line / opening move**: what specifically lands first for this audience (their attention is most expensive in the first 10 seconds)
- **Stakes as this audience sees them**: how the situation's stakes look from their perspective (often different from how the framer sees them)
- **Pre-empted objections**: what this audience will likely push back on; whether to address up-front or leave for Q&A

### 4. Cross-audience consistency check

**Load-bearing.** The translations must remain *the same frame*. Audience-pivot is translation, not substitution. Common failure: in optimizing per-audience landing, the framer produces N *different* frames disguised as N translations of one. Check:

- Are the stakes claims consistent across audiences (the *facts* about what's at risk)? They should be. (The *emphasis* on stakes differs; the *facts* don't.)
- Are the proposed actions consistent? (The *justifications* differ; the *actions* don't.)
- Are there cross-audience contradictions that would break trust if audiences compared notes? (E.g., the board version says "we're cutting Z," the team version says "Z is safe.")

If contradictions emerge, you're really doing reframe-disguised-as-pivot. Stop, surface the contradiction, recommend `frame:reframe` to choose the actual frame, then re-run audience-pivot on the chosen frame.

### 5. Output

Write to:
- **Within composer**: `<contracts-root>/frame-<slug>/audience-pivot.md`
- **Standalone**: `<contracts-root>/audience-pivot-<slug>.md`

Format:

```markdown
# Audience-Pivot: <slug>

## Frame being translated
<one-paragraph distillation of the frame — should be the same frame across all rows below>

## Source
- <link to problem-statement.md / reframe.md / inline statement>

## Audiences

### Audience 1: <name + role/archetype>
- **Who they are**: <role, archetype>
- **What they bring**: <prior knowledge / emotional state / commitments>
- **What they decide / do with this**: <downstream action>
- **Foreground for this audience**: <what gets prominent placement>
- **Background for this audience**: <what's true but de-emphasized>
- **Omissions for this audience**: <what's deliberately not said + why>
- **Vocabulary shift**: <words to translate / drop / preserve>
- **First-line / opening move**: <what lands first>
- **Stakes as they see them**: <stakes from their perspective>
- **Pre-empted objections**: <push-back to address up-front>

### Audience 2: <name + role/archetype>
[same structure]

[repeat per audience; typically 3-5 audiences, max ~7]

## Cross-audience consistency check

- Stakes-facts consistent: <yes | no — flag what differs>
- Proposed-actions consistent: <yes | no — flag what differs>
- Cross-audience contradictions: <none | list>

If contradictions surfaced: this isn't audience-pivot, it's disguised reframe. Recommended next: `frame:reframe`.

## Implications for the Problem Statement
- <how audience-pivot results sharpen Polya 9 fields — typically the "Deliverable shape" + "Stakes" + "User role" fields>

## Extraction-confidence
<low | medium | high>

## Assumptions
- <inferred per-audience stakes or vocabularies>
- <or "none">
```

### 6. Stop

Skill is framing-only. Audience-pivot surfaces the translation matrix; the user (or downstream skills) produces the actual artifacts per audience.

## Anti-Patterns

- **Reframe disguised as pivot.** The translations are actually different frames, not translations of one frame. Caught by the cross-audience consistency check. If facts-of-stakes or proposed-actions diverge across audiences, that's reframe, not pivot.
- **Single polished version + hand-wave for the rest.** "Here's the board version; adapt as needed for the team." Defeats the point — the matrix exists because the adaptation work *is* the work. Each row gets explicit treatment.
- **Vocabulary substitution without genuine translation.** Replacing technical terms with marketing language but keeping the same logic / structure / weights doesn't actually translate — it dresses up. Real translation changes what gets foregrounded, not just the words.
- **No omissions section.** What's *not said* to each audience is as important as what is. An audience-pivot artifact with all "omissions: none" cells is suspicious — it usually means the framer hasn't thought about what would be noise for each audience.
- **Too many audiences.** 3-5 is the target; 7 is a soft cap. Beyond 7, the matrix becomes unmanageable and the per-audience translations get shallow. Group audiences that genuinely need the same message.
- **Skipping the source-frame reference.** The output must cite which frame is being translated. Without that, future readers can't audit the translation against the original.
- **Audience-pivot when there's no actual frame.** If "the frame being translated" section is hand-wavy, you're audience-pivoting on noise. Stop, run frame:probe or frame:reframe, return.

## Rules

- The skill outputs a single Audience-Pivot artifact (markdown file); it never produces the actual per-audience deliverables.
- Source frame must be referenced explicitly (link or inline citation).
- 3-7 audiences; outside this range needs justification.
- Cross-audience consistency check is mandatory. If contradictions surface, recommend `frame:reframe` and stop.
- Per-audience cells: all 8 fields populated (who they are, what they bring, downstream action, foreground, background, omissions, vocabulary, first-line, stakes-as-they-see-them, pre-empted objections). Empty fields must have explicit rationale.
- When invoked from `frame:composer`, artifact lives at `<contracts-root>/frame-<slug>/audience-pivot.md`.
- When invoked standalone, artifact lives at `<contracts-root>/audience-pivot-<slug>.md`.
- `Extraction-confidence` and `Assumptions` fields are non-optional per PRINCIPLES.md §3.
- Question-first (PRINCIPLES.md §10): the artifact structure follows from the three anchor questions (what frame, who audiences, what each needs).
- When a sub-Agent is the worker, the contract MUST specify `model: opus` per PRINCIPLES.md §2.

## Key Files

- Output (standalone): `<contracts-root>/audience-pivot-<slug>.md`
- Output (composer): `<contracts-root>/frame-<slug>/audience-pivot.md`
- Sibling frame skills (all under `frame` plugin, all live): `frame:composer`, `frame:probe`, `frame:problem-statement`, `frame:from-corpus`, `frame:jtbd`, `frame:lens`, `frame:scope`, `frame:stakes`, `frame:reframe`, `frame:devil`.
- Typical upstream: `frame:reframe` (pick a frame) → `frame:audience-pivot` (translate it for audiences). Or `frame:problem-statement` (canonical Polya 9) → `frame:audience-pivot`.
- Theoretical grounding: rhetorical-situation theory (Bitzer) — audience, exigence, constraints; political-comms / change-management practice — same message, multi-audience translation as a discipline distinct from message substitution.
