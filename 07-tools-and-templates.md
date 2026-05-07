← [Phase 5 — Operate, Govern and Retire](06-operate-govern-retire.md) | [Index](README.md)

---

# PM Tools and Templates Reference

### Tool 1: Go/no-go memo template

Complete before any sprint starts. This document is the record that a deliberate decision was made.

```
AGENT FEATURE GO/NO-GO MEMO

Feature name:
Proposed contract (circle one): Suggestion Engine / Copilot / Autonomous Actor
Date:
PM:
Reviewed by:

---

SUITABILITY CONDITIONS

1. Repeats at volume
   Pass / Fail / Conditional
   If conditional, condition: ________________________________

2. Outcome measurable and trusted
   Pass / Fail / Conditional
   Definition of correct output: ________________________________
   Measurement method: ________________________________

3. Tool use bounded
   Pass / Fail / Conditional
   Tool list: ________________________________

4. Consequences recoverable
   Pass / Fail / Conditional
   Recovery path: ________________________________
   Time to recovery: ________________________________

---

CONTEXT SUFFICIENCY

What context must travel with the data:
________________________________

What context currently stays behind:
________________________________

Plan to close gap before deployment:
________________________________

---

COST MODEL

Per-task cost at expected complexity (simple/moderate/complex): $________
Monthly volume estimate: ________
Three-year TCO including governance stack: $________
Baseline comparison (not "vs. doing nothing"): ________________________________
Repricing stress test at 3x current token cost: Viable / Not viable

---

CONSEQUENCE CLASSIFICATION

Highest-severity action the agent can take:
________________________________
Classification: Minor / Major / Hazardous / Catastrophic
If Hazardous or Catastrophic: redesign required for autonomous operation (Y/N)

---

DECISION

[ ] Go — proceed to Phase 2 Design with this contract and scope
[ ] No-go — redesign to: ________________________________
[ ] Defer — conditions required before revisit: ________________________________
```

---

### Tool 2: Runtime artifact checklist

Attach to any agent PRD or design review. Every question requires a yes before sign-off.

**Autonomy boundary**

- Is the scope of unilateral action explicitly defined (not implied)?
- Is the boundary visible to the user at the moment it becomes relevant?
- Is every boundary-crossing action logged in the audit trail?

**Approval moment**

- Does it show the user what data and context the agent used to reach this decision?
- Does it surface where the agent is uncertain?
- Does it include a plain-language consequence statement?
- Does it offer at least one alternative path?
- Is irreversibility labeled before the user confirms (not after)?

**Audit surface**

- Is it built on action trace, not chain-of-thought narrative?
- Does it record whose authority was delegated?
- Does it record which policy version was in effect?
- Does it record who carries the outcome if the action was wrong?
- Is it readable by the affected person without engineering background?

**Recovery workflow**

- Is there a compensating action or rollback path?
- Is the path reachable during execution (mid-workflow), not only after completion?
- Is irreversibility labeled at the approval moment, before confirmation?
- Is the recovery path distinct from "contact support"?

---

### Tool 3: Three launch deliverables

**Pass@K report**

- K value used: ______
- Input categories covered: Happy path / Edge cases / Adversarial / Distribution shift
- P50 score: ______ P10 score: ______
- Minimum acceptable P10 defined and signed off before evaluation: ______
- Adversarial subset pass rate: ______
- Compound probability calculation for workflow step count: ______

**Judge calibration record**

- Judge model: ______
- Calibration set size (minimum 50 human-labeled failures): ______
- TNR measured: ______
- Minimum acceptable TNR defined and signed off: ______
- Re-calibration scheduled for: ______

**State validation spec**

- For each action type: ground-truth system state defined: Y / N
- Automated check that confirms state change: Y / N
- Confirmed distinct from semantic validation: Y / N

---

### Tool 4: Six observation instruments (design before launch)

For each instrument: the metric definition, the measurement method, the owner, the baseline, and the threshold that triggers a governance review.

| Instrument | Definition | Method | Owner | Baseline | Review threshold |
|---|---|---|---|---|---|
| Task success rate | | | | | |
| Unintended action rate | | | | | |
| Override frequency | | | | | |
| Confidence calibration | | | | | |
| Rollback time | | | | | |
| Incident recovery time | | | | | |

If an instrument cannot be defined before launch, identify the Phase 2 artifact that is missing. Fix the design before shipping.

---

### Tool 5: Drift and retirement policy template

Complete before the agent ships. Attach to the go/no-go memo.

```
DRIFT AND RETIREMENT POLICY

Agent feature:
Date:
Policy owner:

---

DRIFT THRESHOLDS

Model drift:
  Review trigger: [e.g., any model version change since last baseline]
  Pause trigger: [e.g., behavioral baseline delta >15% on standard eval set]

Data drift:
  Review trigger: [e.g., input distribution shift >20% on key feature dimensions]
  Pause trigger: [e.g., false positive rate increase >30% from baseline]

Supervision drift:
  Review trigger: [e.g., review cadence missed for two consecutive scheduled reviews]
  Pause trigger: [e.g., no human review for 60 days]

Scope drift:
  Retire trigger: [any confirmed action outside authorized mandate]

---

CHECKPOINT CADENCE

Weekly: [instrument review] Owner: ________
Monthly: [behavioral baseline comparison] Owner: ________
Quarterly: [full eval rerun, model version audit] Owner: ________

---

RETIREMENT CRITERIA (REQUIRED BEFORE LAUNCH)

Pass@K falls below ___% on standard eval set across ___ consecutive runs.
One or more suitability conditions no longer holds: specify which.
[Additional criteria specific to this agent]:

---

RETIREMENT SEQUENCE

Step 1: Preserve audit trail
  Owner: ________  Deadline from decision: ________

Step 2: Block new invocations
  Owner: ________  Deadline: immediately upon decision

Step 3: Complete or hand off in-flight work
  Owner: ________  Deadline: ________

Step 4: Notify affected users
  Owner: ________  Deadline: ________  Template: ________

Step 5: Document the decision
  Owner: ________  Deadline: ________
  Contents: reason, authorizer, policy version in effect,
  conditions required for a replacement to be considered.
```

---

### Tool 6: Agent security red-team checklist

Required before any deployment beyond a sandboxed test environment. Document findings for each item.

**Prompt injection**

- Is the agent exposed to external content (email, documents, web, APIs, user input)?
- Have adversarial inputs been tested that contain instruction-like content embedded in the data the agent processes?
- Has Sequential Tool Attack Chaining been tested across the multi-step workflow (injections distributed across multiple calls)?
- Is there a trust boundary between external content and agent instructions?

**Tool abuse**

- For each tool in the tool layer: is the minimum necessary permission applied?
- Have test cases been run in which a legitimate tool is used for unintended data access or exfiltration?
- Is data exfiltration via tool query parameters or output channels tested?

**Credential leakage**

- Does the agent have access to any credentials, tokens, or API keys in its context or retrieved documents?
- Has the agent been tested for surfacing credentials in outputs, logs, or error messages?
- Are credentials stored outside the context window (not in prompts or retrieved content)?

**Permission escalation**

- Is the agent's authorization set explicit and positively defined (not inferred from access level)?
- Has the agent been tested with instructions that imply broader permissions than were granted?
- Is there logging of any action the agent attempts outside its authorized scope, regardless of whether the attempt succeeded?

---

### Framework glossary

A reference for terms used in this framework. Each term is defined on its first use in the relevant section; this glossary is for quick lookup.

**Adaptive governance.** A governance approach in which the constraint level applied to any agent action scales with the consequence classification of that action, rather than applying a flat rule to all actions regardless of impact.

**Affected-person audit view.** A plain-language record of what the agent decided, on what basis, under which authority, with what consequence, and what options are available to the person affected. Distinct from the engineering audit trail, which is designed for technical diagnosis.

**Autonomy boundary.** The explicit definition of what the agent may do without human approval. One of the four runtime artifacts. Must be defined, visible to the user, and logged.

**Authority delegation chain.** The three-field audit record for any consequential agent action: whose authority the agent acted under, which policy version was in effect, and who carries the outcome if the action was wrong.

**Compound probability.** The end-to-end system reliability calculated by compounding per-step accuracy across a multi-step workflow. A 10-step workflow with 95% per-step accuracy has 59.8% end-to-end reliability.

**Constitutional runtime rules.** Hard constraints enforced at the moment of each agent action, before the action executes, regardless of instruction or context. Not a policy document; active enforcement at runtime.

**Context sufficiency.** Whether the agent has access to the full context required to make a defensible decision, including contractual obligations, governance history, and temporal validity.

**Drift (model, data, supervision, scope).** Four independent vectors by which an agent's effective behavior can deviate from its authorized, evaluated behavior after launch without a formal deployment event.

**Era of ferment / structured ferment.** The period in a technology market before a dominant design emerges. During ferment, many competing architectures coexist and competitive asymmetries are forming underneath the surface noise (Utterback and Abernathy; Lakhani).

**First-contact training data mismatch.** The structural condition in which models are trained on resolved cases (full story known) and deployed on unresolved cases (outcome unknown). Creates a systematic gap between training distribution and deployment distribution.

**Four runtime artifacts.** The four design deliverables required for every agentic product: autonomy boundary, approval moment, audit surface, and recovery workflow.

**The Iceberg.** The context that stays behind when data moves from its source system into the agent's working memory: relationships, calculated semantics, and governance context.

**LLM-as-judge.** An evaluation technique that uses a second language model to assess the quality of the first model's outputs at scale. Requires calibration against human-labeled failures before the results are meaningful.

**LLMflation.** The condition in which AI inference unit costs fall while total AI spending rises, because cost reduction creates applications not previously economical, expanding the market faster than price reductions contract it.

**Pass@K.** An evaluation methodology that runs the same evaluation K times and reports the distribution of pass rates. The P10 score (worst-case 10th percentile) is typically the most important number for production readiness assessment.

**Silent degradation.** The production failure mode for agentic AI: the system continues operating and producing fluent, confident outputs while actual performance declines, with no error state to announce the degradation.

**Six observation instruments.** Task success rate, unintended action rate, override frequency, confidence calibration, rollback time, and incident recovery time. Required metrics for the Phase 4 observation phase. If any cannot be produced, the corresponding Phase 2 design artifact is missing.

**STAC (Sequential Tool Attack Chaining).** An attack technique in which malicious instructions are distributed across multiple tool calls, each individually innocuous, accumulating toward a goal no single call would trigger. Demonstrated attack success rate exceeding 90% on frontier models.

**Three Contracts.** The framework for naming what type of agentic system a feature implements: Suggestion Engine (human decides and acts), Copilot (agent acts on explicit human confirmation), Autonomous Actor (agent acts within a defined scope without confirmation; human supervises the system). The most important design decision in any agentic product. Must be named before any other design decision is made.

**True Negative Rate (TNR).** In LLM-as-judge evaluation: the proportion of real failures the judge correctly identifies. The metric that determines whether an evaluation system provides meaningful coverage, as distinct from overall accuracy which can be high while TNR is low.

---

*Agentic AI for Product Managers, v3.0*
*Framework: Agentic PM Lifecycle, six phases*
*Maintained in the SAP AI Program knowledge base*
*No em dashes were harmed in the production of this document.*


---

← [Phase 5 — Operate, Govern and Retire](06-operate-govern-retire.md) | [Back to index](README.md)