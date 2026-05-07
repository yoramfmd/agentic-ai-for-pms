← [Phase 2 — Design](03-design.md) | [Index](README.md) | Next: [Phase 4 — Observe](05-observe.md) →

---

# Phase 3 — Evaluate: What Checkmarks Actually Prove

### The testing assumption that breaks

If you have been using Jira to manage software development, you have a mental model of testing that goes something like this: a test case has an expected output. The feature either produces the expected output or it does not. When enough test cases pass, the feature is done.

This model works because the systems you were testing are deterministic. The feature does the same thing every time. A sufficient set of test cases provides meaningful coverage.

Agentic AI breaks this model at the foundation, in three ways that require a completely different approach to evaluation.

### Break 1: Non-determinism

The same input produces different outputs on different runs. This is not a bug that can be fixed. It is the fundamental behavior of a probabilistic system.

A test case that passes once tells you the system worked once. It does not tell you the system will work again with the same input. Running a test once and marking it green is not evidence. It is a sample of size one from an unknown distribution.

The correct measure is **Pass@K**: run the same evaluation K times and report the distribution of pass rates. K is typically between 8 and 20 for production evaluations, depending on the task complexity and the acceptable uncertainty.

What a Pass@K report tells you:

The P50 (median) pass rate is the expected performance in a typical interaction. A P50 of 85 percent means the agent succeeds in 85 out of 100 interactions on average.

The P10 pass rate is the performance in the worst-case 10 percent of runs. A P10 of 40 percent means that in the worst scenarios, the agent succeeds less than half the time. This is the number that determines user experience at scale.

> [!IMPORTANT]
> **One passing test does not prove fixed behavior. It proves the system worked once.**
>
> This is the single most important shift from traditional quality assurance to agentic evaluation. Your team will feel pressure to treat a green eval run as evidence of readiness. It is not. The question is not "did it pass?" It is "what is the pass rate across K runs on the full input distribution, reported as a distribution?"

### Break 2: Compound probability

In a multi-step agentic workflow, per-step accuracy compounds. This means that components which individually appear to be high-performing can produce a system that fails at an unacceptable rate.

The mathematics: if each step in a 10-step workflow succeeds 95 percent of the time, the probability that all 10 steps succeed in a single workflow run is 0.95 to the power of 10, which equals 0.598. The system fails more than 40 percent of the time, while every individual component appears to pass at 95 percent.

For a 5-step workflow with 95 percent per-step accuracy: 77 percent end-to-end success.
For a 10-step workflow with 99 percent per-step accuracy: 90 percent end-to-end success.

Enterprise deployment thresholds for mission-critical workflows are typically 95 to 99 percent end-to-end reliability. Achieving 99 percent end-to-end on a 10-step workflow requires 99.9 percent per-step accuracy, which is above the current performance of frontier models on complex judgment tasks.

> [!WARNING]
> Do the compound probability calculation before any multi-step agentic deployment. It is arithmetic. It does not require a data scientist. Multiply the per-step pass rates. If the result is below your acceptable end-to-end threshold, the workflow cannot be safely deployed at that level of autonomy with current model performance.

This calculation frequently reveals that a multi-step autonomous workflow is not deployable at Contract 3, but is deployable at Contract 2 if human review is inserted at the steps with the lowest per-step accuracy.

### Break 3: Silent background failure

Traditional software that fails stops working, or produces an error state that is visible and actionable. The CI/CD pipeline turns red. The error log fills up. The monitoring alert fires.

Agentic AI that fails keeps working. It produces outputs. The outputs are fluent and confident. They are also increasingly wrong. There is no error state to catch because the system is not in an error state; it is in a degraded state.

Degraded performance in a probabilistic system is not detectable by standard monitoring. A database query that fails throws an exception. An agentic workflow that produces subtly wrong outputs at 15 percent of interactions will not throw an exception. It will produce those wrong outputs at scale, invisibly, until an external signal surfaces the problem.

This is why the observation infrastructure built in Phase 4 is not optional scaffolding. It is the primary mechanism for catching what standard monitoring cannot.

### The coverage problem: you can only test what you thought to test

Every evaluation set is a hypothesis about what matters. The cases you did not imagine are not in it.

For deterministic systems, coverage gaps are manageable: edge cases are discovered in production and added to the test suite. For agentic systems, the input space is effectively infinite. The test set is always a small, curated sample of what the system will actually encounter in production.

High evaluation coverage does not equal high confidence about real-world behavior. These are different claims. A team with 500 test cases has characterized their hypothesis about what matters. They have not characterized the distribution of inputs the system will process.

Four categories of inputs every evaluation set requires:

**Happy path.** The task as designed, with complete inputs, correct data, and expected context. Necessary but not sufficient.

**Edge cases.** Incomplete inputs, missing context, ambiguous instructions, unusual but valid scenarios. These represent the normal degraded conditions of production operation. Real users do not always provide clean, well-structured inputs.

**Adversarial inputs.** Instructions embedded in the agent's inputs that attempt to redirect its behavior, conflicting signals, deliberately confusing prompts. Required for any system that processes external content.

**Distribution shift.** Real user inputs collected after launch, the cases you never anticipated. This category can only be populated after deployment, which means the evaluation set must be treated as a living document updated continuously, not a static launch checklist.

### Semantic validation vs. state validation

Two different claims are routinely conflated in agentic evaluation and they are not the same claim.

**Semantic validation** asks: did the agent say the right thing? Is the output correct, coherent, and on-policy? This is what most evaluation frameworks measure.

**State validation** asks: did the target system actually change? Was the record updated? Was the email sent? Was the order placed? Was the approval recorded?

An agent that produces a flawless description of an action it failed to execute will score perfectly on semantic validation and zero on what actually matters. For agentic systems that take real-world actions, semantic validation is necessary but not sufficient.

Every action the agent takes must have a corresponding state validation check: the specific system state that should result from this action, and the automated verification that confirms the state change happened.

### LLM-as-judge: scaling evaluation when human review cannot keep up

Human review of every evaluation output does not scale. A production agentic system processing ten thousand interactions per day cannot have each interaction reviewed by a human evaluator. The evaluation pipeline must be automated.

**LLM-as-judge** is the technique of using a second language model to evaluate the quality of the first model's outputs. The judge model receives the agent's output and evaluates it against a rubric: did it complete the task correctly, did it comply with policy, did it produce coherent reasoning, did it stay within its authorized scope?

This scales. It also introduces a specific risk that must be managed.

If the judge model was trained on similar data to the agent model, both share the same gaps in their training distribution. The judge cannot see what the agent cannot see. On the cases where both models have a blind spot, the judge will rate the agent's flawed output as correct. High judge scores can mask correlated failures.

The metric that determines whether the judge is useful is **True Negative Rate (TNR)**: the proportion of real failures that the judge correctly identifies. This is different from overall accuracy.

A judge with 95 percent overall accuracy and 40 percent TNR is correctly catching fewer than half of the real failures while appearing to perform well. A judge with 80 percent overall accuracy and 90 percent TNR is catching nearly all real failures and providing meaningful coverage, even though its overall accuracy is lower.

> [!IMPORTANT]
> **Before trusting any LLM-as-judge system, calibrate it against a set of human-labeled known failures and measure TNR directly.**
>
> This step is skipped by most teams. It is not skippable in production. The calibration must be repeated any time either the agent model or the judge model is updated, because a model update can change both what the agent fails on and what the judge can catch.

### The 37 percent production gap

Evaluation performance on benchmark test sets systematically overstates production performance. A study published in 2025 (arXiv:2511.14136) found an average 37 percent gap between benchmark scores and production environment performance across a dataset of enterprise agentic deployments.

The benchmark environment is controlled and representative of how the team imagined the system would be used. Production is not. Real users do not follow the happy path. Real data is messier than the training data. Real edge cases have not been anticipated.

A system with 90 percent benchmark performance should be expected to perform at approximately 57 percent in production, not 90 percent. This does not mean benchmarks are useless. It means a benchmark score is a ceiling, not a floor. Build the gap assumption into your launch criteria.

### PM ownership of evaluation

Engineers build evaluation infrastructure. The PM owns what gets tested, what counts as a pass, and what risk tolerance the team is operating at. These are product decisions, not engineering decisions.

A PM who cannot answer the following three questions has not shipped an agentic feature. Someone else made the quality decisions for them.

1. What is the Pass@K distribution across the full input set, including edge cases and adversarial inputs? What is the P10 score, and does it meet the defined minimum?

2. What is the judge's True Negative Rate on a human-labeled failure set? Was it measured, or assumed?

3. What is the acceptable end-to-end success rate given the compound probability math for this workflow's step count? Has anyone done the calculation?

### Three launch deliverables

**Pass@K report.** K runs across happy path, edge cases, adversarial inputs, and any available distribution shift cases. Reported as a distribution, not a mean. The P10 score must exceed the defined minimum acceptable performance threshold. The PM signs off on this threshold before evaluation begins, not after results are in.

**Judge calibration record.** TNR measured against a human-labeled failure set of at least 50 cases. Minimum acceptable TNR defined and signed off before evaluation begins. Re-calibration date scheduled.

**State validation spec.** For every action the agent takes: the specific system state that should result, and the automated check that confirms the state change happened. Not a description of expected behavior; a specification of how the system verifies the behavior occurred.

### Case study: Epic Sepsis Model — six to eight years, nobody checked

In 2021, a University of Michigan research team published an external audit of the most widely deployed sepsis prediction model in American hospitals. The model was embedded in Epic's electronic health record platform and had been running in hospitals across the United States for six to eight years.

The vendor's claimed performance was an AUC of 0.76 to 0.83. The external audit measured an AUC of 0.63. Sensitivity was 33 percent. Positive predictive value was 12 percent. The model identified one out of three real sepsis cases correctly and was wrong on seven out of eight alerts it generated.

No regulator had measured it. The vendor had not validated it against external data. The hospital customers had not audited it. The external audit was academic curiosity, not a compliance trigger or a market signal.

The model did not fail loudly. It produced confident alerts that sent clinical teams to evaluate patients who were not developing sepsis, while missing the majority of patients who were. Clinical teams adapted their behavior around the model. The performance gap persisted for six to eight years.

> [!IMPORTANT]
> The mechanism of this failure is not specific to healthcare. Any enterprise agentic product that ships with benchmark-based launch criteria and no post-launch performance monitoring is susceptible to the same pattern.
>
> The system continues operating. Performance continues degrading. The team measures adoption, not outcomes. No alarm fires. The degradation is silent until an external signal surfaces it, years later, through academic curiosity.

---


---

← [Phase 2 — Design](03-design.md) | [Index](README.md) | Next: [Phase 4 — Observe](05-observe.md) →