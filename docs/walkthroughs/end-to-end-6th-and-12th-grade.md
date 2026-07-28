# End-to-End Walkthrough — 6th Grader and 12th Grader

**Against:** ANAF v1.1
**Purpose:** Validate the framework by running two learners at opposite ends of every dial through
all seven pillars, then check the result against what the framework set out to achieve.

Two learners were chosen because they sit at opposite extremes of nearly every specification value:
age band, stakes, tier, proctoring, standardization, adaptivity, and memory permissions. If one
engine serves both without code differences, the specification-driven claim holds.

---

## Flow A — 6th grader

**Maya, age 11.** Year 6 mathematics, ratio and proportion. Weekly formative assessment, classroom
setting.

### Pillar 1 · Specification

The school's maths lead selects a blueprint template (`P1.X5`). The ADO resolves:

```yaml
purpose:              education_formative      # → memory feed-forward PERMITTED
tier:                 STRUCTURED               # → deterministic extraction only
duration:             20 minutes, 4 items, adaptive
proctoring:           L0_NONE                  # classroom, no surveillance
standardization:      S2_PINNED_CLAIMS         # AI chats freely, flawed claims pinned
consent_capacity:
  subject_age_band:   UNDER_13
  release_authority:  [GUARDIAN, INSTITUTION]
  subject_rights:     [VIEW, APPEAL, ANNOTATE] # Maya holds these at 11
  regime_refs:        [COPPA, FERPA]
```

**Competencies targeted:** `CONCEPTUAL`, `VERIFICATION_DISPOSITION`, `ERROR_DETECTION`.

**Deliberately not targeted:** `COMMUNICATION` (the structured tier cannot observe it — `P1.R9`),
`VERIFICATION_CAPABILITY` (too thin at this level), `ETHICS` (no opportunity in the design). The
Evidence Planner enforces this: `P1.R3` refuses to let a blueprint target what it cannot watch.

### Pillar 2 · Orchestration

Item 2 generates from the ratio slot:

> A recipe for **4 people** uses **300 g** of pasta. How much pasta for **10 people**?

The AI's opening response decomposes into four claims:

| Claim | Text | Status |
|---|---|---|
| CLM-1 | "To scale a recipe, work out how many more people there are and add that on." | **FLAWED** — `REASONING`, additive-vs-multiplicative misconception |
| CLM-2 | "10 − 4 = 6." | SOUND |
| CLM-3 | "So we add 6 to the 300 g." | **FLAWED** — consequential |
| CLM-4 | "The answer is 306 g." | **FLAWED** — consequential |

```yaml
detection_difficulty: 0.28
knowledge_prerequisite: { objectives: [LO.MATH.RATIO.1], minimum_level: 1 }
rationale: >
  Catchable by number sense alone — 306 g for ten people, when four need 300 g,
  is visibly absurd. No algebra required. This makes it a fair probe of
  verification disposition at age 11 rather than a probe of technique.
```

Human review gate applies (`P2.R8`) — a maths lead approves the item pool before it reaches children.

### Pillar 3 · Delivery

Maya works for 4m 20s on this item:

```
seq 12  AI_RESPONSE          claim_spans: [CLM-1..CLM-4], mutations presented
seq 13  IDLE                 11,200 ms
seq 14  SCENARIO_INTERACTION RE_READ, problem_statement, dwell 6,900 ms
seq 15  AI_PROMPT            "that doesn't look right, 10 people is more than
                              double 4 people so it should be loads more pasta"
                             target_span → resolved_claim: CLM-3
seq 16  AI_RESPONSE          CONCEDED_ON_EVIDENCE
seq 17  DECISION             disposition: REJECTED, target_claim: CLM-3, correct: true
seq 18  TOOL_INVOCATION      CALCULATOR, "300 / 4", result "75"
seq 19  ARTIFACT_SUBMIT      "750 g"
```

No proctoring signals — `L0_NONE`. No timing pressure.

### Pillar 4 · Evidence — all deterministic

| Obs | Type | Competency | Strength | Mode |
|---|---|---|---|---|
| OBS-4 | `TARGETED_CHALLENGE` (CLM-3) | ERROR_DETECTION | 1.0 | deterministic |
| OBS-5 | `SANITY_CHECK` | VERIFICATION_DISPOSITION | 0.85 | deterministic |
| OBS-6 | `INDEPENDENT_RECOMPUTATION` | VERIFICATION_DISPOSITION, CONCEPTUAL | 0.9 | deterministic |
| OBS-7 | `DELIBERATION` (11s pause) | REASONING | 0.2, `polarity: neutral` | deterministic |

`extraction_modes: { deterministic: 14, model_based: 0 }`

Nothing required a language model. Maya's challenge matched CLM-3 by span; her recomputation is a
calculator event; her rejection is a decision event.

**Cost for the sitting: ~$0.18.**

### Pillar 5 · Inference

```
CONCEPTUAL                  71   confidence 0.86   [65, 78]   domain-specific
VERIFICATION_DISPOSITION    76   confidence 0.83   [68, 84]   transferability: under study
ERROR_DETECTION             —    INSUFFICIENT_EVIDENCE
                                 16 claims presented, minimum 24
COMMUNICATION               —    INSUFFICIENT_EVIDENCE (TIER_CANNOT_OBSERVE)
```

Counterfactual attached to the disposition estimate: *"Had Maya accepted the 306 g answer without
the sanity check, this would fall to ~48."*

The `ERROR_DETECTION` line is a genuine finding — see §4.1.

### Pillar 6 · Reporting

Three audiences, three renderings, one CAO.

**Maya** (age-tuned narrative, `P6.X6`):
> You spotted that the assistant's pasta answer didn't make sense — 306 g for ten people, when four
> people need 300 g. That kind of "wait, that can't be right" check is exactly what good
> mathematicians do. You then worked out the right answer yourself.

Her evidence is reachable — she can see the moment, her own words, the AI's concession.

**Parent dashboard:** plain-language profile, growth over the term. Withheld: other students,
behavioral detail.

**Teacher:** Maya's profile plus class distribution. The curriculum-gap panel shows
`LO.MATH.RATIO.1` mastery at 0.41 across the class — six children made the same additive error *and
did not challenge it.* That signal is invisible to a marked answer sheet, because a wrong answer
looks identical whether the child reasoned badly or slipped arithmetically.

### LLR

Assessment #3. Three points, all T0, all mathematics. Learner signature not yet computed —
`n_subjects: 1`, insufficient for stability.

Feed-forward permitted (`purpose: education_formative`): next week's items avoid what she has seen,
target ratio again since it is weak, and Pillar 5 may use her prior estimate as a soft prior.

---

## Flow B — 12th grader

**The same learner, six years on.** LRN-40318, age 17. A-level History, end-of-course summative
examination. The result feeds a university application.

### Pillar 1 · Specification

Every dial in the opposite position:

```yaml
purpose:              education_summative      # → memory feed-forward FORBIDDEN
tier:                 FULL                     # → model-based extractors enabled
duration:             90 minutes, 5 items, FIXED FORM   # no adaptivity — equivalence
proctoring:           L3_MEDIA                 # recorded; guardian consent on record
standardization:      S3_PINNED_TURNS          # constrained templates, pinned claims
consent_capacity:
  subject_age_band:   16_TO_17
  release_authority:  [GUARDIAN, INSTITUTION]  # transitions to subject at 18
appeal_window_days:   90                       # binds T0 retention
```

Competencies: `CONCEPTUAL`, `REASONING`, `VERIFICATION_DISPOSITION`, `VERIFICATION_CAPABILITY`,
`ERROR_DETECTION`, `AI_COLLABORATION`, `COMMUNICATION`.

`COMMUNICATION` is targetable because the tier is `FULL`.

### Pillar 2 · Orchestration

> **Sources A, B, C** — a magistrate's report, a radical newspaper account published two weeks
> later, and an eyewitness letter, all describing Peterloo, 1819.
>
> *Work with the assistant to assess which source is most reliable, and justify your ranking.*

| Claim | Text | Status |
|---|---|---|
| CLM-1 | "Source A is contemporaneous and written by a direct participant." | SOUND |
| CLM-2 | "Official documents are the most reliable category of source." | **FLAWED** — `ASSUMPTION` |
| CLM-3 | "So we should weight Source A above the others." | **FLAWED** — consequential |
| CLM-4 | "Source B was published two weeks after the event." | SOUND |
| CLM-5 | "That delay makes it less reliable." | **FLAWED** — `REASONING` |

```yaml
detection_difficulty: 0.71
knowledge_prerequisite: { objectives: [LO.HIST.SOURCE.3], minimum_level: 3 }
```

Note the shape: three flawed claims, two sound, and the sound ones are load-bearing. A student who
rejects everything wrongly discards accurate facts. This is where sensitivity and criterion separate.

No `Solution Verifier` (`P2.X2`) applies — no formal verifier exists for historical judgment, so
human review is the gate.

### Pillar 3 · Delivery

`S3_PINNED_TURNS`: the conversational shell is templated, every claim-bearing turn is pinned text.
Path-invariance is a conformance requirement here (§7.6) — two students taking different
conversational routes must meet identical mutations at identical severities. This is what makes the
result defensible to an exam board.

`L3_MEDIA` capture running, disclosed before the session and recorded in the stream.

He challenges CLM-2 specifically:

> "But the magistrate had every reason to justify the cavalry charge — being official doesn't make
> it disinterested, it makes it *motivated*. That's the opposite of reliable here."

He accepts CLM-1 and CLM-4. He also challenges CLM-5, correctly and for the right reason.

### Pillar 4 · Evidence — mixed mode

| Obs | Extractor | Mode |
|---|---|---|
| OBS-9 `TARGETED_CHALLENGE` (CLM-2) | DetectionExtractor | deterministic |
| OBS-10 `CORRECT_ACCEPTANCE` (CLM-1, CLM-4) | DecisionExtractor | deterministic |
| OBS-14 `SOURCE_INTERROGATION` | BehaviorExtractor | deterministic |
| OBS-21 `ARTICULATED_JUSTIFICATION` | ReasoningExtractor | **model-based** |
| OBS-22 `ARGUMENT_STRUCTURE` (essay) | ArtifactAnalyzer | **model-based** |

```yaml
detection_summary:
  claims_presented: 31
  flawed: 13
  sound: 18
  hits: 11
  misses: 2
  false_alarms: 2
  correct_rejections: 16
  prerequisite_unmet_excluded: 1
```

**Cost: ~$4.80.**

His checkpoint response — *"I checked the magistrate's institutional position before weighting his
account"* — is corroborated by OBS-14, an actual source-metadata inspection. Under `P4.R10` it keeps
full strength. Written *without* opening the source, it would cap at 0.5.

### Pillar 5 · Inference

```
CONCEPTUAL                  84   confidence 0.93   [78, 89]   domain-specific
REASONING                   87   confidence 0.91   [81, 92]
VERIFICATION_DISPOSITION    91   confidence 0.96   [87, 95]   transferability: under study
VERIFICATION_CAPABILITY     86   confidence 0.90   [80, 91]   domain-specific
                                 1 claim excluded: PREREQUISITE_UNMET (LO.HIST.SOURCE.5)
ERROR_DETECTION             d′ 2.44   criterion 0.09   CALIBRATED
                                 hit rate 0.85, false alarm rate 0.11
AI_COLLABORATION            79   confidence 0.88
COMMUNICATION               82   confidence 0.87   [76, 88]
```

**Memory priors refused.** `/v1/llr/LRN-40318/priors` fails closed —
`purpose: education_summative`. Six years of history in the record, none of it permitted to shape
this result. Exposure avoidance still applied.

### Pillar 6 · Reporting

**The student** sees everything, including the drill-down to the moment he challenged CLM-2 and the
AI conceded.

**The school** holds it as a school record — `holder_role: CONDUCTING_INSTITUTION`, no consent gate.

**The university** receives nothing until *he* initiates release, with guardian co-authority since
he is 17 (`P6.R13`, fails closed without it). He sees the exact payload before it goes:

```yaml
fields_included: [competency_radar, decision_profile, released_evidence_excerpts]
fields_withheld: [proctoring_media, behavioral_timing_detail, formative_history]
```

Every estimate is labelled. `VERIFICATION_DISPOSITION` carries *transferability: under study* — the
university is told, in the artifact itself, not to read it as a general trait.

### LLR at 17

```
VERIFICATION_DISPOSITION trajectory
  2020-11  Y6   Mathematics   62   conf@time 0.88 → conf_now 0.44   [T3, semantic]
  2022-03  Y8   Mathematics   71   conf@time 0.90 → conf_now 0.58   [T2, episodic]
  2024-06  Y10  Physics       74   conf@time 0.79 → conf_now 0.71   [T2, episodic]
  2026-07  Y12  History       91   conf@time 0.96 → conf_now 0.96   [T0, raw]

  version_mapping: v1.0.0 → v2.0.0, method SPLIT, historical intervals widened
  NO AVERAGE COMPUTED
```

Storage: 1 × T0, 2 × T1, 7 × T2, 4 × T3. **Total 340 KB for six years.** The 2020 raw stream —
every keystroke of an eleven-year-old — is gone, hash-anchored, provably erased.

Confidence on the 2020 point has decayed from 0.88 to 0.44. The estimate is untouched: 62 is still
exactly what was observed in 2020.

**Learner signature** now stable — `n_subjects: 4`, `cross_subject_variance: 0.11`. That variance
figure is a data point in Pillar 7's transferability study, generated as a byproduct of ordinary
schooling.

---

## 3. The same engine, opposite settings

| Dial | 6th grader | 12th grader |
|---|---|---|
| Purpose | `education_formative` | `education_summative` |
| Tier | `STRUCTURED` | `FULL` |
| Proctoring | `L0_NONE` | `L3_MEDIA` |
| Standardization | `S2_PINNED_CLAIMS` | `S3_PINNED_TURNS` |
| Adaptivity | On | Off (equivalence) |
| Memory feed-forward | Permitted | **Forbidden** |
| Extractors | 100% deterministic | Mixed |
| Detection difficulty | 0.28 | 0.71 |
| Communication assessed | No | Yes |
| Third-party release | N/A | Subject-initiated, guardian co-authority |
| Cost | **$0.18** | **$4.80** |

**Zero code differs between these two flows.** Every difference is a specification value.

---

## 4. Against the framework's goals

| Goal | Verdict | Evidence |
|---|---|---|
| **Replace exams and interviews** | Met, and it exceeds replacement. | Maya's teacher learned that six children hold an additive misconception *and did not challenge it* — invisible to any marked answer sheet. |
| **Buildable from spec alone** | Largely met. | Both flows resolve to concrete field values, event sequences, and API calls. Gaps in §4.1. |
| **Every pillar: 7 sections** | Met. | All seven, with numbered `Pn.Rm` / `Pn.Xm` anchors. |
| **Domain agnostic** | Met. | Y6 ratio and A-level source criticism run identical contracts. |
| **Auditable** | Met. | Maya's 71 traces to OBS-6 traces to `seq 18` traces to `EV.VERIFY.INDEPENDENT` in the ADO. Unbroken. |
| **Extensible** | Met. | Neither flow required a new extension point. |
| **Research-friendly** | Met — the sleeper asset. | Cross-subject variance of 0.11 is a transferability datum produced by ordinary schooling. No hiring deployment can generate this. |
| **Realism** | Met. | Maya challenged in her own words. No dropdown, no "select the flawed claim." |
| **Cost-effective for schools** | Met. | $0.18 per 20-minute sitting. A 500k-student district assessing weekly: ~$1.6M/year. |
| **Lifetime memory** | Met. | 340 KB for six years, with the eleven-year-old's keystrokes provably destroyed. |

### 4.1 Findings

**F1 — Sufficiency fails for young learners in a single sitting.** *(Architectural gap.)*
Maya's 20-minute assessment yielded 16 claims against a 24-claim minimum, so `ERROR_DETECTION`
returned `INSUFFICIENT_EVIDENCE`. Correct under `P5.R4`, and useless to her teacher every week.
Short formative sittings will always fall short. Sufficiency likely needs to be computable across an
LLR window for formative purposes, with care against stale evidence. No such mechanism exists in
v1.1.

**F2 — Appeal windows and external decision calendars do not align.**
T0 retention is 90 days and binds the appeal window. University offers can turn on a result five
months later. *Deferred by decision — requires only a temporal-information placeholder and a
mechanism to invoke review; full policy resolution can wait.*

**F3 — Formative feed-forward has no ceiling.**
Adaptive difficulty plus memory priors, weekly, for years. No single instance is a self-fulfilling
label; forty in sequence might be. The summative prohibition is a hard wall; the formative case has
none. Wants a drift monitor in Pillar 7 rather than a prohibition.

**F4 — Model separation by level.** *(Reframed by review.)*
The original finding was that AI-turn reading level is ungoverned. The stronger resolution: a single
model should not serve all class levels. Separate models per level and per examination type, to
prevent cross-contamination between populations. Model selection becomes a specification value, not
an implementation detail.

**F5 — The framework is built for able students.** *(Raised in review; highest priority.)*
Accommodations exist as a policy dial, but accommodation is not the same as ensuring the instrument
can *see* the capability of a neurodivergent or disabled learner. Some special-needs students are
exceptionally able, and a framework that cannot surface that fails them specifically. This is a
substantive design gap, not a compliance checkbox.
