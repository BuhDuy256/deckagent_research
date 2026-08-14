# Deck Agent Evaluation Research

Research project designing and building an evaluation framework for **Deck Agent**, an AI system that generates presentation decks (PPTX) from source documents. This repo does not contain Deck Agent's implementation — it defines how to measure whether Deck Agent is good, improving, and better than alternatives.

## Mandatory reading order (before any research task)

1. `00_context/PROJECT_CONTEXT.md`
2. `00_context/EVALUATION_MISSION.md`
3. `00_context/SYSTEM_MAP.md`
4. `01_source_docs/SOURCE_INDEX.md`
5. `04_evidence/EVIDENCE_POLICY.md`
6. The relevant RQ file in `02_research_questions/`
7. `05_decisions/DECISION_LOG.md`
8. `05_decisions/OPEN_QUESTIONS.md`

Current task: `RESEARCH_STATUS.md` says what's next. As of this setup, that's **RQ00** (audit the current evaluation proposal against FR/NFR/architecture) — not any of RQ01–08 yet.

## Research discipline — read this before writing any finding

- **Internal documents in `01_source_docs/` are PROJECT SOURCE, not Source of Truth and not external evidence.** They are AI-assisted formalizations of ideas the team/advisor discussed informally. Treat every claim, metric, threshold, or architecture choice in them as the team's current internal proposal — subject to being verified, challenged, refined, or rejected, not adopted by default. This includes the architecture doc's own §8 evaluation proposal.
- **External research is required**, not optional, when answering a research question. Internal docs only establish what the team currently intends to build and believes — they don't validate it. Go find literature, related open-source systems, and existing benchmarks/tests; don't limit research to the 5–6 internal files.
- **Never invent** a citation, paper, benchmark, dataset, repository, numerical result, or competitor/product capability. If an entity's name or existence can't be verified, mark it `UNVERIFIED ENTITY`, log it in `05_decisions/OPEN_QUESTIONS.md`, and do not use it as evidence. See `04_evidence/EVIDENCE_POLICY.md` for evidence levels (E1/E2/E3) and sourcing rules.
- **Label every substantive claim in research output as one of four types:**
  - `PROJECT SOURCE` — what an internal doc says.
  - `EXTERNAL EVIDENCE` — what a verifiable external source (paper/repo/docs) supports, with citation.
  - `INFERENCE` — a conclusion drawn by connecting evidence to Deck Agent's context; say explicitly that it's inference.
  - `RECOMMENDATION` — a proposed action; must trace `problem → evidence → reasoning → recommendation`, never stated as settled fact.
- **A working hypothesis is not a decision.** Watch for wording like "supersedes", "we will use", "the correct approach is", "rejected", "primary target" — if there's no logged, team-confirmed decision behind it, use hypothesis language instead ("current project hypothesis", "candidate", "open question", "to be validated").
- **Research does not automatically become design.** Exploratory research must not edit `06_design/` or add entries to `05_decisions/DECISION_LOG.md`. Research output goes to `03_research/`, and proposed (not yet accepted) conclusions go to `05_decisions/OPEN_QUESTIONS.md`. Only promote something into `06_design/`/`DECISION_LOG.md` when the user explicitly says a decision has been accepted.
- Requirement/component correctness (e.g. "does the exporter produce an openable file") vs. AI-quality evaluation (e.g. "is the ContentPlanner's slide split good") are different concerns — don't collapse them into one evaluator category. See `06_design/EVALUATION_FRAMEWORK.md` §2 for the Evaluation Target × Evaluation Method model used here instead of a flat TEST/METRIC/LLM-JUDGE/HUMAN classification.

## Language

**Vietnamese is the default writing language for research-facing documents** in this workspace (context docs, RQ files, research outputs, decision logs, design documents, prompt templates, README). Keep in English: paper titles, metric/benchmark/project names, code identifiers, schema field names (`ContentPlanner`, `Deck IR`, `source_ref`, `locked_by_user`, etc.), and any term where translation would lose meaning — introduce it once as `English term (giải thích ngắn tiếng Việt)`, then use the English term afterward.

## Domain vocabulary

- **Deck Agent pipeline**: Input Adapters → Extractor → ContentPlanner → Deck IR → DesignSystem/Editor Loop → Selection Resolver/Preview Renderer (extension-phase) → Exporters.
- **ContentPlanner**: per PROJECT SOURCE, described as the architecture's core/hardest component. Whether it deserves the most evaluation investment is a hypothesis pending RQ00/RQ01, not a settled fact — see `00_context/SYSTEM_MAP.md`.
- **Deck IR**: intermediate representation that separates planning from export.

See `00_context/TERMINOLOGY.md` for the full glossary, including the open `slide_type` naming question.
