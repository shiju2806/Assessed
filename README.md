# Assessed

A framework and product for assessing students and professionals through **observed work with AI**,
rather than through timed recall examinations or unstructured interviews.

Traditional examinations measure what a student can produce *without* AI. That is becoming both easy
to fake and decreasingly predictive of real capability, because real work — in university, in
research, in every profession these students will enter — is now performed *with* AI in the loop.

This project measures something harder to fake: **how a person behaves when working alongside a
fallible AI collaborator** — whether they verify, whether they catch the error, whether they can say
why they rejected it, and whether they know when to defer.

Primary context is education: schools, colleges, and universities. Professional and hiring assessment
is the same engine under a different specification.

## The framework

**[AI-Native Assessment Framework (ANAF) v1.1](docs/ANAF-v1.1.md)** — the current specification.
([v1.0](docs/ANAF-v1.0.md) is retained for history.)

Seven pillars, each communicating only with its neighbours, through seven versioned contracts:

```
Assessment Specification  → ADO (Assessment Definition Object)
Assessment Orchestration  → AP  (Assessment Package)
Assessment Delivery       → IES (Interaction Event Stream)
Evidence Collection       → CEM (Candidate Evidence Model)
Competency Inference      → CAO (Competency Assessment Object)
Outcomes & Reporting      → SR  (Stakeholder Reports)
                          → LLR (Longitudinal Learner Record — lifetime memory)
Governance & Evolution    → observes all of them, read-only
```

Those contracts are the platform APIs. An implementation is conformant if and only if it honors them.

## Design commitments

- **Specification-driven, not code-driven.** New subjects, competencies, and report audiences are new
  specifications, not new code.
- **Realism.** The assessed situation must resemble how people actually work with AI. Evidence comes
  from what a student naturally does, not from meta-tasks invented for the grader. Recognition
  scaffolds — "pick which of these four claims is wrong" — are prohibited.
- **Evidence before judgment.** Observation is architecturally separated from inference, so evidence
  can be shown to a student as fact, scoring models can be re-run against history, and an appeal can
  dispute the inference without disputing the record.
- **Not observed ≠ not competent.** Insufficient evidence is reported as insufficient evidence, never
  as a low score.
- **Every score cites its evidence.** An estimate without citations is not emitted.
- **Detection is measured properly.** A student who challenges everything detects nothing. Error
  detection reports sensitivity and criterion separately.
- **Memory is semantic, not byte-level.** A lifetime learner record compresses through four tiers as
  it ages — growing more abstract and less identifying, which is what data-minimization law wants
  anyway. A full K–12 record fits in under a megabyte.
- **Governance is out of the data path.** It can change specifications; it cannot touch a result.

## Cost tiers

| Tier | Extraction | Cost / sitting | Use |
|---|---|---|---|
| `STRUCTURED` | Deterministic only | ~$0.15 – 0.40 | School examinations, formative, large cohorts |
| `FULL` | Deterministic + model-based | ~$2 – 7 | Higher education summative, certification, hiring |

`STRUCTURED` is not a degraded copy. Every extractor carrying the core differentiation — decisions,
detection, verification behavior — is deterministic. Only language analysis needs a model.

## Status

Framework draft under review. Eight open questions tracked in §11. Implementation plan to follow.

## Layout

```
docs/ANAF-v1.1.md    current framework specification
docs/ANAF-v1.0.md    superseded, retained for history
schemas/             JSON Schema for the seven contracts  (to be generated from the spec)
```
