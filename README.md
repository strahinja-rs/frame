# frame

Frame-space cognitive skills for Claude Code. Each skill takes an unstructured situation and helps the user **see, articulate, or reframe** the problem before any approach is chosen.

The plugin operates in the **frame** layer of the `problem → frame → approach → solution` cascade. See [ARCHITECTURE.md](./ARCHITECTURE.md) for the conceptual model and [PRINCIPLES.md](./PRINCIPLES.md) for the rules shared across all skills.

**Pairs with** the `approach` plugin to close the loop: `/frame:composer` produces a canonical Polya 9-target Problem Statement; that statement is the input to `/approach:composer`, which produces the execution plan.

## The 9 skills (v1)

| Skill | Layer | What it does |
|---|---|---|
| `frame:composer` | Meta | Reads unstructured situation, picks frame skills, sequences them, manages 3-4 question intake + bidirectional checkpoint (exit to /approach or continue frame work) |
| `frame:probe` | Perceive | Surface assumptions, foreground/background, missing pieces — questions, not answers |
| `frame:from-corpus` | Perceive → Articulate | Extract a frame from a complex existing artifact (transcripts, brief, set of interviews) |
| `frame:problem-statement` | Articulate | Polya 9-target canonical Problem Statement — **universal terminator** that feeds /approach:composer |
| `frame:jtbd` | Articulate | Jobs-to-be-Done articulation, per stakeholder |
| `frame:lens` | Articulate | Apply named analytical lens(es) — built-in library + custom-define |
| `frame:scope` | Articulate | Boundary clarification (in scope / out / adjacent) |
| `frame:stakes` | Articulate | Cost-of-error + reversibility + opportunity cost |
| `frame:reframe` | Reframe | Generate N alternative frames; foreground/background diff per alternative |

## Architectural location

```
Problem        Frame                   Approach                Solution
   │             │                        │                       │
   ▼             ▼                        ▼                       ▼
"situation"   "articulation +         "approach selection,     "code, docs,
              perception"             decomposition"           decisions"
              (this plugin)           (/approach plugin)       (orchestrator)
```

This plugin's deepest claim: **AI is a perceptual lever, not just a generator.** The right questions at the right moment change what the user *sees* about a situation — and the change in seeing changes everything downstream. Every skill in this plugin is question-first.

See [ARCHITECTURE.md](./ARCHITECTURE.md) for the full model.

## Shared principles

Every skill in the plugin follows the rules in [PRINCIPLES.md](./PRINCIPLES.md). The most important:

- **Frame-only.** Skills produce artifact documents; they never execute, route, or dispatch.
- **Sub-Agent workers always use Opus.** When a contract assigns a Claude sub-Agent, the contract MUST specify `model: opus`.
- **Surface uncertainty.** Every artifact has `Assumptions:` and `Extraction-confidence:` fields.
- **Direct-Use Rule.** Read the real material (corpus, conversation, artifact) — not summaries.
- **Composition is explicit.** Layers, not trees; iteration is the norm. The composition root documents every skill applied with rationale.
- **Knowledge work is the primary use case** (more so than for /approach). Frame work skews heavily toward research, consulting, strategy, sensemaking.
- **Recommend, never force-fit.**
- **Question-first.** Every skill leads with the question(s) it asks, not the structure it produces.
- **Iteration is the norm, not the failure mode.** Frame work is expected to revisit and refine.
- **Checkpoint to /approach is always available.** User controls how deep to go in frame work.
- **Universal terminator.** Every composition ends with `frame:problem-statement`, the canonical hand-off artifact.

## Composition: layers, not trees

Unlike `/approach` (which composes in trees), `/frame` composes in **layers with feedback loops**:

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
frame:problem-statement (universal terminator)
        ↓
        →  hand off to /approach:composer
```

## Hand-off to /approach

`frame:composer` outputs a folder at `<contracts-root>/frame-<slug>/` containing:

- `composition.md` — what was applied, in what order, why
- `problem-statement.md` — Polya 9 (**this is the hand-off artifact**)
- Supporting artifacts (probe.md, jtbd.md, lens.md, scope.md, stakes.md, reframe.md) for whatever skills ran

`/approach:composer` consumes `problem-statement.md` directly as the input to its approach mapping. Both `composition.md` and supporting artifacts remain available as context the user can reference during approach selection.

## Worker types

Frame work is more conversational than approach work, so workers lean toward Claude inline. Sub-Agent (model: opus) is used for isolated probes; Codex is used for `frame:from-corpus` on very large corpora.

| Worker | Strength |
|---|---|
| Claude inline | direction-setting, dialogue, probing, judgment |
| Claude sub-Agent (model: opus) | isolated probe in its own conversation |
| Codex (`codex exec`) | long-context single-task — particularly `frame:from-corpus` on large corpora |
| Human | taste, business decision, who-the-stakeholders-actually-are |

## Future (v0.2.0+, post-talk)

- `frame:five-whys` — root cause investigation (narrow case)
- `frame:audience-pivot` — frame the same situation for N stakeholders
- `frame:devil` — actively challenge a certain frame

## License

MIT
