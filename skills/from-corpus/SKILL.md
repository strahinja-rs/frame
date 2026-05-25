---
name: from-corpus
description: Extract a frame from a complex existing artifact — corpus of transcripts, brief, set of interviews, PDF, prior documents, repository, message thread. ALWAYS invoke this skill when the user wants to frame a problem that lives inside a body of material they can't fit into the conversation, or when "I have all this stuff and I'm not sure what the real problem is" matches the situation. Produces a From-Corpus artifact — corpus inventory, signal extraction (recurring themes, lexicalized terms, conspicuous silences, contradictions), problem-candidates surfaced from the material, and the recommended next frame skill. Reads the material directly per Direct-Use Rule. Skip when no complex artifact exists (use frame:probe on the situation directly), when the user has already done the corpus extraction and is presenting findings, or when the corpus is too small to warrant the overhead (just read it).
# description-style: directive + negative constraint (Seleznov)
# rationale: Many real frame situations live inside material the user hasn't fully processed — discovery transcripts, RFPs, multi-week message threads, prior team docs. Without a skill for corpus extraction, the user is left to either (a) summarize the material themselves and feed the summary (lossy) or (b) hope the rest of the frame skills will handle the corpus inline (they won't — they assume the situation is articulable inline). Directive style forces explicit corpus engagement before downstream framing.
---

<!--
ACTIVATION TESTS (for /skill:validate --bench):
1. SHOULD activate (knowledge work discovery): "I have 12 client interview transcripts and a brief; what's the real problem this work is pointing at?"
2. SHOULD activate (code review prep): "Here's a large message thread and several docs from a team meeting; help me frame what's actually being debated"
3. SHOULD NOT activate (no corpus): "I'm thinking about whether to take this client" → frame:probe or frame:composer
4. SHOULD NOT activate (corpus already summarized by user, source unavailable): "Here's my summary of the discovery — frame from this" → frame:probe on the summary, with low-confidence marker
5. BOUNDARY: "I have a doc, what do I do" (could be from-corpus if there's actually material to extract, could be probe if user just wants reflection — composer should disambiguate based on corpus size + complexity)
-->

# from-corpus

Extracts a frame from a complex existing artifact. The corpus is read **directly** (Direct-Use Rule), not via the user's summary of it. Surfaces what the corpus is *actually* pointing at — recurring themes, lexicalized terms, conspicuous silences, contradictions, and problem-candidates.

> See [PRINCIPLES.md](../../PRINCIPLES.md) for shared rules — particularly **§4 Direct-Use Rule** (load-bearing here), **§10 question-first**, **§7 knowledge-work primacy**.

## When to Use

- The situation lives inside material too large to fit in conversation context (transcripts, RFPs, message threads, repositories, multi-document briefs).
- "I have all this stuff and I'm not sure what the real problem is" — the user has the material but lacks a structured read of it.
- Discovery-style work — synthesizing findings from primary sources (interviews, conversations, observations).
- Composer-invoked when intake reveals a corpus referenced in the situation.

## When NOT to Use

- No complex artifact — the situation is inline / conversational → `frame:probe` or `frame:composer` directly.
- User has already done corpus extraction and is presenting findings → those findings are now the situation; `frame:probe` or other articulation skill on them.
- Corpus is small enough to just read (< 5 pages) — no overhead needed.
- The corpus is summaries already, and the source is unavailable — flag low-confidence and proceed with `frame:probe` on the summaries, but skip this skill (Direct-Use Rule failure).
- The user wants a *summary* of the corpus, not a *frame extracted from it* — that's `/research` or summarization tooling, not frame work.

## Process

The skill outputs a **From-Corpus artifact** — corpus inventory, signal extraction across 5 dimensions, problem-candidates surfaced, recommended next frame skill.

Question-first per [PRINCIPLES.md §10](../../PRINCIPLES.md#10-question-first--new-to-frame): the skill leads with two questions to the corpus —

- *"What does this material keep coming back to?"* (recurring themes, lexicalized terms)
- *"What is this material *not* saying that probably matters?"* (conspicuous silences, contradictions)

### 1. Corpus inventory

Before extracting signal, build an inventory:
- What files / sources are in scope (paths, names, types)
- Size / scope (line count, page count, message count)
- Date range (when the material was produced)
- Author(s) / source(s)
- Format (transcripts, prose, structured data, message thread)
- Confidence in the material (primary sources / derived summaries / hearsay)

Direct-Use Rule applies: read the actual files via Read / Grep / glob. If files are too large to read in full, sample representatively (first/last/middle, plus search for known anchor terms) and note what was sampled vs read in full.

### 2. Signal extraction across 5 dimensions

Read the corpus (in full or via representative sampling) and surface:

**(a) Recurring themes** — what does the material come back to repeatedly? Pattern across multiple sources / sessions / authors.

**(b) Lexicalized terms** — what vocabulary does the corpus consistently use? Concepts the source has *named* — those are recurring lived experiences worth surfacing. (e.g., in Žiška discovery: "auto-pilot", "ne stiže", "back and forth", "predaja", "toster sa krilima" — terms the team uses repeatedly).

**(c) Conspicuous silences** — what is the corpus NOT discussing that you'd expect it to? Topics that should be addressed in this kind of material but aren't (e.g., a strategy document with no mention of pricing; an operational review with no mention of attrition).

**(d) Contradictions** — places where the corpus contradicts itself, or different sources within the corpus contradict each other. These are diagnostic — they reveal where the real tensions are.

**(e) Asymmetric attention** — what does the corpus disproportionately attend to? What is it casual about that might warrant more attention?

### 3. Problem-candidates

Based on the signal extraction, surface 3-5 candidate problems the corpus is pointing at. Each candidate:
- Stated as a problem (not as a finding)
- Cited back to specific corpus signals (which themes / silences / contradictions support this candidate)
- Marked with confidence (high / medium / low) — how strongly the corpus supports this candidate

Candidates are NOT ranked / picked. Surfacing alternatives is the value; the user picks (or `frame:reframe` does the picking exploration).

### 4. Recommended next

Based on what the corpus surfaced:
- If candidates converge: recommend `frame:problem-statement` on the converged candidate
- If candidates diverge meaningfully: recommend `frame:reframe` to explore the alternatives
- If silences or contradictions suggest the user has been missing something: recommend `frame:probe` on the surfaced gap
- If multi-stakeholder dynamics surfaced: recommend `frame:jtbd`
- If specific lens (Cynefin / SWOT / etc.) seems particularly fit: recommend `frame:lens` with that lens

### 5. Output

Write to:
- **Within composer**: `<contracts-root>/frame-<slug>/from-corpus.md`
- **Standalone**: `<contracts-root>/from-corpus-<slug>.md`

Format:

```markdown
# From-Corpus: <slug>

## Corpus inventory

| File / source | Type | Size | Date | Author / source | Confidence in source |
|---|---|---|---|---|---|
| <path or name> | <transcript/prose/data/etc.> | <lines/pages/msgs> | <date> | <author/source> | <primary/derived/hearsay> |
...

## Reading method
- <Read in full | Representatively sampled — and what was sampled>

## Signal extraction

### Recurring themes
1. <theme>: <count of recurrences + which sources>
2. ...

### Lexicalized terms
- `<term>` — appears in <where>, meaning: <interpretation>
- ...

### Conspicuous silences
1. <expected topic that's absent>: <why this is conspicuous>
2. ...

### Contradictions
1. <Source A says X, Source B says ~X>: <interpretation of the tension>
2. <within-source contradiction>: ...

### Asymmetric attention
- Disproportionately attended: <what gets lots of airtime>
- Casual about: <what gets glossed despite likely importance>

## Problem candidates

### Candidate 1: <problem framing>
- Confidence: <high | medium | low>
- Supporting signals: <which themes / silences / contradictions support this>
- Implication: <what becomes operative if this is the real problem>

### Candidate 2: <problem framing>
[same structure]

[up to ~5 candidates]

## Recommended next
- <frame:problem-statement on Candidate X — convergence>
- <frame:reframe — divergence among candidates worth exploring>
- <frame:probe on surfaced gap — silence X warrants follow-up>
- <frame:jtbd — multi-stakeholder dynamics surfaced>
- <frame:lens with X — specific lens seems fit>

## Implications for the Problem Statement
- <how corpus extraction should shape Polya 9 fields downstream>

## Extraction-confidence
<low | medium | high — based on how much of the corpus was read directly vs sampled, and how strongly the signals converged>

## Assumptions
- <inferences when corpus was ambiguous>
- <interpretations of lexicalized terms>
- <or "none">
```

### 6. Stop

Skill is framing-only. The artifact surfaces what the corpus is pointing at; the user (or composer / downstream skills) decides which candidate to pursue.

## Anti-Patterns

- **Reading the user's summary instead of the corpus directly.** Direct-Use Rule violation. The user's summary is exactly the filter we're trying to surface — extracting from it inherits the filter.
- **Summarizing the corpus.** This skill is NOT summarization. It's frame extraction. A summary preserves the corpus's structure; frame extraction looks for what the corpus is *pointing at* (which is usually different from what it's *about*).
- **Picking the "real" problem.** Surfacing candidates is the deliverable. The user (or downstream skills) picks. Skill picking presumes information about user values that the skill doesn't have.
- **Skipping silences / contradictions.** These are the highest-signal outputs. A corpus extraction with only "recurring themes" is just frequency analysis. The diagnostic value is in what's *not* there and where things *don't add up*.
- **Lexicalized terms presented without interpretation.** Listing terms without saying what they reveal is data, not analysis. Each term gets a one-line interpretation.
- **Treating sampled reading as if it were full reading.** If only sampled, mark which sections were sampled vs full-read. Extraction-confidence is bounded by how much of the corpus was actually consumed.
- **Inflating extraction-confidence.** A corpus mostly sampled, with weak signal convergence, is low-confidence regardless of how many candidates were surfaced. Mark honestly.

## Rules

- The skill outputs a single From-Corpus artifact (markdown file); never executes, never picks the problem.
- Corpus inventory is mandatory before signal extraction.
- All 5 signal dimensions (themes, lexicalized terms, silences, contradictions, asymmetric attention) are populated; if a dimension yields nothing, mark as `none` with one-line rationale.
- 3-5 problem candidates is the target; candidates cite corpus signals.
- Recommended next is mandatory.
- Direct-Use Rule (PRINCIPLES.md §4) is load-bearing: corpus is read directly. Summary-only inputs are flagged low-confidence.
- When invoked from `frame:composer`, artifact lives at `<contracts-root>/frame-<slug>/from-corpus.md`.
- When invoked standalone, artifact lives at `<contracts-root>/from-corpus-<slug>.md`.
- `Extraction-confidence` and `Assumptions` fields are non-optional per PRINCIPLES.md §3.
- For corpora too large to read in full: representative sampling with explicit documentation of what was sampled.
- For corpora that require deep depth-on-one-input reading, prefer Codex (`codex exec`) as the worker.
- For corpora needing isolated context (so the main conversation isn't polluted by reading), prefer Claude sub-Agent (model: opus per PRINCIPLES.md §2).
- Question-first (PRINCIPLES.md §10): the artifact structure follows from the two anchor questions to the corpus.

## Key Files

- Output (standalone): `<contracts-root>/from-corpus-<slug>.md`
- Output (composer): `<contracts-root>/frame-<slug>/from-corpus.md`
- Sibling frame skills (all under `frame` plugin, all live): `frame:composer`, `frame:probe`, `frame:problem-statement`, `frame:jtbd`, `frame:lens`, `frame:scope`, `frame:stakes`, `frame:reframe`.
- Theoretical grounding: discourse analysis (what's said vs what's not said); qualitative coding methodologies (recurring themes); pragmatist sensemaking (Weick) — the corpus as material being-made-sense-of.
