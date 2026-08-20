STATUS: SESSION HANDOFF  
AUTHORITY: navigation/context only  
DO NOT treat this file as replacement for Decision Log, Research outputs, source docs, or design specs.

# Deck Agent Evaluation Research — Current Session Handoff

This file helps a new session find the authoritative records and current stopping point. If any statement here conflicts with an authoritative file, stop and follow the authoritative file; then repair this handoff separately.

## A. Project mission

Deck Agent is an AI system that turns source documents into presentation decks/PPTX through a pipeline including Input Adapters, Extractor, ContentPlanner, Deck IR, design/editor stages, and Exporters. This repository does not implement Deck Agent; it defines how to evaluate whether the generated decks are correct, useful, improving over time, and better than defensible baselines.

The evaluation workstream asks what should be measured, which evaluation method fits each target, what baseline answers which comparison question, how to detect regressions, and what evidence is strong enough for engineering or thesis claims.

Internal files under `01_source_docs/` are project inputs/current proposals, not external technical Source of Truth. Technical evaluation choices require external evidence and, where transfer to Deck Agent is uncertain, pilot data. Neither internal proposals nor this handoff validate a metric by themselves.

## B. Current research phase

- **RQ00 = ACCEPTED**
- **Wave 1 = COMPLETE**
  - RQ01
  - RQ06
  - RQ08
- **Wave 2A research = COMPLETE**
  - RQ02
  - RQ03
- **Wave 2A human/consistency review = COMPLETE**
- **Pilot execution = NOT STARTED**
- **Wave 2B = NOT OPENED**
  - RQ04
  - RQ05
- **RQ07 = NOT OPENED**

Work is paused before pilot execution because the source-quality track has no accepted fact/topic presence-detection method, while the slide-type track lacks concrete human-approved definitions for `teaching`, `catchup`, and `speaker_support`.

## C. Current mental model

### Source quality

```text
fact_identity / topic_key
        ↓
presence / coverage

gold_claim
        ↓
correctness / grounding
        ↓
unsupported
        ↓
hallucination / incorrectness / ambiguous

derived numeric transformations
        ↓
derived_number_queue
```

Key boundaries:

- Presence is not correctness.
- A fact that is mentioned with a wrong value is still present; correctness fails separately.
- Reverse MiniCheck using `gold_claim` must not be used as a presence detector.
- Derived numbers are routed for separate checking rather than automatically convicted as hallucination/incorrectness.

### `slide_type`

```text
same source
   ↓
teaching / catchup / speaker_support
   ↓
repeated runs

WITHIN-mode variation
vs
BETWEEN-mode variation
```

- Purpose + information selection are the semantic CORE.
- Word density, image ratio, layout, compression, and similar surface features are SECONDARY/DIAGNOSTIC.
- `speaker_notes` is currently P3/non-core.
- B0 is descriptive smoke/feasibility first.
- B1 confirmatory design is allowed only after B0 and human review.

## D. Critical accepted decisions

- **D-007** — Separate deterministic evaluation from judged/human evaluation; do not hide them in one score.
- **D-008** — Treat hallucination, incorrectness, and coverage gap as separate source-quality failures.
- **D-009** — Demonstrate ContentPlanner contribution with end-to-end evidence + ablation; component metrics are diagnostic only.
- **D-010** — Material-dependent quality needs per-instance criteria/probes; the creation method remains open.
- **D-011** — Measure a model-based evaluator's repeatability/noise floor before using it for regression tracking.
- **D-012** — Historical, naive, ablation, and external baselines answer different questions and do not replace one another.
- **D-013** — External comparison is optional/contextual and does not prove causal architectural contribution.
- **D-014** — Persist the reproducibility/run manifest now, including the plan/outline artifact.
- **D-015** — Coverage measures fact/topic presence; correctness is separate; present-but-wrong is not missing.
- **D-016** — Option C is the working pilot policy; report its categories separately and do not aggregate them.
- **D-017** — Purpose + information selection are `slide_type` CORE; surface features are diagnostic; notes remain P3/non-core.
- **D-018** — Merge A1 with B-1 when the intervention is identical; any separate ablation must ask a different causal question.
- **D-019** — Use SQ-P1…SQ-P4; SQ-P1 does not split hallucination from incorrectness, which belongs to SQ-P4.
- **D-020** — Run descriptive B0 before any B1 confirmatory design; current numerical thresholds remain candidates, not requirements.

Full rationale and authority live in `05_decisions/DECISION_LOG.md`.  
The handoff must not be used to reconstruct or modify a decision from memory.

## E. Current working policy for coverage

**Option C = working pilot policy, not final benchmark truth.**

```text
fact record
├── fact_identity / topic_key
├── gold_claim
└── mode_expectation
    ├── universal_core
    ├── mode_required
    ├── optional
    └── not_expected
```

Report coverage/diagnostic results separately; do not aggregate them or correctness into one score.

Unresolved design detail: `fact_identity` must be specific enough to distinguish the intended fact—period and scope may be part of identity—but must not include factual value in a way that turns presence into correctness. Do not resolve this detail inside the handoff.

## F. Evaluator and pilot status

- **MiniCheck = HOLD.**
- **SQ-P1 = BLOCKED before execution** because the presence component is not selected/frozen.
- **SQ-P2** only tests reverse MiniCheck as a candidate `correctly_supported_fact` / gold-claim-support signal.
- **SQ-P3** tests derived numeric transformation handling.
- **SQ-P4** tests hallucination vs incorrectness vs abstention/ambiguous behavior.

Source-quality sequence:

```text
SQ-P1
→ SQ-P2
→ SQ-P3
→ SQ-P4
→ human SQ review
```

Slide-type sequence:

```text
mode definition
→ Option C authoring
→ freeze B0
→ B0 smoke
→ human review
→ if warranted: design/freeze B1
→ B1
```

The two tracks may progress in parallel. Product-definition work for `slide_type` does not need to wait for the SQ track to finish.

## G. Active blockers and open questions

### Source-quality track

- No accepted method measures `fact_identity/topic_key` presence independently from correctness.
- Fact records and a manual two-label oracle are required.
- Presence, grounding, and number-routing components/configurations must be frozen separately.
- MiniCheck remains HOLD.
- **Q-016 — Fact/topic presence detection = ACTIVE.** It asks:

> Với một `fact_identity/topic_key` đã freeze, phương pháp nào xác định deck có đề cập fact đó hay không, kể cả khi paraphrase/compress hoặc nói sai value, mà không biến presence thành entailment/correctness?

Q-016 is an RQ02 focused follow-up, not a new RQ.

### Slide-type track

**Q-015 remains ACTIVE**, covering:

- concrete purpose of `teaching`;
- concrete purpose of `catchup`;
- concrete purpose of `speaker_support`;
- information-selection obligations;
- Option C category authoring.

## H. Next two workstreams

### TRACK A — SOURCE QUALITY

```text
Q-016
→ focused research/selection of presence method
→ oracle authoring
→ freeze SQ-P1
→ run only after human approval
```

Track A is evaluation research/engineering.

### TRACK B — SLIDE_TYPE PRODUCT DEFINITION

```text
product-definition workshop
→ concrete purpose for 3 modes
→ information-selection obligations
→ examples / non-examples
→ Option C fact-category authoring
→ freeze B0
```

Track B is product-definition work. Do not use literature to invent the semantics of the three modes.

## I. Files the new agent MUST read

Read in this exact order:

1. `AGENTS.md`
2. `CLAUDE.md`
3. `RESEARCH_STATUS.md`
4. `00_context/PROJECT_CONTEXT.md`
5. `00_context/EVALUATION_MISSION.md`
6. `00_context/SYSTEM_MAP.md`
7. `01_source_docs/SOURCE_INDEX.md`
8. `04_evidence/EVIDENCE_POLICY.md`
9. `05_decisions/DECISION_LOG.md`
10. `05_decisions/OPEN_QUESTIONS.md`
11. `03_research/WAVE2A_DECISION_GATE.md`
12. `03_research/cross_rq/SOURCE_COVERAGE_VS_SLIDE_TYPE.md`
13. `06_design/drafts/SLIDE_TYPE_BEHAVIOR_SPEC.md`
14. `07_experiments/pilot/PILOT_PLAN_WAVE2A.md`
15. `06_design/BASELINE_SPEC.md`

Read `03_research/RQ02/recommendation.md` and `03_research/RQ03/recommendation.md` only if deeper reasoning/evidence is needed. Do not reread every paper note by default.

## J. Hard constraints for the next session

- Do not run a pilot automatically.
- Do not open RQ04/RQ05/RQ07.
- Do not promote a decision automatically.
- Do not treat this handoff as technical Source of Truth.
- Do not treat internal source documents as external evidence.
- Do not treat MiniCheck as an accepted evaluator.
- Do not use reverse MiniCheck for presence coverage.
- Do not invent `slide_type` behavior from literature.
- Do not modify accepted D-001→D-020 unless the user explicitly requests it.
- Do not regenerate frozen criteria to fit observed output.
