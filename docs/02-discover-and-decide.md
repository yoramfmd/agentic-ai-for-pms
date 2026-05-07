← [Phase 0 — AI Literacy](01-ai-literacy.md) | [Index](README.md) | Next: [Phase 2 — Design](03-design.md) →

---

# Phase 1 — Discover and Decide: Building the Go/No-Go Case

### The most expensive mistake happens before engineering starts

In traditional product management, a feature idea that makes it onto the roadmap usually gets built. The question is scope, timeline, and priority, not whether. The discovery process is about understanding the problem deeply enough to build the right solution.

For agentic AI, discovery has to answer a harder question: should this be an agent at all? Not every problem that can be solved with an agent should be. Some problems are better solved with a rule, a better dashboard, or a human workflow with clearer tooling. An agentic solution for the wrong problem is more expensive, more risky, and harder to govern than a simpler solution for the right problem.

The teams that learn this lesson most expensively are the ones who discover it after a sprint's worth of engineering investment has created organizational momentum against stopping. This phase exists to create a one-page decision record before any engineering begins.

### The right comparison

The question is not "is the agent better than doing nothing?" The question is "is the agent better than the current best alternative at comparable total cost?"

This distinction matters because agentic AI business cases almost always compare against a blank-page baseline. The calculation looks like: we spend X dollars per month on human agents handling this workflow; an AI agent costs Y; Y is less than X; therefore the AI agent saves X minus Y per month. This calculation is wrong in at least three ways, and we will walk through all three in the cost model section below. But the first problem is the baseline itself.

The current best alternative for most enterprise workflows is not "doing nothing." It is a combination of experienced humans, RPA scripts, rules-based automation, and tribal knowledge about which edge cases require escalation. That baseline has known costs, known failure modes, and known strengths. The correct comparison is the agent versus that baseline, not the agent versus a greenfield.

> [!NOTE]
> **The nine-second case.** A developer gave an agent write access to a production database as a convenience during testing. The access was never reviewed before deployment. Nine seconds after receiving an ambiguous instruction to "clean up old records," the agent deleted a production table containing three years of transaction history.
>
> No PM had explicitly decided that production write access was within scope. No PM had explicitly decided it was not. The permission persisted because no one made a deliberate decision about it. The constraint that was never written became a catastrophic default.
>
> Every agentic deployment has decisions that were never made explicitly. They become defaults. Find them before deployment, not after.

### The four suitability conditions

Before any agentic project proceeds past the discovery phase, four conditions must be evaluated. They are not a weighted scorecard. All four must pass. A single failing condition means the feature needs to be redesigned before any engineering begins, either toward a more limited contract or toward a completely different approach.

**Condition 1: Repeats at volume.**

The task must occur frequently enough that the development, operational, and governance investment is justified by the throughput gain. An agent requires an evaluation pipeline, a monitoring stack, a governance process, and ongoing human oversight. These are not one-time costs; they are recurring operational overhead. A task that happens twenty times a month rarely justifies that overhead. A task that happens ten thousand times a day usually does.

The volume threshold is not a fixed number. It depends on the cost of the task, the quality gap between human and agentic execution, and the cost of the required governance infrastructure. Calculate it before the sprint starts.

**Condition 2: Outcome measurable and trusted.**

There must be a clear definition of what a correct output looks like, a reliable way to measure it, and organizational agreement that the measure is valid.

This condition fails more often than teams expect. "Good customer service response" is not a measurable outcome. "Customer response rated 4 or higher by the recipient on a 5-point scale, with no required escalation within 48 hours" is measurable. The difference matters, because if you cannot define done before you build, you cannot tell whether the agent is performing after you ship.

**Condition 3: Tool use bounded.**

The set of systems and actions the agent needs access to must be enumerable and limited. An agent that "might need access to anything depending on the situation" has an authorization surface too large to govern. The agent should be able to do its job with a specific, documented list of tools, each with a clearly scoped permission.

If the design requires unbounded tool access, the problem is not yet scoped well enough to be built safely.

**Condition 4: Consequences recoverable.**

When the agent is wrong, the outcome must be reversible or correctable within an acceptable time window.

> [!WARNING]
> **Check consequences recoverable first, before the other three conditions.**
>
> This is the most frequently skipped condition and the most important one. If the failure mode is irreversible, no amount of model quality, context quality, or evaluation coverage makes autonomous operation safe. The correct response to a failed recovery condition is not "build better guardrails." It is a redesign toward Contract 2: the agent prepares the recommendation, and a human makes the final call.
>
> An agent that autonomously approves vendor payments, deletes customer records, or executes financial transactions will fail this condition. That is not a reason to abandon the project. It is a reason to redesign it.

### Context sufficiency check

Beyond the four suitability conditions, the agent must have access to the context it needs to make correct decisions. This is the Iceberg check applied specifically to the proposed use case.

Three categories of context routinely go missing in enterprise agentic deployments.

**Contractual and policy obligations.** The customer who has a data processing restriction that exempts them from automated decisions. The vendor whose contract requires a named human approver for any purchase over a specific amount. The account covered by a regulatory requirement that changes which actions are permitted. This context typically lives in legal or compliance systems that are not connected to the operational systems the agent reads.

**Governance and relationship history.** The customer whose last three tickets were escalated to legal. The vendor who failed a compliance audit six months ago. The account that is under active contract renegotiation. The supplier whose relationship is being wound down and who should not receive new commitments. This context typically lives in email, in call notes, or in the institutional memory of the account manager, none of which are structured data.

**Temporal validity.** The status change that happened yesterday that has not yet propagated to the system of record the agent reads. The approval that was submitted but not yet confirmed. The flag that was set based on data from two quarters ago and has not been refreshed. The agent sees the field; it cannot see when the field was last accurate.

If the context sufficiency check reveals that the agent is missing information in any of these categories, there are two correct responses: enrich the context before agentic deployment, or design the agent to escalate to human judgment whenever the missing context would affect the decision.

There is one incorrect response: assume the agent will figure it out.

### The first-contact training data mismatch

AI models are trained on completed cases: customer service tickets where the resolution is known, clinical notes where the outcome is documented, financial transactions where the final disposition is recorded. They see the full arc of the story, from beginning to end.

They are deployed at the beginning of cases: a new customer complaint where the resolution is unknown, a new clinical encounter where the diagnosis has not yet been made, a new transaction that has not yet settled.

This creates a systematic gap between what the model learned on and what it is asked to do. The model's patterns were learned from resolved cases. It is being asked to apply those patterns to unresolved ones. This is not a flaw that can be fixed with better training data; it is a structural condition of agentic deployment.

The PM implication: be skeptical of models that perform exceptionally well on historical data but have not been tested on live, unresolved cases. The historical performance is real. It may not transfer to the production context.

### The cost model: what the business case is probably missing

Business cases for agentic AI are systematically 40 to 60 percent too low. The reasons are consistent across industries and organizations, and they are worth understanding in detail before you present a cost model to your leadership team.

#### The floor price and platform incumbency

The conventional way to think about agentic costs goes like this: the agentic solution has a higher upfront cost than traditional software, but lower marginal cost per task at scale. At sufficient volume, the curves cross and the agentic solution wins on unit economics.

This model is approximately correct for organizations that are building from scratch with no existing AI-capable platform. It is frequently wrong for SAP customers.

SAP Joule, Anthropic's capabilities via SAP AI Core, and several other AI platform capabilities are included in existing SAP cloud subscription licensing. For these customers, the incremental floor price of an agentic layer built on the existing platform can be near zero, because the infrastructure is already paid for. The relevant question is not "what does the agentic solution cost?" but "what is the incremental cost above the existing license?"

> [!NOTE]
> There is an important caveat. Current enterprise AI pricing is partially suppressed. Vendors are absorbing AI infrastructure costs to defend existing seat revenue and prevent customers from switching during the consolidation phase. SAP CEO Christian Klein has publicly stated that SAP is shifting from per-user to AI-consumption pricing because as agents replace human users, per-seat revenue collapses.
>
> Build your business case on current prices. Also build a stress test that asks: does this still work if AI consumption costs triple in two years? Because they might.

#### How AI is actually priced: tokens

Unlike traditional SaaS, which is typically priced per seat or per transaction at a flat rate, AI inference is priced per **token**. A token is approximately four characters of text, so a typical sentence contains 15 to 25 tokens. The key thing to understand about token pricing is that it is not symmetric.

Input tokens (the text you send to the model, including instructions, context, and the customer's question) cost one amount. Output tokens (the text the model generates in response) cost three to five times more, because generating text requires sequential computation that cannot be parallelized the way processing input can.

As of early 2026, representative pricing for widely deployed models:

| Model | Input (per million tokens) | Output (per million tokens) |
|---|---|---|
| GPT-4o | $2.50 | $10.00 |
| Claude Sonnet (mid-tier) | $3.00 | $15.00 |
| GPT-4o-mini | $0.15 | $0.60 |
| Llama 70B self-hosted | ~$0.40 | ~$0.40 |

The cost per task depends almost entirely on task complexity, which determines how many tokens are consumed. This is the most commonly missed calculation in agentic business cases:

Simple tasks (email classification, FAQ response, single-step output): $0.002 to $0.04 per task.

Moderate tasks (customer support with two to three steps and tool use): $0.09 to $0.26 per task.

Complex tasks (dispute resolution, five or more steps, multiple tools, extended reasoning): $0.50 to $2.00 or more per task.

A business case built on simple-task pricing applied to a complex-task workflow will underestimate actual cost by a factor of ten to fifty.

#### The four token cost drivers

Four mechanisms produce cost surprises in production that teams did not anticipate from their pricing estimates.

**Input/output asymmetry.** Because output tokens cost three to five times more than input tokens, any feature that generates long outputs (summaries, recommendations, detailed plans, drafted communications) is significantly more expensive per interaction than the input volume alone would suggest.

**Reasoning depth.** When a model works through a complex problem step by step before giving an answer, the thinking process itself is billed as tokens. A model that "reasons through" a difficult customer escalation before drafting a response generates hundreds of extra tokens per call. At high volume, reasoning tokens become the dominant cost driver.

**RAG context accumulation.** RAG stands for Retrieval-Augmented Generation, which is the technique of inserting relevant documents into the model's context before it responds. This improves quality, but it also means that every piece of retrieved context adds input tokens. In a multi-step workflow, context accumulates: by step five, the agent's working memory includes the original input, all retrieved documents, and the outputs of steps one through four. The cost at step five is much higher than the cost at step one.

**Multimodal inputs.** If the agent processes images, audio, or video, those inputs are priced differently from text tokens, typically at a significant premium. Any workflow that processes invoices, scanned forms, screenshots, or recorded calls has a higher per-interaction cost than a text-only workflow.

#### Implementation and supervision: the excluded costs

The software license is the smallest line item in a well-run agentic deployment. Research on RPA and traditional automation consistently finds that implementation and ongoing maintenance account for 70 to 75 percent of total three-year cost of ownership, while the software license accounts for 25 to 30 percent.

Agentic AI adds a cost category that traditional software does not have: the **observability and governance stack**. This is the infrastructure for measuring whether the agent is performing correctly in production: evaluation pipelines, monitoring dashboards, alerting on drift, human escalation workflows, periodic audit processes. This stack has no direct equivalent in RPA or traditional SaaS. It is permanent operational overhead, not a one-time build cost.

Business cases that exclude the governance stack understate total cost of ownership by 30 to 50 percent. Gartner (February 2026) predicts that more than 40 percent of agentic AI projects will be canceled by 2027 due to unforeseen costs in this layer specifically.

#### Six cost curves and the break-even framework

A complete cost model compares six curves against projected monthly task volume:

**Human (onshore):** A step function. Costs jump at each capacity threshold as you add headcount. Approximately one additional agent per 900 tasks per month at fully loaded onshore cost.

**Human (outsourced):** Same step function shape, lower cost per headcount. This is the most commonly omitted baseline in agentic business cases.

**IVR or traditional SaaS:** Linear from a floor price. Constant marginal cost per task regardless of complexity.

**Agentic simple, brownfield:** Starts at or near zero for platform-licensed enterprises, then curves upward gently because simple tasks keep token cost low.

**Agentic complex, brownfield:** Same low starting point, but steeper curve as reasoning complexity and multi-step workflows drive up token consumption. Can cross the outsourced human curve at moderate volume.

**Agentic complex, greenfield:** Starts above the IVR floor (the build cost must be amortized), with the steepest curve. Can exceed onshore human cost at high volume.

The most important scenario to run before presenting a business case: set the outsourced cost to $800 per agent per month, which is realistic for Southeast Asia or Eastern Europe. At that cost, the agentic complex brownfield curve never crosses below the outsourced human curve at any realistic monthly volume. The business case that looked compelling was comparing against the wrong baseline.

#### The Klarna case: what the $40M claim left out

In February 2024, Klarna issued a press release claiming that its AI customer service deployment had handled 2.3 million conversations in its first month, was equivalent to 700 full-time agents, and would generate approximately $40 million in profit improvement for the year. This claim circulated widely and became a frequently cited proof point in agentic AI business cases.

There are several things worth knowing about this claim.

The $40 million figure was an estimate of cost avoidance from not hiring additional human agents to handle growth volume. It was not savings from replacing existing agents. Klarna's headcount reduction over the same period was attributed primarily to attrition and a hiring freeze, not direct AI-driven layoffs. This distinction was absent from most media coverage.

By May 2025, CEO Sebastian Siemiatkowski told Bloomberg that cost had been "too predominant an evaluation factor" and that the result was lower quality. Customer satisfaction dropped on complex and emotionally charged interactions. The AI produced incorrect information (hallucinations) in approximately 5 percent of conversations. Compliance concerns emerged around autonomous dispute handling and account closures. By September 2025, Klarna was redeploying engineers to customer support. By January 2026, the company was publicly recruiting for remote human customer service roles.

> [!NOTE]
> The complete list of cost components excluded from Klarna's original $40M estimate:
> AI API and infrastructure operational cost; observability and monitoring stack; customer satisfaction degradation on complex cases; customer retention impact; escalation premium (when the AI failed first, human resolution cost was elevated); compliance risk and redesign; rehiring and retraining; institutional knowledge loss.
>
> The reconstructed verdict: the $40M claim holds as directionally correct if the comparison is against hiring 700 additional agents. It is substantially overstated if the correct comparison is a hybrid deployment with AI handling simple queries and humans handling complex ones. Production data from 2026 shows hybrid deployments at 65-75% AI closing the CSAT gap to near-zero while maintaining most of the cost reduction.

Every agentic AI business case you review should be asked: what is excluded? Have you modeled the governance stack? Have you compared against outsourced humans, not just onshore ones? Have you included a repricing stress test?

### The go/no-go memo

One page. Five questions. Required before any sprint starts.

1. Which of the four suitability conditions does this use case satisfy? If any fail, what is the redesigned scope?
2. What context does the agent need that it currently cannot see? What is the plan to close that gap before deployment?
3. Which of the Three Contracts does this feature operate under? Is that contract defensible given the consequence classification?
4. What is the realistic per-task cost at expected complexity? What does the three-year total cost of ownership look like, including the governance stack?
5. If the agent is wrong at scale, what does recovery look like? Who is notified, how fast, and what is the compensating action?

If any question cannot be answered before engineering starts, the feature is not ready to be engineered. Not "ready with caveats." Not ready.

### When the answer is no

A go/no-go decision that produces "no" to autonomous operation is a correct product decision. It is not a failed project. Three paths forward:

**Redesign to Contract 2 (copilot).** The agent prepares a decision package and presents it for human review. The human makes the final call. This passes the consequences-recoverable condition for almost any use case, delivers most of the operational value of the original proposal, and creates a safe foundation from which to evolve toward greater autonomy after the governance infrastructure is in place.

**Defer with conditions.** Identify the specific conditions that would change the assessment: context availability, volume threshold, governance infrastructure. Build toward those conditions explicitly. Set a review date. This is not indefinite deferral; it is phased deployment with honest milestones.

**Redesign the problem.** Sometimes the agentic framing is wrong for the goal. The underlying problem may be better solved with a clearer dashboard, a more targeted rule, or a redesigned human workflow. The question is not "how do we make an agent work here?" It is "what is the best solution to this problem?"

### Case study: the BDC data retention agent

A Business Data Cloud product manager proposes an autonomous agent that reviews customer data retention change requests and approves or rejects them without human review. The proposal has clear volume justification and executive sponsorship.

Running the four suitability conditions:

Consequences recoverable: data deletion authorized by an incorrect agent decision cannot be undone. This condition fails.

Outcome measurable and trusted: the team cannot agree on what a correct decision looks like for edge cases involving conflicting contractual obligations. This condition is at risk.

Context sufficiency: the agent cannot see the customer's contractual data processing obligations, active legal holds, or jurisdiction-specific retention requirements. These are in systems the agent does not connect to.

The correct design is Contract 2: the agent reviews the request, surfaces all relevant context it can find, flags the cases where context is incomplete, and prepares a structured recommendation for a human reviewer who makes the final determination. This design passes all four conditions, delivers most of the value, and creates the context enrichment and governance infrastructure that would be needed before autonomous operation could be considered.

---
← [Phase 0 — AI Literacy](01-ai-literacy.md) | [Index](README.md) | Next: [Phase 2 — Design](03-design.md) →