# AI-Native Assessment Framework (ANAF) v1.1

**Status:** Draft for review
**Editor:** shiju2806
**Supersedes:** ANAF v1.0
**Scope:** A specification-driven framework for assessing students and professionals through observed
work with AI, rather than through timed recall examinations or unstructured interviews.

> **Primary context is education.** ANAF starts with student examinations — schools, colleges, and
> universities. Professional and hiring assessment is the same engine under a different
> specification, and is treated throughout as the secondary case.

---

## Changes in v1.1

| # | Change | Where |
|---|---|---|
| 1 | Mutations attach to **claims**, not items. Detection is measured with signal-detection theory: sensitivity and criterion reported separately. | §2.5, §5.5, §8.2 |
| 2 | **Self-report cannot independently establish evidence.** Strength capped without behavioral corroboration. | P4.R10 |
| 3 | **Timing signals** constrained by hard rule: never negative, never sole citation, excluded under pacing accommodations. | P4.R11, P5.R11 |
| 4 | `COMP.VERIFICATION` split into **disposition** and **capability**. Mutations carry `knowledge_prerequisite`. | §1.2.1, §2.5 |
| 5 | **Transferability is a per-competency registry field** with an evidence status, measured rather than asserted. | §1.2.1, §7.5 |
| 6 | **Assessment tiers** — `STRUCTURED` (deterministic extraction, low cost) and `FULL`. Structured is the primary education product. | §0.6, §4.4 |
| 7 | Extractors MUST declare **deterministic or model-based**. | P4.R12 |
| 8 | **Standardization level** as an explicit dial, with a path-invariance conformance suite. | §3.6, §7.6 |
| 9 | **Realism principle** elevated to a design commitment; recognition scaffolds prohibited. | §0.2, §2.5 |
| 10 | **Conducting institution** is a first-class record holder; third parties require candidate-initiated release. | §6.5 |
| 11 | **Consent capacity model** for minors. | §1.2.2, §6.5 |
| 12 | **Longitudinal Learner Record (LLR)** — seventh contract, lifetime memory with four-tier semantic compression, in a separate store. | §0.5, §8.7 |
| 13 | Longitudinal profiles are **trajectories, never merged scores**. Staleness decays confidence, not the estimate. | §8.7 |
| 14 | Memory **feeds forward into formative assessment only**; forbidden for summative and high-stakes. Item-exposure avoidance always permitted. | §8.7.5 |
| 15 | Canonical worked example is now a **school mathematics examination**. | §8 |

---

## 0. Preamble

### 0.1 The thesis

Traditional examinations measure what a student can produce **without** AI. That measurement is
becoming both easy to fake and decreasingly predictive of real capability, because real work — in
university, in research, in every profession these students will enter — is now performed *with* AI
in the loop.

ANAF measures something different and harder to fake: **how a person behaves when working alongside
a fallible AI collaborator.** Do they verify? Do they catch the error? Can they say why they
rejected it? Do they know when to defer?

The atomic observation is not "was the final answer correct." It is **the interaction record** — the
sequence of prompts, corrections, acceptances, rejections, and justifications that produced the
answer. Correctness is one signal among many, and often not the most informative one.

### 0.2 Design commitments

**Specification-driven, not code-driven.** Every assessment is constructed from versioned
specifications — Assessment Definition, Question Schema, Evidence Schema, Competency Schema, Report
Schema. The engine executes specifications; it does not hard-code domains, competencies, or scoring
logic.

| Property | Meaning |
|---|---|
| **Domain agnostic** | A new subject is a new specification, not a new codebase. |
| **Auditable** | Every score traces to evidence, every piece of evidence to an interaction, every interaction to a specification clause. |
| **Extensible** | New competencies or AI behaviors are additive; they do not restructure the architecture. |
| **Research-friendly** | One specification can evolve while others stay pinned, so changes are attributable. |

**Realism.** *(New in v1.1, and it outranks convenience.)* The assessed situation must resemble how
people actually work with AI. This has teeth:

- Evidence comes from **what the candidate naturally does** — challenging a claim, editing the line,
  re-running the calculation — not from meta-tasks invented for the grader's benefit.
- **Recognition scaffolds are prohibited.** "Select which of these four claims is flawed" measures
  recognition from a supplied list, which never occurs in real work and inflates detection scores
  for candidates who could not have found the flaw unaided. Detection MUST be evidenced by unprompted
  action against the flawed content.
- AI collaborators must be plausible partners: mostly right, occasionally wrong, willing to defend
  themselves — as real ones are.

Realism and cost-efficiency point the same direction more often than expected. Natural challenge
behavior is both more realistic *and* cheaper to detect than essay-grading a written justification.

**Evidence before judgment.** Observation (Pillar 4) is architecturally separated from inference
(Pillar 5), so evidence can be shown to a student as fact, scoring models can be re-run against
history, and an appeal can dispute the inference without disputing the record.

### 0.3 The pillars

```
                External Inputs
                      │
                      ▼
        Pillar 1 — Assessment Specification
                      │  Assessment Definition Object (ADO)
                      ▼
        Pillar 2 — Assessment Orchestration
                      │  Assessment Package (AP)
                      ▼
        Pillar 3 — Assessment Delivery
                      │  Interaction Event Stream (IES)
                      ▼
        Pillar 4 — Evidence Collection
                      │  Candidate Evidence Model (CEM)
                      ▼
        Pillar 5 — Competency Inference
                      │  Competency Assessment Object (CAO)
                      ▼                      ╲
        Pillar 6 — Outcomes & Reporting       ╲──▶  Longitudinal Learner Record (LLR)
                      │  Stakeholder Reports  ╱          [separate store]
                      ▼                      ╱
        Pillar 7 — Governance & Evolution ◀──
```

### 0.4 The adjacency rule

**Every pillar communicates only with adjacent pillars, and only through the named contract.**

A pillar may not reach backwards for context it was not given, and may not reach forwards to
influence how its output is consumed. If Pillar 5 needs a question's difficulty, that difficulty
must have been carried forward in the CEM — Pillar 5 does not query Pillar 2.

Two consequences, both load-bearing:

1. **Each contract must be self-sufficient.** This is why contracts carry provenance and redundant
   context. It is deliberate denormalization.
2. **Any pillar can be replaced wholesale** — including by a human, or a competitor's engine — as
   long as it honors the contract on both sides.

**Two exceptions, both narrow.** Pillar 7 is not in the data path; it observes all contracts
read-only and acts only by changing specifications (§7). The LLR is a store, not a pillar: written
by Pillar 5, read by Pillar 6, governed by Pillar 7 (§8.7).

### 0.5 The seven contracts

```
Assessment Definition Object  (ADO)   ↓
Assessment Package            (AP)    ↓
Interaction Event Stream      (IES)   ↓
Candidate Evidence Model      (CEM)   ↓
Competency Assessment Object  (CAO)   ↓ ──▶ Longitudinal Learner Record (LLR)
Stakeholder Reports           (SR)
```

**These are the platform APIs.** An implementation is conformant if and only if it honors them.

### 0.6 Assessment tiers

Education at national scale and hiring at boutique scale have economics that differ by two orders of
magnitude. One engine serves both through a declared tier.

| Tier | Extraction | Cost / sitting | Primary use |
|---|---|---|---|
| **`STRUCTURED`** | Deterministic extractors only. Evidence from event structure: decisions, tool use, challenges, edits, exposure patterns. | ~$0.15 – 0.40 | School examinations, formative assessment, practice, large-cohort summative. |
| **`FULL`** | Deterministic + model-based extractors. Adds written reasoning analysis and open-ended artifact grading. | ~$2 – 7 | Higher education summative, certification, professional and hiring assessment. |

`STRUCTURED` is **not a degraded copy of `FULL`.** It keeps the entire core of what makes ANAF
different — every accept/reject/modify/ignore decision scored against ground truth, sensitivity and
criterion, every verification action that leaves a trace. What it gives up is language-mediated
evidence: quality of written explanation and open-ended artifact grading.

The tier MUST be declared in the ADO and MUST appear in every report. A `STRUCTURED` assessment
reports `INSUFFICIENT_EVIDENCE` for competencies it cannot observe (P5.R4) rather than inventing a
number — so the tier cannot silently degrade quality. Pillar 7 measures both tiers against the same
validity program (§7.5).

### 0.7 How to read a pillar section

Seven parts, in order: **Purpose** · **Inputs** · **Responsibilities** (`Pn.Rm`) · **Internal
Components** · **Outputs** · **Interfaces** · **Extension Points** (`Pn.Xm`).

Conformance language follows RFC 2119: **MUST**, **SHOULD**, **MAY**.

---

## Pillar 1 — Assessment Specification

### 1.1 Purpose

**Describe what assessment should exist.** Not generate it. Not deliver it. Simply define it.

Pillar 1 is declarative. Its output is a complete, validated, human-auditable description of an
assessment's intent — domain, competencies, the evidence that would constitute proof of them, and
the policies under which it runs.

**Out of scope:** producing questions or scenarios, selecting AI models, scheduling, anything
candidate-specific.

**Why separate.** If specification and generation are fused, you cannot answer "was this a fair
examination?" independently of "was this a good question?" Separating them makes intent reviewable
*before* content exists, and makes generation reproducible against a fixed target.

### 1.2 Inputs

| Input | Source | Required | Notes |
|---|---|---|---|
| **Domain** | Author / catalog | Yes | Mathematics, Physics, History, Biology, Law, Medicine, Programming, … MUST resolve to a registered Domain Pack (P1.X1). |
| **Curriculum context** | Standards body, institution | Education use | Year/grade, unit, learning outcomes, standards, objectives. |
| **Role context** | Employer, job description | Professional use | Role title, seniority, job family. |
| **Assessment blueprint intent** | Author | Yes | Tier, difficulty target, duration, adaptivity, question count, question types, interaction patterns, pass criteria. |
| **Competency selection** | Competency Registry | Yes | Which competencies, at what weight, to what level. |
| **AI configuration intent** | Author | Yes | Allowed AI behaviors, mutation classes, challenge policy, hint policy, standardization level. |
| **Governance policies** | Pillar 7 | Yes | Proctoring level, accessibility, accommodations, privacy class, consent capacity model, retention schedule. |

Curriculum context and role context are alternative fillings of one slot: *what body of external
expectation is this assessment accountable to?* An implementation MUST support curriculum context
and SHOULD support role context. Everything downstream is identical.

#### 1.2.1 The Competency Registry

Competencies are registry entries with stable IDs, so a score means the same thing across
assessments, subjects, and years.

```yaml
competency_id: COMP.VERIFICATION_DISPOSITION
name: Verification Disposition
definition: >
  The candidate attempts to establish the truth of a claim by means independent
  of the source that made it, proportionate to the stakes.
observable_indicators:
  - initiates a check without being prompted
  - seeks a source independent of the one that made the claim
  - checks a surprising result before acting on it
anti_indicators:
  - restates the AI's justification as their own
  - accepts a material claim with no check
proficiency_scale:
  - { level: 1, descriptor: "Checks only when prompted, and only by re-reading." }
  - { level: 2, descriptor: "Checks independently when a claim is surprising." }
  - { level: 3, descriptor: "Checks routinely, choosing a method independent of the source." }
  - { level: 4, descriptor: "Checks proportionately — calibrates effort to stakes and risk." }
transferability:                            # NEW in v1.1
  claim: cross_domain
  evidence_status: under_study              # asserted | under_study | validated | refuted
  coefficient: null                         # populated by Pillar 7 when validated
  study_ref: null
parent: COMP.EPISTEMIC
version: 2.0.0
```

**Transferability is measured, not asserted.** *(v1.1)* Whether a competency travels across subjects
varies enormously — knowing concurrency tells you nothing about tort law, but how someone decides
what to delegate to an AI plausibly travels everywhere. Each entry declares its claim and its
evidence status, and Pillar 7 either earns the claim or downgrades it (§7.5). Reports MUST surface
the status: an estimate marked `asserted` or `under_study` is labelled domain-specific.

This is also why education is the right starting context. A school gives you the same student across
many subjects and many years — the natural experiment that settles transferability. A single
cohort's data answers it as a byproduct of normal operation. Hiring never can; you see each
candidate once, in one domain.

**Verification is split.** *(v1.1)* v1.0's single `COMP.VERIFICATION` conflated two things:

- `COMP.VERIFICATION_DISPOSITION` — *do they attempt to check?* More trait-like, plausibly transferable.
- `COMP.VERIFICATION_CAPABILITY` — *are their checks sound?* Domain-bound; you cannot verify what you
  do not understand.

The conflation had a serious consequence: it read **domain knowledge gaps as character deficits**,
which falls hardest on students from less-resourced backgrounds. That is an adverse-impact problem
hiding inside a competency definition. Pillar 5 MUST condition capability estimates on demonstrated
domain competence (§5.4, Sufficiency Gate), using the `knowledge_prerequisite` carried on each
mutation (§2.5).

**ANAF Core Competency Set v2.0:**

- `COMP.CONCEPTUAL` — Conceptual Understanding *(domain-bound)*
- `COMP.REASONING` — Reasoning
- `COMP.VERIFICATION_DISPOSITION` — Verification Disposition
- `COMP.VERIFICATION_CAPABILITY` — Verification Capability *(domain-bound)*
- `COMP.ERROR_DETECTION` — Error Detection *(reported as sensitivity + criterion, §5.5)*
- `COMP.AI_COLLABORATION` — AI Collaboration *(claim: cross-domain)*
- `COMP.DECISION` — Decision Making Under Uncertainty
- `COMP.ETHICS` — Ethical Judgment
- `COMP.COMMUNICATION` — Explanation & Justification *(`FULL` tier only)*

#### 1.2.2 Consent capacity model *(new in v1.1)*

Most candidates in the primary market are minors. Consent cannot be modelled as a single adult
signature.

```yaml
consent_capacity:
  subject_age_band: UNDER_13 | 13_TO_15 | 16_TO_17 | ADULT
  release_authority: [GUARDIAN, INSTITUTION]     # who may authorize third-party release
  subject_rights: [VIEW, APPEAL, ANNOTATE]       # always held by the subject, at every age
  transition_age: 18                             # jurisdiction-set; authority passes to subject
  jurisdiction: [UK]
  regime_refs: [UK_GDPR, DPA_2018, KEEPING_CHILDREN_SAFE]
```

Rules:
- The subject holds **view, appeal, and annotate** rights at every age. These are never delegated.
- **Release authority** for third parties sits with guardian and institution below the transition
  age, passing to the subject at it.
- Jurisdictional regimes disagree (FERPA and COPPA in the US, GDPR Art. 8 in the EU, and national
  variation within it). The regime set MUST be explicit in the ADO, not assumed by the
  implementation.

### 1.3 Responsibilities

- **P1.R1** — MUST resolve all inputs into a single self-contained Assessment Definition Object.
- **P1.R2** — MUST validate that every targeted competency exists in the registry at a pinned version.
- **P1.R3** — MUST validate **evidence sufficiency**: for every targeted competency, the blueprint
  MUST specify at least one interaction pattern capable of producing observable evidence for it *at
  the declared tier*. A competency this assessment cannot observe MUST NOT be targeted. This is the
  most important validation in Pillar 1 — it is what stops the system reporting confident scores for
  things it never watched.
- **P1.R4** — MUST attach governance policy by reference *and* resolved value, so downstream pillars
  need not consult Pillar 7.
- **P1.R5** — MUST assign an immutable versioned `ado_id`. An ADO is never edited; a change produces
  a new version.
- **P1.R6** — MUST record provenance for every field: who or what set it, when, from which source.
- **P1.R7** — MUST be deterministic. Same inputs produce a byte-identical ADO modulo timestamps and IDs.
- **P1.R8** — MUST reject, not silently repair, an under-specified or contradictory blueprint.
- **P1.R9** *(v1.1)* — MUST declare the assessment tier, and MUST NOT target `FULL`-only competencies
  in a `STRUCTURED` assessment.
- **P1.R10** *(v1.1)* — MUST resolve the consent capacity model, including subject age band and
  jurisdictional regime set.

### 1.4 Internal Components

| Component | Responsibility |
|---|---|
| **Intent Intake** | Accepts authored input (UI, YAML, API); normalizes to canonical form. |
| **Domain Resolver** | Binds `domain` to a Domain Pack; pulls topic taxonomy, notation rules, difficulty anchors, domain-appropriate mutation classes. |
| **Curriculum / Role Mapper** | Maps external standards or job descriptions to internal objectives and competency IDs. Emits an explicit, auditable mapping table. |
| **Competency Resolver** | Resolves IDs at pinned versions; expands tree nodes; checks tier compatibility. |
| **Evidence Planner** | For each targeted competency, selects interaction patterns and expected-evidence descriptors that would demonstrate it *at the declared tier*. Produces the Evidence Blueprint and makes P1.R3 checkable. |
| **Blueprint Composer** | Assembles the four blueprints; allocates question counts and weights. |
| **Policy Binder** | Resolves governance references to concrete values: retention schedule, proctoring mode, accommodations, consent capacity. |
| **Specification Validator** | Runs the full validation suite; produces a per-rule pass/fail report. |
| **ADO Serializer** | Emits the signed, versioned ADO. |

### 1.5 Outputs

1. **Assessment Blueprint** — tier, shape, duration, adaptivity, pass criteria.
2. **Competency Blueprint** — what is measured, at what weight, to what target level.
3. **Question Blueprint** — how many questions of what type, at what difficulty, covering which
   topics, competencies, and mutation classes.
4. **Evidence Blueprint** — what observable behaviors count as evidence for each competency.

### 1.6 Interface

**Produces: Assessment Definition Object (ADO).** Schema in §8.1.

```
POST   /v1/specifications                 -> create draft ADO from intent
POST   /v1/specifications/{id}/validate   -> validation report
POST   /v1/specifications/{id}/publish    -> freeze, sign, version
GET    /v1/specifications/{id}
GET    /v1/competencies                   -> registry listing (with transferability status)
```

**Consumed by:** Pillar 2 only.

### 1.7 Extension Points

- **P1.X1 — Domain Pack.** Topic taxonomy, notation/rendering, difficulty anchors, domain-appropriate
  mutation classes. Adding "A-Level Chemistry" is a Domain Pack, not a code change.
- **P1.X2 — Competency Registry Provider.** Institutions may supply their own registry (a national
  curriculum framework, an employer competency model) if entries satisfy the registry schema.
- **P1.X3 — Standards Importer.** Ingests an external standards document (national curriculum, IB,
  Common Core, a professional syllabus) and emits objective mappings.
- **P1.X4 — Validation Rule Plugin.** Institution-specific rules run alongside the core suite.
- **P1.X5 — Blueprint Template.** Reusable parameterized blueprints ("Year 10 end-of-unit
  mathematics, structured tier").
- **P1.X6 — Consent Regime Module.** Jurisdiction-specific capacity and release rules.

---

## Pillar 2 — Assessment Orchestration

### 2.1 Purpose

**Generate the assessment from the blueprint.** Pillar 2 turns declarative intent into concrete,
validated content: questions, scenarios, expert solutions, and — distinctively — the *deliberate
flaws* the AI collaborator will exhibit.

**Out of scope:** knowing who the candidate is, running sessions, scoring.

**Why separate.** Content generation is the least trustworthy step in the pipeline — it is where a
model is most likely to produce something ambiguous, wrong, or unfair. Isolating it behind a
validation gate lets the rest of the system assume its input is sound.

### 2.2 Inputs

- **Assessment Definition Object** — the sole functional input.
- **Item Bank** (optional) — previously generated, calibrated items available for reuse.
- **Generation Model Configuration** — models, versions, decoding parameters. Pinned and recorded.

### 2.3 Responsibilities

- **P2.R1** — MUST satisfy every constraint in the Question Blueprint: count, type mix, difficulty
  distribution, topic and competency coverage, mutation mix.
- **P2.R2** — MUST produce for every question an **Expert Solution** that is independently correct
  and does not depend on the AI collaborator's output.
- **P2.R3** — MUST produce, for every claim carrying a mutation, a **ground-truth flaw record**
  (§2.5). Without it the claim is unscoreable and the item MUST be rejected.
- **P2.R4** — MUST calibrate difficulty so parallel forms are psychometrically equivalent within a
  stated tolerance.
- **P2.R5** — MUST validate every item for ambiguity, duplication, invalid mutation, cultural and
  linguistic bias, reading level appropriate to the age band, and accessibility.
- **P2.R6** — MUST NOT emit an item that fails validation. Failures are quarantined with a reason.
- **P2.R7** — MUST record full provenance: generating model and version, prompt template and version,
  seed, timestamp, human reviewer.
- **P2.R8** — MUST support human-in-the-loop review as a publication gate where policy requires it.
- **P2.R9** — MUST NOT include candidate-identifying information. A package is candidate-agnostic.
- **P2.R10** — MUST emit machine-readable **Expected Evidence** and **Scoring Rubric** per item, so
  Pillars 4 and 5 need no access to Pillar 1.
- **P2.R11** *(v1.1)* — MUST decompose every AI response into **discrete claims**, and MUST meet the
  blueprint's sound/flawed claim ratio (§2.5).
- **P2.R12** *(v1.1)* — MUST NOT generate recognition scaffolds. Items MUST NOT present the candidate
  with an enumerated list of candidate flaws to select from (§0.2, Realism).

### 2.4 Internal Components

| Component | Responsibility |
|---|---|
| **Question Generator** | Produces the stem, prompt, and required assets from the blueprint slot. |
| **Scenario Generator** | Produces the surrounding context — case, dataset, codebase, document, vignette. Reusable across items. |
| **Expert Solution Generator** | Gold-standard solution and reasoning trace. MUST be generated independently of the Mutation Engine (different call, ideally different model) to avoid correlated error. |
| **Claim Decomposer** *(v1.1)* | Splits the AI's response into discrete, individually adjudicable claims. |
| **AI Mutation Engine** | Injects deliberate flaws into selected claims. See §2.5. |
| **Difficulty Calibrator** | Estimates item and claim difficulty; enforces the blueprint's distribution. |
| **Validation Engine** | Ambiguity, duplication, mutation validity, bias, reading level, accessibility, solvability. |
| **Item Bank** | Calibrated items with usage statistics and exposure control. |
| **Package Assembler** | Sequences items, attaches adaptation rules, binds rubrics and expected evidence, serializes the package. |

### 2.5 The AI Mutation Engine

This is what makes ANAF an *AI-native* assessment rather than an examination with a chatbot attached.

**A mutation is a deliberate, characterized, ground-truthed flaw in AI output.**

#### 2.5.1 Claims, not items *(changed in v1.1)*

In v1.0 a mutation attached to an item, which broke in two ways.

**Arithmetically.** An 8-item assessment at 25% control gives you *two* clean items. A false-alarm
rate estimated from n=2 can only be 0, 0.5, or 1 — you cannot compute a usable decision criterion
from that.

**Conceptually.** Real AI output is mixed: mostly sound with something wrong in the middle. An
assessment where each response is wholly right or wholly wrong is unrealistic (§0.2) and teaches
candidates to make a single global trust judgment rather than a local one.

**In v1.1 mutations attach to claims.** Every AI response is decomposed into discrete claims, each
independently sound or flawed. A 6-item examination then carries 30–40 claim-level detection
opportunities. Default sound:flawed ratio is **60:40**, declared in the blueprint.

The candidate is never told which claims are which, and — per P2.R12 — is never shown the claim
enumeration. Decomposition is an internal scoring structure, not a candidate-facing artifact.
Detection is evidenced by unprompted action against the flawed claim: challenging it, correcting it,
or re-deriving it.

#### 2.5.2 Sensitivity and criterion

Claim-level granularity makes signal detection theory applicable, which fixes a scoring bug in v1.0.

A candidate who challenges everything has a perfect hit rate and a terrible false-alarm rate. v1.0
would have scored them highly on error detection. They are not detecting anything; they are
indiscriminate.

`COMP.ERROR_DETECTION` MUST therefore be reported as **two numbers**:

- **Sensitivity (d′)** — how well they distinguish flawed from sound claims.
- **Criterion (c)** — how readily they challenge, independent of accuracy. Reported as a
  disposition, not a score: `CREDULOUS` · `CALIBRATED` · `SKEPTICAL` · `INDISCRIMINATE`.

Over-suspicion becomes a *finding* — pedagogically useful, and precisely the thing a teacher wants
to know — rather than a high score.

#### 2.5.3 Mutation taxonomy

| Class | Description | Detectable by |
|---|---|---|
| `ARITHMETIC` | Numeric slip in an otherwise sound method. | Recomputation. |
| `REASONING` | Invalid inferential step; conclusion does not follow. | Following the logic. |
| `ASSUMPTION` | Unstated or unwarranted premise smuggled in. | Interrogating premises. |
| `INTERPRETATION` | Misreads the question, the data, or the source. | Re-reading the prompt. |
| `CITATION` | Attributes a claim to a source that does not support it. | Checking the source. |
| `SCOPE` | Answers a neighboring question rather than the one asked. | Comparing answer to ask. |
| `OVERCONFIDENCE` | Correct content, unwarranted certainty on a genuinely uncertain point. | Calibration judgment. |
| `OMISSION` | Materially incomplete; a required case or caveat is missing. | Completeness checking. |
| `ETHICAL` | Proposes an efficient but impermissible course of action. | Ethical judgment. |
| `SOUND` | No flaw. The control condition. | — |

#### 2.5.4 The mutation record

```yaml
mutation_id: MUT-8823
claim_ref: CLM-3                  # which claim in the AI response
class: INTERPRETATION
severity: material                # cosmetic | material | critical
location: { span: "setup, sentence 2", anchor: "we need fencing on all four sides" }
correct_value: "only three sides need fencing; the river bounds the fourth"
detection_difficulty: 0.48        # 0..1, calibrated
knowledge_prerequisite:           # NEW in v1.1 — see §1.2.1
  objectives: [LO.MATH.OPT.2]
  minimum_level: 2
  note: >
    A candidate who has not met LO.MATH.OPT.2 cannot be expected to detect this.
    Pillar 5 MUST NOT count a miss here against VERIFICATION_CAPABILITY unless
    domain competence at level 2 is independently evidenced.
rationale: >
  Misreads the problem's boundary condition. The subsequent algebra and arithmetic
  are correct, which makes this detectable only by re-reading the question — not by
  checking the working.
```

`knowledge_prerequisite` is what prevents the system from reading ignorance as carelessness.

### 2.6 Outputs

**Assessment Instance** — a materialized, candidate-agnostic assessment: ordered items, scenarios,
claim decompositions, adaptation rules, rubrics, expected evidence, AI behavior configuration, and
the sealed answer key.

The **answer key is a separately encrypted section**. Pillar 3 receives the package but MUST NOT be
able to read it; only Pillars 4 and 5 hold that grant. The delivery surface — the part exposed to
the candidate — never holds the answers.

### 2.7 Interface

**Produces: Assessment Package (AP).** Schema in §8.2.

```
POST /v1/packages                      { ado_id } -> generate (async)
GET  /v1/packages/{id}                 -> package (key section redacted by default)
GET  /v1/packages/{id}/validation      -> per-item validation report
POST /v1/packages/{id}/review          -> human reviewer decision
POST /v1/packages/{id}/publish         -> freeze and sign
GET  /v1/items?competency=&difficulty= -> item bank query
```

**Consumed by:** Pillar 3 (delivery view), Pillars 4 and 5 (key view).

### 2.8 Extension Points

- **P2.X1 — Generator Plugin.** Alternative question and scenario generators per domain or item type.
- **P2.X2 — Solution Verifier.** Domain-specific formal verification of the expert solution: a CAS for
  mathematics, a test runner for programming, a rules engine for law. Where one exists it SHOULD gate
  publication.
- **P2.X3 — Mutation Class.** New classes with their own generation and validity checks.
- **P2.X4 — Calibration Model.** Swap the psychometric model (IRT 1PL/2PL/3PL, Elo, learned).
- **P2.X5 — Validation Rule Plugin.** Institutional style, reading level, jurisdictional accuracy.
- **P2.X6 — Item Bank Provider.** Bring an existing institutional bank.
- **P2.X7 — Claim Decomposition Strategy** *(v1.1)*. Domain-appropriate rules for what counts as a
  discrete claim.

---

## Pillar 3 — Assessment Delivery

### 3.1 Purpose

**Execute the assessment.** Pillar 3 runs a live session: authenticates the candidate, presents
items, hosts the AI collaborator, enforces timing and adaptation, and — its most important
obligation — **emits a complete, faithful, tamper-evident record of everything that happened.**

**Out of scope:** interpreting anything. Pillar 3 forms no judgment about quality, correctness, or
competence.

**Why separate.** Recording and interpreting have opposite failure modes. A recorder that interprets
discards what it deems irrelevant — and in ANAF the discarded material (the hesitation, the abandoned
prompt, the re-read) is often the signal. Keeping Pillar 3 judgment-free guarantees the evidence
layer gets everything.

### 3.2 Inputs

- **Assessment Package** (delivery view, key sealed).
- **Candidate identity and entitlement.**
- **Accommodation profile** — extra time, screen reader, language, input modality.
- **Session policy** — proctoring level, standardization level, permitted resources, break rules.

### 3.3 Responsibilities

- **P3.R1** — MUST authenticate to the proctoring level required by policy.
- **P3.R2** — MUST present items exactly as packaged, applying accommodations without altering
  construct-relevant content.
- **P3.R3** — MUST host the AI collaborator so that it exhibits *only* packaged behaviors and
  mutations. Free-running model behavior on assessed content is a conformance failure.
- **P3.R4** — MUST emit an event for **every** candidate-observable and candidate-generated action,
  including significant non-actions (idle, focus loss, re-reads).
- **P3.R5** — MUST emit a **tamper-evident**, append-only, hash-chained stream with monotonic
  sequence numbers and server-authoritative timestamps.
- **P3.R6** — MUST enforce timing and adaptation deterministically, logging every adaptation decision
  *with its trigger*.
- **P3.R7** — MUST degrade safely. Network loss, tab crash, or client failure MUST NOT lose committed
  events or silently truncate. Gaps MUST be explicitly marked, never elided.
- **P3.R8** — MUST NOT reveal correctness, mutation presence, or score during the session unless the
  package configures formative feedback.
- **P3.R9** — MUST record the exact AI responses shown, so Pillar 4 interprets against what the
  candidate actually saw.
- **P3.R10** — MUST collect required Reasoning Checkpoint responses at their triggers.
- **P3.R11** *(v1.1)* — MUST record, for each AI response, the claim decomposition boundaries as
  rendered, so Pillar 4 can attribute a challenge to a specific claim by span.

### 3.4 Internal Components

| Component | Responsibility |
|---|---|
| **Session Manager** | Lifecycle: create, start, pause, resume, submit, abandon, expire. Owns state and recovery. |
| **Identity & Authentication** | Verification to the required assurance level. |
| **Proctoring Controller** | Applies the configured level (§3.5); emits integrity signals as observations, never verdicts. |
| **Presentation Layer** | Renders items, scenarios, assets; applies accommodations. |
| **AI Conversation Engine** | Hosts the collaborator, constrained to packaged persona, capabilities, hints, and claim-level mutations. See §3.6. |
| **Timer & Pacing Controller** | Per-item and whole-session timing, extensions, breaks. |
| **Adaptive Controller** | Next-item selection per packaged rules; logs decision plus trigger. |
| **Interaction Manager** | Captures the raw surface — edits, selections, scroll/focus, tool invocations. |
| **Checkpoint Prompter** | Injects Reasoning Checkpoints at triggers; captures responses. |
| **Claim Span Tracker** *(v1.1)* | Maps candidate challenges and edits onto claim boundaries in the rendered response. |
| **Event Emitter** | Sequences, hashes, signs, buffers, durably ships. |

### 3.5 Proctoring levels

A policy dial set in Pillar 1, applied here, audited by Pillar 7. Ordered, each a superset:

| Level | Description | Signals |
|---|---|---|
| `L0_NONE` | Practice / formative. | Interaction only. |
| `L1_SOFT` | Integrity nudges, no surveillance. | Focus/blur, paste, elapsed time. |
| `L2_BEHAVIORAL` | Behavioral analytics, no media capture. | + response-latency profile, navigation patterns. |
| `L3_MEDIA` | Recorded session. | + webcam / screen / audio capture. |
| `L4_LIVE` | Live human proctor. | + proctor annotations. |

Binding at every level:
- Proctoring signals are **observations, never accusations**. Adjudication belongs to Pillar 7.
- The level in force MUST be disclosed before the session and recorded in the stream.
- For candidates below the age of majority, `L3_MEDIA` and above MUST have explicit guardian consent
  recorded in the ADO's consent block.

### 3.6 The AI Conversation Engine

The engine hosts a **scripted-but-conversational** collaborator: natural enough to be a realistic
partner, controlled enough that two candidates receive equivalent treatment.

- **R-A. Mutation fidelity.** Packaged mutations MUST appear in the specified claims, at their
  specified locations and severities.
- **R-B. Defense under challenge.** On challenge, the AI follows the packaged `challenge_policy`:
  - `CONCEDE_IMMEDIATELY` — admits on any challenge.
  - `CONCEDE_ON_EVIDENCE` — admits only on a specific, correct refutation. **Default**; it is the
    condition that distinguishes assertion from verification.
  - `DEFEND_ONCE` — pushes back once, then concedes on evidence.
  - `NEVER_CONCEDE` — maintains position. Tests conviction under social pressure.
- **R-C. Hint policy.** Hints are packaged, tiered, and logged with their cost. An unlogged hint
  corrupts the evidence record.
- **R-D. Non-leakage.** Responses MUST NOT reveal the flaw, the expert solution, or that a mutation
  exists. A post-generation guard MUST enforce this.
- **R-E. Full fidelity logging.** Every AI turn logged verbatim with model, version, latency, claim
  boundaries, and which packaged behavior it realized.
- **R-F. Off-script containment.** If driven outside packaged bounds, deflect within persona and emit
  `AI_DEFLECTION` rather than improvising assessed content.

#### 3.6.1 Standardization level *(new in v1.1)*

Naturalism and equivalence trade against each other, and the right point differs by stakes. This is
a dial independent of proctoring:

| Level | Conversational surface | Claim-bearing turns | Use |
|---|---|---|---|
| `S1_FREE` | Free-running. | Free-running. | Practice only. |
| `S2_PINNED_CLAIMS` | Free-running. | Pinned text. | Formative, low-stakes summative. |
| `S3_PINNED_TURNS` | Constrained templates. | Pinned text. | High-stakes summative, certification. |

**Equivalence is required on evidence-bearing turns, not on the whole conversation.** Naturalism
lives in the chat; standardization lives in the assessed content. §7.6's path-invariance suite makes
this a measured property rather than an aspiration.

### 3.7 Outputs

**Interaction Event Stream** — the complete, ordered, hash-chained record. Every click. Every prompt.
Every correction. Every hesitation.

### 3.8 Interface

**Produces: Interaction Event Stream (IES).** Schema in §8.3.

```
POST /v1/sessions                  { package_id, candidate_id } -> session
POST /v1/sessions/{id}/start
GET  /v1/sessions/{id}/next-item
POST /v1/sessions/{id}/events      -> client-observed batch (server re-stamps)
POST /v1/sessions/{id}/ai-turn     -> candidate message to collaborator
POST /v1/sessions/{id}/submit
GET  /v1/sessions/{id}/stream      -> IES (authorized consumers only)
```

**Consumed by:** Pillar 4.

### 3.9 Extension Points

- **P3.X1 — Proctoring Provider.** Third-party proctoring at L3/L4.
- **P3.X2 — Identity Provider.** SSO, school roster, national ID, biometric.
- **P3.X3 — AI Model Provider.** Swap the collaborator's model, subject to the AI-behavior conformance
  suite (§7.6).
- **P3.X4 — Interaction Surface.** New work environments: an IDE, a spreadsheet, a graphing tool, a
  lab simulator, a clinical simulator. Each implements the event-emission contract.
- **P3.X5 — Adaptive Policy.** Alternative next-item selection.
- **P3.X6 — Accommodation Provider.** New accommodation types.

---

## Pillar 4 — Evidence Collection

> This is where the framework becomes different from every examination platform.

### 4.1 Purpose

**Transform interactions into evidence.** Pillar 4 reads a raw behavioral stream and produces
structured, competency-mapped, provenance-carrying observations. It answers *what did this person
demonstrably do?* — never *how good are they?*

**Out of scope:** scoring, ranking, weighting, or inference beyond what the record supports.

**Why separate.** This is the boundary between observation and judgment, and it is the single most
important separation in ANAF. Because it exists:

- Evidence can be **shown to the student as fact**, independent of any contested score.
- Scoring models can be **replaced and re-run** against historical evidence.
- Bias audits can ask a sharp question: *was the evidence different, or only the scoring?*
- An appeal can dispute the inference without disputing the record.

### 4.2 Inputs

- **Interaction Event Stream.**
- **Assessment Package key view** — expert solutions, claim decompositions, mutation records,
  expected-evidence descriptors, rubrics.

The key view is required because "the candidate identified the flawed boundary condition" is only
assertable if you know one was planted.

### 4.3 Responsibilities

- **P4.R1** — MUST extract observations and emit them as typed, discrete evidence items.
- **P4.R2** — Every observation MUST cite its source events by sequence number. **An observation with
  no event citation is invalid** and MUST NOT enter the Evidence Graph.
- **P4.R3** — MUST map observations to competency IDs using the package's Expected Evidence
  descriptors, recording *which* descriptor licensed the mapping.
- **P4.R4** — MUST record **negative and null evidence** explicitly: did not verify; did not detect
  the planted flaw; challenged a sound claim. Absence of positive evidence is itself evidence.
- **P4.R5** — MUST attach **strength** and **reliability** to each observation (§4.5).
- **P4.R6** — MUST NOT compute scores, aggregate into proficiency estimates, or apply competency
  weights.
- **P4.R7** — MUST be **re-runnable**: same IES and package regenerate equivalent evidence. Extractor
  versions are recorded so historical evidence can be regenerated and differences attributed.
- **P4.R8** — MUST flag low-confidence extractions for human review rather than emitting them at full
  strength.
- **P4.R9** — MUST preserve verbatim quotations for any observation derived from candidate free text.
- **P4.R10** *(v1.1)* — **Self-report cannot independently establish evidence.** An observation whose
  only citations are `CHECKPOINT_RESPONSE` or other self-descriptive events MUST have strength capped
  at **0.5** unless corroborated by a behavioral event — a tool invocation, an artifact edit, a
  targeted challenge, a recomputation. See §4.6.
- **P4.R11** *(v1.1)* — **Timing constraints.** Temporal observations MUST have `polarity: neutral`
  or `positive`, MUST NOT be the sole citation for any estimate, and MUST be suppressed entirely when
  a pacing-affecting accommodation is active. See §4.7.
- **P4.R12** *(v1.1)* — Every extractor MUST declare itself `deterministic` or `model_based`, and the
  CEM MUST record which produced each observation. A `STRUCTURED` assessment MUST use deterministic
  extractors only.
- **P4.R13** *(v1.1)* — MUST attribute challenges and edits to specific **claims** by span, and MUST
  emit both hits (challenged a flawed claim) and false alarms (challenged a sound claim) so Pillar 5
  can compute sensitivity and criterion.

### 4.4 Internal Components

| Component | Mode | Responsibility |
|---|---|---|
| **Stream Normalizer** | deterministic | Validates the hash chain, orders events, reconstructs state, marks gaps. |
| **Decision Extractor** | deterministic | Classifies every disposition of AI output — **accepted / rejected / modified / ignored** — per claim, with latency and correctness against the mutation record. The highest-value extractor in the system, and it needs no model. |
| **Detection Extractor** *(v1.1)* | deterministic | Maps challenges and edits to claim spans; emits hits, misses, false alarms, correct rejections. |
| **Behavior Extractor** | deterministic | Verification actions that leave traces: recomputation, source-opening, tool use, re-reads, revisits, systematic vs. scattershot exploration. |
| **Temporal Analyzer** | deterministic | Hesitation, dwell, revision cycles, time-to-first-challenge. Constrained by P4.R11. |
| **Reasoning Extractor** | model-based | Analyzes free text and checkpoint responses. Extracts claims, justifications, causal structure, hedging and calibration language. **`FULL` tier only.** |
| **Artifact Analyzer** | model-based *(pluggable)* | Evaluates work products against expert solution and rubric. Deterministic where a formal verifier exists (test runner, CAS). |
| **Evidence Mapper** | deterministic | Binds observations to competency IDs via descriptors. |
| **Evidence Graph Builder** | deterministic | Assembles observations, events, competencies, and relations (`supports`, `contradicts`, `supersedes`, `elaborates`). |
| **Quality Gate** | deterministic | Reliability scoring, contradiction detection, review flagging. |

**Note the distribution.** Every extractor carrying the core of ANAF's differentiation — decisions,
detection, verification behavior — is deterministic. Only language analysis needs a model. This is
what makes the `STRUCTURED` tier viable rather than merely cheap.

### 4.5 Evidence strength and reliability

Two orthogonal numbers, both required, frequently conflated:

- **Strength (0..1)** — *how much this observation tells you about the competency, if true.*
  Independently recomputing and catching a planted flaw is strong. Taking four seconds longer than
  average is weak.
- **Reliability (0..1)** — *how confident the extractor is that the observation occurred as
  described.* A verbatim explicit statement is near 1. An inferred intent from an ambiguous prompt is
  much lower.

A strong-but-unreliable observation and a weak-but-certain one must not be treated alike.

### 4.6 Self-report and coachability *(new in v1.1)*

Examinations are the most coached artifact on earth. A job interview gets a weekend of preparation;
a national examination gets two years of it, from an industry whose entire business is
pattern-matching to assessment formats.

The right distinction is not coachable versus not. It is coaching that produces the **behavior**
versus coaching that produces the **markers**. A coach who teaches students to always check a
surprising result before accepting it has taught them to verify — that is just teaching, and scores
*should* rise. The threat is narrower: learning that writing *"I verified this by cross-checking
against the source"* scores well, without doing it.

Hence P4.R10. Self-report may **corroborate** behavior; it may not **establish** it. This costs
nothing in the honest case — students who actually verify leave behavioral traces — and closes the
cheapest attack in the system.

Two supporting requirements:
- Item-bank churn is a continuous operational cost, not a one-off. Pillar 7 owns exposure monitoring
  and retirement (P7.R9).
- The framework may be published; the item pool may not.

### 4.7 Timing signals *(new in v1.1)*

Timing is informative and badly confounded — by disability, accommodation, device and connection
quality, language fluency, and cultural pacing norms. It is also the highest-litigation-risk signal
in the system.

v1.0 capped its strength by convention. v1.1 makes it a hard rule (P4.R11):

- A long pause MAY support *"deliberated carefully."*
- A long pause MUST NEVER support *"did not understand"* or any other negative inference.
- Timing MUST NOT be the only thing a competency estimate rests on.
- Timing evidence is suppressed entirely under any pacing-affecting accommodation.

### 4.8 Outputs

**Evidence Graph.** Not scores. Evidence.

Nodes: observations, events, competencies, claims, artifacts, items.
Edges: `cites`, `maps_to`, `supports`, `contradicts`, `supersedes`, `produced_in`.

### 4.9 Interface

**Produces: Candidate Evidence Model (CEM).** Schema in §8.4.

```
POST /v1/evidence            { session_id } -> extract (async)
GET  /v1/evidence/{id}       -> CEM
GET  /v1/evidence/{id}/graph -> graph projection
POST /v1/evidence/{id}/rerun { extractor_version }
GET  /v1/evidence/{id}/review-queue -> low-reliability items
```

**Consumed by:** Pillar 5. Readable by Pillar 6 for drill-down, but only via the CAO's citations —
Pillar 6 MUST NOT re-derive competency claims from raw evidence.

### 4.10 Extension Points

- **P4.X1 — Extractor Plugin.** New extractors for new surfaces or signal types. MUST declare mode
  (P4.R12).
- **P4.X2 — Evidence Type.** New observation types with their strength semantics.
- **P4.X3 — Artifact Analyzer.** Domain-specific work-product analysis: test execution, proof
  checking, CAS equivalence, clinical protocol conformance.
- **P4.X4 — Mapping Policy.** Alternative observation → competency mapping strategies.
- **P4.X5 — Reliability Model.** Swap the extractor-confidence estimator.

---

## Pillar 5 — Competency Inference

> This is the brain.

### 5.1 Purpose

**Infer competency from evidence — with calibrated confidence and a stated reason.** The only place
in ANAF where a judgment about a person is made.

**Out of scope:** deciding what the estimate *means* for a stakeholder — pass/fail, grade, placement,
hire. That is Pillar 6, driven by policy.

### 5.2 Inputs

- **Candidate Evidence Model.**
- **Competency Registry** definitions (pinned versions, carried in the CEM).
- **Rubrics** (carried forward via the CEM).
- **Calibration parameters** — population norms, item and claim parameters, model weights from
  Pillar 7.
- **Prior LLR context** — *formative assessments only* (§8.7.5). Forbidden for summative.

### 5.3 Responsibilities

- **P5.R1** — MUST produce, per targeted competency, an estimate on the registry's declared scale.
- **P5.R2** — MUST produce a **calibrated** confidence. "Calibrated" is falsifiable: across the
  population, estimates at stated confidence *c* must be correct at rate ≈ *c*, and Pillar 7 MUST
  measure this continuously (§7.5).
- **P5.R3** — MUST produce a human-readable explanation citing specific observations. **An estimate
  without citations MUST NOT be emitted.**
- **P5.R4** — MUST propagate evidence insufficiency as *wide uncertainty or explicit
  non-determination* — never as a low score. **Not observed ≠ not competent.** This is the most
  common and most damaging failure mode in assessment systems.
- **P5.R5** — MUST detect and report internal inconsistency rather than averaging it away.
- **P5.R6** — MUST run bias detection across protected and proxy attributes; attach results to the CAO.
- **P5.R7** — MUST be **deterministic and reproducible** given the same CEM and model version. Where
  a model participates, it MUST be pinned, temperature-0 or seeded, with raw output retained.
- **P5.R8** — MUST record model identity and version per estimate.
- **P5.R9** — MUST NOT use candidate demographic attributes as inference features.
- **P5.R10** — MUST support **counterfactual explanation**: what different evidence would have moved
  this estimate, and roughly how much.
- **P5.R11** *(v1.1)* — MUST NOT produce an estimate resting solely on temporal evidence (§4.7).
- **P5.R12** *(v1.1)* — MUST condition `COMP.VERIFICATION_CAPABILITY` and detection-miss penalties on
  demonstrated domain competence, using each mutation's `knowledge_prerequisite`. A miss on a claim
  whose prerequisite is unmet MUST be excluded from the capability estimate and reported as
  `PREREQUISITE_UNMET`.
- **P5.R13** *(v1.1)* — MUST report `COMP.ERROR_DETECTION` as **sensitivity and criterion**, never as
  a single conflated score (§2.5.2).
- **P5.R14** *(v1.1)* — MUST propagate the competency's `transferability.evidence_status` into the
  CAO, so reports can label domain-specific estimates as such.

### 5.4 Internal Components

| Component | Responsibility |
|---|---|
| **Evidence Aggregator** | Groups observations by competency; resolves `supersedes`; weights by strength × reliability. |
| **Signal Detection Estimator** *(v1.1)* | Computes d′ and criterion from claim-level hits, misses, false alarms, correct rejections. |
| **Rubric Engine** | Applies rubrics to artifact and reasoning evidence for criterion-level judgments. |
| **Competency Estimator** | Produces the estimate. Pluggable (§5.5). |
| **Confidence Calculator** | Uncertainty from evidence quantity, strength, reliability, coherence. |
| **Consistency Checker** | Flags contradictory patterns and within-competency variance. |
| **Sufficiency Gate** | Enforces P5.R4 and P5.R12 — emits `INSUFFICIENT_EVIDENCE` or `PREREQUISITE_UNMET` rather than a number. |
| **Bias Detection** | Group fairness, differential item functioning, proxy-feature auditing. |
| **Explainability Engine** | Citation-backed narrative and counterfactuals. |
| **Profile Composer** | Assembles the CAO. |

### 5.5 Estimation models

At least one MUST be provided, and the one in use MUST be declared per competency:

1. **Rubric-weighted aggregation** — transparent, defensible, no training data. The recommended
   default and required fallback.
2. **Signal detection** — required for `COMP.ERROR_DETECTION` (§2.5.2).
3. **Bayesian latent-trait (IRT-style)** — posterior over proficiency; confidence falls out as
   posterior width. Needs calibrated parameters from Pillar 7.
4. **Learned model** — highest ceiling, highest governance burden. Permitted only with a documented
   training set, published fairness metrics, and a rubric-weighted fallback available for appeals.

Regardless of model, **the explanation MUST reflect the actual computation.** A post-hoc narrative
rationalizing a black-box score is a conformance failure, not an explanation.

### 5.6 Outputs

**Competency Profile.** Per competency: estimate, confidence, citations, explanation, counterfactuals,
consistency, sufficiency, transferability status.

```
Conceptual Understanding      78    confidence 94%   (domain-specific)
Verification Disposition      91    confidence 97%   (transferability: under study)
Verification Capability       84    confidence 89%   (domain-specific)
Error Detection            d′ 2.1   criterion CALIBRATED
AI Collaboration              73    confidence 88%   (transferability: under study)
Communication                  —    INSUFFICIENT_EVIDENCE (structured tier does not observe this)
Ethical Judgment               —    INSUFFICIENT_EVIDENCE (0 observations, minimum 3)
```

Those last two lines are not gaps in the product. They are the product working.

### 5.7 Interface

**Produces: Competency Assessment Object (CAO).** Schema in §8.5.

```
POST /v1/inference            { cem_id } -> infer (async)
GET  /v1/inference/{id}       -> CAO
GET  /v1/inference/{id}/explanation?competency=
POST /v1/inference/{id}/rerun { model_version }
GET  /v1/inference/{id}/fairness
```

**Consumed by:** Pillar 6. Written to the LLR (§8.7).

### 5.8 Extension Points

- **P5.X1 — Estimator Plugin.** New models per competency or domain.
- **P5.X2 — Rubric Type.** Analytic, holistic, single-point, learning progression.
- **P5.X3 — Confidence Model.** Alternative uncertainty quantification.
- **P5.X4 — Fairness Metric.** Additional or jurisdiction-mandated tests.
- **P5.X5 — Explanation Renderer.** Audience-tuned styles (child, parent, teacher, regulator).
- **P5.X6 — Norm Provider.** Population norms for percentile reporting.

---

## Pillar 6 — Outcomes & Reporting

### 6.1 Purpose

**Convert competency into useful outputs.** One CAO rendered for many audiences, each with different
needs, rights, and permitted detail.

**Out of scope:** changing any estimate. Pillar 6 selects, aggregates, contextualizes, and presents —
it never re-scores.

### 6.2 Inputs

- **Competency Assessment Object.**
- **Longitudinal Learner Record** — for trajectory views (§8.7).
- **Stakeholder context** — who is viewing, in what role, under what entitlement.
- **Reporting policy** — thresholds, disclosure rules, retention (from Pillar 7).
- **Cohort data** — for aggregate views.

### 6.3 Responsibilities

- **P6.R1** — MUST render audience-appropriate reports without altering estimates.
- **P6.R2** — MUST enforce **disclosure policy per audience** at generation time, not by UI hiding.
- **P6.R3** — MUST carry uncertainty into every report. Point estimates without confidence are a
  conformance failure, including in aggregate and visual views.
- **P6.R4** — MUST make evidence reachable: every reported claim drills down to its citations.
- **P6.R5** — MUST apply decision thresholds *as declared policy*, labelled as such ("does not meet
  this school's threshold of 70"), never as properties of the person.
- **P6.R6** — MUST support subject-facing transparency. The student MUST be able to see what was
  concluded about them, on what evidence, and how to contest it — at every age.
- **P6.R7** — MUST anonymize and suppress small cells in aggregate reporting (default minimum 5).
- **P6.R8** — MUST record every report generation and access in the audit log.
- **P6.R9** — Learning recommendations MUST cite the competency gap and evidence that motivated them.
- **P6.R10** — MUST NOT produce a composite "overall score" unless the reporting policy defines one,
  including its formula, which is disclosed alongside the number.
- **P6.R11** *(v1.1)* — MUST render longitudinal views as **trajectories, never merged scores**
  (§8.7.4).
- **P6.R12** *(v1.1)* — MUST label estimates whose competency `transferability.evidence_status` is
  not `validated` as domain-specific.
- **P6.R13** *(v1.1)* — MUST verify release authority per the consent capacity model before any
  third-party disclosure, and MUST fail closed without it (§6.5).

### 6.4 Internal Components

| Component | Responsibility |
|---|---|
| **Report Composer** | Renders from a Report Specification + CAO (+ LLR for trajectories). |
| **Disclosure Filter** | Per-audience field-level policy applied before rendering. |
| **Threshold Engine** | Applies declared decision rules; emits outcome plus the rule that produced it. |
| **Aggregation Engine** | Cohort roll-ups, distributions, gap analysis; small-cell suppression. |
| **Trajectory Renderer** *(v1.1)* | Time-series views from the LLR with per-point confidence and staleness widening. |
| **Narrative Generator** | Audience-appropriate prose, constrained to claims present in the CAO. |
| **Visualization Service** | Competency radar, evidence timeline, growth trajectory, distributions — all uncertainty-bearing. |
| **Learning Recommendation Engine** | Maps gaps to interventions via a pluggable catalog. |
| **Delivery & Access Control** | Distribution, entitlement, expiring share links, revocation. |

### 6.5 Audiences, holders, and release

Reports are **specifications**, not code. A Report Specification declares audience, permitted fields,
aggregation level, narrative style, and thresholds.

#### 6.5.1 Two roles, not one *(revised in v1.1)*

v1.0 applied a single "candidate pushes, nobody pulls" rule. That was built for hiring and is wrong
for education: a school holding its own students' results is not a third party looking something up
— it is a school keeping school records.

| Role | Access | Consent |
|---|---|---|
| **Conducting institution** — the school, college, university, or company that ran the assessment | First-class holder. Full results it commissioned, longitudinal view across its own assessments, cohort aggregates. | None required. This is a school record. |
| **Any other party** — a different institution, an admissions body, a prospective employer | Receives only by **subject-initiated release**, per the consent capacity model. Cannot query for existence of prior results. | Required, per §1.2.2. |

The asymmetry is the whole point: the subject can **push**, third parties cannot **pull**. Same data,
and the difference between a credential the learner owns and a permanent record they cannot escape
is one access rule.

#### 6.5.2 Audience matrix

| Audience | Sees | Never sees |
|---|---|---|
| **Student** | Strengths, weaknesses, growth trajectory, full evidence, all explanations, appeal route. | Other students; raw item keys. |
| **Teacher** | Per-student profiles, class distribution, competency gaps, curriculum-gap analysis. | Data outside their roster. |
| **Parent / Guardian** | Their child's profile in plain language; growth over time. | Other students; behavioral proctoring detail. |
| **School / Institution** | Cohort aggregates, trends, programme effectiveness, longitudinal cohort views. | Individual data beyond entitlement. |
| **Admissions body** | Released profile with evidence excerpts, subject-initiated only. | Non-released assessments; formative history. |
| **Employer** | Competency radar, decision profile, risk areas, released evidence excerpts. | Health/demographic data, proctoring media, non-released evidence. |
| **Certification body** | Outcome, evidence sufficiency, integrity attestation, audit trail. | Formative and practice data. |

### 6.6 Outputs

**Stakeholder Reports** — rendered documents, dashboards, structured payloads:

- *Student:* strengths, weaknesses, growth, evidence.
- *Teacher:* class insights, competency distribution, curriculum gaps.
- *School:* cohort trends, programme effectiveness.
- *Employer:* competency radar, decision profile, risk areas.

### 6.7 Interface

**Produces: Stakeholder Report (SR).** Schema in §8.6.

```
POST /v1/reports                { cao_id, audience, spec_id } -> report
GET  /v1/reports/{id}
GET  /v1/reports/{id}/evidence/{claim_id} -> drill-down
POST /v1/reports/trajectory     { learner_id, competencies, spec_id }
POST /v1/reports/aggregate      { cohort, spec_id }
POST /v1/reports/{id}/share     { recipient, expires_at } -> subject-initiated release
POST /v1/consent                { cao_id, audience, fields, authority }
```

### 6.8 Extension Points

- **P6.X1 — Report Specification.** New audiences and formats without code changes.
- **P6.X2 — Visualization Plugin.** New chart and evidence-display types.
- **P6.X3 — Recommendation Catalog.** Institution-specific resources and interventions.
- **P6.X4 — Export Adapter.** MIS/SIS, LMS, ATS, PDF, Open Badges, verifiable credentials.
- **P6.X5 — Threshold Policy.** Custom decision rules per institution or role.
- **P6.X6 — Localization.** Language and cultural adaptation of narrative and visuals.

---

## Pillar 7 — Governance & Evolution

> This pillar never participates directly in assessment. It governs everything.

### 7.1 Purpose

**Ensure the system is secure, fair, lawful, valid, and improving — and prove it.**

Out of the data path by construction. It observes all contracts read-only, and its only lever is
**changing specifications**. It cannot alter a live session, an evidence record, or an issued result.

**Why the separation is absolute.** A governance layer that can reach into results is not governance;
it is an unlogged actor. Every Pillar 7 action is a versioned specification change with an author, a
rationale, and an effective date.

### 7.2 Inputs

Read-only observation of every contract (ADO, AP, IES, CEM, CAO, SR, LLR), plus audit logs, appeals,
incident reports, external standards and regulations, and research findings.

### 7.3 Responsibilities

- **P7.R1** — MUST maintain a complete, immutable, tamper-evident audit trail across all pillars.
- **P7.R2** — MUST enforce retention, minimization, and deletion per privacy policy and jurisdiction,
  including the LLR compression schedule (§8.7.2).
- **P7.R3** — MUST continuously monitor fairness across protected groups at claim, item, evidence, and
  outcome level.
- **P7.R4** — MUST monitor and publish **calibration** — the falsifiable claim in P5.R2.
- **P7.R5** — MUST operate an appeals process with a defined SLA, human review, and power to
  invalidate an outcome. The appeal window MUST NOT exceed the LLR T0 retention period (§8.7.3).
- **P7.R6** — MUST version every specification, model, rubric, and prompt template, and pin the full
  version set per assessment so results are reproducible years later.
- **P7.R7** — MUST measure **predictive validity** against real-world outcomes where lawfully
  available, and publish it. A framework claiming to predict capability better than examinations MUST
  be willing to be wrong in public.
- **P7.R8** — MUST NOT modify any assessment artifact. Improvements land as new specification versions
  with effective dates.
- **P7.R9** — MUST detect and respond to integrity incidents (content leakage, collusion, impostor
  testing), including item retirement, and MUST monitor item exposure continuously (§4.6).
- **P7.R10** — MUST maintain accessibility conformance (target WCAG 2.2 AA) and validate that
  accommodations do not alter the construct measured.
- **P7.R11** *(v1.1)* — MUST measure and publish **transferability coefficients** per competency,
  updating registry `evidence_status` from the data (§1.2.1).
- **P7.R12** *(v1.1)* — MUST validate the `STRUCTURED` tier against `FULL` on competencies both claim
  to measure, and MUST flag divergence beyond a stated tolerance.
- **P7.R13** *(v1.1)* — MUST maintain the consented, de-identified **research corpus** and enforce its
  sampling rate, consent basis, and access controls (§8.7.3).

### 7.4 Internal Components

| Group | Components |
|---|---|
| **Security** | Identity governance, encryption at rest and in transit, key management, audit logging, access control, penetration testing. |
| **Fairness** | Bias monitoring, differential item and claim functioning, accessibility conformance, accommodation validation, calibration monitoring. |
| **Compliance** | Privacy (GDPR / UK GDPR / FERPA / COPPA / EEOC and local equivalents), retention enforcement, consent management, appeals administration, regulatory reporting. |
| **Research** | Claim analytics, item analytics, competency analytics, difficulty calibration, transferability studies, predictive and construct validity, A/B experimentation. |
| **Versioning** | Specification, model, rubric, prompt, and registry version control; version-set pinning; migration and deprecation. |
| **Memory Governance** *(v1.1)* | LLR compression scheduling, tier transition execution and audit, hash anchoring, research corpus sampling, erasure processing. |
| **Continuous Improvement** | Improvement pipeline for generation, evidence mapping, scoring models, and the registry itself. |

### 7.5 The validity program

Everything else is machinery. This determines whether ANAF is a real assessment standard or an
elaborate opinion generator. Seven claims, each with a measurement:

| Claim | Measurement |
|---|---|
| **Reliability** | Test–retest correlation; parallel-form equivalence; internal consistency per competency. |
| **Construct validity** | Do estimates correlate with independent measures of the same construct, and *not* with unrelated ones? |
| **Predictive validity** | Do estimates predict later performance — subsequent grades, progression, degree outcome, job performance — better than the incumbent method? |
| **Transferability** *(v1.1)* | Does a competency estimate in one subject predict the same competency in another? Measured per competency; updates registry `evidence_status`. |
| **Calibration** | Do stated confidences match observed accuracy? Reliability diagrams, expected calibration error. |
| **Fairness** | Differential item and claim functioning; group-wise error-rate parity; adverse-impact ratio. |
| **Resistance to gaming** | Do coached candidates score higher without capability gain? Red-team studies against known strategies. |

Each MUST have a published methodology, a target threshold, a measurement cadence, and an escalation
path when it degrades.

**Transferability deserves a note.** ANAF's largest claim — that how someone works with AI is a real,
measurable, partly transferable capability — is currently a hypothesis. The education-first context
is what allows it to be tested: the same student, across many subjects, over many years, is a natural
experiment that arrives as a byproduct of ordinary operation. No hiring deployment can produce it.

### 7.6 Conformance and test suites

- **Contract conformance** — schema validation for all seven contracts at every boundary.
- **Golden-path fixtures** — reference ADO → AP → IES → CEM → CAO → SR chains an implementation must
  reproduce.
- **AI-behavior conformance** — a collaborator swapped in under P3.X3 must prove mutation fidelity,
  challenge-policy adherence, and non-leakage.
- **Path-invariance suite** *(v1.1)* — replay N conversational routes to the same item and assert that
  mutation presentation, severity, location, and claim boundaries are identical every time. This makes
  the standardization level (§3.6.1) a measured property.
- **Tier-equivalence suite** *(v1.1)* — run the same session through `STRUCTURED` and `FULL`
  extraction; assert estimates agree within tolerance on shared competencies (P7.R12).
- **Compression-fidelity suite** *(v1.1)* — assert that each LLR tier transition preserves reconstructible
  claims and destroys reconstructible raw behavior (§8.7.2).
- **Adversarial suite** — prompt injection against the collaborator, answer extraction, evidence
  spoofing, self-report gaming, indiscriminate challenging.
- **Fairness regression** — fairness metrics as blocking CI on every model or rubric change.

### 7.7 Outputs

- **Framework Updates** — new versions of ANAF specifications and schemas.
- **Assessment Updates** — item retirements, recalibrations, rubric revisions, registry transferability
  updates.
- **Research Reports** — validity, transferability, fairness, and calibration findings, published on a
  stated cadence.
- **Audit Attestations** — evidence for regulators, inspectorates, accreditors, and institutional buyers.
- **Incident Reports** — integrity events and remediation.

### 7.8 Extension Points

- **P7.X1 — Compliance Module.** Jurisdiction-specific regimes.
- **P7.X2 — Audit Sink.** External SIEM or immutable log providers.
- **P7.X3 — Fairness Metric.** Additional or mandated metrics.
- **P7.X4 — Research Connector.** Outcome-data linkage for validity studies, under consent.
- **P7.X5 — Certification Authority.** Verifiable credentials for outcomes.
- **P7.X6 — Improvement Policy.** Rules governing automated recalibration and rollout.
- **P7.X7 — Compression Policy** *(v1.1)*. Alternative tier schedules and retention regimes per
  jurisdiction.

---

## 8. Cross-Cutting Contracts

The worked example below follows **one Year 10 mathematics examination item** end to end, through
every contract. A professional example appears in §9.1.

### 8.0 Common envelope

```yaml
anaf_version: "1.1"
object_type: ADO | AP | IES | CEM | CAO | SR | LLR
object_id: <uuid>
version: <semver>
created_at: <iso8601>
created_by: { actor_type: human|system, actor_id: <id> }
provenance:
  upstream_object_id: <uuid or null>
  pipeline_version_set: <version-set-id>
signature: <detached signature over the canonical serialization>
```

`pipeline_version_set` is what makes P7.R6 achievable: one identifier resolving to the exact versions
of every specification, model, prompt, and rubric involved.

### 8.1 Assessment Definition Object (ADO)

```yaml
ado_id: ADO-2026-0114
version: 1.0.0
title: "Year 10 Mathematics — Optimisation, AI-Collaborative"
purpose: education_summative     # education_formative | education_summative | certification | hiring | practice
tier: STRUCTURED                 # v1.1 — deterministic extraction only

context:
  domain: MATHEMATICS
  domain_pack: { id: DP.MATHEMATICS.SECONDARY, version: 3.0.0 }
  curriculum:
    framework: NATIONAL_CURRICULUM_ENGLAND
    key_stage: KS4
    year: 10
    unit: "Quadratics and Optimisation"
    learning_outcomes: [LO.MATH.OPT.1, LO.MATH.OPT.2, LO.MATH.OPT.3]
  role: null

assessment_blueprint:
  duration_minutes: 45
  question_count: { min: 5, max: 6 }
  adaptive: false                # summative — fixed form for equivalence
  question_types: [OPTIMISATION_WITH_AI, MODEL_CRITIQUE]
  interaction_patterns: [AI_COLLABORATION, CHALLENGE_RESPONSE]
  standardization_level: S3_PINNED_TURNS
  difficulty: { target: 0.55, distribution: normal, spread: 0.12 }
  pass_criteria:
    type: competency_thresholds
    rules:
      - { competency: COMP.CONCEPTUAL, min_level: 2 }
      - { competency: COMP.VERIFICATION_DISPOSITION, min_level: 2 }

competency_blueprint:
  targets:
    - { competency: COMP.CONCEPTUAL, registry_version: 2.0.0, weight: 0.30, target_level: 3, min_observations: 5 }
    - { competency: COMP.VERIFICATION_DISPOSITION, registry_version: 2.0.0, weight: 0.25, target_level: 3, min_observations: 5 }
    - { competency: COMP.VERIFICATION_CAPABILITY, registry_version: 2.0.0, weight: 0.20, target_level: 2, min_observations: 4 }
    - { competency: COMP.ERROR_DETECTION, registry_version: 2.0.0, weight: 0.25, target_level: 3, min_claims: 24 }
  # COMP.COMMUNICATION deliberately not targeted — STRUCTURED tier cannot observe it (P1.R9)

question_blueprint:
  slots:
    - slot_id: Q1
      topic: OPTIMISATION
      subtopic: CONSTRAINED_AREA
      cognitive_level: ANALYZE
      difficulty: 0.5
      competencies: [COMP.CONCEPTUAL, COMP.ERROR_DETECTION, COMP.VERIFICATION_DISPOSITION]
      claims_per_response: { min: 4, max: 6 }
      time_limit_seconds: 480
  claim_mix:                     # v1.1 — sound:flawed ratio at claim level
    SOUND: 0.60
    INTERPRETATION: 0.15
    ARITHMETIC: 0.10
    REASONING: 0.10
    ASSUMPTION: 0.05

evidence_blueprint:
  descriptors:
    - descriptor_id: EV.CHALLENGE.TARGETED
      competency: COMP.ERROR_DETECTION
      observable: "Candidate challenges a specific flawed claim, unprompted."
      detectable_from: [AI_PROMPT, ARTIFACT_EDIT]
      extraction_mode: deterministic
      strength: 1.0
    - descriptor_id: EV.CHALLENGE.FALSE_ALARM
      competency: COMP.ERROR_DETECTION
      observable: "Candidate challenges a sound claim."
      polarity: negative
      detectable_from: [AI_PROMPT]
      extraction_mode: deterministic
      strength: 0.8
    - descriptor_id: EV.VERIFY.INDEPENDENT
      competency: COMP.VERIFICATION_DISPOSITION
      observable: "Candidate re-derives or recomputes an AI-supplied result by independent means."
      detectable_from: [TOOL_INVOCATION, ARTIFACT_EDIT]
      extraction_mode: deterministic
      strength: 0.9
    - descriptor_id: EV.ACCEPT.UNVERIFIED
      competency: COMP.VERIFICATION_DISPOSITION
      observable: "Candidate accepts a material AI claim with no verification action."
      polarity: negative
      detectable_from: [DECISION]
      extraction_mode: deterministic
      strength: 0.7

ai_configuration:
  persona: COLLABORATIVE_PEER
  capabilities: [EXPLAIN, SOLVE, CRITIQUE]
  challenge_policy: CONCEDE_ON_EVIDENCE
  hint_policy: { enabled: true, max_hints: 2, cost_per_hint: 0.1, tiered: true }
  reasoning_prompts: { enabled: true, frequency: per_item }
  recognition_scaffolds: false   # v1.1 — P2.R12, always false

governance:
  proctoring_level: L1_SOFT
  accessibility: { wcag_target: "2.2-AA", screen_reader: true, dyscalculia_profile: true }
  accommodations_supported: [EXTRA_TIME_1_25X, EXTRA_TIME_1_5X, HIGH_CONTRAST, EXTENDED_BREAKS]
  consent_capacity:
    subject_age_band: 13_TO_15
    release_authority: [GUARDIAN, INSTITUTION]
    subject_rights: [VIEW, APPEAL, ANNOTATE]
    transition_age: 18
    jurisdiction: [UK]
    regime_refs: [UK_GDPR, DPA_2018]
  conducting_institution: { id: INST-2201, type: SECONDARY_SCHOOL, name: "..." }
  retention:
    schedule_ref: RET.EDU.STANDARD      # resolves to the LLR tier schedule, §8.7.2
    appeal_window_days: 90
    research_corpus_consent: OPTED_IN

validation:
  status: PASSED
  evidence_sufficiency: PASSED
  tier_compatibility: PASSED
  report_ref: VAL-2026-0114
```

### 8.2 Assessment Package (AP)

```yaml
package_id: AP-2026-0114
ado_ref: { id: ADO-2026-0114, version: 1.0.0 }
generated_at: 2026-07-27T09:00:00Z
tier: STRUCTURED
generation_provenance:
  models: [{ role: question_gen, id: <model-id>, version: <ver>, seed: 4471 }]
  prompt_templates: [{ id: PT.QGEN.MATHS.SECONDARY, version: 4.0.1 }]
  human_review: { required: true, reviewer_id: U-77, decision: APPROVED }

items:
  - question_id: Q-11402
    slot_ref: Q1
    domain: MATHEMATICS
    topic: OPTIMISATION
    subtopic: CONSTRAINED_AREA
    learning_objectives: [LO.MATH.OPT.2, LO.MATH.OPT.3]
    competencies_targeted: [COMP.CONCEPTUAL, COMP.ERROR_DETECTION, COMP.VERIFICATION_DISPOSITION]
    difficulty_level: 0.51
    cognitive_level: ANALYZE

    scenario:
      scenario_id: SC-9012
      type: WORD_PROBLEM
      content: >
        A farmer has 200 m of fencing to enclose a rectangular field. One side of the
        field runs along a straight river and needs no fence. What dimensions give the
        largest possible area?

    prompt: "Work with the assistant to solve this. Submit your final answer and dimensions."
    interaction_pattern: AI_COLLABORATION
    time_limit_seconds: 480

    allowed_ai_behaviours:
      persona: COLLABORATIVE_PEER
      capabilities: [EXPLAIN, SOLVE, CRITIQUE]
      challenge_policy: CONCEDE_ON_EVIDENCE
      standardization_level: S3_PINNED_TURNS
      hints: [{ tier: 1, text: "...", cost: 0.1 }, { tier: 2, text: "...", cost: 0.2 }]
      forbidden: [REVEAL_MUTATION, REVEAL_EXPERT_SOLUTION, DISCUSS_SCORING, ENUMERATE_CLAIMS]

    reasoning_checkpoints:
      - { checkpoint_id: CP-1, trigger: ON_ITEM_SUBMIT, prompt: "How did you check your answer?", required: true }
      # NOTE: checkpoint evidence is corroborating only — P4.R10 caps it at 0.5 alone

    expected_evidence:
      - { descriptor_ref: EV.CHALLENGE.TARGETED, weight: 1.0 }
      - { descriptor_ref: EV.CHALLENGE.FALSE_ALARM, weight: 0.8, polarity: negative }
      - { descriptor_ref: EV.VERIFY.INDEPENDENT, weight: 0.9 }
      - { descriptor_ref: EV.ACCEPT.UNVERIFIED, weight: 0.7, polarity: negative }

    adaptation_rules: []            # fixed form (summative)

    # ---- sealed key section: not readable by Pillar 3 ----
    key:
      correct_expert_solution:
        content: >
          Let x be the side parallel to the river and y the two perpendicular sides.
          Constraint: x + 2y = 200. Area A = xy = (200 - 2y)y = 200y - 2y².
          dA/dy = 200 - 4y = 0 → y = 50, x = 100. Maximum area = 5000 m².
        reasoning_trace: "..."
        verifier: { type: CAS, expression: "maximize(x*y, x+2*y=200)", status: CONFIRMED }

      ai_initial_response:
        content: "..."
        claims:                     # v1.1 — claim-level decomposition
          - claim_id: CLM-1
            span: "sentence 1"
            text: "We need to enclose a rectangle, so the fencing must cover all four sides."
            status: FLAWED
            mutation_ref: MUT-8823
          - claim_id: CLM-2
            span: "sentence 2"
            text: "So the perimeter constraint is 2x + 2y = 200, giving x + y = 100."
            status: FLAWED
            mutation_ref: MUT-8824      # consequential — inherits from CLM-1
          - claim_id: CLM-3
            span: "sentence 3-4"
            text: "Area A = xy = x(100 - x). Differentiating: dA/dx = 100 - 2x."
            status: SOUND               # the method is correct given its (wrong) premise
          - claim_id: CLM-4
            span: "sentence 5"
            text: "Setting dA/dx = 0 gives x = 50."
            status: SOUND
          - claim_id: CLM-5
            span: "conclusion"
            text: "So the field is 50 m by 50 m with maximum area 2500 m²."
            status: FLAWED
            mutation_ref: MUT-8825      # consequential

      mutations:
        - mutation_id: MUT-8823
          claim_ref: CLM-1
          class: INTERPRETATION
          severity: critical
          location: { span: "sentence 1", anchor: "must cover all four sides" }
          correct_value: "only three sides need fencing; the river bounds the fourth"
          detection_difficulty: 0.48
          knowledge_prerequisite:
            objectives: [LO.MATH.OPT.2]
            minimum_level: 2
            note: >
              Requires understanding that a constraint equation must be derived from
              the problem's physical setup, not assumed from the shape.
          rationale: >
            Misreads the boundary condition. All subsequent algebra and arithmetic are
            internally correct, which makes this detectable only by re-reading the
            question — not by checking the working. Students who verify the arithmetic
            but not the setup will miss it.
          consequential_claims: [CLM-2, CLM-5]

      scoring_rubric:
        rubric_id: RB-4402
        type: analytic
        criteria:
          - { id: C1, competency: COMP.ERROR_DETECTION, descriptor: "Challenges CLM-1 or CLM-2 specifically", levels: [...] }
          - { id: C2, competency: COMP.CONCEPTUAL, descriptor: "Derives the correct constraint x + 2y = 200", levels: [...] }
          - { id: C3, competency: COMP.VERIFICATION_DISPOSITION, descriptor: "Checks the result against the problem statement", levels: [...] }

validation:
  status: PASSED
  per_item:
    - question_id: Q-11402
      ambiguity: PASS
      duplication: PASS
      mutation_validity: PASS
      claim_decomposition: PASS
      claim_mix: PASS            # 3 flawed / 2 sound within this item; ratio met across the form
      recognition_scaffold: PASS  # none present — P2.R12
      bias: PASS
      reading_level: PASS
      accessibility: PASS
      solvability: PASS
  quarantined_items: []

sequencing: { mode: FIXED, order: [Q-11402, Q-11403, Q-11404, Q-11405, Q-11406] }
```

### 8.3 Interaction Event Stream (IES)

```yaml
stream_id: IES-2026-88214
session_id: SES-2026-88214
package_ref: { id: AP-2026-0114, version: 1.0.0 }
candidate_ref: LRN-40318          # pseudonymous; PII held separately
started_at: 2026-07-27T10:00:00Z
ended_at: 2026-07-27T10:44:31Z
completion_status: SUBMITTED
integrity: { hash_algorithm: sha256, chain_head: <hash>, gaps: [] }
proctoring: { level: L1_SOFT, disclosed_at: 2026-07-27T09:58:00Z }
standardization: { level: S3_PINNED_TURNS }
accommodations_applied: []

events:
  - { seq: 1,  ts: "...T10:00:00.000Z", type: SESSION_START, item: null, payload: {}, prev_hash: null, hash: <h1> }
  - { seq: 2,  ts: "...T10:00:03.100Z", type: ITEM_PRESENTED, item: Q-11402, payload: {}, prev_hash: <h1>, hash: <h2> }
  - { seq: 3,  ts: "...T10:00:51.400Z", type: AI_PROMPT, item: Q-11402, payload: { text: "how do I do this one?", compose_ms: 6200, revisions: 0 } }
  - { seq: 4,  ts: "...T10:00:56.900Z", type: AI_RESPONSE, item: Q-11402, payload: {
        text: "...", model: <id>, version: <ver>, latency_ms: 5500,
        claim_spans: [ { claim_id: CLM-1, start: 0,   end: 71 },
                       { claim_id: CLM-2, start: 72,  end: 138 },
                       { claim_id: CLM-3, start: 139, end: 214 },
                       { claim_id: CLM-4, start: 215, end: 251 },
                       { claim_id: CLM-5, start: 252, end: 316 } ],
        realized_behavior: MUTATIONS_PRESENTED, mutation_refs: [MUT-8823, MUT-8824, MUT-8825] } }
  - { seq: 5,  ts: "...T10:01:40.220Z", type: IDLE, item: Q-11402, payload: { duration_ms: 43320 } }
  - { seq: 6,  ts: "...T10:02:22.000Z", type: SCENARIO_INTERACTION, item: Q-11402, payload: { action: RE_READ, target: "problem_statement", dwell_ms: 11400 } }
  - { seq: 7,  ts: "...T10:02:58.750Z", type: TOOL_INVOCATION, item: Q-11402, payload: { tool: SKETCHPAD, action: DRAW, description: "rectangle with river annotated on one side" } }
  - { seq: 8,  ts: "...T10:03:41.120Z", type: AI_PROMPT, item: Q-11402, payload: {
        text: "wait, the river side doesn't need a fence — so it's not 2x + 2y",
        target_span: { start: 72, end: 138 }, resolved_claim: CLM-2 } }
  - { seq: 9,  ts: "...T10:03:46.300Z", type: AI_RESPONSE, item: Q-11402, payload: { realized_behavior: CONCEDED_ON_EVIDENCE, mutation_refs: [MUT-8823, MUT-8824] } }
  - { seq: 10, ts: "...T10:03:52.000Z", type: DECISION, item: Q-11402, payload: { disposition: REJECTED, target_claim: CLM-2, correct: true, latency_ms: 175100 } }
  - { seq: 11, ts: "...T10:04:10.400Z", type: DECISION, item: Q-11402, payload: { disposition: ACCEPTED, target_claim: CLM-3, correct: true } }
  - { seq: 12, ts: "...T10:06:33.900Z", type: ARTIFACT_EDIT, item: Q-11402, payload: { action: WORKING_STEP, content: "x + 2y = 200, A = (200-2y)y" } }
  - { seq: 13, ts: "...T10:08:02.100Z", type: TOOL_INVOCATION, item: Q-11402, payload: { tool: CALCULATOR, expression: "100*50", result: "5000" } }
  - { seq: 14, ts: "...T10:08:44.000Z", type: ARTIFACT_SUBMIT, item: Q-11402, payload: { artifact_id: ART-7710, answer: "100 m by 50 m, area 5000 m²" } }
  - { seq: 15, ts: "...T10:09:20.500Z", type: CHECKPOINT_RESPONSE, item: Q-11402, payload: { checkpoint_id: CP-1, text: "I drew it out and saw the river side shouldn't be fenced, then redid the algebra and checked 100x50." } }
  - { seq: 16, ts: "...T10:44:31.000Z", type: SESSION_SUBMIT, item: null, payload: {} }
```

**Event taxonomy (v1.1, extensible via P3.X4):**

`SESSION_START` · `SESSION_PAUSE` · `SESSION_RESUME` · `SESSION_SUBMIT` · `SESSION_EXPIRE`
`ITEM_PRESENTED` · `ITEM_COMPLETED` · `ITEM_SKIPPED` · `ITEM_REVISITED`
`AI_PROMPT` · `AI_RESPONSE` · `AI_DEFLECTION` · `HINT_REQUESTED` · `HINT_DELIVERED`
`DECISION` *(per claim: accepted / rejected / modified / ignored)*
`TOOL_INVOCATION` · `SCENARIO_INTERACTION` · `ARTIFACT_EDIT` · `ARTIFACT_SUBMIT`
`CHECKPOINT_PROMPTED` · `CHECKPOINT_RESPONSE`
`IDLE` · `FOCUS_LOST` · `FOCUS_GAINED` · `NAVIGATION`
`ADAPTATION_DECISION` · `TIMER_EVENT` · `ACCOMMODATION_APPLIED`
`INTEGRITY_SIGNAL` · `SYSTEM_ERROR` · `STREAM_GAP`

Every event carries `{ seq, ts, type, item, payload, prev_hash, hash }`. Server timestamps are
authoritative; client timestamps travel in the payload as `client_ts` and never substitute for `ts`.

### 8.4 Candidate Evidence Model (CEM)

```yaml
cem_id: CEM-2026-88214
stream_ref: IES-2026-88214
package_ref: { id: AP-2026-0114, version: 1.0.0 }
tier: STRUCTURED
extractor_version_set: EVS-2.0.0
extracted_at: 2026-07-27T10:46:00Z

carried_context:
  competency_definitions:
    - { id: COMP.VERIFICATION_DISPOSITION, version: 2.0.0, proficiency_scale: [...], transferability: { claim: cross_domain, evidence_status: under_study } }
  rubrics: [{ rubric_id: RB-4402, ... }]
  item_metadata: [{ question_id: Q-11402, difficulty: 0.51, claims: 5, flawed_claims: 3 }]

observations:
  - observation_id: OBS-1
    type: TARGETED_CHALLENGE
    item: Q-11402
    claim_ref: CLM-2
    competencies: [COMP.ERROR_DETECTION]
    descriptor_ref: EV.CHALLENGE.TARGETED
    polarity: positive
    strength: 1.0
    reliability: 0.99
    cites_events: [8, 9, 10]
    extractor: { id: DetectionExtractor, version: 2.0.0, mode: deterministic }
    summary: "Challenged the flawed constraint claim, naming the specific defect."
    verbatim: "wait, the river side doesn't need a fence — so it's not 2x + 2y"

  - observation_id: OBS-2
    type: INDEPENDENT_REDERIVATION
    item: Q-11402
    competencies: [COMP.VERIFICATION_DISPOSITION, COMP.CONCEPTUAL]
    descriptor_ref: EV.VERIFY.INDEPENDENT
    polarity: positive
    strength: 0.9
    reliability: 0.97
    cites_events: [6, 7, 12]
    extractor: { id: BehaviorExtractor, version: 2.0.0, mode: deterministic }
    summary: "Re-read the problem, sketched the constraint, and re-derived the equation independently."

  - observation_id: OBS-3
    type: CORRECT_ACCEPTANCE
    item: Q-11402
    claim_ref: CLM-3
    competencies: [COMP.ERROR_DETECTION]
    polarity: positive
    strength: 0.6
    reliability: 0.95
    cites_events: [11]
    extractor: { id: DecisionExtractor, version: 2.0.0, mode: deterministic }
    summary: "Correctly accepted the sound differentiation step — a correct rejection in SDT terms."

  - observation_id: OBS-4
    type: DELIBERATION
    item: Q-11402
    competencies: [COMP.REASONING]
    polarity: neutral                # P4.R11 — never negative
    strength: 0.2
    reliability: 0.96
    cites_events: [5]
    sole_citation_permitted: false    # P4.R11 — may not alone support an estimate
    extractor: { id: TemporalAnalyzer, version: 2.0.0, mode: deterministic }
    summary: "43s pause after the AI response, before re-reading the problem."

  - observation_id: OBS-5
    type: SELF_REPORTED_VERIFICATION
    item: Q-11402
    competencies: [COMP.VERIFICATION_DISPOSITION]
    polarity: positive
    strength: 0.9                     # NOT capped — corroborated (P4.R10)
    strength_uncapped_basis: [OBS-2]  # behavioral corroboration present
    reliability: 0.88
    cites_events: [15]
    extractor: { id: ReasoningExtractor, version: 2.0.0, mode: deterministic_keyword }
    summary: "States they drew it out, redid the algebra, and checked the arithmetic."
    verbatim: "I drew it out and saw the river side shouldn't be fenced, then redid the algebra and checked 100x50."

detection_summary:                    # v1.1 — feeds the SDT estimator
  claims_presented: 28
  flawed: 11
  sound: 17
  hits: 8
  misses: 3
  false_alarms: 2
  correct_rejections: 15
  prerequisite_unmet_excluded: 1      # P5.R12

null_evidence:
  - { competency: COMP.ETHICS, expected_descriptors: [], observed: false, reason: NOT_TARGETED }
  - { competency: COMP.COMMUNICATION, observed: false, reason: TIER_CANNOT_OBSERVE }

relations:
  - { from: OBS-2, to: OBS-1, type: supports }
  - { from: OBS-2, to: OBS-5, type: corroborates }

graph_summary:
  observation_count: 34
  by_competency: { COMP.CONCEPTUAL: 9, COMP.VERIFICATION_DISPOSITION: 8, COMP.VERIFICATION_CAPABILITY: 5, COMP.ERROR_DETECTION: 12 }
  mean_reliability: 0.94
  extraction_modes: { deterministic: 34, model_based: 0 }

quality: { review_flagged: [], contradictions: [], stream_gaps: [] }
```

### 8.5 Competency Assessment Object (CAO)

```yaml
cao_id: CAO-2026-88214
cem_ref: CEM-2026-88214
learner_ref: LRN-40318
inferred_at: 2026-07-27T10:47:00Z
tier: STRUCTURED
model_version_set: MVS-3.0.0

competency_profile:
  - competency: COMP.VERIFICATION_DISPOSITION
    registry_version: 2.0.0
    estimate: { value: 88, scale: 0-100, level: 3, level_descriptor: "Checks routinely, choosing a method independent of the source." }
    confidence: 0.94
    uncertainty_interval: [82, 93]
    sufficiency: SUFFICIENT
    consistency: CONSISTENT
    transferability: { claim: cross_domain, evidence_status: under_study }   # P5.R14
    estimator: { type: rubric_weighted, version: 3.0.0 }
    evidence_citations: [OBS-2, OBS-5, OBS-9, OBS-14, OBS-21, OBS-27, OBS-31]
    explanation: >
      Checked independently on 6 of 7 opportunities. On the optimisation item, re-read
      the problem, sketched the constraint, and re-derived the equation rather than
      accepting the assistant's setup. The one unchecked acceptance (OBS-21) concerned
      a low-stakes rounding convention.
    counterfactuals:
      - "Had the unchecked acceptance at OBS-21 concerned a material claim, this would fall to ~74."

  - competency: COMP.ERROR_DETECTION
    registry_version: 2.0.0
    estimate:                                    # P5.R13 — two numbers, never one
      sensitivity_d_prime: 1.94
      criterion: 0.12
      disposition: CALIBRATED                    # CREDULOUS | CALIBRATED | SKEPTICAL | INDISCRIMINATE
      hit_rate: 0.73
      false_alarm_rate: 0.12
    confidence: 0.91
    sufficiency: SUFFICIENT                      # 28 claims >= min 24
    estimator: { type: signal_detection, version: 3.0.0 }
    evidence_citations: [OBS-1, OBS-3, OBS-6, OBS-11, ...]
    explanation: >
      Detected 8 of 11 flawed claims while wrongly challenging only 2 of 17 sound ones.
      This is discriminating detection rather than blanket suspicion — the candidate
      accepted correct working (OBS-3) while rejecting the flawed premise it rested on.
      One miss was excluded: its knowledge prerequisite (LO.MATH.OPT.3) is not yet
      evidenced at the required level.

  - competency: COMP.VERIFICATION_CAPABILITY
    estimate: { value: 79, scale: 0-100, level: 2 }
    confidence: 0.86
    sufficiency: SUFFICIENT
    transferability: { claim: domain_bound, evidence_status: asserted }
    prerequisite_exclusions: [{ claim: CLM-19, objective: LO.MATH.OPT.3, status: PREREQUISITE_UNMET }]
    explanation: >
      Checks chosen were sound where the underlying objective was mastered. One claim
      was excluded from this estimate because the candidate has not yet demonstrated
      LO.MATH.OPT.3 — a miss there reflects curriculum position, not carelessness.

  - competency: COMP.COMMUNICATION
    estimate: null
    sufficiency: INSUFFICIENT_EVIDENCE
    reason: TIER_CANNOT_OBSERVE
    explanation: >
      This assessment ran at the STRUCTURED tier, which does not analyse written
      explanation. No conclusion can be drawn. This is a limitation of the instrument,
      not a finding about the student.

fairness:
  checks_run: [PROXY_FEATURE_AUDIT, DIF_SCREEN, CLAIM_LEVEL_DIF]
  demographic_features_used: none
  flags: []

integrity: { proctoring_level: L1_SOFT, signals: [], status: NO_ADVERSE_FINDING }

reproducibility:
  deterministic: true
  pipeline_version_set: PVS-2026-07-27-a
  extraction_modes: { deterministic: 34, model_based: 0 }
```

### 8.6 Stakeholder Report (SR)

```yaml
report_id: SR-2026-99120
cao_ref: CAO-2026-88214
llr_ref: LLR-40318                   # for the trajectory panel
audience: TEACHER
spec_ref: { id: RS.TEACHER.STANDARD, version: 2.0.0 }
generated_at: 2026-07-27T11:00:00Z
holder_role: CONDUCTING_INSTITUTION  # v1.1 — no consent gate; this is a school record
consent_ref: null

disclosure:
  fields_included: [competency_profile, detection_analysis, trajectory, evidence_drill_down, class_position, recommendations]
  fields_withheld: [other_students_individual_data]
  withholding_basis: POLICY

content:
  competency_profile:
    - { competency: COMP.CONCEPTUAL, value: 81, confidence: 0.93, interval: [75, 87], label: domain_specific }
    - { competency: COMP.VERIFICATION_DISPOSITION, value: 88, confidence: 0.94, interval: [82, 93], label: transferability_under_study }
    - { competency: COMP.VERIFICATION_CAPABILITY, value: 79, confidence: 0.86, interval: [71, 86], label: domain_specific }
    - { competency: COMP.COMMUNICATION, value: null, status: NOT_ASSESSED, reason: "Structured tier" }

  detection_analysis:
    sensitivity: 1.94
    disposition: CALIBRATED
    teacher_note: >
      Discriminating — challenges flawed claims without challenging sound ones. Compare
      with three students in this class showing INDISCRIMINATE patterns, who challenge
      everything and so detect nothing.

  trajectory:                        # P6.R11 — never a merged score
    competency: COMP.VERIFICATION_DISPOSITION
    points:
      - { date: 2025-11-14, value: 62, confidence: 0.88, interval: [54, 70], instrument: ADO-2025-0902, subject: MATHEMATICS }
      - { date: 2026-03-02, value: 74, confidence: 0.91, interval: [67, 81], instrument: ADO-2026-0041, subject: MATHEMATICS }
      - { date: 2026-06-19, value: 71, confidence: 0.79, interval: [61, 81], instrument: ADO-2026-0088, subject: PHYSICS }
      - { date: 2026-07-27, value: 88, confidence: 0.94, interval: [82, 93], instrument: ADO-2026-0114, subject: MATHEMATICS }
    note: "Points are shown separately by instrument and subject. No average is computed."

  curriculum_gaps:
    - { objective: LO.MATH.OPT.3, cohort_mastery: 0.34, note: "Weakest objective in this class; consider re-teaching." }

  recommendations:
    - { intervention: "Constraint-derivation practice set", motivated_by: COMP.VERIFICATION_CAPABILITY, evidence: [OBS-19] }

drill_down: { enabled: true, endpoint: /v1/reports/SR-2026-99120/evidence/{claim_id} }

audit: { generated_by: system, access_log_ref: AL-2026-99120, expires_at: null }
```

### 8.7 Longitudinal Learner Record (LLR) *(new in v1.1)*

#### 8.7.1 Purpose and placement

The LLR is the learner's **lifetime memory** — the record of how their capability developed across
every assessment, subject, and year.

In education this is not a hazard to be managed, it is the point. A single examination shows a
snapshot; a trajectory across years shows learning, and learning is what schools exist to produce. It
is also the only structure that can answer the transferability question (§7.5).

**Placement.** The LLR is a store, not a pillar. Written by Pillar 5, read by Pillar 6, governed by
Pillar 7. It sits in a **separate database** from the operational pipeline, for four reasons: a
different access pattern (write-once, read-rarely, retained for decades vs. hot transactional), a
different retention regime, a different security boundary, and because "erase this learner's history"
must be a bounded operation rather than a hunt.

#### 8.7.2 Four-tier semantic compression

ANAF's pipeline is already a compression pipeline: IES → CEM → CAO is roughly 100× reduction per
step, and each step is *more* meaningful, not less. Lifetime memory continues that same gradient
along the time axis.

| Tier | Age | Holds | Per session |
|---|---|---|---|
| **T0 Raw** | 0 – 90 days | Full IES + CEM + CAO. Every event, verbatim. | 5 – 50 MB |
| **T1 Distilled** | 90 d – 2 y | Observations + CAO. Verbatims only where cited by a surviving observation. Keystroke, focus, and idle detail collapses to aggregate signatures. | 100 – 500 KB |
| **T2 Episodic** | 2 – 7 y | One record per assessment: estimates, confidence, item metadata, detection summary, a small set of representative evidence exemplars. | 5 – 20 KB |
| **T3 Semantic** | 7 y + / lifetime | Trajectory only: estimates over time with confidence and version pins, plus the learner signature (§8.7.4). | 1 – 5 KB |

**A complete K–12 lifetime record lands well under a megabyte at T3.** Storage was never the
constraint — meaning was. Byte-level compression would keep everything and make none of it useful;
semantic compression keeps what a teacher would actually want in year seven.

**The governing rule for every transition:**

> A tier transition MUST preserve the ability to reconstruct the **claims**, and MUST NOT preserve
> the ability to reconstruct the **raw behavior**.

This is also the privacy mechanism. Lifetime retention of keystroke-level records on an eleven-year-
old runs straight into erasure rights and data-minimization law. A record that grows progressively
more abstract as it ages *is* data minimization, operating on a schedule rather than as an exception.

Every transition is:
- **Irreversible.** No path back to the discarded fidelity.
- **Logged.** An audit event with actor, schedule, and effective date.
- **Hash-anchored.** A hash over the discarded data is retained, so its prior existence and integrity
  are provable without holding it.
- **Verified.** The compression-fidelity suite (§7.6) asserts both halves of the governing rule.

#### 8.7.3 Interaction with appeals and research

**Appeals bind to T0.** You cannot contest a result whose evidence has been compressed away. The
appeal window MUST NOT exceed T0 retention (P7.R5); the default 90 days aligns them. After the
window, results are final and compression is safe. An open appeal freezes its session at T0 until
resolved.

**Re-extraction needs a corpus.** P4.R7 promises evidence can be regenerated under improved
extractors, which is impossible once raw streams are gone. The resolution is a **consented,
de-identified research corpus** — a default 5% sample retained at T0 fidelity indefinitely, under
separate consent, with access restricted to Pillar 7's research function. This preserves the
improvement flywheel and the validity program without anyone else's raw data surviving.

#### 8.7.4 Structure

```yaml
llr_id: LLR-40318
learner_ref: LRN-40318
opened_at: 2024-09-03
last_updated: 2026-07-27
conducting_institutions: [INST-2201]
consent_capacity: { subject_age_band: 13_TO_15, release_authority: [GUARDIAN, INSTITUTION], transition_age: 18 }

trajectories:                        # P6.R11 — per competency, never merged
  - competency: COMP.VERIFICATION_DISPOSITION
    registry_versions_seen: [1.0.0, 2.0.0]
    version_mapping: [{ from: 1.0.0, to: 2.0.0, method: SPLIT, note: "v1 VERIFICATION split; historical points mapped to DISPOSITION with widened interval." }]
    points:
      - cao_ref: CAO-2025-33102
        date: 2025-11-14
        subject: MATHEMATICS
        instrument: ADO-2025-0902
        value: 62
        confidence_at_assessment: 0.88
        confidence_now: 0.71          # decayed by staleness — see below
        interval_now: [48, 76]
        tier: STRUCTURED
        pipeline_version_set: PVS-2025-11-14-c
        storage_tier: T2
      - cao_ref: CAO-2026-88214
        date: 2026-07-27
        subject: MATHEMATICS
        instrument: ADO-2026-0114
        value: 88
        confidence_at_assessment: 0.94
        confidence_now: 0.94
        interval_now: [82, 93]
        tier: STRUCTURED
        storage_tier: T0

learner_signature:                    # §8.7.5 — survives to T3
  computed_at: 2026-07-27
  from_assessments: 7
  dimensions:
    verification_propensity: 0.81
    detection_sensitivity_mean: 1.72
    criterion_disposition: CALIBRATED
    delegation_style: SELECTIVE       # what they hand to AI vs. keep
    iteration_depth_mean: 2.4         # prompts per claim before resolution
    recovery_after_misled: 0.88       # do they re-check harder after being caught out
    confidence_calibration: 0.79      # stated vs. actual
  stability: { n_subjects: 3, cross_subject_variance: 0.14 }

storage:
  current_tiers: { T0: 1, T1: 2, T2: 4, T3: 0 }
  next_transition: { session: SES-2026-71190, from: T0, to: T1, scheduled: 2026-09-17 }
  research_corpus_member: true
  total_bytes: 214880

audit:
  transitions: [{ session: SES-2025-20114, from: T0, to: T1, at: 2026-02-12, hash_anchor: <hash>, actor: system }]
  erasure_requests: []
```

**Staleness decays confidence, not the estimate.** *(v1.1)* A result from three years ago remains an
accurate record of what was observed then; what should weaken is your belief that it still holds. So
`value` is immutable and `confidence_now` narrows over time, widening `interval_now`. This is more
honest than decaying the score, and far easier to explain to a student contesting an old result.

**Registry version changes are mapped, not silently applied.** When a competency definition changes —
as `COMP.VERIFICATION` did between registry v1 and v2 — the trajectory records the mapping and widens
intervals on remapped historical points, rather than pretending the old and new measures are the same
thing.

#### 8.7.5 Feed-forward: formative only

Memory may influence future assessment **only** under these rules:

| Use | Formative | Summative / high-stakes |
|---|---|---|
| Item-exposure avoidance (never serve the same item twice) | **Permitted** | **Permitted** |
| Difficulty targeting from prior estimates | **Permitted** | **Forbidden** |
| Competency prioritization (assess what's weak) | **Permitted** | **Forbidden** |
| Prior estimates as inference priors (Pillar 5) | **Permitted** | **Forbidden** |

**Why the line is here.** Adapting to a learner is what a good teacher does, and forbidding it in
formative assessment would waste the framework's best pedagogical asset. But in a summative context
it is indefensible: a student inferred to be weak receives shaped assessment, which produces evidence
confirming the inference. Self-fulfilling labels, applied at the moment they carry the most
consequence.

Exposure avoidance is exempt in both directions because it removes a confound rather than creating
one — nobody should be scored on an item they have already seen.

The ADO MUST declare `purpose`, and Pillar 5 MUST refuse LLR priors when `purpose` is
`education_summative`, `certification`, or `hiring`.

#### 8.7.6 Interface

```
POST /v1/llr/{learner_id}/append        { cao_id }          -> write a CAO into the record
GET  /v1/llr/{learner_id}/trajectory    ?competency=&subject=
GET  /v1/llr/{learner_id}/signature
POST /v1/llr/{learner_id}/compress      { session_id, to_tier }   -> Pillar 7 only
GET  /v1/llr/{learner_id}/priors        ?ado_id=            -> fails closed for summative purposes
POST /v1/llr/{learner_id}/erase         { scope, authority } -> capacity-checked erasure
GET  /v1/llr/{learner_id}/audit
```

**Written by:** Pillar 5. **Read by:** Pillar 6, and Pillar 5 for formative priors only.
**Governed by:** Pillar 7.

---

## 9. The Question Schema

The Question is the atomic unit. One schema must carry a quadratic optimisation problem, a history
source critique, and a software debugging task without change — that is the test of whether the
framework is genuinely domain-agnostic.

```yaml
Question_ID:
Domain:
Topic:
Subtopic:

Learning_Objectives:         # [] — curriculum objectives or role requirements
Competencies_Targeted:       # [] — registry IDs

Difficulty_Level:            # 0..1, calibrated
Cognitive_Level:             # domain-pack-declared taxonomy

Scenario:                    # case, dataset, codebase, document, vignette, word problem

Correct_Expert_Solution:     # gold standard + reasoning trace              [SEALED]
AI_Initial_Response:         # opening turn, decomposed into claims          [SEALED]
Claims:                      # [] — id, span, text, SOUND | FLAWED           [SEALED]
Mutations:                   # [] — per flawed claim; class, severity,       [SEALED]
                             #      location, correct value, rationale,
                             #      knowledge_prerequisite, consequential_claims

Allowed_AI_Behaviours:       # persona, capabilities, challenge policy, hints,
                             # standardization level, forbidden acts

Interaction_Pattern:         # AI_COLLABORATION | CHALLENGE_RESPONSE | CRITIQUE | DEBATE | ...

Expected_Evidence:           # [] — descriptor refs, weights, polarity, extraction mode
  - Challenge the flawed claim, unprompted
  - Verify independently
  - Accept sound claims correctly

Reasoning_Checkpoints:       # [] — prompt, trigger, required?
                             # corroborating evidence only (P4.R10)

Scoring_Rubric:              # rubric ref or inline                          [SEALED]
Time_Limit:
Adaptation_Rules:            # [] — condition → effect
```

**Sealing.** `[SEALED]` fields are encrypted separately and never delivered to Pillar 3 (§2.6). The
delivery surface cannot leak what it cannot decrypt.

**No recognition scaffolds.** The claim decomposition is an internal scoring structure. It is never
rendered to the candidate as a list to choose from (P2.R12, §0.2).

### 9.1 The same schema across four domains

| Field | Mathematics *(Y10)* | History *(Y12)* | Biology *(Y11)* | Programming *(professional)* |
|---|---|---|---|---|
| `Scenario` | Fencing against a river; maximise area. | Three primary sources on one event. | Experimental data with a confounded variable. | A repo with an intermittent failure. |
| Flawed claim | "Fencing must cover all four sides." | "This source is reliable because it is contemporaneous." | "The correlation shows the treatment caused the effect." | "The lock here is unnecessary." |
| `Mutations[].class` | `INTERPRETATION` | `ASSUMPTION` | `REASONING` | `REASONING` |
| Sound claims alongside | The differentiation and arithmetic are correct. | The chronology is accurate. | The statistics are computed correctly. | The stack trace reading is correct. |
| `Expected_Evidence` | Re-reads the problem and re-derives the constraint. | Interrogates the source's provenance. | Identifies the uncontrolled variable. | Reproduces the failure before accepting the diagnosis. |
| Rubric criterion | Derives x + 2y = 200. | Names the unstated assumption. | Distinguishes correlation from causation. | Identifies the true root cause. |

Same engine. Same contracts. Different specifications.

---

## 10. Conformance

**Level 1 — Contract conformant.** Implements all seven contracts with valid schemas; passes contract
conformance and golden-path fixtures (§7.6). Any pillar may be a stub.

**Level 2 — Pillar conformant.** All of Level 1, plus every MUST in Pillars 1–6. In particular:
- P1.R3 evidence sufficiency, P1.R9 tier compatibility
- P2.R3 ground-truth mutation records, P2.R11 claim decomposition and mix, P2.R12 no recognition scaffolds
- P3.R5 tamper-evident stream, P3.R11 claim span recording
- P4.R2 event-cited observations, P4.R4 null evidence, P4.R10 self-report cap, P4.R11 timing constraints, P4.R12 declared extraction mode
- P5.R3 citation-backed explanations, P5.R4 insufficiency-not-low-score, P5.R12 prerequisite conditioning, P5.R13 sensitivity and criterion
- P6.R2 per-audience disclosure, P6.R3 uncertainty in every report, P6.R11 trajectories not merged scores, P6.R13 release authority

**Level 3 — Governed.** All of Level 2, plus an operating Pillar 7: audit trail, appeals with SLA
bound to T0 retention, published fairness and calibration monitoring, version-set pinning, LLR
compression governance, and a validity program with published methodology (§7.5).

**Only Level 3 may be used for high-stakes decisions** — public examination, certification, admission,
or hiring. Levels 1 and 2 are for formative, practice, and development use.

---

## 11. Open Questions

Resolved in v1.1 and removed: the control-item ratio *(→ claim-level detection, §2.5)*; coachability
*(→ P4.R10)*; timing signals *(→ P4.R11)*; fidelity vs. equivalence *(→ §3.6.1 and the path-invariance
suite)*; cost *(→ tiers, §0.6)*; data rights *(→ §6.5.1)*; cross-assessment persistence *(→ §8.7)*.

Still open:

1. **Construct validity remains a hypothesis.** ANAF's central claim — that AI-collaboration
   behavior is a real, measurable, partly transferable capability — is not yet evidenced. §7.5 and
   the registry's `transferability.evidence_status` exist to settle it. A `refuted` outcome would
   require restructuring the competency set, not the pillars.
2. **What is the right claim decomposition granularity?** A sentence, a step, a proposition? Too fine
   and the candidate is punished for not challenging trivia; too coarse and detection statistics get
   noisy. Probably domain-dependent (P2.X7), but the principle needs settling.
3. **Consequential claims.** When CLM-1 is flawed and CLM-2 and CLM-5 inherit the error, does catching
   CLM-5 count as one detection or three? v1.1 marks them via `consequential_claims` but does not
   specify the scoring. Under-counting punishes a student who spots the downstream absurdity; over-
   counting rewards one insight three times.
4. **Structured-tier ceiling.** How much of `COMP.REASONING` can deterministic extraction actually
   reach? P7.R12's tier-equivalence suite will measure it, but if the gap is large, the primary
   education product has a real limitation to disclose.
5. **Learner signature stability.** Is the signature (§8.7.4) stable enough to be meaningful across a
   decade of development, or does it mostly track maturation? If the latter, it is a growth measure
   rather than a characterization, and should be presented as one.
6. **Compression and fairness auditing.** Fairness audits at T2/T3 fidelity may be unable to detect
   bias visible only in raw interaction. Does the 5% research corpus give enough power, and is it
   representative once consent is opt-in?
7. **Erasure versus trajectory integrity.** If a learner erases year 8, does the trajectory show a
   gap, silently close up, or refuse to render? Each choice leaks or distorts something.
8. **Cross-border learners.** A student assessed in one jurisdiction and applying in another meets
   two incompatible consent and retention regimes. Which governs the record?

---

## Appendix A — Glossary

| Term | Meaning |
|---|---|
| **ADO** | Assessment Definition Object — Pillar 1's output; declarative intent. |
| **AP** | Assessment Package — Pillar 2's output; materialized, candidate-agnostic content. |
| **IES** | Interaction Event Stream — Pillar 3's output; the raw behavioral record. |
| **CEM** | Candidate Evidence Model — Pillar 4's output; structured observations. |
| **CAO** | Competency Assessment Object — Pillar 5's output; estimates with confidence. |
| **SR** | Stakeholder Report — Pillar 6's output; audience-scoped rendering. |
| **LLR** | Longitudinal Learner Record — lifetime memory; separate store, compressed by tier. |
| **Claim** | A discrete, individually adjudicable assertion within an AI response. |
| **Mutation** | A deliberate, characterized, ground-truthed flaw in a claim. |
| **Observation** | A single event-cited evidence item mapped to a competency. |
| **Strength** | How much an observation says about a competency, if true. |
| **Reliability** | How confident the extractor is that the observation occurred as described. |
| **Sensitivity (d′)** | How well a candidate distinguishes flawed claims from sound ones. |
| **Criterion** | How readily a candidate challenges, independent of accuracy. |
| **Descriptor** | An Evidence Blueprint entry defining an observable behavior for a competency. |
| **Sufficiency** | Whether enough evidence exists to make a determination at all. |
| **Tier (assessment)** | `STRUCTURED` or `FULL` — which extractors run, and so which competencies are observable. |
| **Tier (storage)** | T0–T3 — LLR compression level, a function of age. |
| **Learner signature** | The compact, durable description of how a learner works with AI. |
| **Version set** | A pinned bundle of every specification, model, and prompt version used. |
| **Domain Pack** | Pluggable domain bundle: taxonomy, notation, difficulty anchors, mutation classes. |

## Appendix B — Document conventions

- Responsibilities are numbered `Pn.Rm`, extension points `Pn.Xm`. These IDs are stable across
  revisions and are the anchors for conformance testing and traceability.
- **MUST / SHOULD / MAY** follow RFC 2119.
- YAML in this document is illustrative. The normative artifacts are the JSON Schema files under
  `schemas/`.
