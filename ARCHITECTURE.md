# ARCHITECTURE

The conceptual model the `frame` plugin sits inside, and where this plugin's responsibility starts and stops.

---

## The four-space model

Any non-trivial task can be located in one of four cognitive spaces:

| Space | What | Cognitive operation | Where AI helps |
|---|---|---|---|
| **Problem** | the situation in the world | (pre-language; no operation yet) | — |
| **Frame** | how the situation is articulated, what filter you apply, what you let through | **articulation + perception** | **questioner / probe / mirror** — clarifies seeing, surfaces what's assumed |
| **Approach** | how you'll work on the framed problem (which structural pattern, which decomposition) | structural composition | pattern-matcher — proposes patterns from a known family |
| **Solution** | the artifact you ship (code, document, decision, message) | generation + curation | generator — produces candidates, human curates |

The relationships are not just sequential — they are **space-defining**:

- Frame defines the approach space. (Once you've framed the problem, only certain approaches are coherent.)
- Approach defines the solution space. (Once you've chosen the approach, only certain solutions are reachable.)
- **Wrong frame compounds the worst, because it silently cuts off the approaches that would have worked, which silently cuts off the solutions that would have moved anything.**

Cost of error compounds upstream:

- Wrong solution: hours to fix. Iterate.
- Wrong approach: weeks to recover.
- **Wrong frame: months or years lost.** You'll perfect something that wasn't the right thing.

---

## Where the `frame` plugin lives

The `frame` plugin operates in the **frame space**. Each skill takes an unstructured situation and helps the user produce a *frame artifact* — a structured record of how the problem is being seen, named, scoped, and challenged.

The plugin does not solve the problem (solution-space). It does not pick the execution pattern (approach-space). It produces an *articulation* — what the problem actually is, with the filters and assumptions made visible.

```
Problem        Frame                   Approach                Solution
   │             │                        │                       │
   ▼             ▼                        ▼                       ▼
"unstructured  "articulation +         "approach selection,     "code, docs,
situation"     perception"             decomposition"           decisions,
                                                                sent messages"
              (this plugin)            (/approach plugin)       (orchestrator)
```

**Inputs to this plugin:**
- An unstructured situation (text, conversation context, brief, situation description)
- Optional: an existing artifact / corpus / transcripts / set of interviews that the situation references

**Outputs from this plugin:**
- A Polya 9-target Problem Statement (the canonical hand-off artifact — universal terminator per PRINCIPLES.md §13)
- Optional supporting frame artifacts: probe analysis, lens applications, JTBD statements, reframe alternatives, scope boundary, stakes assessment

**Not in this plugin's scope:**
- Approach-selection / decomposition / contract authoring — handled by `/approach`
- Solution-space execution — handled by the orchestrator, by Codex, by Agent sub-types
- Frame-to-approach routing — handled by user decision at a `frame:composer` checkpoint, not by automated dispatch

---

## AI as perceptual lever (the deepest claim)

Most AI tooling treats AI as a **generation surface**: you say what you want, AI produces output. This plugin embodies a different claim: AI is also a **perceptual lever**. The right questions, asked at the right moment, change what the user *sees* about the situation — and the change in seeing changes everything downstream.

This is why every frame skill is *question-first* (PRINCIPLES.md §10). The skill's primary job is to ask the question that surfaces the filter the user didn't know they were applying. The artifact it produces is the *evidence of the surfaced filter* — not the substance of the answer.

---

## The three cognitive layers

Frame skills are organized into three cognitive operations. The layers iterate — perception surfaces something that articulation needs to capture, articulation reveals a tension that reframing must address, reframing exposes a new assumption that perception must probe.

### Layer A — Perceive

What am I looking at? What am I assuming? What's foregrounded vs backgrounded? What's missing?

- **`frame:probe`** — surface assumptions, check foreground/background balance, flag missing pieces. Questions, not answers.
- **`frame:from-corpus`** (also spans Layer B) — when the situation references a complex existing artifact, perceive what's actually in it (vs what the user *thinks* is in it)

### Layer B — Articulate

What is this, in structured form? Apply known articulation patterns.

- **`frame:problem-statement`** — Polya 9-target structured intake (universal terminator)
- **`frame:jtbd`** — Jobs-to-be-Done articulation, per stakeholder
- **`frame:lens`** — apply named analytical lens(es) to the situation (built-in library + custom-define)
- **`frame:scope`** — boundary clarification (in scope / out / adjacent)
- **`frame:stakes`** — cost-of-error + reversibility + opportunity cost

### Layer C — Reframe

What else could this be?

- **`frame:reframe`** — generate N alternative frames; foreground/background diff per alternative

### Plus the meta-skill

- **`frame:composer`** — picks frame skills based on signals in the unstructured situation; sequences them; manages user-controlled checkpoints with bidirectional exit to `/approach`; ensures every composition ends with `frame:problem-statement`

---

## Composition: layers, not trees

`/approach` composes in trees (a Pipeline whose stage is a Critic). `/frame` composes in **layers with feedback loops**:

```
Unstructured situation
        ↓
PERCEIVE (probe / from-corpus)
        ↓     ↑
ARTICULATE (problem-statement / jtbd / lens / scope / stakes)
        ↓     ↑
REFRAME (reframe)
        ↓     ↑
[Checkpoint — exit to /approach OR continue]
        ↓
frame:problem-statement (terminator)
        ↓
        →  hand off to /approach:composer
```

The arrows back up represent the iteration that PRINCIPLES.md §11 names as the norm. Composer manages this loop, surfacing the bidirectional checkpoint after each layer (PRINCIPLES.md §12).

---

## Hand-off to /approach

The output of `frame:composer` is structured for direct consumption by `/approach:composer`:

```
frame-<slug>/
├── composition.md           ← what was applied, in what order, why
├── problem-statement.md     ← Polya 9 (universal terminator) ← THIS is the hand-off artifact
├── probe.md                 ← (if probe ran) assumptions, foreground/background, missing
├── jtbd.md                  ← (if jtbd ran) per-stakeholder JTBDs
├── lens.md                  ← (if lens ran) per-lens applications
├── scope.md                 ← (if scope ran) boundary diagram
├── stakes.md                ← (if stakes ran) cost-of-error analysis
└── reframe.md               ← (if reframe ran) alternatives + diff
```

`/approach:composer`'s Phase 1 (problem extraction) becomes a thin delegate: instead of running its own Dialogue intake, it consumes `frame-<slug>/problem-statement.md` directly, validates that the Polya 9 is populated, and moves to Phase 2 (approach mapping).

The composition root in `/approach` references the source frame:

```markdown
# Composition contract: <task-name>

## Problem Statement
- Source: ../frame-<slug>/problem-statement.md
- Frame composition: ../frame-<slug>/composition.md
- Extraction-confidence: <inherited from frame artifact>
- Assumptions: <inherited + any added during approach mapping>
...
```

This is the **full cascade made auditable**:

```
unstructured situation
       ↓
/frame:composer    →  frame-<slug>/problem-statement.md (+ supporting)
       ↓
/approach:composer  →  composition-<slug>.md + child contracts
       ↓
orchestrator        →  execution
```

Every step has a written artifact. Every transition is a user-controlled checkpoint. The cognitive work is visible from situation to shipped output.

---

## The 9 skills (v1)

| Skill | Layer | Job |
|---|---|---|
| **`frame:composer`** | Meta | Reads situation, picks frame skills, sequences, manages intake (3-4 Q cap) + bidirectional checkpoint |
| **`frame:probe`** | A | Surface assumptions, foreground/background, missing pieces — questions, not answers |
| **`frame:from-corpus`** | A→B | Extract a frame from a complex existing artifact (corpus, transcripts, docs) |
| **`frame:problem-statement`** | B | Polya 9-target canonical Problem Statement (universal terminator) |
| **`frame:jtbd`** | B | Jobs-to-be-Done articulation, per stakeholder |
| **`frame:lens`** | B | Apply named analytical lens(es) — built-in library + custom-define |
| **`frame:scope`** | B | Boundary clarification (in scope / out / adjacent) |
| **`frame:stakes`** | B | Cost-of-error + reversibility + opportunity-cost articulation |
| **`frame:reframe`** | C | Generate N alternative frames; foreground/background diff per alternative |

**Deferred to v0.2.0** (Tier B, post-talk):
- `frame:five-whys` — root cause investigation (narrow case)
- `frame:audience-pivot` — frame the same situation for N stakeholders (special case of reframe)
- `frame:devil` — actively challenge a certain frame (specialized reframe + probe)

---

## The frame-only invariant

Every skill in this plugin produces an artifact and stops. None of them execute, route, or dispatch. The user (or a downstream tool the user invokes) takes the next step.

See [PRINCIPLES.md §1](./PRINCIPLES.md#1-frame-onlynever-executes) for the full rationale.

---

## Worker types

Frame skills assign workers per operation. The candidate worker types:

| Worker | Strength | Use when |
|---|---|---|
| **Claude inline** | direction-setting, dialogue, probing, judgment | the work needs Claude's reasoning in the current conversation context |
| **Claude sub-Agent (model: opus)** | isolated probe that shouldn't pollute the main context | the work needs Claude's reasoning in its own conversation (e.g., reading a corpus without polluting the main thread) |
| **Codex (`codex exec`)** | long-context single-task — particularly useful for `frame:from-corpus` on very large corpora | the situation involves a large artifact and the framer needs to read it deeply |
| **Human** | taste, business decision, who-the-stakeholders-actually-are | the framer needs information only the human has |

Default lean: **Claude inline for most frame work**. Frame work is more conversational than approach work (which leans toward Codex for long-mechanical execution). Sub-Agent + Codex are used when isolation or depth-on-one-input matters.

When the worker is a Claude sub-Agent, the contract MUST specify `model: opus` per [PRINCIPLES.md §2](./PRINCIPLES.md#2-sub-agent-workers-always-use-opus).

---

## Why this plugin exists

Most AI tooling lives in solution space. Most people use AI as a generator. The leverage of AI moves upstream when the *frame* itself is co-shaped rather than pre-decided alone — and the cost of getting the frame wrong compounds across every downstream choice.

This plugin exists to make the upstream frame work explicit and operational. Each skill encodes one cognitive operation (probe, articulate via N patterns, reframe) that has emerged across years of consulting / research / sensemaking practice. Naming the operation is the smallest unit of clarification; producing the artifact is the smallest unit of commitment.

The plugin's deepest claim is structural: **once the right frame is articulated, the approach space is dramatically narrowed and approach selection becomes a smaller, more legible problem.** And: AI's contribution to frame work is the **right question at the right moment**, not the right answer.

---

## Pairing with /approach

The `/frame` and `/approach` plugins are designed to compose. Together they make the upstream cognitive work — *all* of it, perception through execution-pattern selection — visible and auditable.

Without `/frame`, `/approach:composer`'s Phase 1 absorbs frame work inline (as a transitional accommodation). With `/frame` installed, that Phase 1 delegates to `/frame:composer` and `/approach:composer` becomes a pure approach-space tool.

Both plugins follow the same architectural discipline (frame-only, sub-Agent Opus, explicit composition, knowledge-work parity, recommend-never-force-fit). The two plugins differ in cognitive layer (frame vs approach) and composition shape (layers vs trees).

See `/approach`'s own [ARCHITECTURE.md](../approach/ARCHITECTURE.md) for that plugin's perspective on the cascade.
