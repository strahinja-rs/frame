---
name: lens
description: Apply named analytical lens(es) to a situation to surface what each lens reveals. ALWAYS invoke this skill when the user wants to look at a situation through a specific analytical pattern (JTBD as standalone lens, SWOT, Cynefin, 5 Whys, OODA, Double Diamond, problem-tree, value-stream-map, RACI, etc.) OR through a custom user-defined lens (e.g., the 10 analytical lenses from a discovery report). Built-in lens library + custom-define mechanism. Produces a Lens artifact — per-lens application result showing what each lens surfaces and what it backgrounds. Skip when no specific lens is wanted (run frame:probe instead), when the lens is itself a full skill (use that skill — JTBD via frame:jtbd, not via frame:lens with JTBD), or when applying multiple lenses without enough material to make each meaningful (apply fewer, deeply, not many shallowly).
# description-style: directive + negative constraint (Seleznov)
# rationale: Named analytical lenses are well-established cognitive tools (Polya, McKinsey 7S, Porter's Five Forces, etc.). This skill makes lens-application explicit, lets the user bring custom lenses (the Žiška 10-lens framework is a real example), and surfaces what each lens emphasizes vs de-emphasizes. The latter is the deepest output — lenses don't just illuminate; they also obscure.
---

<!--
ACTIVATION TESTS (for /skill:validate --bench):
1. SHOULD activate (user-requested lens): "Look at this through a SWOT lens" or "Apply Cynefin to this situation"
2. SHOULD activate (custom lens library): "Apply the 10 discovery lenses from the Žiška report to this client's situation"
3. SHOULD NOT activate (lens has dedicated skill): "Frame this as JTBD" → use frame:jtbd (the dedicated skill, not frame:lens with JTBD as parameter)
4. SHOULD NOT activate (no specific lens wanted): "Help me see what's going on" → use frame:probe
5. BOUNDARY: "Help me think about the strategic options" (could be lens with strategic-options pattern, could be frame:reframe, could be frame:dialogue — composer should disambiguate)
-->

# lens

Applies one or more **named analytical lenses** to a situation. Each lens surfaces specific aspects while backgrounding others — the skill makes both visible.

> See [PRINCIPLES.md](../../PRINCIPLES.md) for shared rules — particularly **§10 question-first** (each lens *is* a question), **§4 Direct-Use Rule** (read the situation directly when applying the lens).

## When to Use

- User explicitly wants a specific named lens applied (SWOT, Cynefin, 5 Whys, OODA, Double Diamond, RACI, problem-tree, value-stream-map, etc.).
- User has a custom analytical lens (or set of lenses — like the Žiška 10-lens discovery framework) and wants it applied.
- Composer-invoked when the user mentions a lens by name.
- Comparative work — applying 2-3 different lenses to the same situation to see what each one surfaces vs misses.

## When NOT to Use

- The lens has a dedicated skill in this plugin — use that skill, not this one:
  - JTBD → `frame:jtbd`
  - Polya 9 problem articulation → `frame:problem-statement`
  - Stakes / cost-of-error → `frame:stakes`
  - Scope / boundary → `frame:scope`
  - Reframe / alternatives → `frame:reframe`
- No specific lens wanted; user just wants to see the situation more clearly → `frame:probe`
- Applying many lenses without enough material → apply fewer, deeply. Lens application is best when it surfaces specific evidence, not when it produces ceremonial section headings.

## Process

The skill outputs a **Lens artifact** with per-lens application results plus a synthesis section if multiple lenses ran.

Question-first per [PRINCIPLES.md §10](../../PRINCIPLES.md#10-question-first--new-to-frame): each lens *is* a question (or set of questions) about the situation. The artifact's structure follows from the lens's questions, not from a generic template.

### 1. Resolve the lens(es)

If user named a built-in lens, look up its question set from the library (below). If user named a custom lens (e.g., "the 10 lenses from my discovery report"), ask the user for the lens definition or read it from the artifact they referenced (Direct-Use Rule per [PRINCIPLES.md §4](../../PRINCIPLES.md#4-direct-use-rule--exercise-the-real-material-not-summaries)).

### 2. Apply each lens

For each lens, apply its specific question set to the situation:

- Read the situation through the lens's questions
- Capture what each question surfaces
- Capture what the lens explicitly *doesn't* address (the lens's blind spots)

### 3. If multiple lenses ran, write a synthesis

- What each lens uniquely surfaced
- Where lenses converge (multiple lenses pointing at the same thing)
- Where lenses contradict (and what the contradiction reveals)
- What no lens surfaced (and whether that's a gap to probe)

### 4. Output

Write to:
- **Within composer**: `<contracts-root>/frame-<slug>/lens.md`
- **Standalone**: `<contracts-root>/lens-<slug>.md`

Format:

```markdown
# Lens: <slug>

## Situation
<one-paragraph distillation>

## Lenses applied
- <lens 1 name + source: built-in | custom-from-<artifact>>
- <lens 2 name + source>
...

## Lens 1: <name>

### What this lens asks
<the question(s) the lens applies>

### Application to this situation
<the answer(s), specific to this situation>

### What this lens surfaces (foreground)
<aspects this lens makes prominent>

### What this lens backgrounds or misses
<aspects this lens doesn't address>

[repeat per lens]

## Cross-lens synthesis (if multiple ran)

### Convergence
<where lenses point at the same thing>

### Divergence
<where lenses contradict, and what the contradiction reveals>

### Gaps no lens surfaces
<aspects of the situation no lens captures>

## Implications for the Problem Statement
- <how lens results should shape Polya 9 fields>

## Extraction-confidence
<low | medium | high>

## Assumptions
<inferences during lens application, or "none">
```

### 5. Stop

Skill is framing-only. Lens application is interpretation, not action. Hand off to the next frame skill (or back to composer) with the artifact.

## Built-in lens library

These lenses have well-established question sets and don't require custom definition:

### SWOT
- What are the **Strengths** of this situation / actor / position?
- What are the **Weaknesses**?
- What **Opportunities** exist?
- What **Threats** exist?
- Best for: strategic position analysis, competitive situations.
- Backgrounds: process dynamics, multi-stakeholder tensions.

### Cynefin
- Is the situation **Clear** (known cause-effect, best practice applies)?
- **Complicated** (cause-effect knowable with analysis, good practice applies)?
- **Complex** (cause-effect only knowable in retrospect; probe-sense-respond)?
- **Chaotic** (no cause-effect knowable; act-sense-respond)?
- **Confused / Disorder** (don't yet know which domain)?
- Best for: situations where the right *kind* of response matters (analysis vs experimentation vs immediate action).
- Backgrounds: specific content; focuses on response style not substance.

### 5 Whys
- Why is this happening? (round 1)
- Why is *that* happening? (round 2)
- ... up to 5 rounds
- Best for: root-cause investigation of recurring failures.
- Backgrounds: structural / systemic factors; tends toward linear causation.

### OODA
- What do you **Observe** about the situation?
- How do you **Orient** to it (mental models, prior experience)?
- What can you **Decide**?
- What can you **Act** on (and how fast)?
- Best for: time-pressured competitive situations.
- Backgrounds: deep deliberation, multi-stakeholder coordination.

### Double Diamond
- Discover (diverge in problem space): what's the breadth of the situation?
- Define (converge in problem space): what's the framed problem?
- Develop (diverge in solution space): what approaches could work?
- Deliver (converge in solution space): which approach ships?
- Best for: design / product / consulting work where both problem and solution need exploration.
- Backgrounds: maintenance / iteration after delivery.

### RACI
- Who is **Responsible** (does the work)?
- Who is **Accountable** (owns the outcome, single name)?
- Who is **Consulted** (input sought before decision)?
- Who is **Informed** (kept aware after)?
- Best for: decision-rights clarity in multi-party situations.
- Backgrounds: motivation / capability of named parties; just maps the rights.

### Problem-tree
- What is the central problem?
- What are its immediate causes? (branches)
- What are root causes? (deeper branches)
- What are immediate consequences? (other side)
- What are downstream consequences? (deeper consequences)
- Best for: complex causal situations with cascading effects.
- Backgrounds: non-causal aspects (stakeholder dynamics, identity).

### Value-stream-map
- What are the value-adding steps?
- What are the necessary-non-value-adding steps?
- What is waste (rework, waiting, re-derivation, defects)?
- Where are the wait states?
- Best for: process / operational analysis.
- Backgrounds: creative / judgment-dense work where value is harder to quantify.

### Custom lenses
When the user provides a custom lens (e.g., "the 10 lenses from my Žiška discovery"), the skill reads the lens definition from the source artifact and applies it using the same Process. The artifact captures the source so the lens application is auditable.

## Anti-Patterns

- **Using `frame:lens` with JTBD when `frame:jtbd` exists.** JTBD has its own dedicated skill because it's load-bearing enough to deserve specialized treatment. Same for stakes, scope, reframe, problem-statement — use the dedicated skill, not `frame:lens` with those names.
- **Applying many lenses shallowly.** A lens artifact with 8 lenses each getting 2 lines of content is decorative, not analytical. Apply fewer (2-3) lenses deeply.
- **Skipping the "what this backgrounds" section.** Every lens illuminates AND obscures. Naming the blind spot is the most under-used output of lens-application.
- **Lens-as-template-filling.** Going through the lens's questions mechanically without interpreting what the answer reveals about this specific situation.
- **Cross-lens synthesis as concatenation.** When multiple lenses ran, the synthesis should look for convergence / divergence / gaps — not just summarize each lens again.
- **Custom lens applied without source.** If the user names a custom lens, the skill MUST read the lens definition from a source artifact (Direct-Use Rule) or ask the user to define it. Inventing a "discovery lens" or "operations lens" by guessing is hallucination.

## Rules

- The skill outputs a single Lens artifact (markdown file); never executes.
- Each lens applied gets its own section with: what-it-asks, application, foreground, background.
- If multiple lenses run, cross-lens synthesis is mandatory.
- Custom lenses MUST have a documented source (file path or user-provided definition in the artifact).
- Built-in lenses use the question sets in this SKILL.md's library section.
- When invoked from `frame:composer`, artifact lives at `<contracts-root>/frame-<slug>/lens.md`.
- When invoked standalone, artifact lives at `<contracts-root>/lens-<slug>.md`.
- `Extraction-confidence` and `Assumptions` fields are non-optional per PRINCIPLES.md §3.
- Direct-Use Rule (PRINCIPLES.md §4) applies: read source material directly for custom lenses; read the situation directly for lens application.
- Question-first (PRINCIPLES.md §10): each lens IS a set of questions; the artifact structure follows from those questions.
- If the lens requested has a dedicated skill (JTBD, stakes, scope, reframe, problem-statement), recommend that skill and stop.
- When a sub-Agent is the worker, the contract MUST specify `model: opus` per PRINCIPLES.md §2.

## Key Files

- Output (standalone): `<contracts-root>/lens-<slug>.md`
- Output (composer): `<contracts-root>/frame-<slug>/lens.md`
- Sibling frame skills (all under `frame` plugin, all live): `frame:composer`, `frame:probe`, `frame:problem-statement`, `frame:from-corpus`, `frame:jtbd`, `frame:scope`, `frame:stakes`, `frame:reframe`.
- Theoretical grounding: SWOT (Andrews), Cynefin (Snowden), 5 Whys (Toyota Production System), OODA (Boyd), Double Diamond (UK Design Council), RACI (project management standard), problem-tree (logframe), value-stream-map (Lean / Toyota Production System).
