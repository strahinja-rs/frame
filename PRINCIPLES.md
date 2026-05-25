# PRINCIPLES

Shared rules across every skill in the `frame` plugin. Each SKILL.md references this file rather than restating the rules. If a rule needs to change, change it here.

The `frame` plugin operates in the **frame space** of the problem → frame → approach → solution cascade. Many rules are inherited from `/approach`; some are specific to frame work and named here as additions.

---

## 1. Frame-only — never executes

Every skill in this plugin produces a **frame artifact** (a markdown document capturing perception, articulation, or reframing). It never executes the work it frames. Execution happens downstream — first as approach-space framing (via `/approach`), then as solution-space execution by the orchestrator.

Same architectural reason as `/approach`'s frame-only rule, applied one layer earlier: separating perception and articulation from approach-selection lets each cognitive operation be audited, revised, and handed off cleanly. A frame artifact written and stopped is a checkpoint that preserves the user's seeing for downstream work.

**Operational consequence:** no skill in this plugin invokes `/approach:composer`, generates approach contracts, spawns Agents for execution, or otherwise crosses into approach- or solution-space. Skills produce markdown artifacts and stop. The hand-off to `/approach` is a *user-controlled* action surfaced at composer's checkpoints.

---

## 2. Sub-Agent workers always use Opus

When a frame skill assigns a Claude sub-Agent (via the `Agent` tool) as the worker for a perception probe, articulation pass, lens application, or reframe generation, the contract **MUST** specify `model: opus`.

Never Sonnet. Never Haiku. No exceptions — including for "small probes" or "lightweight lens applications." Frame work is judgment-bound; capability dominates token cost.

This is inherited verbatim from `/approach`'s PRINCIPLES.md §2 and applies identically here.

---

## 3. Assumptions + extraction-confidence — surface uncertainty, don't hide it

Every frame artifact has an `Assumptions:` field, even when empty (`none`). When the framer infers anything not stated explicitly by the user — about the situation, scope, stakes, or stakeholders — the inference goes in `Assumptions:`, not in the body.

Every frame artifact also carries an `Extraction-confidence: low | medium | high` marker:

- **high** — every required field was stated or directly inferable
- **medium** — some inference, no blocking unknowns
- **low** — significant inference; artifact is provisional, revisit on first downstream failure

**Operational consequence in frame context:** because frame artifacts feed `/approach:composer`, low-confidence frame work produces low-confidence approach selection. The composer must surface confidence prominently so the user can decide whether to refine before crossing the bridge.

Inherited from `/approach` PRINCIPLES.md §3.

---

## 4. Direct-Use Rule — exercise the real material, not summaries

In `/approach`, the Direct-Use Rule says: exercise the real intended *surface* (deployed endpoint, installed MCP, real browser) as early as possible.

In `/frame`, the analog is: **read the real material, not summaries.** When the situation involves an existing artifact (a corpus of transcripts, a brief, a set of interviews, a long doc), the frame skill should consult the artifact directly, not a synopsis of it. Summaries pre-filter — and the framer's job is to set the filter, not to inherit someone else's filter.

When the artifact is large or the framer is rate-limited, sample meaningfully (representative excerpts) rather than rely on second-hand summaries.

Concretely:
- `frame:from-corpus` consumes the corpus directly via Read / Grep / glob
- `frame:probe` invited to consult original source artifacts when the situation references them
- `frame:problem-statement` validates Polya 9 fields against original material when present
- `frame:reframe` generates alternatives by re-reading the source, not by re-reading the current frame

**Operational consequence:** prefer original artifacts over derived summaries. Where summaries are unavoidable, mark them as inputs of lower epistemic rank than originals.

Adapted from `/approach` PRINCIPLES.md §4.

---

## 5. Contracts-root resolution

Frame artifacts land at `<contracts-root>/<frame-skill>-<slug>.md` (single skill) or `<contracts-root>/frame-<slug>/<artifact>.md` (composer output, folder structure).

`<contracts-root>` resolution, in order (first match wins):

1. **User-specified path** — explicit override at invocation
2. **Task-implied project folder** — e.g., a task referencing `~/work/myapp` writes artifacts there
3. **Current working directory if it is a project** — has `.git/` or `CLAUDE.md` → `<cwd>/.claude/contracts/`
4. **Fallback** — `/tmp/frame-contracts/` (only when no project context exists)

**Slug** is kebab-case from the situation name (`take-on-client-decision`, `ziska-phase-2-shape`).

Inherited from `/approach` PRINCIPLES.md §5, with the only difference being the fallback path name (`frame-contracts` vs `approach-contracts`) so the two don't collide in `/tmp/`.

---

## 6. Composition is explicit — no hidden nesting (and frame composes in layers, not trees)

When a composer applies multiple frame skills, the composition root document **MUST** explicitly name every skill applied, in order, with rationale per skill.

The composition structure differs from `/approach`:
- **/approach composes in trees** (a Pipeline whose stage is a Critic): hierarchical, executable as a graph
- **/frame composes in layers** (probe → articulate → reframe → re-probe): sequential with feedback loops, iterative

`frame:composer`'s output documents the sequence + the iteration pattern, not a tree. But the principle is identical: hidden composition is forbidden. Reviewers (human or LLM) must be able to audit what was applied and why.

**Operational consequence:** every `frame:composer` output folder includes `composition.md` listing all skills applied, in sequence, with per-skill rationale citing what about the situation justified that skill.

Adapted from `/approach` PRINCIPLES.md §6 to layer-composition shape.

---

## 7. Knowledge work is the primary use case (more so than for /approach)

Where `/approach` declares knowledge work co-equal with code work, `/frame` leans heavily toward knowledge work. The vast majority of frame situations are research, consulting, strategy, sensemaking, decision-making — domains where the *problem itself* is unclear and articulation is the work.

Code-shaped tasks can use `/frame` (architectural decisions, scope-clarification, lens-application before refactor) but they're the minority case. Frame work for code tends to be lightweight (the problem is already specific); frame work for knowledge / strategy / consulting tends to be heavyweight.

**Operational consequence:** examples in every SKILL.md lead with knowledge-work cases; code cases are secondary. The framing language ("articulation," "perception," "stakeholder," "lens," "scope") is naturally knowledge-shaped.

---

## 8. Recommend sibling — never force-fit

If the user's situation doesn't fit the active skill's shape, recommend the right sibling (within `/frame`, or back to `/approach` if the situation isn't actually a frame problem) and stop.

When recommending a sibling, check `<available_skills>` first. If installed, recommend by name (`frame:probe`, `approach:dialogue`). If not, describe the operation inline.

Inherited from `/approach` PRINCIPLES.md §8.

---

## 9. Architectural location — the frame layer

The `frame` plugin operates in the **frame space** of the problem → frame → approach → solution cascade.

```
Problem        Frame                   Approach                Solution
   │             │                        │                       │
   ▼             ▼                        ▼                       ▼
"situation"   "articulation,         "approach selection,    "execution"
              perception"            decomposition"
              (this plugin)          (/approach plugin)      (orchestrator)
```

Inputs: an unstructured situation (text, conversation, corpus, brief).
Outputs: a frame artifact — most commonly a Polya 9-target Problem Statement, supported by optional probe / lens / reframe / stakes / scope / jtbd artifacts.

**Hand-off:** the natural downstream consumer of `frame:composer`'s output is `/approach:composer`. The bridge is the Problem Statement. See ARCHITECTURE.md for the full handoff protocol.

**Operational consequence:** no skill in this plugin attempts approach-selection or execution. Its scope ends at "the user has stable seeing of the problem and a portable Problem Statement."

---

## 10. Question-first — *new to /frame*

Every frame skill leads with the **question(s) it asks** the user (and/or itself), not with the structure it produces.

In `/approach`, the SKILL.md leads with the structural pattern ("multi-stage with handoffs"). In `/frame`, the SKILL.md should lead with the perceptual question ("what am I including / excluding?"; "who is this for?"; "what makes this hard?").

The output is **downstream of the perception**, not the other way around. A frame skill that produces a structure without surfacing the questions that generated it has skipped the work.

**Operational consequence:** every frame SKILL.md's Process section names the questions the skill is asking explicitly, as user-facing prompts when invoked interactively or as self-prompts when invoked from composer.

---

## 11. Iteration is the norm, not the failure mode — *new to /frame*

Frame work is *expected* to revisit and refine. Re-probing after articulation reveals new assumptions. Re-articulating after reframing produces a different Polya 9. Re-checking scope after running a lens surfaces overlooked stakeholders.

This is **the practice**, not a sign that something went wrong on the first pass.

`/approach`'s composer Phase 7 treats adaptive recomposition as opt-in for failure cases. `/frame`'s composer treats iteration as default behavior — the user is presented with re-frame opportunities at each checkpoint, not just at failure points.

**Operational consequence:** frame artifacts are *versioned in place* (later applications append, prior states preserved). Composer offers re-iteration as a first-class option at every checkpoint, not as a fallback.

---

## 12. Checkpoint to /approach is always available — *new to /frame*

At any natural juncture (after intake, after each frame skill runs), `frame:composer` surfaces a bidirectional checkpoint:

- **Hand off to `/approach:composer`** with the current frame artifact (Problem Statement + supporting docs)
- **Continue frame work** (additional skill, deeper probe, fresh lens, alternative reframe)

The user chooses. The composer never silently keeps going deeper into frame work without explicit user consent at a checkpoint.

This is **the fundamental affordance** of /frame: the user controls how deep to go. Frame work can be light (one skill + Polya 9 + exit to approach) or deep (4+ skills + iteration before exit). Composer's job is to surface "ready to hand off" as soon as it has a usable Polya 9 — and let the user decide whether to keep refining.

**Operational consequence:** every `frame:composer` checkpoint includes the exit-to-approach option as the primary action. The user can also invoke this exit any time outside of a composer-presented checkpoint by explicitly saying "ship to /approach" or equivalent.

---

## 13. Universal terminator — every composition ends with frame:problem-statement — *new to /frame*

Whatever sequence of frame skills `frame:composer` applies, the composition **MUST** end with `frame:problem-statement` producing a Polya 9-target structured artifact.

This is the **canonical hand-off artifact** that feeds `/approach:composer`. The other frame skills (probe, lens, reframe, etc.) produce supporting material that informs the Polya 9 but does not replace it.

Even if the user exits to /approach early (before composer has run all planned skills), composer produces a minimum-viable `frame:problem-statement` from whatever frame material is currently available, marks `extraction-confidence` appropriately, and hands off.

**Operational consequence:** `frame:problem-statement` is callable standalone (e.g., user already articulated the problem and just wants the canonical form) and is also the mandatory final step of every `frame:composer` composition. Always present in the output folder.
