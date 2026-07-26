# Assessed

A framework and product for assessing students and job seekers through **observed work with AI**,
rather than through timed recall exams or unstructured interviews.

Traditional assessment measures what a person can produce *without* AI. That is becoming both easy
to fake and decreasingly predictive of real performance. This project measures something harder to
fake: **how a person behaves when working alongside a fallible AI collaborator** — whether they
verify, whether they detect the error, whether they can say why they rejected it, and whether they
know when to defer.

## The framework

**[AI-Native Assessment Framework (ANAF) v1.0](docs/ANAF-v1.0.md)** — the specification.

Seven pillars, each communicating only with its neighbours, through six versioned contracts:

```
Assessment Specification  → ADO (Assessment Definition Object)
Assessment Orchestration  → AP  (Assessment Package)
Assessment Delivery       → IES (Interaction Event Stream)
Evidence Collection       → CEM (Candidate Evidence Model)
Competency Inference      → CAO (Competency Assessment Object)
Outcomes & Reporting      → SR  (Stakeholder Reports)
Governance & Evolution    → observes all six, read-only
```

Those six contracts are the platform APIs. An implementation is conformant if and only if it honors
them.

## Design commitments

- **Specification-driven, not code-driven.** New subjects, competencies, and report audiences are
  new specifications, not new code.
- **Evidence before scores.** Observation (Pillar 4) is separated from judgment (Pillar 5), so
  evidence can be shown to the candidate as fact, scoring models can be re-run against history, and
  an appeal can dispute the inference without disputing the record.
- **Not observed ≠ not competent.** Insufficient evidence is reported as insufficient evidence, never
  as a low score.
- **Every score cites its evidence.** An estimate without citations is not emitted.
- **Governance is out of the data path.** It can change specifications; it cannot touch a result.

## Status

Framework draft under review. Implementation plan to follow.

## Layout

```
docs/ANAF-v1.0.md    the framework specification
schemas/             JSON Schema for the six contracts  (to be generated from the spec)
```
