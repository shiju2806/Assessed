# AI-Native Assessment Framework (ANAF) v1.0

**Status:** Draft for review
**Editor:** shiju2806
**Scope:** A specification-driven framework for assessing students and job seekers through observed
work with AI, rather than through timed recall exams or unstructured interviews.

---

## 0. Preamble

### 0.1 The thesis

Traditional exams and interviews measure what a person can produce **without** AI. That measurement
is becoming both easy to fake and decreasingly predictive of real performance, because real work is
now performed *with* AI in the loop.

ANAF measures something different and harder to fake: **how a person behaves when working alongside
a fallible AI collaborator.** Do they verify? Do they detect the error? Do they explain why they
rejected it? Do they know when to defer?

The atomic observation in ANAF is not "was the final answer correct." It is **the interaction
record** — the sequence of prompts, corrections, acceptances, rejections, and justifications that
produced the answer. Correctness is one signal among many, and often not the most informative one.

### 0.2 The design rule: specification-driven, not code-driven

Every assessment is constructed from **versioned specifications** — Assessment Definition, Question
Schema, Evidence Schema, Competency Schema, Report Schema. The engine executes specifications; it
does not hard-code domains, competencies, or scoring logic.

This yields four properties that are the point of the whole exercise:

| Property | Meaning |
|---|---|
| **Domain agnostic** | A new subject is a new specification, not a new codebase. |
| **Auditable** | Every score traces to evidence, every piece of evidence traces to an interaction, every interaction traces to a specification clause. |
| **Extensible** | New competencies or AI behaviors are additive; they do not restructure the architecture. |
| **Research-friendly** | One specification can evolve while the others stay pinned, so changes are attributable. |

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
                      ▼
        Pillar 6 — Outcomes & Reporting
                      │  Stakeholder Reports (SR)
                      ▼
        Pillar 7 — Governance & Evolution
```

### 0.4 The adjacency rule

**Every pillar communicates only with adjacent pillars, and only through the named contract.**

A pillar may not reach backwards for context it was not given, and may not reach forwards to
influence how its output is consumed. If Pillar 5 needs to know the question's difficulty, that
difficulty must have been carried forward in the CEM — Pillar 5 does not query Pillar 2.

Two consequences follow, and both are load-bearing:

1. **Each contract must be self-sufficient.** This is why the contracts below carry provenance and
   redundant context. It is deliberate denormalization.
2. **Any pillar can be replaced wholesale** — including by a human, or by a competitor's engine —
   as long as it honors the contract on both sides.

**Pillar 7 is the sole exception.** It is not in the data path. It observes all six contracts
read-only and acts by *changing specifications*, never by intervening in a live assessment. See
§7.

### 0.5 How to read a pillar section

Each pillar is specified in seven parts, in this order:

1. **Purpose** — the one thing this pillar is for, and what it is explicitly *not* for.
2. **Inputs** — what it receives, and from where.
3. **Responsibilities** — the obligations it must discharge. Numbered `Pn.Rm` for traceability.
4. **Internal Components** — the decomposition. Component boundaries are normative; component
   *implementations* are not.
5. **Outputs** — what it emits.
6. **Interfaces** — the exact contract, plus operational API surface.
7. **Extension Points** — where an implementer plugs in new behavior without forking the pillar.
   Numbered `Pn.Xm`.

Conformance language follows RFC 2119: **MUST**, **SHOULD**, **MAY**.

---

## Pillar 1 — Assessment Specification

### 1.1 Purpose

**Describe what assessment should exist.**

Not generate it. Not deliver it. Simply define it.

Pillar 1 is a *declarative* layer. Its output is a complete, validated, human-auditable description
of an assessment's intent — the domain, the competencies to be measured, the evidence that would
constitute proof of those competencies, and the policies under which the assessment runs.

**Explicitly out of scope:** producing questions, producing scenarios, selecting AI models,
scheduling, or anything candidate-specific.

**Why this pillar exists separately.** If specification and generation are fused, you cannot answer
"was this a fair assessment?" independently of "was this a good question?" Separating them makes the
intent reviewable *before* any content is produced, and makes generation reproducible against a
fixed target.

### 1.2 Inputs

| Input | Source | Required | Notes |
|---|---|---|---|
| **Domain** | Author / catalog | Yes | Mathematics, Physics, History, Law, Medicine, Programming, … Free-form but MUST resolve to a registered Domain Pack (§1.7 P1.X1). |
| **Curriculum context** | Standards body, institution | For education use | Grade, unit, learning outcomes, standards, objectives. |
| **Role context** | Employer, job description | For hiring use | Role title, seniority, job family, required competencies. |
| **Assessment blueprint intent** | Author | Yes | Difficulty target, duration, adaptivity on/off, question count, question types, interaction pattern, pass criteria. |
| **Competency selection** | Competency Registry | Yes | Which competencies are targeted, and at what weight. |
| **AI configuration intent** | Author | Yes | Allowed AI behaviors, error types to inject, hint policy, reasoning-prompt policy. |
| **Governance policies** | Pillar 7 | Yes | Proctoring level, accessibility requirements, accommodations, privacy/retention class. |

**Note on the education/hiring split.** Curriculum context and role context are alternative framings
of the same slot: *what body of external expectation is this assessment accountable to?* An
implementation MUST support at least one and SHOULD support both. Everything downstream is
identical.

#### 1.2.1 The Competency Registry

Competencies are not free text. They are registry entries with stable IDs, so that a score means the
same thing across assessments, domains, and years.

A registry entry MUST have:

```yaml
competency_id: COMP.VERIFICATION          # stable, namespaced, never reused
name: Independent Verification
definition: >
  The candidate establishes the truth of a claim by means independent of the
  source that made the claim.
observable_indicators:                     # what Pillar 4 looks for
  - recomputes a result by a second method
  - seeks a corroborating source
  - checks a result against a known bound or sanity constraint
anti_indicators:                           # what counts as evidence against
  - restates the AI's justification as their own
  - accepts a numeric result without any check
proficiency_scale:                         # ordered, defined levels
  - level: 1
    descriptor: Verifies only when prompted, and only by re-reading.
  - level: 2
    descriptor: Verifies independently when the claim is surprising.
  - level: 3
    descriptor: Verifies routinely, choosing a method independent of the source.
  - level: 4
    descriptor: Verifies proportionately — calibrates effort to stakes and risk.
parent: COMP.EPISTEMIC                     # optional, forms a competency tree
version: 1.0.0
```

The **ANAF Core Competency Set** ships as the default registry. It is a starting point, not a
closed list:

- `COMP.CONCEPTUAL` — Conceptual Understanding
- `COMP.REASONING` — Reasoning
- `COMP.VERIFICATION` — Independent Verification
- `COMP.ERROR_DETECTION` — Error Detection
- `COMP.AI_COLLABORATION` — AI Collaboration (prompting, delegation, iteration)
- `COMP.DECISION` — Decision Making Under Uncertainty
- `COMP.ETHICS` — Ethical Judgment
- `COMP.COMMUNICATION` — Explanation & Justification

### 1.3 Responsibilities

- **P1.R1** — MUST resolve all inputs into a single, self-contained Assessment Definition Object.
- **P1.R2** — MUST validate that every targeted competency exists in the registry at a pinned
  version.
- **P1.R3** — MUST validate **evidence sufficiency**: for every targeted competency, the blueprint
  MUST specify at least one interaction pattern capable of producing observable evidence for it.
  *A competency that cannot be observed by this assessment's design MUST NOT be targeted.* This is
  the single most important validation in Pillar 1 — it is what prevents the system from reporting
  confident scores for things it never actually watched.
- **P1.R4** — MUST attach governance policy to the ADO by reference and by resolved value, so that
  downstream pillars need not consult Pillar 7.
- **P1.R5** — MUST assign an immutable, versioned `ado_id`. An ADO is never edited; a change
  produces a new version.
- **P1.R6** — MUST record provenance for every field: who or what set it, when, and from which
  source specification.
- **P1.R7** — MUST be deterministic. The same inputs MUST produce a byte-identical ADO modulo
  timestamps and IDs.
- **P1.R8** — MUST reject, not silently repair, an under-specified or contradictory blueprint.

### 1.4 Internal Components

| Component | Responsibility |
|---|---|
| **Intent Intake** | Accepts authored input (UI, YAML, API) and normalizes it into canonical form. |
| **Domain Resolver** | Binds `domain` to a registered Domain Pack; pulls domain-specific topic taxonomy, notation rules, and default AI-behavior profiles. |
| **Curriculum / Role Mapper** | Maps external standards or job descriptions onto internal learning objectives and competency IDs. Emits an explicit mapping table (auditable, not implicit). |
| **Competency Resolver** | Resolves competency IDs against the registry at pinned versions; expands parents into children where the blueprint targets a tree node. |
| **Evidence Planner** | For each targeted competency, selects the interaction patterns and expected-evidence descriptors that would demonstrate it. Produces the Evidence Blueprint. This is the component that makes P1.R3 checkable. |
| **Blueprint Composer** | Assembles Assessment / Competency / Question / Evidence blueprints into a coherent whole; allocates question counts and weights across competencies. |
| **Policy Binder** | Resolves governance policy references into concrete values (retention days, proctoring mode, accommodation set). |
| **Specification Validator** | Runs the full validation suite (P1.R2, R3, R8) and produces a pass/fail report with per-rule detail. |
| **ADO Serializer** | Emits the signed, versioned ADO. |

### 1.5 Outputs

Four blueprints, carried inside one object:

1. **Assessment Blueprint** — shape, duration, adaptivity, pass criteria.
2. **Competency Blueprint** — what is measured, at what weight, to what proficiency target.
3. **Question Blueprint** — how many questions of what type, at what difficulty distribution,
   covering which topics and competencies.
4. **Evidence Blueprint** — what observable behaviors will count as evidence for each competency.

### 1.6 Interface

**Produces: Assessment Definition Object (ADO).** Full schema in §8.1.

Operational surface (illustrative REST shape; transport is not normative):

```
POST   /v1/specifications                 -> create draft ADO from intent
POST   /v1/specifications/{id}/validate   -> validation report
POST   /v1/specifications/{id}/publish    -> freeze, sign, assign version
GET    /v1/specifications/{id}            -> retrieve ADO
GET    /v1/competencies                   -> registry listing
```

**Consumed by:** Pillar 2 only.

### 1.7 Extension Points

- **P1.X1 — Domain Pack.** A pluggable bundle providing topic taxonomy, notation/rendering rules,
  default difficulty anchors, and domain-appropriate AI error types. Adding "Actuarial Science" is
  a Domain Pack, not a code change.
- **P1.X2 — Competency Registry Provider.** Institutions may supply their own registry (e.g. a
  national curriculum framework, or an employer's internal competency model) so long as entries
  satisfy the registry schema.
- **P1.X3 — Standards Importer.** Adapter that ingests an external standards document (Common Core,
  NGSS, a job architecture, a professional body syllabus) and emits objective mappings.
- **P1.X4 — Validation Rule Plugin.** Additional institution-specific validation rules, run by the
  Specification Validator alongside the core suite.
- **P1.X5 — Blueprint Template.** Reusable parameterized blueprints ("30-minute screening for
  mid-level backend engineer") that pre-fill intent.

---

## Pillar 2 — Assessment Orchestration

### 2.1 Purpose

**Generate the assessment from the blueprint.**

Pillar 2 turns declarative intent into concrete, validated, ready-to-deliver content: questions,
scenarios, expert solutions, and — distinctively — the *deliberate flaws* the AI collaborator will
exhibit.

**Explicitly out of scope:** knowing who the candidate is, running sessions, or scoring.

**Why this pillar exists separately.** Content generation is the least trustworthy step in the
pipeline (it is where an LLM is most likely to produce something ambiguous, wrong, or unfair).
Isolating it behind a validation gate means the rest of the system can assume its input is sound.

### 2.2 Inputs

- **Assessment Definition Object** (from Pillar 1) — the sole functional input.
- **Item Bank** (optional) — previously generated, calibrated questions available for reuse.
- **Generation Model Configuration** — which models, at which versions, with which decoding
  parameters. Pinned and recorded, never implicit.

### 2.3 Responsibilities

- **P2.R1** — MUST produce a set of questions satisfying every constraint in the Question Blueprint
  (count, type mix, difficulty distribution, topic and competency coverage).
- **P2.R2** — MUST produce, for every question, an **Expert Solution** that is independently correct
  and does not depend on the AI collaborator's output.
- **P2.R3** — MUST produce, for every question that uses AI mutation, a **ground-truth flaw record**:
  what error was injected, of what type, at what location, and what the correct value/reasoning was.
  Without this record the question is unscoreable and MUST be rejected.
- **P2.R4** — MUST calibrate difficulty so that parallel forms of the same assessment are
  psychometrically equivalent within a stated tolerance.
- **P2.R5** — MUST validate every item for ambiguity, duplication, invalid mutation (a mutation that
  is not actually wrong, or that makes the item unanswerable), cultural/linguistic bias, and
  accessibility.
- **P2.R6** — MUST NOT emit an item that fails validation. Failed items are quarantined with a
  reason, and regeneration is attempted up to a configured bound.
- **P2.R7** — MUST record full provenance: generating model + version, prompt template + version,
  seed, timestamp, human reviewer (if any).
- **P2.R8** — MUST support human-in-the-loop review as a gate before publication, where policy
  requires it.
- **P2.R9** — MUST NOT include any candidate-identifying information. An Assessment Package is
  candidate-agnostic.
- **P2.R10** — MUST emit, alongside each question, the machine-readable **Expected Evidence** and
  **Scoring Rubric** derived from the Evidence Blueprint, so Pillars 4 and 5 need no access to
  Pillar 1.

### 2.4 Internal Components

| Component | Responsibility |
|---|---|
| **Question Generator** | Produces the question stem, prompt, and required assets from the blueprint slot. |
| **Scenario Generator** | Produces the surrounding context — the case, dataset, codebase, document, or clinical vignette the question operates on. Scenarios are reusable across several questions. |
| **Expert Solution Generator** | Produces the gold-standard solution and its reasoning trace. MUST be generated independently of the AI Mutation Engine (different model call, ideally different model) to avoid correlated error. |
| **AI Mutation Engine** | Injects the deliberate flaw the AI collaborator will present. See §2.5. |
| **Difficulty Calibrator** | Estimates item difficulty (a priori from features; a posteriori from response data supplied by Pillar 7) and enforces the blueprint's distribution. |
| **Validation Engine** | Ambiguity detection, duplicate/near-duplicate detection against the item bank, mutation validity check, bias screening, accessibility check, solvability check. |
| **Item Bank** | Persistent store of calibrated items with usage statistics and exposure control. |
| **Package Assembler** | Sequences items, attaches adaptation rules, binds rubrics and expected evidence, and serializes the Assessment Package. |

### 2.5 The AI Mutation Engine

This is the mechanism that makes ANAF an *AI-native* assessment rather than an exam with a chatbot
attached. It deserves its own specification.

**A mutation is a deliberate, characterized, ground-truthed flaw in the AI collaborator's output.**

Mutation taxonomy (extensible via P2.X3):

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
| `NONE` | Control condition — the AI is correct. | (See below.) |

**`NONE` is mandatory, not optional.** An assessment in which the AI is always wrong measures
reflexive contrarianism, not verification. Every Assessment Package MUST include a configured
proportion of `NONE` items, default **≥ 25%**, so that *accepting* AI output can be the correct
behavior and false-positive error-flagging is measurable. The candidate MUST NOT be told which
items are which.

Each mutation record MUST carry:

```yaml
mutation_id: MUT-8823
class: ARITHMETIC
severity: material          # cosmetic | material | critical
location:                   # where in the AI's response the flaw sits
  span: "step 3, second line"
  anchor: "= 4,812"
correct_value: "4,182"
detection_difficulty: 0.62  # 0..1, calibrated
rationale: >
  Digit transposition in the sum. Method is correct; only the arithmetic fails.
  A candidate who recomputes will catch it; a candidate who only reads the
  method will not.
```

### 2.6 Outputs

**Assessment Instance** — a fully materialized, candidate-agnostic assessment: ordered items,
scenarios, adaptation rules, rubrics, expected evidence, AI behavior configuration, and the sealed
answer key (expert solutions + mutation records).

The **answer key is a separately encrypted section** of the package. Pillar 3 receives the package
but MUST NOT be able to read the key section; only Pillars 4 and 5 hold that grant. This prevents
the delivery surface — the part exposed to the candidate — from ever holding the answers.

### 2.7 Interface

**Produces: Assessment Package (AP).** Full schema in §8.2.

```
POST /v1/packages                     { ado_id } -> generate package (async)
GET  /v1/packages/{id}                -> package (key section redacted by default)
GET  /v1/packages/{id}/validation     -> per-item validation report
POST /v1/packages/{id}/review         -> human reviewer decision
POST /v1/packages/{id}/publish        -> freeze and sign
GET  /v1/items?competency=&difficulty= -> item bank query
```

**Consumed by:** Pillar 3 (delivery view), Pillar 4 and Pillar 5 (key view).

### 2.8 Extension Points

- **P2.X1 — Generator Plugin.** Alternative question/scenario generators per domain or item type
  (e.g. a code-repository generator that produces a real failing test suite).
- **P2.X2 — Solution Verifier.** Domain-specific formal verification of the expert solution: a CAS
  for mathematics, a test runner for programming, a rules engine for law. Where a verifier exists,
  it SHOULD gate publication.
- **P2.X3 — Mutation Class.** Register new mutation classes with their own generation and validity
  checks.
- **P2.X4 — Calibration Model.** Swap the psychometric model (IRT 1PL/2PL/3PL, Elo, learned
  feature-based estimator).
- **P2.X5 — Validation Rule Plugin.** Additional item-level checks (institutional style, reading
  level, jurisdiction-specific legal accuracy).
- **P2.X6 — Item Bank Provider.** Bring an existing institutional item bank.

---

## Pillar 3 — Assessment Delivery

### 3.1 Purpose

**Execute the assessment.**

Pillar 3 runs a live session: authenticates the candidate, presents items, hosts the AI
collaborator, enforces time and adaptation rules, and — its most important obligation — **emits a
complete, faithful, tamper-evident record of everything that happened.**

**Explicitly out of scope:** interpreting anything. Pillar 3 forms no judgment about quality,
correctness, or competence. It observes and records.

**Why this pillar exists separately.** The recording obligation and the interpretation obligation
have opposite failure modes. A recorder that interprets will discard what it deems irrelevant —
and in ANAF the discarded material (the hesitation, the abandoned prompt, the re-read) is often
the signal. Keeping Pillar 3 judgment-free guarantees the evidence layer gets everything.

### 3.2 Inputs

- **Assessment Package** (delivery view, key section sealed).
- **Candidate identity + entitlement** — who is sitting, and their authorization to sit.
- **Accommodation profile** — extra time, screen reader, language, input modality.
- **Session policy** — proctoring level, permitted resources, break rules.

### 3.3 Responsibilities

- **P3.R1** — MUST authenticate the candidate to the proctoring level required by policy.
- **P3.R2** — MUST present items exactly as packaged, applying accommodations without altering
  construct-relevant content.
- **P3.R3** — MUST host the AI collaborator such that it exhibits *only* the behaviors and mutations
  the package specifies. Free-running model behavior is a conformance failure.
- **P3.R4** — MUST emit an event for **every** candidate-observable and candidate-generated action,
  including non-actions of significance (idle periods, focus loss, re-reads).
- **P3.R5** — MUST emit events in a **tamper-evident**, append-only, hash-chained stream with
  monotonic sequence numbers and server-authoritative timestamps.
- **P3.R6** — MUST enforce timing and adaptation rules deterministically and log every adaptation
  decision *with its trigger*.
- **P3.R7** — MUST degrade safely: network loss, tab crash, or client failure MUST NOT lose
  committed events or silently truncate the stream. Gaps MUST be explicitly marked, never elided.
- **P3.R8** — MUST NOT reveal correctness, mutation presence, or score at any point during the
  session unless the package explicitly configures formative feedback.
- **P3.R9** — MUST record the exact AI responses shown, so that Pillar 4 interprets against what the
  candidate actually saw, not what was intended.
- **P3.R10** — MUST collect required Reasoning Checkpoint responses at their specified triggers.

### 3.4 Internal Components

| Component | Responsibility |
|---|---|
| **Session Manager** | Lifecycle: create, start, pause, resume, submit, abandon, expire. Owns session state and recovery. |
| **Identity & Authentication** | Candidate verification to the required assurance level. |
| **Proctoring Controller** | Applies the configured proctoring level (see §3.5); emits integrity signals as events, never as verdicts. |
| **Presentation Layer** | Renders items, scenarios, and assets; applies accommodations. |
| **AI Conversation Engine** | Hosts the collaborator. Constrains it to the packaged persona, capability set, hint policy, and scripted mutations. See §3.6. |
| **Timer & Pacing Controller** | Per-item and whole-session timing, extensions, break handling. |
| **Adaptive Controller** | Selects the next item per the package's adaptation rules; logs decision + trigger. |
| **Interaction Manager** | Captures the raw interaction surface — keystroke-level edits, selections, scroll/focus, tool invocations. |
| **Checkpoint Prompter** | Injects Reasoning Checkpoints at their triggers and captures responses. |
| **Event Emitter** | Sequences, hashes, signs, buffers, and durably ships events. |

### 3.5 Proctoring levels

Proctoring is a *policy dial* set in Pillar 1, applied here, and audited by Pillar 7. Levels are
ordered and each is a superset of the one before:

| Level | Description | Signals collected |
|---|---|---|
| `L0_NONE` | Practice / formative. | Interaction only. |
| `L1_SOFT` | Integrity nudges, no surveillance. | Focus/blur, paste, elapsed time. |
| `L2_BEHAVIORAL` | Behavioral analytics, no media capture. | + keystroke dynamics, response-latency profile, navigation patterns. |
| `L3_MEDIA` | Recorded session. | + webcam/screen/audio capture. |
| `L4_LIVE` | Live human proctor. | + proctor annotations. |

Two rules bind at every level:
- Proctoring signals are emitted as **observations**, never as accusations. Adjudication is Pillar 7.
- The proctoring level in force MUST be disclosed to the candidate before the session starts and
  recorded in the stream.

### 3.6 The AI Conversation Engine

The engine hosts a **scripted-but-conversational** collaborator. It must be simultaneously natural
enough to be a realistic partner and controlled enough that two candidates receive equivalent
treatment.

Requirements:

- **R-A. Mutation fidelity.** The packaged mutation MUST appear in the AI's first substantive
  response, at its specified location, with its specified severity.
- **R-B. Defense under challenge.** When the candidate challenges the flawed content, the AI follows
  the packaged `challenge_policy`:
  - `CONCEDE_IMMEDIATELY` — admits on any challenge.
  - `CONCEDE_ON_EVIDENCE` — admits only when the candidate supplies a specific, correct refutation.
    *(Default. It is the condition that distinguishes assertion from verification.)*
  - `DEFEND_ONCE` — pushes back once, then concedes on evidence.
  - `NEVER_CONCEDE` — maintains the position. Tests conviction under social pressure.
- **R-C. Hint policy.** Hints are packaged, tiered, and **logged as events with their cost**. An
  unlogged hint corrupts the evidence record.
- **R-D. Equivalence.** Free-form conversation is permitted, but responses on the assessed content
  MUST stay within packaged bounds. Implementations SHOULD use constrained generation with a
  post-generation guard that checks the response does not leak the flaw, the answer, or the fact
  that a mutation exists.
- **R-E. Full fidelity logging.** Every AI turn is logged verbatim, with model, version, latency,
  and which packaged behavior it realized.
- **R-F. Off-script containment.** If the candidate drives the conversation outside packaged bounds,
  the engine MUST deflect within persona and emit an `AI_DEFLECTION` event, rather than improvising
  assessed content.

### 3.7 Outputs

**Interaction Event Stream** — the complete, ordered, hash-chained record of the session.

Every click. Every prompt. Every correction. Every hesitation.

### 3.8 Interface

**Produces: Interaction Event Stream (IES).** Full schema in §8.3.

```
POST /v1/sessions                      { package_id, candidate_id } -> session
POST /v1/sessions/{id}/start
GET  /v1/sessions/{id}/next-item
POST /v1/sessions/{id}/events          -> client-observed event batch (server re-stamps)
POST /v1/sessions/{id}/ai-turn         -> candidate message to collaborator
POST /v1/sessions/{id}/submit
GET  /v1/sessions/{id}/stream          -> IES (authorized consumers only)
```

Streams SHOULD also be available over an append-only log transport for real-time consumption by
Pillar 4.

**Consumed by:** Pillar 4.

### 3.9 Extension Points

- **P3.X1 — Proctoring Provider.** Plug in third-party proctoring at L3/L4.
- **P3.X2 — Identity Provider.** SSO, national ID, biometric, institutional roster.
- **P3.X3 — AI Model Provider.** Swap the collaborator's underlying model, subject to conformance
  against the AI-behavior test suite (§7.6).
- **P3.X4 — Interaction Surface.** New work environments: an IDE, a spreadsheet, a CAD tool, a
  clinical simulator. Each surface implements the event-emission contract.
- **P3.X5 — Adaptive Policy.** Alternative next-item selection strategies.
- **P3.X6 — Accommodation Provider.** New accommodation types.

---

## Pillar 4 — Evidence Collection

> This is where the framework becomes different from every exam platform.

### 4.1 Purpose

**Transform interactions into evidence.**

Pillar 4 reads a raw behavioral stream and produces *structured, competency-mapped, provenance-
carrying observations.* It answers "what did this person demonstrably do?" — never "how good are
they?"

**Explicitly out of scope:** scoring, ranking, weighting, or inference beyond what the record
directly supports.

**Why this pillar exists separately.** This is the boundary between **observation and judgment**,
and it is the single most important separation in ANAF. Because it exists:

- Evidence can be **shown to the candidate** as fact, independent of any contested score.
- Scoring models can be **replaced and re-run** against historical evidence.
- Bias audits can ask a sharp question: *was the evidence different, or only the scoring?*
- An appeal can dispute the inference without disputing the record.

### 4.2 Inputs

- **Interaction Event Stream** (from Pillar 3).
- **Assessment Package key view** — expert solutions, mutation records, expected evidence
  descriptors, rubrics.

Pillar 4 needs the key view because "the candidate identified the arithmetic error at step 3" is
only assertable if you know an arithmetic error was planted at step 3.

### 4.3 Responsibilities

- **P4.R1** — MUST extract observations from the event stream and emit them as typed, discrete
  evidence items.
- **P4.R2** — Every observation MUST cite its source events by sequence number. **An observation
  with no event citation is invalid** and MUST NOT enter the Evidence Graph.
- **P4.R3** — MUST map observations to competency IDs using the package's Expected Evidence
  descriptors, and MUST record *which* descriptor licensed the mapping.
- **P4.R4** — MUST record **negative and null evidence** explicitly: the candidate did not verify;
  the candidate did not detect the planted flaw; the candidate flagged a flaw where none existed.
  Absence of positive evidence is itself evidence and MUST be represented, not left silent.
- **P4.R5** — MUST attach a **strength** and **reliability** to each observation (§4.5).
- **P4.R6** — MUST NOT compute scores, aggregate across observations into a proficiency estimate,
  or apply competency weights.
- **P4.R7** — MUST be **re-runnable**: given the same IES and package, it regenerates equivalent
  evidence. Extractor versions are recorded so historical evidence can be regenerated under new
  extractors and the difference attributed.
- **P4.R8** — MUST flag low-confidence extractions for human review rather than silently emitting
  them at full strength.
- **P4.R9** — MUST preserve verbatim quotations for any observation derived from candidate free
  text, so the claim is checkable by a human.

### 4.4 Internal Components

| Component | Responsibility |
|---|---|
| **Stream Normalizer** | Validates the hash chain, orders events, reconstructs session state, marks gaps. |
| **Behavior Extractor** | Detects behavioral patterns from event structure. *Asked AI for evidence → Verification.* Independent recomputation, source-checking, re-reading, revisiting, systematic vs. scattershot exploration. |
| **Reasoning Extractor** | Analyzes candidate free text and checkpoint responses. *"I think this is wrong because…" → Reasoning.* Extracts claims, justifications, causal structure, and hedging/calibration language. |
| **Decision Extractor** | Classifies each disposition of AI output: **accepted / rejected / modified / ignored**, plus latency and whether the disposition was correct given the mutation record. This is the highest-value extractor in the system. |
| **Temporal Analyzer** | Hesitation, dwell, revision cycles, time-to-first-challenge, pacing shifts. Emits behavioral-signal evidence — deliberately weak-strength, since timing is noisy and culturally variable. |
| **Artifact Analyzer** | Evaluates produced work products (code, essay, diagram, calculation) against expert solution and rubric. |
| **Evidence Mapper** | Binds observations to competency IDs via Expected Evidence descriptors. |
| **Evidence Graph Builder** | Assembles observations, their events, their competencies, and their relations (`supports`, `contradicts`, `supersedes`, `elaborates`) into the graph. |
| **Quality Gate** | Reliability scoring, contradiction detection, human-review flagging. |

### 4.5 Evidence strength and reliability

Two orthogonal numbers, both required, frequently conflated — keep them distinct:

- **Strength (0..1)** — *how much this observation tells you about the competency, if true.*
  Independently recomputing and catching a planted error is strong. Taking 4 seconds longer than
  average is weak.
- **Reliability (0..1)** — *how confident the extractor is that the observation occurred as
  described.* A verbatim explicit statement is near-1. An LLM-inferred intent from an ambiguous
  prompt is much lower.

Pillar 5 needs both: a strong-but-unreliable observation and a weak-but-certain one must not be
treated alike.

### 4.6 Outputs

**Evidence Graph.** Not scores. Evidence.

Nodes: observations, events, competencies, artifacts, items.
Edges: `cites`, `maps_to`, `supports`, `contradicts`, `supersedes`, `produced_in`.

### 4.7 Interface

**Produces: Candidate Evidence Model (CEM).** Full schema in §8.4.

```
POST /v1/evidence            { session_id } -> extract (async)
GET  /v1/evidence/{id}       -> CEM
GET  /v1/evidence/{id}/graph -> graph projection
POST /v1/evidence/{id}/rerun { extractor_version } -> re-extraction
GET  /v1/evidence/{id}/review-queue -> low-reliability items
```

**Consumed by:** Pillar 5. Also readable by Pillar 6 for evidence-drill-down in reports, but only
via the CAO's citations — Pillar 6 MUST NOT re-derive competency claims from raw evidence.

### 4.8 Extension Points

- **P4.X1 — Extractor Plugin.** New extractors for new interaction surfaces or new signal types.
- **P4.X2 — Evidence Type.** Register new observation types with their strength semantics.
- **P4.X3 — Artifact Analyzer.** Domain-specific work-product analysis (test-suite execution, proof
  checking, clinical protocol conformance).
- **P4.X4 — Mapping Policy.** Alternative observation → competency mapping strategies.
- **P4.X5 — Reliability Model.** Swap the extractor-confidence estimator.

---

## Pillar 5 — Competency Inference

> This is the brain.

### 5.1 Purpose

**Infer competency from evidence — with calibrated confidence and a stated reason.**

Pillar 5 is the only place in ANAF where a judgment about a person is made. It converts an evidence
graph into proficiency estimates, each carrying an uncertainty and an explanation that cites the
evidence it rests on.

**Explicitly out of scope:** deciding what the estimate *means* for a stakeholder — pass/fail,
hire/no-hire, placement. That is Pillar 6, driven by policy.

### 5.2 Inputs

- **Candidate Evidence Model** (from Pillar 4).
- **Competency Registry** definitions (pinned versions, carried in the CEM).
- **Rubrics** (carried forward from the package via the CEM).
- **Calibration parameters** — population norms, item parameters, model weights supplied by Pillar 7.

### 5.3 Responsibilities

- **P5.R1** — MUST produce, for each targeted competency, a proficiency estimate on the registry's
  declared scale.
- **P5.R2** — MUST produce a **calibrated** confidence for each estimate. "Calibrated" is a
  falsifiable claim: across the population, estimates at stated confidence *c* must be correct at
  rate ≈ *c*, and Pillar 7 MUST measure this continuously (§7.5).
- **P5.R3** — MUST produce a human-readable explanation for every estimate that cites specific
  evidence observations. **An estimate without citations MUST NOT be emitted.**
- **P5.R4** — MUST propagate evidence insufficiency as *wide uncertainty or explicit
  non-determination* — never as a low score. Not observed ≠ not competent. This is the most common
  and most damaging failure mode in assessment systems, and conformance requires getting it right.
- **P5.R5** — MUST detect and report internal inconsistency (evidence pulling in opposite
  directions) rather than averaging it away.
- **P5.R6** — MUST run bias detection across protected and proxy attributes and attach the result
  to the CAO.
- **P5.R7** — MUST be **deterministic and reproducible** given the same CEM and model version.
  Where an LLM participates in inference, it MUST be pinned, temperature-0 or seeded, and its raw
  output retained.
- **P5.R8** — MUST record model identity and version for every estimate, so a later model change is
  attributable and historical results can be re-derived.
- **P5.R9** — MUST NOT use candidate demographic attributes as inference features.
- **P5.R10** — MUST support **counterfactual explanation**: for any estimate, state what different
  evidence would have moved it, and by roughly how much.

### 5.4 Internal Components

| Component | Responsibility |
|---|---|
| **Evidence Aggregator** | Groups observations by competency; resolves `supersedes`; weights by strength × reliability. |
| **Rubric Engine** | Applies rubrics to artifact and reasoning evidence to produce criterion-level judgments. |
| **Competency Estimator** | Produces the proficiency estimate. Pluggable: rubric-weighted, Bayesian/IRT latent-trait, or learned model (§5.5). |
| **Confidence Calculator** | Computes uncertainty from evidence quantity, strength, reliability, and coherence. |
| **Consistency Checker** | Flags contradictory evidence patterns and within-competency variance. |
| **Sufficiency Gate** | Enforces P5.R4 — if evidence falls below the competency's declared minimum, emits `INSUFFICIENT_EVIDENCE` instead of a number. |
| **Bias Detection** | Group-fairness metrics, differential item functioning, proxy-feature auditing. |
| **Explainability Engine** | Generates the citation-backed narrative and counterfactuals. |
| **Profile Composer** | Assembles the Competency Assessment Object. |

### 5.5 Estimation models

An implementation MUST provide at least one and MUST declare which is in use per competency:

1. **Rubric-weighted aggregation** — transparent, defensible, no training data required. The
   recommended default and the required fallback.
2. **Bayesian latent-trait (IRT-style)** — posterior over proficiency given item parameters;
   confidence falls out naturally as posterior width. Needs calibrated item parameters from Pillar 7.
3. **Learned model** — highest ceiling, highest governance burden. Permitted only with a documented
   training set, published fairness metrics, and a rubric-weighted fallback available for appeals.

Regardless of model, **the explanation MUST reflect the actual computation.** A post-hoc narrative
that rationalizes a black-box score is a conformance failure, not an explanation.

### 5.6 Outputs

**Competency Profile.** Per competency: estimate, confidence, evidence citations, explanation,
counterfactuals, consistency and sufficiency flags.

```
Verification        91    confidence 97%
Error Detection     84    confidence 92%
AI Collaboration    73    confidence 88%
Ethical Judgment    —     INSUFFICIENT_EVIDENCE (2 observations, minimum 5)
```

That last line is not a gap in the product. It is the product working.

### 5.7 Interface

**Produces: Competency Assessment Object (CAO).** Full schema in §8.5.

```
POST /v1/inference            { cem_id } -> infer (async)
GET  /v1/inference/{id}       -> CAO
GET  /v1/inference/{id}/explanation?competency=
POST /v1/inference/{id}/rerun { model_version }
GET  /v1/inference/{id}/fairness
```

**Consumed by:** Pillar 6.

### 5.8 Extension Points

- **P5.X1 — Estimator Plugin.** New estimation models per competency or domain.
- **P5.X2 — Rubric Type.** New rubric formalisms (analytic, holistic, single-point, learning
  progression).
- **P5.X3 — Confidence Model.** Alternative uncertainty quantification.
- **P5.X4 — Fairness Metric.** Additional or jurisdiction-mandated fairness tests.
- **P5.X5 — Explanation Renderer.** Audience-tuned explanation styles (child, teacher, regulator).
- **P5.X6 — Norm Provider.** Population norms for percentile reporting.

---

## Pillar 6 — Outcomes & Reporting

### 6.1 Purpose

**Convert competency into useful outputs.**

Pillar 6 takes one CAO and renders it for many audiences, each with different needs, different
rights, and different levels of permitted detail.

**Explicitly out of scope:** changing any estimate. Pillar 6 selects, aggregates, contextualizes,
and presents — it never re-scores.

### 6.2 Inputs

- **Competency Assessment Object** (from Pillar 5).
- **Stakeholder context** — who is viewing, in what role, under what entitlement.
- **Reporting policy** — decision thresholds, disclosure rules, retention rules (from Pillar 7).
- **Cohort data** — for aggregate views (teacher, school, employer funnel).

### 6.3 Responsibilities

- **P6.R1** — MUST render audience-appropriate reports without altering underlying estimates.
- **P6.R2** — MUST enforce **disclosure policy per audience**. Different stakeholders see different
  subsets, and this MUST be enforced at generation time, not by UI hiding.
- **P6.R3** — MUST carry uncertainty into every report. Point estimates presented without their
  confidence are a conformance failure — including in aggregate and visual views.
- **P6.R4** — MUST make evidence reachable: every reported claim drills down to its citations.
- **P6.R5** — MUST apply decision thresholds *as declared policy*, and label them as such
  ("does not meet this employer's threshold of 70"), never as properties of the person.
- **P6.R6** — MUST support candidate-facing transparency: the candidate MUST be able to see what was
  concluded about them, on what evidence, and how to contest it.
- **P6.R7** — MUST anonymize and suppress small cells in aggregate reporting (default minimum cell
  size 5).
- **P6.R8** — MUST record every report generation and access in the audit log.
- **P6.R9** — Learning recommendations MUST cite the competency gap and evidence that motivated
  them.
- **P6.R10** — MUST NOT produce a single composite "overall score" unless the reporting policy
  explicitly defines one, including its formula, which is then disclosed with the number.

### 6.4 Internal Components

| Component | Responsibility |
|---|---|
| **Report Composer** | Renders a report from a Report Specification + CAO. |
| **Disclosure Filter** | Applies per-audience field-level policy before rendering. |
| **Threshold Engine** | Applies declared decision rules; emits outcome + the rule that produced it. |
| **Aggregation Engine** | Cohort roll-ups, distributions, gap analysis; enforces small-cell suppression. |
| **Narrative Generator** | Audience-appropriate prose from CAO explanations. Constrained to claims present in the CAO. |
| **Visualization Service** | Competency radar, evidence timeline, growth trajectory, distribution views — all uncertainty-bearing. |
| **Learning Recommendation Engine** | Maps gaps to interventions/resources via a pluggable catalog. |
| **Delivery & Access Control** | Distribution, entitlement checks, expiring share links, revocation. |

### 6.5 Report specifications and audiences

Reports are **specifications**, not code (§0.2). A Report Specification declares audience, permitted
fields, aggregation level, narrative style, and thresholds.

| Audience | Sees | Never sees |
|---|---|---|
| **Candidate / Student** | Strengths, weaknesses, growth, full evidence, all explanations, appeal route. | Other candidates; raw item keys. |
| **Teacher** | Per-student profiles, class distribution, competency gaps, curriculum-gap analysis. | Data outside their roster. |
| **Parent/Guardian** | Child's profile in plain language; growth over time. | Other students; behavioral proctoring detail. |
| **School / Institution** | Cohort aggregates, trends, program effectiveness. | Individual-level data beyond entitlement. |
| **Employer** | Competency radar, decision profile, risk areas, role fit, evidence excerpts *the candidate consented to release*. | Health/demographic data, proctoring media, non-consented evidence. |
| **Certification Body** | Outcome, evidence sufficiency, integrity attestation, audit trail. | Formative and practice data. |

**Candidate consent gates employer disclosure.** The candidate MUST be able to see exactly what an
employer will receive before releasing it. Report generation for an employer audience MUST verify a
consent record and fail closed without one.

### 6.6 Outputs

**Stakeholder Reports** — rendered documents, dashboards, and structured payloads:

- *Student:* strengths, weaknesses, growth, evidence.
- *Teacher:* class insights, competency distribution, curriculum gaps.
- *Employer:* competency radar, decision profile, risk areas.

### 6.7 Interface

**Produces: Stakeholder Report (SR).** Full schema in §8.6.

```
POST /v1/reports                { cao_id, audience, spec_id } -> report
GET  /v1/reports/{id}
GET  /v1/reports/{id}/evidence/{claim_id} -> drill-down
POST /v1/reports/aggregate      { cohort, spec_id }
POST /v1/reports/{id}/share     { recipient, expires_at } -> consented share
POST /v1/consent                { cao_id, audience, fields }
```

**Consumed by:** stakeholders (humans and systems). Observed by Pillar 7.

### 6.8 Extension Points

- **P6.X1 — Report Specification.** New audiences and formats without code changes.
- **P6.X2 — Visualization Plugin.** New chart and evidence-display types.
- **P6.X3 — Recommendation Catalog.** Institution-specific learning resources and interventions.
- **P6.X4 — Export Adapter.** LMS, SIS, ATS, HRIS, PDF, Open Badges / verifiable credentials.
- **P6.X5 — Threshold Policy.** Custom decision rules per institution or role.
- **P6.X6 — Localization.** Language and cultural adaptation of narrative and visuals.

---

## Pillar 7 — Governance & Evolution

> This pillar never participates directly in assessment. It governs everything.

### 7.1 Purpose

**Ensure the system is secure, fair, lawful, valid, and improving — and prove it.**

Pillar 7 is out of the data path by construction. It observes all six contracts read-only, and its
only lever is **changing specifications**. It cannot alter a live session, an evidence record, or an
issued score.

**Why the separation is absolute.** A governance layer that can reach into results is not
governance; it is an unlogged actor. Every Pillar 7 action is a versioned specification change with
an author, a rationale, and an effective date.

### 7.2 Inputs

Read-only observation of every contract (ADO, AP, IES, CEM, CAO, SR), plus audit logs, appeals,
incident reports, external standards and regulations, and research findings.

### 7.3 Responsibilities

- **P7.R1** — MUST maintain a complete, immutable, tamper-evident audit trail across all pillars.
- **P7.R2** — MUST enforce data retention, minimization, and deletion per privacy policy and
  jurisdiction.
- **P7.R3** — MUST continuously monitor fairness across protected groups at item, evidence, and
  outcome level.
- **P7.R4** — MUST monitor and publish **calibration** — the falsifiable claim in P5.R2.
- **P7.R5** — MUST operate an appeals process with defined SLA, human review, and the power to
  invalidate an outcome.
- **P7.R6** — MUST version every specification, model, rubric, and prompt template, and pin the full
  version set used for every assessment so results are reproducible years later.
- **P7.R7** — MUST measure **predictive validity** against real-world outcomes where such data is
  lawfully available, and publish it. A framework claiming to predict performance better than
  interviews MUST be willing to be wrong in public.
- **P7.R8** — MUST NOT modify any assessment artifact. Improvements land as new specification
  versions with effective dates.
- **P7.R9** — MUST detect and respond to integrity incidents (content leakage, collusion, impostor
  testing), including item retirement.
- **P7.R10** — MUST maintain accessibility conformance (target: WCAG 2.2 AA) and validate that
  accommodations do not alter the construct measured.

### 7.4 Internal Components

| Group | Components |
|---|---|
| **Security** | Identity governance, encryption at rest/in transit, key management, audit logging, access control, penetration testing. |
| **Fairness** | Bias monitoring, differential item functioning, accessibility conformance, accommodation validation, calibration monitoring. |
| **Compliance** | Privacy (GDPR / FERPA / COPPA / EEOC / local equivalents), data retention, consent management, appeals administration, regulatory reporting. |
| **Research** | Question analytics, competency analytics, difficulty analytics, predictive-validity studies, construct-validity studies, A/B experimentation. |
| **Versioning** | Specification, model, rubric, prompt, and competency-registry version control; version-set pinning; migration and deprecation management. |
| **Continuous Improvement** | Improvement pipeline for question generation, evidence mapping, scoring models, and the competency registry itself. |

### 7.5 The validity program

Everything else is machinery. This is the part that determines whether ANAF is a real assessment
standard or an elaborate opinion generator. Six claims, each with a measurement:

| Claim | Measurement |
|---|---|
| **Reliability** | Test–retest correlation; parallel-form equivalence; internal consistency per competency. |
| **Construct validity** | Do competency estimates correlate with independent measures of the same construct, and *not* with unrelated ones? |
| **Predictive validity** | Do estimates predict later performance (course grades, job performance ratings, retention) better than the incumbent method? |
| **Calibration** | Do stated confidences match observed accuracy? Reliability diagrams, expected calibration error. |
| **Fairness** | Differential item functioning; group-wise error-rate parity; adverse-impact ratio (four-fifths rule). |
| **Resistance to gaming** | Do coached candidates score higher without competency gain? Red-team studies against known strategies. |

Each MUST have a published methodology, a target threshold, a measurement cadence, and an escalation
path when it degrades.

### 7.6 Conformance and test suites

Pillar 7 owns the suites that make "ANAF-conformant" meaningful:

- **Contract conformance** — schema validation for all six contracts at every boundary.
- **Golden-path fixtures** — reference ADO → AP → IES → CEM → CAO → SR chains that an implementation
  must reproduce.
- **AI-behavior conformance** — an AI collaborator swapped in under P3.X3 must pass a suite proving
  mutation fidelity, challenge-policy adherence, and non-leakage.
- **Adversarial suite** — known attacks: prompt injection against the collaborator, answer
  extraction, evidence spoofing, checkpoint gaming.
- **Fairness regression** — fairness metrics run as blocking CI on every model or rubric change.

### 7.7 Outputs

- **Framework Updates** — new versions of ANAF specifications and schemas.
- **Assessment Updates** — item retirements, recalibrations, rubric revisions.
- **Research Reports** — validity, fairness, and calibration findings, published on a stated cadence.
- **Audit Attestations** — evidence for regulators, accreditors, and institutional buyers.
- **Incident Reports** — integrity events and their remediation.

### 7.8 Extension Points

- **P7.X1 — Compliance Module.** Jurisdiction-specific regimes.
- **P7.X2 — Audit Sink.** External SIEM / immutable log providers.
- **P7.X3 — Fairness Metric.** Additional or mandated metrics.
- **P7.X4 — Research Connector.** Outcome-data linkage for validity studies, under consent.
- **P7.X5 — Certification Authority.** Issue verifiable credentials for outcomes.
- **P7.X6 — Improvement Policy.** Rules governing automated recalibration and rollout.

---

## 8. Cross-Cutting Contracts

Every pillar communicates using contracts:

```
Assessment Definition Object  ↓
Assessment Package            ↓
Interaction Event Stream      ↓
Candidate Evidence Model      ↓
Competency Assessment Object  ↓
Stakeholder Reports
```

**These six interfaces are the platform APIs.** They are the normative core of ANAF: an
implementation is conformant if and only if it honors them. Schemas below are illustrative YAML;
the normative artifacts are the JSON Schema files under `schemas/` (to be generated from this
document).

### 8.0 Common envelope

Every contract object carries this envelope:

```yaml
anaf_version: "1.0"
object_type: ADO | AP | IES | CEM | CAO | SR
object_id: <uuid>
version: <semver>
created_at: <iso8601>
created_by: { actor_type: human|system, actor_id: <id> }
provenance:
  upstream_object_id: <uuid or null>
  pipeline_version_set: <version-set-id>   # pins every spec/model/prompt version in play
signature: <detached signature over the canonical serialization>
```

`pipeline_version_set` is what makes P7.R6 achievable: one identifier resolving to the exact
versions of every specification, model, prompt, and rubric involved.

### 8.1 Assessment Definition Object (ADO)

```yaml
ado_id: ADO-2026-0001
version: 1.0.0
title: "Mid-level Backend Engineer — AI Collaboration Screen"
purpose: hiring            # education | hiring | certification | practice

context:
  domain: PROGRAMMING
  domain_pack: { id: DP.PROGRAMMING, version: 2.1.0 }
  curriculum: null
  role:
    title: Backend Engineer
    seniority: mid
    job_family: SOFTWARE_ENGINEERING

assessment_blueprint:
  duration_minutes: 60
  question_count: { min: 5, max: 8 }
  adaptive: true
  question_types: [DEBUG_WITH_AI, DESIGN_CRITIQUE, CODE_REVIEW]
  interaction_patterns: [AI_COLLABORATION, CHALLENGE_RESPONSE]
  difficulty: { target: 0.6, distribution: normal, spread: 0.15 }
  pass_criteria:
    type: competency_thresholds
    rules:
      - { competency: COMP.VERIFICATION, min_level: 3 }
      - { competency: COMP.ERROR_DETECTION, min_level: 3 }

competency_blueprint:
  targets:
    - competency: COMP.VERIFICATION
      registry_version: 1.0.0
      weight: 0.30
      target_level: 3
      min_observations: 5
    - competency: COMP.ERROR_DETECTION
      registry_version: 1.0.0
      weight: 0.25
      target_level: 3
      min_observations: 5
    - competency: COMP.AI_COLLABORATION
      registry_version: 1.0.0
      weight: 0.25
      target_level: 3
      min_observations: 4
    - competency: COMP.COMMUNICATION
      registry_version: 1.0.0
      weight: 0.20
      target_level: 2
      min_observations: 3

question_blueprint:
  slots:
    - slot_id: Q1
      topic: CONCURRENCY
      cognitive_level: ANALYZE      # Bloom or equivalent, declared by domain pack
      difficulty: 0.5
      competencies: [COMP.ERROR_DETECTION, COMP.VERIFICATION]
      mutation_class: REASONING
      time_limit_seconds: 600
    - slot_id: Q2
      topic: API_DESIGN
      cognitive_level: EVALUATE
      difficulty: 0.65
      competencies: [COMP.AI_COLLABORATION, COMP.COMMUNICATION]
      mutation_class: NONE          # control item
      time_limit_seconds: 600
  mutation_mix:
    NONE: 0.25                       # see §2.5 — mandatory control proportion
    REASONING: 0.25
    ASSUMPTION: 0.25
    ARITHMETIC: 0.25

evidence_blueprint:
  descriptors:
    - descriptor_id: EV.VERIFY.INDEPENDENT
      competency: COMP.VERIFICATION
      observable: "Candidate independently recomputes or re-derives an AI-supplied result."
      detectable_from: [TOOL_INVOCATION, ARTIFACT_EDIT, AI_PROMPT_CONTENT]
      strength: 0.9
    - descriptor_id: EV.REJECT.JUSTIFIED
      competency: COMP.ERROR_DETECTION
      observable: "Candidate rejects flawed AI output and names the specific defect."
      detectable_from: [DECISION_EVENT, CHECKPOINT_RESPONSE]
      strength: 1.0
    - descriptor_id: EV.ACCEPT.UNVERIFIED
      competency: COMP.VERIFICATION
      observable: "Candidate accepts a material AI claim with no verification action."
      polarity: negative
      detectable_from: [DECISION_EVENT]
      strength: 0.7

ai_configuration:
  persona: COLLABORATIVE_PEER
  capabilities: [EXPLAIN, GENERATE_CODE, CRITIQUE, CITE]
  challenge_policy: CONCEDE_ON_EVIDENCE
  hint_policy: { enabled: true, max_hints: 2, cost_per_hint: 0.1, tiered: true }
  reasoning_prompts: { enabled: true, frequency: per_item }

governance:
  proctoring_level: L2_BEHAVIORAL
  accessibility: { wcag_target: "2.2-AA", screen_reader: true }
  accommodations_supported: [EXTRA_TIME_1_5X, EXTENDED_BREAKS, HIGH_CONTRAST]
  privacy: { retention_days: 730, data_class: SENSITIVE, jurisdiction: [EU, US] }
  consent_required: [EMPLOYER_DISCLOSURE, BEHAVIORAL_ANALYTICS]

validation:
  status: PASSED
  evidence_sufficiency: PASSED       # P1.R3
  report_ref: VAL-2026-0001
```

### 8.2 Assessment Package (AP)

```yaml
package_id: AP-2026-0001
ado_ref: { id: ADO-2026-0001, version: 1.0.0 }
generated_at: 2026-07-26T10:00:00Z
generation_provenance:
  models: [{ role: question_gen, id: <model-id>, version: <ver>, seed: 8823 }]
  prompt_templates: [{ id: PT.QGEN.PROGRAMMING, version: 3.2.0 }]
  human_review: { required: true, reviewer_id: U-104, decision: APPROVED }

items:
  - question_id: Q-88231
    slot_ref: Q1
    domain: PROGRAMMING
    topic: CONCURRENCY
    subtopic: RACE_CONDITIONS
    learning_objectives: [LO.CONC.3]
    competencies_targeted: [COMP.ERROR_DETECTION, COMP.VERIFICATION]
    difficulty_level: 0.52
    cognitive_level: ANALYZE
    scenario:
      scenario_id: SC-4410
      type: CODE_REPOSITORY
      content_ref: assets/sc-4410/
      description: "Service with an intermittent double-charge under concurrent requests."
    prompt: "Work with the AI assistant to find and fix the defect."
    interaction_pattern: AI_COLLABORATION
    time_limit_seconds: 600

    allowed_ai_behaviours:
      persona: COLLABORATIVE_PEER
      capabilities: [EXPLAIN, GENERATE_CODE, CRITIQUE]
      challenge_policy: CONCEDE_ON_EVIDENCE
      hints: [{ tier: 1, text: "...", cost: 0.1 }, { tier: 2, text: "...", cost: 0.2 }]
      forbidden: [REVEAL_MUTATION, REVEAL_EXPERT_SOLUTION, DISCUSS_SCORING]

    reasoning_checkpoints:
      - checkpoint_id: CP-1
        trigger: ON_AI_OUTPUT_DISPOSITION
        prompt: "Why did you accept or reject the assistant's diagnosis?"
        required: true
      - checkpoint_id: CP-2
        trigger: ON_ITEM_SUBMIT
        prompt: "How did you verify your fix?"
        required: true

    expected_evidence:
      - { descriptor_ref: EV.REJECT.JUSTIFIED, weight: 1.0 }
      - { descriptor_ref: EV.VERIFY.INDEPENDENT, weight: 0.9 }
      - { descriptor_ref: EV.ACCEPT.UNVERIFIED, weight: 0.7, polarity: negative }

    adaptation_rules:
      - { if: "evidence(EV.REJECT.JUSTIFIED).present", then: "next_difficulty += 0.1" }
      - { if: "hints_used >= 2", then: "next_difficulty -= 0.1" }

    # ---- sealed key section: not readable by Pillar 3 ----
    key:
      correct_expert_solution:
        content: "..."
        reasoning_trace: "..."
        verifier: { type: TEST_SUITE, ref: assets/sc-4410/tests, status: PASSING }
      ai_initial_response:
        content: "..."
        contains_mutation: true
      mutation:
        mutation_id: MUT-8823
        class: REASONING
        severity: material
        location: { span: "diagnosis paragraph 2", anchor: "the lock is unnecessary here" }
        correct_value: "the lock is required; the check-then-act is not atomic"
        detection_difficulty: 0.62
        rationale: "Plausible but invalid inference from single-threaded reasoning."
      scoring_rubric:
        rubric_id: RB-771
        type: analytic
        criteria:
          - { id: C1, competency: COMP.ERROR_DETECTION, descriptor: "Identifies the race condition as the root cause", levels: [...] }
          - { id: C2, competency: COMP.VERIFICATION, descriptor: "Confirms the fix by an independent means", levels: [...] }

validation:
  status: PASSED
  per_item: [{ question_id: Q-88231, ambiguity: PASS, duplication: PASS, mutation_validity: PASS, bias: PASS, accessibility: PASS, solvability: PASS }]
  quarantined_items: []

sequencing:
  mode: ADAPTIVE
  entry_item: Q-88231
  termination: { max_items: 8, min_items: 5, stop_on: SUFFICIENT_EVIDENCE }
```

### 8.3 Interaction Event Stream (IES)

```yaml
stream_id: IES-2026-55120
session_id: SES-2026-55120
package_ref: { id: AP-2026-0001, version: 1.0.0 }
candidate_ref: CAND-90211           # pseudonymous; PII held separately
started_at: 2026-07-26T14:00:00Z
ended_at: 2026-07-26T14:58:12Z
completion_status: SUBMITTED         # SUBMITTED | EXPIRED | ABANDONED | VOIDED
integrity:
  hash_algorithm: sha256
  chain_head: <hash>
  gaps: []                           # explicitly marked, never elided (P3.R7)
proctoring: { level: L2_BEHAVIORAL, disclosed_at: 2026-07-26T13:58:00Z }
accommodations_applied: [EXTRA_TIME_1_5X]

events:
  - { seq: 1, ts: "...T14:00:00.000Z", type: SESSION_START, item: null, payload: {}, prev_hash: null, hash: <h1> }
  - { seq: 2, ts: "...T14:00:04.220Z", type: ITEM_PRESENTED, item: Q-88231, payload: { presentation_ms: 220 }, prev_hash: <h1>, hash: <h2> }
  - { seq: 3, ts: "...T14:00:41.910Z", type: SCENARIO_INTERACTION, item: Q-88231, payload: { action: FILE_OPEN, target: "payments/charge.py" } }
  - { seq: 4, ts: "...T14:01:15.003Z", type: AI_PROMPT, item: Q-88231, payload: { text: "why does this double charge?", chars: 31, compose_ms: 8400, revisions: 2 } }
  - { seq: 5, ts: "...T14:01:19.660Z", type: AI_RESPONSE, item: Q-88231, payload: { text: "...", model: <id>, version: <ver>, realized_behavior: MUTATION_PRESENTED, mutation_ref: MUT-8823, latency_ms: 4657 } }
  - { seq: 6, ts: "...T14:02:02.140Z", type: IDLE, item: Q-88231, payload: { duration_ms: 42480 } }
  - { seq: 7, ts: "...T14:02:44.300Z", type: TOOL_INVOCATION, item: Q-88231, payload: { tool: TEST_RUNNER, args: "-k concurrent", result: "1 failed" } }
  - { seq: 8, ts: "...T14:03:10.881Z", type: AI_PROMPT, item: Q-88231, payload: { text: "the concurrent test still fails with your fix — the check-then-act isn't atomic" } }
  - { seq: 9, ts: "...T14:03:16.002Z", type: AI_RESPONSE, item: Q-88231, payload: { realized_behavior: CONCEDED_ON_EVIDENCE, mutation_ref: MUT-8823 } }
  - { seq: 10, ts: "...T14:03:40.115Z", type: DECISION, item: Q-88231, payload: { disposition: REJECTED, target: AI_RESPONSE_seq5, latency_ms: 140455 } }
  - { seq: 11, ts: "...T14:04:02.900Z", type: CHECKPOINT_RESPONSE, item: Q-88231, payload: { checkpoint_id: CP-1, text: "I rejected it because the test still failed after applying its suggestion..." } }
  - { seq: 12, ts: "...T14:07:55.400Z", type: ARTIFACT_SUBMIT, item: Q-88231, payload: { artifact_id: ART-3301, type: CODE_DIFF, ref: "artifacts/ART-3301.patch" } }
  - { seq: 13, ts: "...T14:07:56.000Z", type: INTEGRITY_SIGNAL, item: Q-88231, payload: { signal: PASTE, chars: 412, source: EXTERNAL } }   # observation, not accusation
  - { seq: 14, ts: "...T14:58:12.000Z", type: SESSION_SUBMIT, item: null, payload: {} }
```

**Event type taxonomy (v1.0, extensible via P3.X4):**

`SESSION_START` · `SESSION_PAUSE` · `SESSION_RESUME` · `SESSION_SUBMIT` · `SESSION_EXPIRE`
`ITEM_PRESENTED` · `ITEM_COMPLETED` · `ITEM_SKIPPED` · `ITEM_REVISITED`
`AI_PROMPT` · `AI_RESPONSE` · `AI_DEFLECTION` · `HINT_REQUESTED` · `HINT_DELIVERED`
`DECISION` (accepted / rejected / modified / ignored)
`TOOL_INVOCATION` · `SCENARIO_INTERACTION` · `ARTIFACT_EDIT` · `ARTIFACT_SUBMIT`
`CHECKPOINT_PROMPTED` · `CHECKPOINT_RESPONSE`
`IDLE` · `FOCUS_LOST` · `FOCUS_GAINED` · `NAVIGATION`
`ADAPTATION_DECISION` · `TIMER_EVENT` · `ACCOMMODATION_APPLIED`
`INTEGRITY_SIGNAL` · `SYSTEM_ERROR` · `STREAM_GAP`

Every event carries `{ seq, ts, type, item, payload, prev_hash, hash }`. Server timestamps are
authoritative; client timestamps, where present, are carried in the payload as `client_ts` and never
substituted for `ts`.

### 8.4 Candidate Evidence Model (CEM)

```yaml
cem_id: CEM-2026-55120
stream_ref: IES-2026-55120
package_ref: { id: AP-2026-0001, version: 1.0.0 }
extractor_version_set: EVS-1.4.0
extracted_at: 2026-07-26T15:02:00Z

carried_context:                     # denormalized so Pillar 5 needs no upstream access (§0.4)
  competency_definitions: [{ id: COMP.VERIFICATION, version: 1.0.0, proficiency_scale: [...] }]
  rubrics: [{ rubric_id: RB-771, ... }]
  item_metadata: [{ question_id: Q-88231, difficulty: 0.52, mutation_class: REASONING }]

observations:
  - observation_id: OBS-1
    type: DECISION_CORRECT_REJECTION
    item: Q-88231
    competencies: [COMP.ERROR_DETECTION]
    descriptor_ref: EV.REJECT.JUSTIFIED
    polarity: positive
    strength: 1.0
    reliability: 0.98
    cites_events: [5, 8, 10, 11]
    summary: "Rejected the flawed diagnosis and named the specific defect (non-atomic check-then-act)."
    verbatim: "the concurrent test still fails with your fix — the check-then-act isn't atomic"
    extractor: { id: DecisionExtractor, version: 1.4.0 }

  - observation_id: OBS-2
    type: INDEPENDENT_VERIFICATION
    item: Q-88231
    competencies: [COMP.VERIFICATION]
    descriptor_ref: EV.VERIFY.INDEPENDENT
    polarity: positive
    strength: 0.9
    reliability: 0.99
    cites_events: [7]
    summary: "Ran the test suite to check the AI's proposed fix rather than accepting it."
    extractor: { id: BehaviorExtractor, version: 1.4.0 }

  - observation_id: OBS-3
    type: DELIBERATION
    item: Q-88231
    competencies: [COMP.REASONING]
    polarity: neutral
    strength: 0.2                    # weak by design — timing is noisy (§4.4)
    reliability: 0.95
    cites_events: [6]
    summary: "42s pause after the AI response, before any action."
    extractor: { id: TemporalAnalyzer, version: 1.4.0 }

null_evidence:                       # P4.R4 — absence recorded explicitly
  - { competency: COMP.ETHICS, expected_descriptors: [EV.ETHICS.FLAG], observed: false, reason: NO_OPPORTUNITY_PRESENTED }

relations:
  - { from: OBS-2, to: OBS-1, type: supports }

graph_summary:
  observation_count: 27
  by_competency: { COMP.VERIFICATION: 7, COMP.ERROR_DETECTION: 6, COMP.AI_COLLABORATION: 9, COMP.COMMUNICATION: 5, COMP.ETHICS: 0 }
  mean_reliability: 0.91

quality:
  review_flagged: [OBS-19]
  contradictions: []
  stream_gaps: []
```

### 8.5 Competency Assessment Object (CAO)

```yaml
cao_id: CAO-2026-55120
cem_ref: CEM-2026-55120
inferred_at: 2026-07-26T15:04:00Z
model_version_set: MVS-2.1.0

competency_profile:
  - competency: COMP.VERIFICATION
    registry_version: 1.0.0
    estimate: { value: 91, scale: 0-100, level: 3, level_descriptor: "Verifies routinely, choosing an independent method." }
    confidence: 0.97
    uncertainty_interval: [86, 95]
    sufficiency: SUFFICIENT          # observations 7 >= min 5
    consistency: CONSISTENT
    estimator: { type: rubric_weighted, version: 2.1.0 }
    evidence_citations: [OBS-2, OBS-7, OBS-11, OBS-14, OBS-18, OBS-22, OBS-25]
    explanation: >
      Verified independently on 6 of 7 opportunities, choosing a method independent of
      the assistant in 5 of those (ran the test suite, recomputed by hand, checked the
      source). The single unverified acceptance (OBS-14) was a low-stakes formatting
      claim, consistent with level-4 proportionate verification rather than an omission.
    counterfactuals:
      - "Had the unverified acceptance at OBS-14 concerned a material claim, this estimate would fall to ~78."

  - competency: COMP.ETHICS
    estimate: null
    confidence: null
    sufficiency: INSUFFICIENT_EVIDENCE      # P5.R4 — not a low score
    observations_available: 0
    minimum_required: 3
    explanation: >
      This assessment presented no situation calling for ethical judgment. No conclusion
      can be drawn. This is a limitation of the assessment, not a finding about the candidate.

fairness:
  checks_run: [PROXY_FEATURE_AUDIT, DIF_SCREEN]
  demographic_features_used: none
  flags: []

integrity:
  proctoring_level: L2_BEHAVIORAL
  signals: [{ type: PASTE, count: 3, adjudication: PENDING }]   # Pillar 7 adjudicates, not here
  status: NO_ADVERSE_FINDING

reproducibility:
  deterministic: true
  pipeline_version_set: PVS-2026-07-26-a
```

### 8.6 Stakeholder Report (SR)

```yaml
report_id: SR-2026-77401
cao_ref: CAO-2026-55120
audience: EMPLOYER
spec_ref: { id: RS.EMPLOYER.STANDARD, version: 1.2.0 }
generated_at: 2026-07-26T15:10:00Z
consent_ref: CONSENT-9931            # required for EMPLOYER audience (§6.5); fails closed without

disclosure:
  fields_included: [competency_radar, decision_profile, risk_areas, evidence_excerpts]
  fields_withheld: [proctoring_media, behavioral_timing_detail, formative_history]
  withholding_basis: POLICY + CONSENT_SCOPE

content:
  competency_radar:
    - { competency: COMP.VERIFICATION, value: 91, confidence: 0.97, interval: [86, 95] }
    - { competency: COMP.ERROR_DETECTION, value: 84, confidence: 0.92, interval: [77, 90] }
    - { competency: COMP.ETHICS, value: null, status: NOT_ASSESSED }

  decision_profile:
    outcome: MEETS_THRESHOLD
    threshold_rule: { id: TR.BACKEND.MID, expression: "VERIFICATION >= 70 AND ERROR_DETECTION >= 70" }
    statement: "Meets this employer's declared threshold for mid-level backend engineer."
    caveat: "Ethical judgment was not assessed by this instrument."

  risk_areas:
    - { area: "AI over-reliance under time pressure", evidence: [OBS-14], severity: low, confidence: 0.71 }

  evidence_excerpts:
    - { claim: "Detected and corrected a flawed AI diagnosis", citations: [OBS-1], verbatim_released: true }

drill_down:
  enabled: true
  endpoint: /v1/reports/SR-2026-77401/evidence/{claim_id}

audit:
  generated_by: system
  access_log_ref: AL-2026-77401
  expires_at: 2026-10-26T00:00:00Z
```

---

## 9. The Question Schema

The Question is the atomic unit of assessment. One schema must carry an algebra problem, a history
essay critique, and a software debugging task without change — that is the test of whether the
framework is genuinely domain-agnostic.

```yaml
Question_ID:                 # stable, unique
Domain:
Topic:
Subtopic:

Learning_Objectives:         # [] — curriculum or role objectives
Competencies_Targeted:       # [] — registry IDs

Difficulty_Level:            # 0..1, calibrated
Cognitive_Level:             # domain-pack-declared taxonomy (e.g. Bloom)

Scenario:                    # the context: case, dataset, codebase, document, vignette

Correct_Expert_Solution:     # gold standard + reasoning trace  [SEALED]
AI_Initial_Response:         # what the collaborator opens with  [SEALED]
Mutation:                    # class, severity, location, correct value, rationale  [SEALED]

Allowed_AI_Behaviours:       # persona, capabilities, challenge policy, hints, forbidden acts

Interaction_Pattern:         # AI_COLLABORATION | CHALLENGE_RESPONSE | CRITIQUE | DEBATE | ...

Expected_Evidence:           # [] — descriptor refs with weights and polarity
  - Detect arithmetic error
  - Verify independently
  - Explain reasoning

Reasoning_Checkpoints:       # [] — prompt, trigger, required?
  - Why did you reject the AI?
  - How did you verify your answer?

Scoring_Rubric:              # rubric ref or inline  [SEALED]
Time_Limit:
Adaptation_Rules:            # [] — condition → effect
```

**Sealing.** Fields marked `[SEALED]` are encrypted separately and never delivered to Pillar 3
(P2.R6, §2.6). The delivery surface cannot leak what it cannot decrypt.

### 9.1 The same schema across three domains

| Field | Mathematics | History | Programming |
|---|---|---|---|
| `Scenario` | A multi-step optimization problem. | Three primary sources on one event. | A repo with an intermittent failure. |
| `AI_Initial_Response` | A worked solution with a transposed digit at step 3. | An interpretation resting on an unstated source-reliability assumption. | A diagnosis that misidentifies the root cause. |
| `Mutation.class` | `ARITHMETIC` | `ASSUMPTION` | `REASONING` |
| `Expected_Evidence` | Recomputes step 3 independently. | Interrogates the source's provenance. | Reproduces the failure before accepting the diagnosis. |
| `Reasoning_Checkpoints` | "How did you check the arithmetic?" | "What would change your reading?" | "How did you confirm the root cause?" |
| Rubric criterion | Detects and corrects the slip. | Names the unstated assumption. | Identifies the true root cause. |

Same engine. Same contracts. Different specifications.

---

## 10. Conformance

An implementation claims **ANAF v1.0 conformance** at one of three levels.

**Level 1 — Contract conformant.** Implements all six contracts with valid schemas; passes contract
conformance and golden-path fixtures (§7.6). Any pillar may be a stub.

**Level 2 — Pillar conformant.** All Level 1, plus every MUST responsibility in Pillars 1–6
discharged. In particular:
- P1.R3 evidence sufficiency validation
- P2.R3 ground-truth mutation records and the mandatory `NONE` proportion
- P3.R5 tamper-evident stream
- P4.R2 event-cited observations and P4.R4 null evidence
- P5.R3 citation-backed explanations and P5.R4 insufficiency-not-low-score
- P6.R2 per-audience disclosure and P6.R3 uncertainty carried into every report

**Level 3 — Governed.** All Level 2, plus an operating Pillar 7: audit trail, appeals with SLA,
published fairness and calibration monitoring, version-set pinning, and a validity program with
published methodology (§7.5).

**Only Level 3 may be used for high-stakes decisions** — certification, admission, or hiring. Levels
1 and 2 are for formative, practice, and development use.

---

## 11. Open Questions

Carried deliberately. These are unresolved in v1.0 and are the agenda for v1.1.

1. **Construct validity of AI-collaboration competencies.** Is "verification behavior with a
   fallible AI" a stable trait, or is it situational? Until measured, ANAF's central claim is a
   hypothesis. §7.5 exists to test it, and the answer could require restructuring the competency
   registry.
2. **Detection vs. suspicion.** A candidate who challenges everything catches every mutation. The
   `NONE` control proportion (§2.5) is the countermeasure — is 25% sufficient, and how should
   false-positive flagging be weighted against true detection?
3. **AI collaborator fidelity vs. equivalence.** More naturalistic AI means less standardized
   treatment. Where the tradeoff should sit is unresolved, and it may differ by stakes.
4. **Coachability.** Any published framework can be trained against. Which competencies survive
   coaching, and does coaching on verification behavior actually produce verification behavior?
   (If it does, that is a feature, not a leak.)
5. **Timing signals across cultures and disabilities.** Hesitation and latency are informative and
   also confounded. v1.0 caps their strength (§4.4); whether they should be used at all in
   high-stakes contexts is open.
6. **Cost.** Multi-model generation, live AI hosting, and LLM-based extraction are expensive per
   candidate. The economics need modeling before scale claims.
7. **Candidate data rights.** Who owns the evidence graph? v1.0 asserts candidate visibility and
   consent-gated release; portability, deletion versus audit retention, and cross-employer reuse are
   unresolved.
8. **Cross-assessment competency persistence.** Should estimates from separate assessments combine
   into a longitudinal profile? Powerful and hazardous — decay, drift, and staleness all bite.

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
| **Mutation** | A deliberate, characterized, ground-truthed flaw in AI output. |
| **Observation** | A single event-cited evidence item, mapped to a competency. |
| **Strength** | How much an observation says about a competency, if true. |
| **Reliability** | How confident the extractor is that the observation occurred as described. |
| **Descriptor** | An Evidence Blueprint entry defining an observable behavior for a competency. |
| **Sufficiency** | Whether enough evidence exists to make a determination at all. |
| **Version set** | A pinned bundle of every specification, model, and prompt version used. |
| **Domain Pack** | Pluggable domain bundle: taxonomy, notation, difficulty anchors, error types. |

## Appendix B — Document conventions

- Responsibilities are numbered `Pn.Rm`, extension points `Pn.Xm`. These IDs are stable across
  revisions and are the anchors for conformance testing and traceability.
- **MUST / SHOULD / MAY** follow RFC 2119.
- YAML in this document is illustrative. The normative artifacts are the JSON Schema files under
  `schemas/`.
