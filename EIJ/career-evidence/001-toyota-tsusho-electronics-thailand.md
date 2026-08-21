---
id: career-001
title: Toyota Tsusho Electronics Thailand — Embedded Software Engineering & QA
type: career-evidence
status: canonical-draft
visibility: public-safe
company: Toyota Tsusho Electronics (Thailand) Co., Ltd. 
company_current_name: Toyota Tsusho NEXTY Electronics (Thailand) Co., Ltd.
employment_start: 2007-04
employment_end: 2009-08
initial_title: Software Development Engineer
later_role: Quality Assurance / Engineering Verification
later_official_title: TODO
domain:
  - automotive
  - embedded-software
  - software-verification
technologies:
  - Embedded C
  - Excel
  - VBA
  - Source Insight
  - HIL
engineering_concepts:
  - finite-state-machine
  - state-transition-testing
  - decision-table
  - branch-coverage
  - condition-coverage
  - MC/DC
  - HIL-testing
  - MISRA-C
  - specification-recovery
  - engineering-automation
  - independent-verification
source_confidence: mixed 
last_reviewed: 2026-08-18
tags:
  - career-evidence
  - embedded
  - automotive
  - testing
  - quality-assurance
---

# Toyota Tsusho Electronics Thailand — Embedded Software Engineering & QA

## 1. Career Context

I joined **Toyota Tsusho Electronics (Thailand) Co., Ltd.** as a new-graduate engineer.

Public career information currently indicates:

- Employment period: **April 2007 – August 2009**
- Initial title: **Software Development Engineer**

These dates and the exact official title should be verified against my original LinkedIn profile or resume.

The company was established in Thailand as an offshore development location for in-vehicle embedded software.

At the time of my employment, the company was still named:

**Toyota Tsusho Electronics (Thailand) Co., Ltd.**

It was renamed Toyota Tsusho NEXTY Electronics (Thailand) Co., Ltd. in 2017.

---

# 2. Engineering Context

Japanese engineers developed embedded C software for automotive systems.

In some cases, implementation progressed ahead of the detailed specification.

The Thailand engineering team therefore needed to inspect the original source code, understand its actual behavior, reconstruct detailed design/specification artifacts, design test cases, and verify the behavior.

The approximate engineering flow was:

```text
Embedded C Source Code
        ↓
Program / Logic Analysis
        ↓
Recovered Detailed Specification
        ↓
State / Decision Models
        ↓
Test Cases
        ↓
HIL Verification
```

Because this was automotive embedded software, correctness and completeness of control logic were important.

---

# 3. Source-Code Analysis

The original embedded C source code from the Japanese engineering team was inspected using **Source Insight**.

The implementation contained detailed conditional logic such as:

- nested `if / else-if / else`;
- Boolean flags;
- state-dependent conditions;
- actions associated with particular conditions;
- state transitions.

Understanding an individual `if` statement was not enough.

The engineer needed to understand how current state, input conditions, and flags interacted to determine system behavior.

Conceptually:

```text
Current State
    +
Input / Event
    +
Condition / Flag A
    +
Condition / Flag B
    +
...
    ↓
Decision
    ↓
Action
    ↓
Next State
```

---

# 4. Finite State Machine Analysis

A major part of the specification work involved understanding the software as a **Finite State Machine (FSM)**.

Engineering documentation included:

- State Diagrams
- State Transition Tables
- transition conditions
- actions associated with transitions
- resulting states

The State Diagram provided a visual representation of system behavior.

The State Transition Table provided the detailed conditions governing each transition.

A transition therefore was not simply:

```text
STATE_A → STATE_B
```

but more accurately:

```text
Current State
+ Event
+ Flag / Condition Set
→ Action
→ Next State
```

Different transitions could require different combinations of flags and conditions.

The current state itself was therefore part of the decision context.

---

# 5. Decision and Condition Analysis

The source code could contain multiple nested conditions.

For example:

```c
if (state == STATE_A) {
    if (flag_1) {
        if (flag_2 || flag_3) {
            ...
        }
    }
}
```

Manually reading the code and translating every possible case into specification and tests created a risk of human omission.

Potential omissions included:

- missing a branch;
- missing a Boolean condition;
- missing a combination of flags;
- missing state-specific behavior;
- missing a state transition;
- missing a test case corresponding to a logic path.

To make the decision space explicit, I started representing the logic in **Excel-based truth / decision tables**.

A simplified representation was:

|Current State|Flag A|Flag B|Expected Action|Next State|
|---|--:|--:|---|---|
|STATE_A|1|0|Action X|STATE_B|
|STATE_A|0|0|No Action|STATE_A|
|STATE_A|1|1|Action Y|STATE_C|

This made it possible to visually inspect how changing states or individual flags affected the resulting decision.

---

# 6. Structural Coverage

Test analysis included checking whether relevant source-code logic had been exercised.

The work involved concepts corresponding to:

### Branch / Decision Coverage

Verify that relevant decision outcomes and branches were exercised.

### Condition Coverage

Verify the behavior of individual Boolean conditions rather than checking only the final branch result.

### State-Dependent Condition Coverage

The current state was considered together with flags and other conditions.

For example:

```text
STATE_A + A=True  + B=False → Decision X
STATE_A + A=False + B=False → Decision Y
STATE_A + A=True  + B=True  → Decision Z
```

This allowed changes in individual conditions to be compared against changes in resulting behavior.

### MC/DC Relationship

The analysis strongly corresponds to **Modified Condition / Decision Coverage (MC/DC)** reasoning because individual conditions could be varied while observing whether they independently changed the resulting decision.

However, I currently do not have evidence confirming whether the project formally measured or reported MC/DC according to a specific standard.

Therefore:

**Safe current claim**

> Performed MC/DC-oriented condition and decision analysis.

**Do not yet claim**

> Achieved formal MC/DC compliance.

TODO: Verify whether MC/DC was explicitly required by the project's test standard.

---

# 7. Excel / VBA Engineering Tooling

Initially the state, condition, and decision analysis was performed using structured Excel tables.

As the logic became increasingly complicated, manually creating and maintaining these tables also became repetitive and vulnerable to human error.

I initiated and developed **VBA automation inside Excel** to generate structured engineering artifacts.

The tooling helped generate and format:

- truth / decision tables;
- state-transition tables;
- combinations of states and conditions;
- visual representations using formatting and color.

The intention was not simply to automate documentation.

The important engineering purpose was to transform logic hidden inside nested source code into a representation that could be inspected systematically.

The workflow evolved from:

```text
Read Source
→ Manually Understand Logic
→ Manually Build Tables
→ Build Specification
```

toward:

```text
Read Source
→ Model State + Conditions
→ Generate Structured Tables
→ Inspect Completeness
→ Build / Verify Specification and Tests
```

The approach/tool began to be used more widely by other engineers in the team.

Exact adoption scale:

**TODO**

---

# 8. Specification Recovery

Because detailed specifications could follow the implementation, part of the engineering work can now be described as:

**Specification Recovery / Program Comprehension / Reverse Engineering**

The task was to derive documented system behavior from the actual embedded C implementation.

Source-code captures from Source Insight were used together with generated tables and explanations in detailed engineering documents.

Exact document/template names:

**TODO**

---

# 9. HIL Verification

The automotive embedded software was verified using a **Hardware-in-the-Loop (HIL)** environment.

The HIL environment simulated relevant vehicle/system signals and conditions so that software behavior could be exercised under controlled scenarios.

The overall verification chain was approximately:

```text
Source Code
    ↓
Recovered Behavior
    ↓
State / Decision Model
    ↓
Test Case
    ↓
HIL Execution
    ↓
Observed Result
```

Exact HIL platform/tool:

**TODO**

---

# 10. MISRA C and Defensive Programming

The source code followed strict embedded-C coding rules.

One remembered rule required an `if / else-if` chain to explicitly contain a final `else`, including situations where no operation was required.

For example:

```c
if (condition_a) {
    ...
}
else if (condition_b) {
    ...
}
else {
    /* Nothing to do */
}
```

This made it explicit that the remaining condition had been considered intentionally rather than accidentally omitted.

This corresponds to **MISRA C / defensive-programming practice**.

Exact MISRA version and exact internal Toyota coding rule:

**TODO**

Important terminology:

```text
MISRA C
→ source-code / defensive coding standard

CMMI
→ software-development / organizational process maturity
```

These are separate concepts.

CMMI maturity level of the organization/project:

**TODO — do not claim until verified**

---

# 11. Progression Into Quality Assurance

Following strong engineering performance, I was selected to move into a **Quality Assurance / engineering verification role** reporting to a Japanese leader.

The exact official QA title is currently:

**TODO**

This responsibility was not limited to formatting or document inspection.

To verify another engineer's work, I needed to independently understand the source implementation.

The QA process involved:

```text
Original Embedded C
        ↓
Independent Source Analysis
        ↓
Understand State Machine
        ↓
Understand Conditions / Branches
        ↓
Understand Expected Transitions
        ↓
Compare Against Engineer's Specification
        ↓
Identify Missing / Incorrect Cases
```

This meant effectively performing a second engineering interpretation of the implementation.

Review areas included:

- source-derived behavior;
- state-machine correctness;
- transition completeness;
- conditions and branches;
- specification completeness;
- test coverage;
- required annotations / documentation conventions;
- coding-standard-related checks.

---

# 12. Outcome / Influence / Trust

The strongest evidence from this period is not simply that I learned embedded C or VBA.

The progression was:

```text
Perform assigned engineering work
        ↓
Identify a recurring quality / complexity problem
        ↓
Introduce structured decision/state analysis
        ↓
Automate repetitive analysis with VBA
        ↓
Approach adopted by other engineers
        ↓
Selected to verify other engineers' work
```

This provides early evidence of:

- engineering initiative;
- abstraction;
- quality mindset;
- automation;
- reusable tooling;
- influence beyond my own assigned work;
- organizational trust.

I also received strong performance recognition and progressed into the QA responsibility.

Exact performance-ranking terminology:

**TODO**

Personal compensation details are intentionally excluded from this public EKMP record.

---

# 13. Engineering Pattern Mapping

|Engineering Situation|Pattern / Concept|
|---|---|
|Behavior depends on current state|Finite State Machine|
|Visualize states and transitions|State Diagram|
|Define transition conditions|State Transition Table|
|Verify transitions|State Transition Testing|
|Multiple Boolean combinations|Truth Table / Decision Table|
|Verify decision paths|Branch / Decision Coverage|
|Verify individual Boolean conditions|Condition Coverage|
|Verify independent condition influence|MC/DC|
|Implementation precedes detailed specification|Specification Recovery / Program Comprehension|
|Explicit default/no-action handling|Defensive Programming / MISRA C|
|Verify embedded behavior with simulated signals|HIL Testing|
|Repetitive manual engineering analysis|Engineering Tooling / Test Automation|
|Verify another engineer's interpretation|Independent Verification / Engineering QA|
|Link implementation to specification and tests|Traceability|

---

# 14. Interview Evidence

## Technical Depth

Embedded C, source-code analysis, finite-state-machine reasoning, decision/condition analysis, structural test coverage, HIL verification, MISRA C-style defensive programming.

## Engineering Judgment

Recognized that complex state/condition logic was difficult and error-prone to analyze purely manually.

Converted implicit source behavior into explicit decision/state models.

## Automation

When manual structured analysis itself became increasingly complex, created VBA tooling to automate generation and visualization.

## Influence

The approach expanded beyond personal use and was adopted by other engineers.

## Quality Ownership

Progressed from producing my own engineering artifacts to independently verifying artifacts produced by peers.

---

# 15. Staff / Principal Signal

This was an early-career role and should not be presented as Staff-level scope.

However, it demonstrates a recurring engineering behavior worth tracking across later career stories:

```text
Observe recurring problem
→ Model / abstract the problem
→ Create reusable mechanism
→ Improve consistency / quality
→ Other engineers adopt it
```

Later career stories should be examined to determine whether this behavior expanded from individual/team-level influence into system-level, multi-team, or platform-level influence.

---

# 16. Facts to Verify

Before treating this record as fully canonical, verify:

- exact employment start month;
- exact employment end month;
- exact initial official job title;
- exact QA official title;
- exact team/department names;
- exact HIL platform;
- exact MISRA C version/internal standard;
- whether MC/DC was a formally measured requirement;
- CMMI maturity level, if relevant;
- exact performance-ranking terminology;
- scale of VBA-tool adoption.

Until verified, these facts must not be upgraded into stronger external claims.

---

# 17. Source Notes

### Primary

Personal recollection reconstructed in August 2026.

### External cross-check

Public career profile indicates:

- Software Development Engineer
- April 2007 – August 2009

### Company-history cross-check

Toyota Tsusho Electronics (Thailand) Co., Ltd. was established in 2005.

The company changed its name to Toyota Tsusho NEXTY Electronics (Thailand) Co., Ltd. in 2017.

---

# 18. Related Knowledge

Pattern extraction:

`Concepts/pattern-index.md`

Future related career evidence:

`EIJ/career-evidence/002-...`