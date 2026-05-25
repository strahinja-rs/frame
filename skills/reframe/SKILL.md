---
name: reframe
description: Generate N alternative framings of the same situation — same facts, different foreground/background, different implications. ALWAYS invoke this skill when the user has one framing in hand and stakes are high enough that exploring alternatives is worth the time, when the user is stuck in a frame that feels off but they can't see past, or when a decision under uncertainty deserves multiple framings before commitment. Produces a Reframe artifact — current frame named, N alternative frames generated (typically 3-5), foreground/background diff per alternative, and the structural-tension surfacing (which alternative reveals what the current frame has been suppressing). Skip when the user has already considered alternatives, when single-frame articulation is sufficient (frame:problem-statement), or when reframing would be premature (probe first to clarify what the current frame even is).
# description-style: directive + negative constraint (Seleznov)
# rationale: Reframing is the third layer of frame work. Without it, framing is one-shot — the user commits to whatever frame they walked in with. Directive style forces alternative generation. The foreground/background diff is the deepest output: alternatives that don't diff from the current frame in their emphasis aren't really alternatives.
---

<!--
ACTIVATION TESTS (for /skill:validate --bench):
1. SHOULD activate (stuck in frame): "I keep thinking about this as a capacity problem but something feels off"
2. SHOULD activate (high-stakes decision): "Before I commit, what are other ways I could be seeing this?"
3. SHOULD NOT activate (single-frame sufficient): "Capture this problem in the canonical format" → frame:problem-statement
4. SHOULD NOT activate (no current frame to reframe): "Help me understand what's going on" → frame:probe first
5. BOUNDARY: "What are the different ways to think about this?" (could be reframe, could be frame:dialogue, could be brainstorming via approach:search — composer should disambiguate)
-->

# reframe

Generates N alternative framings of the same situation. Same facts, different foreground/background, different implications. The user keeps whichever frame is most useful (sometimes the original, sometimes an alternative, sometimes a synthesis).

> See [PRINCIPLES.md](../../PRINCIPLES.md) for shared rules — particularly **§10 question-first**, **§11 iteration-as-norm**, **§4 Direct-Use Rule**.

## When to Use

- User has one framing of a situation and wants alternatives before committing.
- High-stakes decision where exploring alternative framings is worth the time.
- User feels "stuck in a frame" — sense that the current framing is off but they can't see past it.
- Composer-invoked when intake reveals high-stakes decision under uncertainty, or when probe surfaces a strong implicit foreground that warrants exploring alternatives.

## When NOT to Use

- User has already considered alternatives and is presenting their chosen frame — capture in `frame:problem-statement`, don't redo.
- Single-frame articulation is sufficient (the situation is genuinely well-suited to one framing) → `frame:problem-statement`.
- No current frame exists to reframe — `frame:probe` first to surface what the implicit frame is.
- Time pressure makes reframing impractical and a single high-confidence frame is fine.

## Process

The skill outputs a **Reframe artifact** with the current frame named, N alternative frames generated, foreground/background diff per alternative, and structural-tension surfacing.

Question-first per [PRINCIPLES.md §10](../../PRINCIPLES.md#10-question-first--new-to-frame): the skill leads with two questions —

- *"What is the current frame foregrounding?"* (must be named explicitly before alternatives can be meaningful)
- *"What else could this be?"*

### 1. Name the current frame

Before generating alternatives, the skill names the current frame explicitly:

- What's foregrounded
- What's backgrounded
- What's implicit (assumptions made without naming them)
- What kind of problem the current frame says this is (decision / optimization / discovery / crisis / opportunity / etc.)

If the current frame is unclear or implicit, this step might surface that `frame:probe` should run first.

### 2. Generate alternative frames

Generate 3-5 alternative framings. Each alternative should be:

- **Same facts** (the situation hasn't changed)
- **Different foreground** (something the current frame backgrounded becomes central)
- **Different implications** (different approaches, stakes, stakeholders become primary)

Alternative-generation strategies:

- **Dimension shift**: if current frame foregrounds capacity, alternative foregrounds identity. If current foregrounds money, alternative foregrounds relationship. If current foregrounds technical, alternative foregrounds organizational.
- **Stakeholder pivot**: if current frame is from one stakeholder's perspective, alternative is from another's.
- **Time-horizon shift**: if current frame is short-term, alternative is 3-year. If current is long-term, alternative is "what would I do this week."
- **Problem-type shift**: if current frame is "decision," alternative is "discovery." If "optimization," try "design." If "crisis," try "opportunity."
- **Reversal**: what if the opposite were true? What if the obvious answer were wrong?
- **Archetype match**: which other situations does this resemble structurally? Borrow their framing.

Don't aim for many shallow alternatives — aim for 3-5 substantively different ones.

### 3. Per-alternative foreground/background diff

For each alternative, explicitly state:

- What this alternative **foregrounds** that the current frame backgrounds
- What this alternative **backgrounds** that the current frame foregrounds
- What **new approaches** become natural under this frame (without crossing into approach-space — just naming the shifts in available approaches)
- What **new stakes** become visible
- What this alternative **shares** with the current frame (acknowledge continuity, not just difference)

### 4. Structural-tension surfacing

Looking across the current frame plus alternatives, identify:

- **Which alternative reveals what the current frame has been most actively suppressing**?
- **Are there structural tensions between alternatives** that suggest no single frame is sufficient — a synthesis is needed?
- **Which alternatives are mutually incompatible** vs **complementary**?

This is the meta-analysis layer. Alternatives are means; the structural read is the end.

### 5. Recommended next

The skill recommends what to do with the reframe artifact:

- **Adopt an alternative**: if one alternative is more accurate, surface that.
- **Synthesize**: if multiple alternatives each capture something true, recommend a synthesis (the user crafts; this skill points).
- **Keep the original, with awareness**: sometimes the original frame is right but the user benefits from knowing what it's backgrounding.
- **Re-probe**: if alternatives surface assumptions worth checking, recommend a `frame:probe` iteration.
- **Proceed to articulation**: if reframing has produced clarity, the user is ready for `frame:problem-statement`.

### 6. Output

Write to:
- **Within composer**: `<contracts-root>/frame-<slug>/reframe.md`
- **Standalone**: `<contracts-root>/reframe-<slug>.md`

Format:

```markdown
# Reframe: <slug>

## Situation
<one-paragraph distillation>

## Current frame
- Foregrounded: <what the current frame emphasizes>
- Backgrounded: <what the current frame de-emphasizes>
- Implicit: <unstated assumptions>
- Problem type: <decision | optimization | discovery | crisis | opportunity | design | other>

## Alternative frames

### Alternative 1: <name>
- Foregrounds: <what becomes central>
- Backgrounds: <what becomes peripheral>
- New approaches become natural: <without crossing into approach-space, just naming the shift>
- New stakes become visible: <what's at risk under this frame that wasn't visible under the current>
- Shares with current frame: <continuity>
- Generation strategy: <dimension shift | stakeholder pivot | time-horizon shift | problem-type shift | reversal | archetype match>

### Alternative 2: <name>
[same structure]

### Alternative 3: <name>
[same structure]

[up to 5 alternatives total]

## Structural-tension surfacing

- Which alternative reveals what the current frame has been most suppressing: <name + what's surfaced>
- Mutually incompatible alternatives: <pairs that can't coexist as the operating frame>
- Complementary alternatives: <pairs that suggest synthesis>
- Headline tension: <one-sentence read on the deepest structural choice the reframing exposes>

## Recommended next
- <adopt alternative X | synthesize X+Y | keep original with awareness of backgrounded Z | re-probe | proceed to frame:problem-statement>

## Implications for the Problem Statement
- <how reframing should shape Polya 9 fields if the user adopts an alternative or synthesizes>

## Extraction-confidence
<low | medium | high>

## Assumptions
<inferences during reframe generation, or "none">
```

### 7. Stop

Skill is framing-only. Reframe surfaces alternatives and structural choices; the user decides which frame to adopt (or to synthesize). The skill does not pick.

## Anti-Patterns

- **Alternatives that are just restatements of the current frame.** If an "alternative" doesn't diff from the current frame in foreground/background, it isn't really an alternative. Force a real shift.
- **Many shallow alternatives.** 5 alternatives each getting one line is decorative. 3-5 substantively different alternatives is the target.
- **Skipping the current-frame naming step.** Reframing requires knowing what the current frame is. Skipping this step produces alternatives without anchor.
- **Crossing into approach-space.** Reframe alternatives might suggest different approaches become natural — name the shifts, but don't generate the approach contracts. That's `/approach`'s job.
- **No structural-tension section.** Generating alternatives without surfacing the deepest structural choice they expose makes the artifact a brainstorm dump rather than an analysis.
- **Picking the "right" alternative.** The skill surfaces alternatives + structural reads; the user picks. Skill picking violates frame-only and presumes information the skill doesn't have (the user's values, context, priorities).
- **Alternatives that share no continuity with the original.** Every alternative should acknowledge what it *shares* with the current frame, not just differ. Pure-difference alternatives feel arbitrary.
- **Question-second.** Producing alternatives without first asking the two anchor questions ("what is current frame foregrounding?" "what else could this be?") — alternatives without explicit anchoring questions are templated, not generated.

## Rules

- The skill outputs a single Reframe artifact (markdown file); never executes, never picks the frame.
- Current frame must be named before alternatives are generated.
- 3-5 substantive alternatives is the target; outside this range needs explicit justification.
- Each alternative has foreground/background diff, shared continuity, new approaches becoming natural, new stakes becoming visible.
- Structural-tension surfacing is mandatory.
- Recommended next is mandatory — what should happen with the artifact.
- When invoked from `frame:composer`, artifact lives at `<contracts-root>/frame-<slug>/reframe.md`.
- When invoked standalone, artifact lives at `<contracts-root>/reframe-<slug>.md`.
- `Extraction-confidence` and `Assumptions` fields are non-optional per PRINCIPLES.md §3.
- Question-first (PRINCIPLES.md §10): the artifact structure follows from the two anchor questions.
- When a sub-Agent is the worker, the contract MUST specify `model: opus` per PRINCIPLES.md §2.
- Direct-Use Rule (PRINCIPLES.md §4): when reframing a situation that references existing artifacts, the skill re-reads the source rather than reframing the user's summary of it.

## Key Files

- Output (standalone): `<contracts-root>/reframe-<slug>.md`
- Output (composer): `<contracts-root>/frame-<slug>/reframe.md`
- Sibling frame skills (all under `frame` plugin, all live): `frame:composer`, `frame:probe`, `frame:problem-statement`, `frame:from-corpus`, `frame:jtbd`, `frame:lens`, `frame:scope`, `frame:stakes`.
- Theoretical grounding: Russell Ackoff on "right vs wrong problem"; Mitroff's Type III error; Schön on framing and reframing in practice; behavioral decision theory on frame blindness (Russo & Schoemaker, Kahneman).
