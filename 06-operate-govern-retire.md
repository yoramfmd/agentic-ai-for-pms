← [Phase 4 — Observe](05-observe.md) | [Index](README.md) | Next: [PM Tools and Templates](07-tools-and-templates.md) →

---

# Phase 5 — Operate, Govern, and Retire

### The transition from watching to governing

Phase 4 gives you instruments. Phase 5 defines what you do with the readings.

Observation without governance is a dashboard nobody acts on. The instruments from Phase 4 produce signals. This phase defines the thresholds at which those signals require a response, what the response is, and who is responsible for executing it.

Most organizations that reach this phase have a gap: the signals exist but the governance structure to act on them does not. There is no defined threshold at which a degrading pass rate requires the team to pause the agent. There is no defined process for reviewing a model update before it affects production traffic. There is no retirement criteria that would tell anyone when the agent should be decommissioned.

This is the most organizational-maturity-intensive phase of the framework. It requires the PM to build structures that involve engineering, legal, compliance, and operations, structures that need to be agreed upon before the first incident, not designed reactively in response to one.

### The four drift vectors

Drift is not one thing. An agent can drift in four independent directions simultaneously, and the combination can be more dangerous than any single vector in isolation.

> [!NOTE]
> **Check all four at every scheduled post-release review.**

**Model drift.** Has the foundation model version changed since the last behavioral baseline was established? The provider does not reliably notify the product team when this happens. The only reliable way to detect model drift is to run a behavioral comparison against the baseline after each scheduled review period.

**Data drift.** Has the input distribution shifted significantly from the distribution the agent was evaluated on? Schema migrations, new customer segments, seasonal patterns, and upstream system changes can move the actual inputs the agent receives away from the inputs it was tested on. A model that performed well on last year's customer service vocabulary may perform less well on this year's vocabulary if product terminology has changed.

**Supervision drift.** Has the frequency or quality of human review changed since launch? The monthly review meeting that was quietly canceled because "things have been running smoothly" is supervision drift. It does not announce itself. It is discovered when an incident requires a review that has not happened in four months.

**Scope drift.** Is the agent taking actions outside its originally authorized mandate? This is often invisible until someone asks why the agent is doing something it was never designed to do. It can be introduced by a prompt change that removed an exclusion list, a tool configuration change that added new permissions, or an orchestration change that expanded the agent's decision-making authority.

### Tiered checkpoint protocol

Define the governance response thresholds before the first incident. Three tiers with specific triggers, written into the go/no-go memo before launch.

**Review.** One drift vector above its defined threshold. No operational pause required. Scheduled review within the defined window. Document findings. Update the behavioral baseline if the model has changed. Confirm that the governance posture remains appropriate for the current operating conditions.

**Pause.** Two or more drift vectors above threshold simultaneously. Or: supervision drift (the review cadence has lapsed) with no replacement oversight defined. Pause new invocations. Review before resuming. When resuming, do not restore the agent to its prior autonomy level without a documented basis for that specific decision.

**Retire.** Scope drift outside the authorized mandate has been confirmed. Incident recovery time is not improving across three consecutive incidents. One or more of the four suitability conditions from Phase 1 no longer holds. Retirement is a product decision made through a defined process, not an emergency shutdown in response to a crisis.

> [!IMPORTANT]
> **The agent that has no defined retirement criteria is an agent nobody authorized to run indefinitely.**
>
> This is not a hypothetical risk. It is the default outcome of every agentic deployment that treats Phase 5 as out of scope for the initial launch. The agent runs. Conditions change. Nobody has a defined trigger that says "this agent should stop." It keeps running.

### Constitutional runtime rules

A governance policy document does not constrain the agent at runtime. It constrains the humans who are supposed to govern the agent. This is an important distinction.

Constitutional rules are different. They are constraints enforced at the moment of each agent action, before the action is taken, regardless of what the agent was instructed to do and regardless of what context it received.

Four categories of constitutional rules must be defined for every agentic deployment before engineering starts:

**Actions the agent may never take.** Hard stops. Regardless of instruction, context, or user request. If an action is in this category, the agent refuses it and escalates to a human. This list should be short and unambiguous. If it is long, the authorization model is not scoped correctly.

**Actions requiring explicit human authorization.** Regardless of confidence level. The agent does not proceed without a named human decision. The agent presents the decision package; the authorization is logged before the action is taken.

**Actions the agent may take autonomously.** The positively-defined scope of autonomous action, with the specific conditions under which that authorization can be suspended or revoked.

**The kill-switch procedure.** Who can pause the agent, by what mechanism, how fast it takes effect, and what happens to in-flight work when the agent is paused. This procedure must be tested before launch. A kill-switch that exists only in documentation is not a kill-switch.

### Adaptive governance: matching constraint to consequence

A flat governance policy applies the same review requirement to a low-stakes output and a high-stakes financial transaction. This approach, in which every action requires the same level of review regardless of what it does, has a predictable outcome: supervisors learn to treat high-consequence approvals with the same level of attention they give low-consequence ones. All approvals become rubber stamps.

Adaptive governance ties the constraint level to the consequence classification established in Phase 2.

Minor-consequence actions: proceed with logging; human review on a sampling basis only.

Major and hazardous-consequence actions: human authorization required before the action is taken; confidence threshold applied; alternatives presented at the approval moment.

Catastrophic-consequence actions: the agent does not proceed autonomously. Full context is escalated to a named human supervisor. The human decision is logged with the supervisor's name and the policy version in effect.

> [!NOTE]
> Adaptive governance is not risk aversion. It is calibration. The agent gets less friction on low-stakes decisions and appropriate friction on high-stakes ones. This is how you keep supervision meaningful over time: by ensuring that the approvals that require careful human judgment actually receive it, rather than being lost in a stream of approvals that should never have required human review.

### The authority delegation chain

For every consequential action the agent takes, three questions must be answerable from the audit trail, not from memory, not from a policy document, not by calling the person who was responsible at the time. From the audit trail.

**Whose authority was the agent acting under?** Not "the system was authorized." Which person or role, in which organizational position, explicitly authorized this class of action? The chain from the agent's action back to a named human principal must be traceable.

**Which version of the governance policy was in effect at the time of the action?** Policies change. The version in effect when the action was taken is the policy against which the action should be assessed. If the policy has been revised since the action was taken, both the original version and the current version must be available for comparison.

**Who carries the outcome if the action was wrong?** If this question has no answer, the system is not governed. It is operating. The answer must be established before the agent is deployed, not assigned reactively when an incident occurs.

> [!WARNING]
> If any of the three questions cannot be answered from the audit trail, the system is not supervised. It is merely operating.
>
> "The agent did it" is not a defensible answer to any of these three questions in any regulated context, and in an increasing number of non-regulated ones.

### The affected-person audit view

Most audit trails are designed for engineers diagnosing a failure. They contain system logs, API call records, error codes, and timing data. They tell the engineer what happened technically. They are not readable by anyone without engineering background, and they are not designed to answer the question the affected person is asking.

The affected person, the customer whose request was denied, the vendor whose payment was delayed, the employee whose application was rejected, is asking a different question: why did this happen, was it correct, and what can I do about it?

Most enterprise agentic systems cannot answer this question. The affected person has no designed path to understanding the agent's decision.

Build the affected-person audit view before a regulator requires it. It requires the same action trace as the engineering audit but presented differently: a plain-language description of what the agent decided, the basis for that decision, the authority under which it acted, and the options available to the affected person if they believe the decision was wrong.

In an increasing number of regulated environments, this is becoming a legal requirement. In healthcare, GDPR, and various state-level privacy laws, the right to a meaningful explanation of automated decisions is moving from a design preference to a compliance obligation.

### Retirement as a designed lifecycle stage

Retirement is the phase that almost no team plans for and everyone eventually needs. It is typically triggered by a crisis: the agent produces a serious error, someone escalates, and the response is to shut it down as quickly as possible while minimizing damage.

Crisis-driven retirement produces data loss, user confusion, audit gaps, and no institutional learning. The team that improvised a retirement once will improvise the next one too.

Designed retirement has four steps, executed in sequence, with named owners assigned before launch.

**Step 1: Preserve the audit trail.** Before any systems are decommissioned, archive the complete action trace with the retention policy applied. This step cannot be deferred to "after we figure out what to do." Once the agent stops running and infrastructure is decommissioned, the audit record may be permanently unrecoverable.

**Step 2: Block new invocations.** From the moment the retirement decision is made, the agent accepts no new tasks. In-flight tasks are either completed under heightened human review or handed to a human workflow. Do not allow new work to enter an agent that is being retired.

**Step 3: Notify affected users.** Plain-language explanation of the retirement reason, the date the agent will stop operating, and what the replacement workflow is. Users who have adapted their work around the agent need enough lead time to readapt.

**Step 4: Document the decision.** The reason for retirement, who authorized it, which policy version was in effect, and what specific conditions would need to change before a replacement is considered. This document is the institutional memory that prevents the same design from being reproposed in six months without the context of why the previous version was retired.

> [!IMPORTANT]
> The drift and retirement policy must be written before the agent ships and attached to the go/no-go memo. It is not a document for later. An agent that ships without retirement criteria defined is an agent nobody has authorized to run indefinitely, but that is exactly what it will do.

### Case study A: SAP financial postings agent — all four vectors, nine months

An SAP agent flags anomalies in financial postings. It passes all launch criteria. Over nine months, all four drift vectors go active simultaneously without any single event triggering a review.

Model drift: the foundation model is updated twice by the provider. The team receives the vendor's release notes, which note no behavioral changes. No behavioral baseline comparison is run.

Data drift: the customer migrates to a new chart of accounts. Input patterns shift significantly. The agent's false positive rate increases. The operations team attributes it to migration noise and does not investigate further.

Supervision drift: the weekly review of the agent's flagging decisions is canceled by the operations manager because "things have been quiet." No replacement threshold or interim process is defined.

Scope drift: an exclusion list that prevented the agent from flagging intercompany transfers is silently removed from the prompt during an unrelated prompt update. The agent begins flagging transfers that are correct and authorized.

Nine months after launch, a financial controller asks why intercompany transfers are appearing in the exception queue. The investigation reveals all four drift vectors were active. No drift thresholds were defined at launch. No retirement criteria were written.

### Case study B: Practice Fusion — the governance failure was not the prompt

In 2019, the electronic health record vendor Practice Fusion paid $145 million to settle federal allegations that it had embedded opioid prescribing suggestions in clinical workflows without disclosure, paid for by an opioid manufacturer.

The prompt worked exactly as designed. The governance failure was not the prompt itself. It was the complete absence of an authority delegation chain.

Nobody had documented whose authority the clinical decision support prompt was acting under. There was no policy document that governed which organizations could influence the content of suggestions shown to physicians. There was no version control on what was in the suggestion logic at any point. Patients who received opioid prescriptions influenced by the prompt had no mechanism to learn that the suggestion had appeared, or that it had been funded by a manufacturer.

Three fields in the authority delegation chain were all blank: whose authority, which policy version, who carries the outcome.

This is not primarily a healthcare story. It is a governance architecture story. Any enterprise agentic deployment where the agent surfaces recommendations, suggestions, or content without a traceable chain from the recommendation to a named authorizer under a versioned policy is vulnerable to the same governance gap, regardless of industry.

---


---

← [Phase 4 — Observe](05-observe.md) | [Index](README.md) | Next: [PM Tools and Templates](07-tools-and-templates.md) →