---
name: composer
description: Composition planner for the frame family — reads an unstructured situation, picks the right frame skills based on signals, sequences them, manages intake (max 3-4 questions) and a bidirectional checkpoint (exit to /approach OR continue frame work). ALWAYS invoke this skill when the user brings an unstructured situation they want to think through — "help me figure out", "I'm trying to decide", "I don't know where to start", "I need to make sense of this" — or asks explicitly to frame, articulate, or scope a problem before acting. Universal terminator: every composition ends with frame:problem-statement (Polya 9), the canonical artifact that feeds /approach:composer. Does not execute; produces frame artifacts and stops at a user-controlled checkpoint. Skip when problem is already well-articulated (use frame:problem-statement directly), when user wants execution not framing (use /approach), or when work is trivial.
# description-style: directive + negative constraint (Seleznov)
# rationale: Composer is the apex of the frame family. Without explicit framing, unstructured situations default to either ad-hoc framing (lossy, no audit) or to premature approach selection (working on the wrong problem). Directive style forces the upstream pause. The 3-4 question cap + bidirectional checkpoint pattern prevents intake fatigue while keeping user in control of depth.
---

<!--
ACTIVATION TESTS (for /skill:validate --bench):
1. SHOULD activate (knowledge work / decision): "I'm thinking about whether to take on a new client. Six-figure budget but 6 months of work. Help me figure out what's actually going on here."
2. SHOULD activate (sensemaking): "I have these 12 client interview transcripts and I'm not sure what the real problem is"
3. SHOULD NOT activate (well-articulated): "Refactor the auth module to support OAuth2 — see existing brief in /docs/auth-migration.md"
4. SHOULD NOT activate (already in execution): "Run the test suite and tell me what failed" (approach:one-shot or just do it)
5. BOUNDARY: "I want to write a strategy doc for X" (could be frame:composer for problem articulation, or approach:dialogue if user already knows what to articulate — composer should run intake to disambiguate)
-->

# composer

Composition planner for the `frame` family. Reads an unstructured situation, identifies which frame skills are warranted, sequences them with user-controlled checkpoints, and ensures the composition always terminates with a Polya 9-target Problem Statement ready for hand-off to `/approach:composer`.

> See [PRINCIPLES.md](../../PRINCIPLES.md) for shared rules — particularly **§10 question-first**, **§11 iteration-as-norm**, **§12 checkpoint-to-approach-always-available**, **§13 universal terminator**.
>
> See [ARCHITECTURE.md](../../ARCHITECTURE.md) for the `problem → frame → approach → solution` model and the layered composition shape unique to /frame.

## When to Use

- User brings an unstructured situation they want to think through, not act on (yet).
- Problem definition is unclear, partial, or feels off — the user isn't sure what the *real* problem is.
- High-stakes decision where wrong frame would compound expensively.
- Multi-stakeholder situation where different views might foreground different problems.
- Large existing artifact (corpus, transcripts, brief) that needs framing before approach selection.
- User explicitly invokes `/frame:composer` or says "frame this," "help me see what's going on," "I need to figure out the real problem first."

## When NOT to Use

- Problem is already well-articulated and the user wants to act — invoke `frame:problem-statement` directly to produce the canonical Polya 9, then hand off to `/approach`.
- User is in execution mode and a frame pause would be friction — read the room.
- Work is trivial / scratch — no framing pays back the overhead.
- User has already done the frame work and is presenting a Problem Statement; just hand to `/approach:composer`.
- Situation is a question or lookup, not a problem — answer it.

## Process

The skill outputs a **frame composition** — a folder at `<contracts-root>/frame-<slug>/` containing per-skill artifacts plus a `composition.md` root document. Composition manages intake, runs frame skills in sequence, surfaces bidirectional checkpoints, always terminates with `frame:problem-statement`.

### Phase 1: Silent assessment (no user input yet)

Read the unstructured situation. Pattern-match signals to candidate skills.

**Signal → candidate skill mapping:**

| Signal in the unstructured situation | Suggests skill |
|---|---|
| Vague situation, "figure out", "I don't know", "help me see" | **`frame:probe`** (perception first) |
| Decision-shaped ("should I do X or Y") | `frame:reframe` (alternatives) + `frame:stakes` |
| Multi-stakeholder context (mentioned or likely) | `frame:jtbd` (per-stakeholder articulation) |
| High stakes (irreversible, costly, identity-touching) | `frame:stakes` + consider `frame:reframe` if user seems certain |
| Fuzzy boundaries ("where does this end?") | `frame:scope` |
| Large existing artifact referenced (corpus, transcripts, PDF) | `frame:from-corpus` |
| User mentions a specific analytical lens (JTBD, SWOT, Cynefin, 5 Whys, etc.) | `frame:lens` |
| Output will be communicated to multiple stakeholder groups | (consider `frame:reframe` with audience as dimension) |
| Well-articulated already | go directly to `frame:problem-statement` — composer may not be needed |

Build a *tentative plan* (which skills, in what order). Do not commit yet — Phase 2 intake will refine.

**Default sequence shape:** Perceive (probe / from-corpus) → Articulate (problem-statement plus optional jtbd / lens / scope / stakes) → optionally Reframe → terminate with problem-statement. Iteration loops between these as needed.

### Phase 2: Intake (hard cap 4 questions; optional Phase 3(d) refinement +3; absolute ceiling 7)

Composer asks the user up to 4 questions in Phase 2 to gather context the situation didn't surface. Use the question budget intelligently:

- **Question 1** typically clarifies the *mode*: are you trying to decide / understand / explore / scope?
- **Question 2** typically gathers *missing context*: stakes, stakeholders, timeline, what's been tried.
- **Question 3** typically surfaces a *load-bearing assumption*: gut read, default option, conflict point.
- **Question 4** (reserved, ask only if needed): the *disambiguator* if Q1-Q3 didn't converge.

**Hard cap: never ask more than 4 questions in Phase 2.** If composer can't get enough signal in 4 questions, proceed to Phase 3 with low extraction-confidence and surface that prominently.

**Total intake ceiling: Phase 2 cap 4 + optional Phase 3(d) refinement +3 = absolute ceiling of 7 questions across the full composition.** If the user picks option (d) at Phase 3 (more clarifying questions before deciding) more than once, the absolute ceiling fires — composer proceeds with whatever signal it has, marks extraction-confidence honestly, and surfaces the checkpoint again.

The questions should be specific and answerable. Avoid "what do you want?" / "tell me more" — those are abdications. Each question should narrow the space.

### Phase 3: Refined plan + first bidirectional checkpoint

After intake, composer presents:

```
Based on what you shared:

PROPOSED FRAME WORK:
1. <frame:X> — <rationale: what about the situation justifies this>
2. <frame:Y> — <rationale>
3. <frame:Z> — <rationale>
4. frame:problem-statement — universal terminator (canonical Polya 9)

Skills considered and skipped:
- <frame:A>: <why skipped>
- <frame:B>: <why skipped>

Estimated time: <N> minutes.

WHAT NEXT?
  (a) Proceed with this plan
  (b) Skip directly to frame:problem-statement (light frame work, hand off to /approach faster)
  (c) Hand off to /approach NOW with whatever I have (minimum-viable Polya 9 from intake)
  (d) Ask 2-3 more clarifying questions before deciding
```

Option (c) is the **bidirectional checkpoint** — user can exit to /approach at any point. Per [PRINCIPLES.md §12](../../PRINCIPLES.md#12-checkpoint-to-approach-is-always-available--new-to-frame), this is the fundamental affordance of the plugin.

If user picks (d), ask 2-3 more questions (counts against the absolute ceiling — Phase 2 hard cap 4 + Phase 3(d) refinement +3 = ceiling 7). Then re-ask Phase 3. Refinement round can fire only once; second invocation of (d) triggers absolute ceiling — composer proceeds with current signal, marks extraction-confidence honestly, and surfaces the checkpoint as the final decision point before running skills.

If user picks (b), skip to `frame:problem-statement` only. Produce minimum-viable Polya 9 from intake answers. Mark `extraction-confidence: low` or `medium`. Surface bidirectional checkpoint again.

If user picks (c), exit to /approach immediately with whatever Polya 9 composer can produce from intake. Mark `extraction-confidence` honestly.

If user picks (a), proceed to Phase 4.

### Phase 4: Run frame skills in sequence

For each skill in the plan:

1. Invoke the skill (conceptually) with the situation + intake context + any prior frame artifacts produced.
2. Skill produces its artifact at `<contracts-root>/frame-<slug>/<skill>.md`.
3. **Between skills, run an inline adaptive check**: did the artifact surface something that changes the plan? (e.g., probe surfaced a new stakeholder → suggest adding jtbd; reframe surfaced an unconsidered scope question → suggest adding scope).
4. **After each non-terminator skill, surface the bidirectional checkpoint** (small version):

```
Just ran: frame:<X>
Surfaced: <one-line summary of what changed>

WHAT NEXT?
  (a) Continue with planned next skill: frame:<Y>
  (b) Insert <suggested-skill> before continuing (because <surfaced material>)
  (c) Hand off to /approach NOW
  (d) Stop here, produce frame:problem-statement, then hand off
```

The user decides at each checkpoint. Composer never silently keeps going deeper without a checkpoint surface.

### Phase 5: Universal terminator

Whatever path the composition took, **always end with `frame:problem-statement`** producing the canonical Polya 9. This is the artifact that feeds `/approach:composer`.

Per [PRINCIPLES.md §13](../../PRINCIPLES.md#13-universal-terminator--every-composition-ends-with-frameproblem-statement--new-to-frame), this is non-negotiable. Even if the user exits to /approach early (option (c) at any checkpoint), composer first produces a minimum-viable `frame:problem-statement` from whatever frame material exists. Always.

**Minimum-viable Polya 9 from 3-4 intake answers** (the early-exit case):

When the user exits early after only intake (Phase 2) plus the Phase 3 checkpoint, composer must still produce a complete 9-target Problem Statement. The inference rules:

| Polya field | When intake didn't cover it explicitly |
|---|---|
| 1. Desired end state | Infer from situation text + Q1 (mode) answer. Mark Assumption if not user-stated. |
| 2. Current state | Infer from situation text. Mark Assumption if interpretation. |
| 3. Gap | Infer from end-state − current-state. Always derivable from 1+2. |
| 4. Deliverable shape | Infer from situation type (decision / artifact / behavior). Mark Assumption. |
| 5. Verification criterion | Most likely to be missing. Either ask in Phase 2 (recommended) or infer + mark Assumption + flag `medium`-or-`low` confidence. |
| 6. Constraints | Combine: stated in situation + intake Q2 (timeline/stakes) answer + inferred reversibility. Mark Assumptions per inferred constraint. |
| 7. Known vs unknown | Composer's own meta-view: what intake surfaced as known, what remains unknown. Always populatable. |
| 8. User role | Default to "gates: at irreversible steps; autonomy: on local/reversible work" if not stated. Mark Assumption. |
| 9. Stakes | Infer from situation + intake Q3 (load-bearing assumption / gut). Mark Assumption if not user-stated. |

The minimum-viable Polya 9 will typically land at `extraction-confidence: low` or `medium`. **Mark honestly**: low for 3-4 fields populated from inference, medium for ~6/9 fields stated or directly inferable. Never inflate confidence to make the artifact look complete.

Mark `extraction-confidence` honestly based on how much frame work actually ran:
- **low** (4 of 9 fields stated, 5 inferred): single-skill composition (just problem-statement, exited at Phase 3 checkpoint)
- **medium** (6-7 of 9 fields stated): single skill + Phase 3 refinement, OR 2-3 skills ran lightly
- **high** (8-9 of 9 fields stated or directly inferable): full composition with probe + 2-3 articulation skills + reframe + terminator

### Phase 6: Write composition root

Write `composition.md` to the folder root, documenting:

```markdown
# Frame composition: <slug>

## Situation as received
<original unstructured situation, verbatim or summarized if very long>

## Intake (Phase 2)
<the 3-4 questions asked, with user's answers>

## Skills applied (in order)
1. frame:<X> → <X>.md — <rationale citing what about the situation justified this>
2. frame:<Y> → <Y>.md — <rationale>
3. frame:problem-statement → problem-statement.md (universal terminator)

## Skills considered and skipped
- frame:<A>: <why>
- frame:<B>: <why>

## Checkpoints surfaced (Phase 3 + 4)
- After intake: user picked <(a)/(b)/(c)/(d)>
- After frame:<X>: user picked <choice>
- After frame:<Y>: user picked <choice>

## Iteration log (if any re-probes / re-articulates happened)
- <round> @ <step>: <what changed and why>

## Extraction-confidence
<low | medium | high>

## Assumptions
<inferences during composition, or "none">

## Hand-off
Problem Statement: ./problem-statement.md
Ready for: /approach:composer (pass problem-statement.md as Phase 1 input)
```

### Phase 7: Hand off

Surface to the user:
- Path to composition folder
- Path to `problem-statement.md` (the hand-off artifact)
- Summary of what was applied
- `extraction-confidence` and any unresolved Assumptions
- Suggested next: `/approach:composer ./frame-<slug>/problem-statement.md`

Composer stops. Does NOT auto-invoke `/approach:composer` — that's the user's call.

### Phase 8: Adaptive re-iteration (when re-invoked)

`frame:composer` can be re-invoked with context about a downstream result — e.g., the user ran `/approach:composer` on the Problem Statement and the resulting composition felt wrong, or execution surfaced something that suggests the frame was off.

When re-invoked with that context:
1. **Read existing state**: the original composition folder, problem-statement.md, the downstream signal indicating what went wrong.
2. **Decide**:
   - **Re-iterate** within existing composition (run another probe / reframe / lens on the existing artifacts to refine) — same composition folder, append iteration log
   - **Re-compose from scratch** if the original frame was fundamentally off — new composition folder, link back to the original
3. **Update** the composition log + problem-statement; surface to user before further hand-off.

Re-iteration is opt-in — only fires when explicitly re-invoked. Default to original frame if downstream result aligns with expectations.

## Anti-Patterns

- **Skipping intake and assuming the situation as given.** Surface questions to refine, even if the user is confident. Confidence is a signal but not a substitute for the user's role in the framing — the 3-4 question cap is permission, not avoidance.
- **Asking more than 4 questions in Phase 2 intake.** The cap is firm. If 4 questions don't yield enough signal, proceed with low confidence — that's the right move.
- **Holding the user captive in frame work.** Every checkpoint must include "hand off to /approach now" as a valid choice. Composer's job is to make exit easy, not to keep the user in frame work until composer feels satisfied.
- **Skipping the universal terminator.** Every composition ends with `frame:problem-statement`. Even if composer thinks the existing frame material is enough. The Polya 9 is the canonical hand-off artifact, not a redundant step.
- **Auto-invoking /approach:composer.** Composer stops at hand-off. The user invokes /approach when ready.
- **Running every relevant skill rather than the load-bearing 2-4.** A frame composition with 6 skills is a sign composer wasn't disciplined. Pick fewer, run them well, surface checkpoints.
- **Hiding the iteration log.** If composer re-iterated mid-composition, the composition.md must show it. Hidden iteration is hidden composition (PRINCIPLES.md §6 violation).
- **Treating low extraction-confidence as a failure.** Low confidence is *information*. It tells the user (and downstream tools) that the artifact is provisional. Hiding it is worse than producing it.
- **Question-second.** A frame skill that starts with structure ("here's the Polya template, fill it in") instead of with questions ("what does done look like here?") violates the question-first principle (PRINCIPLES.md §10).
- **Re-framing as avoidance / frame work as procrastination.** Iteration is the norm *only when new material justifies it* (PRINCIPLES.md §11). Re-probing because the user is uncomfortable with the existing frame's implications — rather than because new material surfaced — is approach-/solution-space avoidance masquerading as frame work. Composer should ask "what new material justifies this iteration?" at each iteration trigger and accept "no new material" as the cue to push toward terminator + hand-off, not as license to keep framing. This is the realistic v1 failure mode for high-frame-fluency users; structurally invited by the iteration-is-norm + checkpoint-always-available principles, and must be guarded against by the composer's discipline.

## Rules

- The skill outputs a composition folder; it never executes, never auto-invokes downstream tools.
- Phase 2 intake is capped at 4 questions (with one reserved 4th for disambiguation). Total intake across Phase 2 + Phase 3 refinement loops never exceeds 7 questions.
- Phase 3 checkpoint MUST include exit-to-/approach as an option.
- Phase 4 checkpoint after each non-terminator skill MUST include exit-to-/approach as an option.
- Every composition MUST end with `frame:problem-statement` producing a Polya 9 artifact.
- Composition root (`composition.md`) MUST document: situation, intake Q&A, skills applied with rationale, skills considered and skipped, checkpoints surfaced and user choices, iteration log if any, extraction-confidence, Assumptions, hand-off path.
- When a sub-Agent is the worker for any frame skill internally, the contract MUST specify `model: opus` per [PRINCIPLES.md §2](../../PRINCIPLES.md#2-sub-agent-workers-always-use-opus).
- If the unstructured situation is already well-articulated (Polya 9 targets directly inferable from the situation), recommend `frame:problem-statement` directly and skip composition — no need for a full composer pass.
- If the situation is not actually a frame problem (e.g., user wants execution), recommend `/approach:composer` (or appropriate direct skill) and stop.
- Composition cost should be a fraction of the downstream work it frames. If composing a frame would take longer than the work, recommend lighter framing (just `frame:problem-statement`).
- Contracts-root resolution per [PRINCIPLES.md §5](../../PRINCIPLES.md#5-contracts-root-resolution).

## Key Files

- Output folder: `<contracts-root>/frame-<slug>/`
  - `composition.md` — root document
  - `problem-statement.md` — universal terminator (the hand-off artifact)
  - Optional supporting artifacts: `probe.md`, `from-corpus.md`, `jtbd.md`, `lens.md`, `scope.md`, `stakes.md`, `reframe.md`
- Sibling frame skills (all under the `frame` plugin namespace, all live): `frame:probe`, `frame:from-corpus`, `frame:problem-statement`, `frame:jtbd`, `frame:lens`, `frame:scope`, `frame:stakes`, `frame:reframe`. Composer invokes their Process conceptually when generating per-skill artifacts.
- Downstream sibling plugin: `approach` — receives `problem-statement.md` as input to `/approach:composer` Phase 1.

## Worked example

**Situation:** *"I'm thinking about whether to take on a new client. They want me to lead the AI transformation at their company. Six-figure budget. But the work would consume most of my next 6 months. Help me figure out what's actually going on here."*

### Phase 1 — Silent assessment

Signals identified:
- "Help me figure out what's actually going on" → `frame:probe` required (perception first)
- Decision-shaped ("whether to take on") → `frame:reframe` + `frame:stakes`
- High stakes (6 months, six figures, opportunity cost) → `frame:stakes` confirmed
- Multi-stakeholder unclear → `frame:jtbd` candidate, ask in intake
- No specific corpus → skip `frame:from-corpus`
- No specific lens → `frame:lens` skipped

Tentative plan: **probe → stakes → reframe → problem-statement** (4 skills incl. terminator).

### Phase 2 — Intake (3 questions used)

1. *"When you say 'help me figure out what's actually going on,' which is closest: (a) I don't know if I should do this; (b) I think I know but want to pressure-test; (c) deciding between this and other specific things; (d) something else?"*
2. *"What does next 6 months look like if you DON'T take this on — anything specific planned, or open?"*
3. *"Who else does this decision affect — partner, team, family, other clients?"*

User answers reveal: it's mode (a) — genuinely unsure. Next 6 months has 1 other commitment in play. Decision affects family + other clients.

Multi-stakeholder confirmed → add `frame:jtbd`. New plan: probe → jtbd → stakes → reframe → problem-statement (5 skills incl. terminator).

### Phase 3 — Checkpoint

```
PROPOSED:
1. frame:probe — perception (you signaled uncertainty about what's going on)
2. frame:jtbd — multi-stakeholder (family + other clients affected)
3. frame:stakes — high cost-of-error in both directions
4. frame:reframe — generate alternative framings for the decision
5. frame:problem-statement — universal terminator

Skipped: from-corpus (no corpus), lens (no specific lens), scope (boundaries clear)

Estimated 20-25 min.

WHAT NEXT?
  (a) Proceed
  (b) Skip to problem-statement only (light frame work, faster hand-off)
  (c) Hand off to /approach NOW with intake-only Polya 9
  (d) Ask more clarifying questions
```

User picks (a) — proceed.

### Phase 4 — Run skills with checkpoints

- Run `frame:probe`. Surfaces: "user is treating this as a money decision but it's actually an identity decision." Checkpoint: continue.
- Run `frame:jtbd`. Surfaces: "the other-clients JTBD is being suppressed." Checkpoint: continue.
- Run `frame:stakes`. Surfaces: "cost-of-yes is identity drift; cost-of-no is financial." Checkpoint: continue.
- Run `frame:reframe`. Surfaces: "current frame is 'capacity decision', alternatives include identity decision, optionality decision, relationship decision." Checkpoint: continue.

### Phase 5 — Terminator

Run `frame:problem-statement` consuming all prior artifacts. Polya 9 filled. Extraction-confidence: high (everything stated or inferred from probe).

### Phase 6 — Composition root

`frame-take-on-client-decision/composition.md` written with all the above.

### Phase 7 — Hand-off

```
Frame composition complete:
- folder: ~/.claude/contracts/frame-take-on-client-decision/
- problem-statement.md ready for /approach:composer
- 5 skills ran; extraction-confidence: high
- 0 unresolved assumptions

Suggested next:
  /approach:composer ./frame-take-on-client-decision/problem-statement.md
```

Composer stops. User invokes /approach when ready (or doesn't — sometimes the frame work alone is the answer).

## Scope boundary

`frame:composer` FRAMES; it does not select approaches, does not execute, does not auto-route. Its product is a frame artifact folder. The orchestrator (user) decides when to invoke `/approach:composer` next.

The one thing composer DOES route to: other `frame:*` skills within itself, via the Phase 4 inline adaptive check (e.g., probe surfaces a stakeholder → insert jtbd). This is intra-/frame routing only, not cross-plugin.
