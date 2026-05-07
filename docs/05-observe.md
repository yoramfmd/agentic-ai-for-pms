← [Phase 3 — Evaluate](04-evaluate.md) | [Index](README.md) | Next: [Phase 5 — Operate, Govern and Retire](06-operate-govern-retire.md) →

---

# Phase 4 — Observe: Measuring What the Agent Actually Does

### Launch is not the end

In traditional product management, launch is the culmination of the development cycle. You ship, you measure, you iterate. The post-launch work is about improving and growing a product that is essentially doing what it is supposed to do.

For agentic AI, launch is the moment when the real questions begin. The evaluation was a hypothesis about how the system would behave. Production is the test. And unlike a deterministic system, where production behavior is predictable from the spec, an agentic system's production behavior can diverge from the evaluation behavior in ways that are invisible until they become consequential.

The PM who treats launch as the end of a project and hands it off to an operations team has not shipped an agentic product. They have deployed an unmonitored system.

### Why traditional monitoring fails

Standard product monitoring is designed for deterministic systems. It catches binary failures: the API returned a 500 error, the transaction did not complete, the feature did not load. These are conditions the system can report on itself.

An agentic system that is performing below expectations does not report a 500 error. It produces outputs. The outputs are coherent. They pass basic quality checks. They are less accurate than they were at launch, or less accurate in specific segments of the input distribution, in ways that accumulate slowly enough that no threshold is ever crossed.

Utilization metrics go up. Completion rates look fine. Time-to-completion is acceptable. The product health dashboard shows green. Meanwhile, 70 percent of the agent's outputs are being significantly rewritten by users who do not trust them but have not been given a feedback mechanism to surface that distrust.

Traditional monitoring cannot catch this. The observation framework in this phase is designed specifically for what traditional monitoring misses.

### Automation complacency: the predictable failure mode of supervision

When a system performs reliably over time, the people supervising it adapt. This is rational behavior. If the agent has been right 98 percent of the time for six months, spending 30 seconds carefully reviewing each output before approving it is not efficient supervision. It is expensive theater.

Supervisors learn to trust the system. They start approving faster. They stop reading as carefully. The supervision that appeared to be a meaningful check becomes a rubber stamp with extra steps.

This is **automation complacency**: not a character flaw in the supervisors, but the predictable behavioral output of a supervisory role that was never designed to maintain vigilance as conditions change.

The dangerous moment arrives when system performance degrades. The supervisors are calibrated to the system's performance from several months ago. The system is performing worse. The supervisors do not know this, because nothing told them.

> [!NOTE]
> Tesla Autopilot provides the most extensively documented example. The National Highway Traffic Safety Administration documented 467 crashes, 54 injuries, and 14 deaths attributable in part to drivers who had learned to trust the system enough to reduce their attentiveness, and then were not able to intervene quickly enough when the system failed.
>
> "Keep your hands on the wheel" was stated clearly in the user agreement. It was not a sustainable design constraint. The instruction was in conflict with the behavior the system trained over time through consistent performance.
>
> The lesson for enterprise agentic products: you cannot write a policy that fixes automation complacency. You have to build a product that counters it. The supervisory role must be designed to maintain meaningful vigilance, which means the product must give supervisors real signals about system performance, not just completion rates.

### The six observation instruments

Six metrics, when designed in before launch, provide meaningful coverage of what traditional monitoring misses. They are not dashboards to build after you ship; they are design requirements that must be defined before the product ships, because several of them can only exist as metrics if the corresponding design artifact from Phase 2 was built correctly.

> [!NOTE]
> **1. Task success rate.** Did the agent do what the user actually intended, not just what it reported doing? Requires a definition of task success that goes beyond "the agent completed without an error state." Semantic success means the user's actual goal was achieved, which often requires follow-up measurement.

> [!NOTE]
> **2. Unintended action rate.** Actions taken outside the authorized boundary. This metric can only exist if the autonomy boundary from Phase 2 was designed to be logged. If your team cannot produce this metric, it means the autonomy boundary was not designed; it exists only as an implicit assumption.

> [!NOTE]
> **3. Override frequency.** The rate at which users modify, reject, or override the agent's outputs before acting on them. Too high suggests a trust problem: the agent's outputs require too much correction to be useful. Too low in a high-uncertainty domain suggests supervision has stopped, not that the agent has become perfect.

> [!NOTE]
> **4. Confidence calibration.** The relationship between the agent's expressed certainty and its actual accuracy. A miscalibrated agent that is highly confident on cases it gets wrong builds fragile trust, because users correctly learn to rely on high-confidence signals and then encounter failures they were not prepared for.

> [!NOTE]
> **5. Rollback time.** The time from identifying an incorrect action to completing a correcting action. This metric exists as a real number only if the recovery workflow from Phase 2 was built as a product surface. If rollback time is measured by how long an email thread took to resolve the problem, the recovery workflow does not exist as a designed artifact.

> [!NOTE]
> **6. Incident recovery time.** When the agent misbehaves at scale: the time from first signal of a problem to freezing new invocations, attributing the cause, notifying affected users, and restoring safe operation. This measures the speed of improvisation if the response sequence was never designed before launch.

The absence of any instrument is a design gap, not a measurement gap. If your team cannot produce override frequency data, the approval moment in Phase 2 was not designed to be logged. Fix the design, then measure.

### Shadow workflows: what adoption numbers hide

Shadow workflows are the parallel manual processes that teams maintain alongside an AI feature when they do not trust the agent enough to rely on it for consequential decisions. The workflow runs. The metrics look good. The actual work is happening somewhere else.

A sales team using an AI feature that drafts account plans achieves 90 percent adoption within two months. The PM reports strong results at the quarterly review. A closer look: 70 percent of those plans are being significantly rewritten before submission. The representatives are opening the draft, extracting the structure, discarding most of the content, and writing a new plan. The agent saved them from a blank page. It did not save them from the work.

The metric tracked was opens. The metric that would have revealed the quality problem was override frequency, which was never instrumented.

> [!NOTE]
> **The signal that surfaces shadow workflows:** override frequency trending too low in a high-uncertainty domain.
>
> If users are not overriding outputs in a domain where a meaningful proportion of outputs should require modification, one of three things is true: the outputs are uniformly excellent (test this directly); users have stopped trusting the agent enough to engage with it critically and have moved their real work to a parallel process; or users have come to treat the agent's outputs as a starting point that is always rewritten, making the "adoption" metric meaningless.

### Silent degradation: the scissors dynamic

Silent degradation is the production failure mode specific to agentic AI. The system continues operating, continues producing outputs, and continues appearing healthy on standard metrics while its actual performance falls.

The dynamic works like scissors: one blade is system accuracy, trending slowly downward over time as models drift, training data becomes stale, and input distributions shift. The other blade is user trust, trending slowly upward as users grow familiar with the system and reduce their vigilance. The blades cross at some point after deployment. After that crossing, the system is wrong more often than users expect, but users do not know this because their trust was calibrated to earlier performance.

The system does not announce this. There is no dashboard indicator that shows "accuracy has crossed below trust level." There is just a slow accumulation of confident, fluent, wrong answers being acted upon by supervisors who have no reason to question them.

On the system side, six independent mechanisms can contribute to degradation and compound with each other:

The foundation model provider silently updates the model. The training cutoff moves and recent events fall outside the model's knowledge. The context window specification changes. The tokenizer changes in ways that affect how inputs are processed. The retrieval corpus for RAG shifts as upstream data sources are updated. The upstream tool APIs the agent calls evolve in ways that change the agent's effective capabilities.

None of these are announced with a deployment event that would trigger a governance review. All of them can shift the agent's behavior outside its evaluated range.

### Deployment events as governance triggers

The observation instruments must be re-run whenever any component the agent depends on changes. Three categories of change constitute deployment events:

**Model update.** Any change to the foundation model version requires re-running the full evaluation suite and establishing a new behavioral baseline before restoring production traffic. Do not assume that a model update with no announced behavioral changes is behaviorally neutral.

**Prompt change.** Any change to the agent's instructions, including "minor clarifications" and "small wording improvements," alters behavior across the full input distribution. Treat every prompt change as a release requiring a Pass@K run.

**Tool configuration change.** Any change to the tools the agent can access, including permission scope changes and API version updates, changes the agent's action surface. Requires boundary re-validation and a new audit baseline.

The observation instruments designed at launch have a half-life. Plan to recalibrate them at each major foundation model generation, approximately every 18 months, as the models they were calibrated against are replaced by different architectures.

### Case study: SAP account plan and the missing instruments

An SAP sales team deploys an AI feature that drafts account plans based on CRM data, opportunity history, and product usage signals. Usage reaches 90 percent of the sales team within two months. The PM reports strong adoption at the quarterly business review and flags the feature as a success.

Follow-up analysis finds that 70 percent of account plans submitted to the pipeline have been significantly rewritten from the AI draft before submission. The AI draft is being used as a starting point that saves the representative from a blank page, but the substantive content is being replaced by the representative's own analysis.

None of the six observation instruments were designed before launch. There is no override frequency measurement, no task success rate tied to plan quality, no confidence calibration data, and no rollback time because no recovery workflow was built.

The product appears healthy. The signal that would have revealed the quality gap was never designed in.

Bank of New York Mellon's Eliza platform, which runs over a hundred digital agents in regulated financial services, designed the observation instruments into the product brief before the first agent was deployed. The checkpoint reviews that caught early drift were a designed function, not a reactive response to an incident.

---


---

← [Phase 3 — Evaluate](04-evaluate.md) | [Index](README.md) | Next: [Phase 5 — Operate, Govern and Retire](06-operate-govern-retire.md) →