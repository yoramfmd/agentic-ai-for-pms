← [Phase 1 — Discover and Decide](02-discover-and-decide.md) | [Index](README.md) | Next: [Phase 3 — Evaluate](04-evaluate.md) →

---

# Phase 2 — Design: Building Both Halves of the Product

### The half-built product problem

Ask any agentic product team to describe their product and they will describe the agent: what it does, what it can access, what it produces, how users interact with it. Ask them to describe the supervisory system that governs it and most teams go quiet.

The supervisory system is the second half of the product. It is how humans maintain appropriate oversight of what the agent does: how they monitor it, how they intervene when something is wrong, how they adjust its behavior as conditions change, how they audit its decisions after the fact. Without the supervisory system, the agent runs without meaningful governance. The governance exists on paper, in a policy document that no one reads at the moment an intervention is needed.

Most agentic products ship with only Channel 1 designed. This phase covers what it takes to design both.

### Start with the contract

The Three Contracts determination from Phase 0 is not abstract. It produces specific design requirements.

A Contract 1 (Suggestion Engine) requires careful design of how recommendations are displayed, how confidence is communicated, and how users understand that they are responsible for the final decision. It does not require an extensive approval workflow, because there is no action to approve.

A Contract 2 (Copilot) requires a well-designed approval moment at each consequential step, a clear audit trail of what the human approved and when, and a recovery path for when the approved action turns out to be wrong. The governance burden is the quality of the decision package.

A Contract 3 (Autonomous Actor) requires all of the above plus a defined autonomy boundary, a supervision interface that lets humans monitor what the agent is doing in real time, and a kill-switch procedure that has been tested, not just documented.

If the design review begins without naming the contract, the team will make design decisions at incompatible levels of autonomy. Some parts of the product will be designed for Contract 2 (careful approval moments) while other parts are designed for Contract 3 (no human in the loop). The resulting product will be confusing to users, difficult to govern, and expensive to fix.

### Consequence classification: the FAA model applied to agentic AI

The Federal Aviation Administration uses a consequence taxonomy to determine how much scrutiny any design decision requires based on what happens if it fails. This same logic applies directly to agentic AI design.

Before designing any approval moment or recovery workflow, classify the consequences of every action the agent can take.

> [!NOTE]
> **Minor consequence:** The agent's action, if wrong, produces a brief inconvenience or a small error that is easily corrected. Proceed with low friction. No explicit acknowledgment required from the user.
>
> Example: the agent incorrectly categorizes a support ticket. A human corrects it in thirty seconds.

> [!NOTE]
> **Major consequence:** The agent's action, if wrong, produces meaningful disruption or requires significant effort to correct. Requires explicit user acknowledgment before proceeding.
>
> Example: the agent routes a customer complaint to the wrong service team, causing a 24-hour delay in resolution and requiring a re-explanation from the customer.

> [!WARNING]
> **Hazardous consequence:** The agent's action, if wrong, causes serious disruption that is difficult or expensive to reverse, or puts a customer, employee, or business relationship at significant risk. Mandatory pause. Alternatives presented. A named human decision-maker must authorize.
>
> Example: the agent sends a formal notice of contract termination to a customer whose account was flagged in error.

> [!CAUTION]
> **Catastrophic consequence:** The agent's action, if wrong, causes irreversible harm, significant legal or financial exposure, or irrecoverable damage. The agent does not proceed autonomously under any circumstances. Full context is escalated to a named human supervisor. No autonomous execution.
>
> Example: the agent deletes customer data, executes a financial transaction above a defined threshold, or takes any action that cannot be undone within an acceptable time window.

Classifying consequences before design begins forces the team to enumerate what the agent can actually do. This frequently surfaces scope issues that were not visible in the requirements document: actions that seemed minor in the abstract but are hazardous in context, or permissions that seemed reasonable but enable catastrophic outcomes in edge cases.

### The four runtime artifacts

Every agentic product requires four designed artifacts, regardless of contract type. Most teams produce none of them deliberately. When they are missing, they are not absent; they exist as accidental defaults. The question is whether the defaults were the right design.

#### Runtime artifact 1: Autonomy boundary

The autonomy boundary defines explicitly what the agent may do without asking. Not what it can do technically (that is the tool layer). What it is authorized to do without human approval.

In most deployments this boundary is invisible to the user. The user discovers it by hitting it: the agent does something the user expected it to ask about first. The resulting surprise erodes trust even if the action was technically correct.

Design requirements for the autonomy boundary:

It must be defined before any action is taken. "The agent uses its judgment" is not a defined autonomy boundary.

It must be visible to the user at the moment it matters. Not in documentation somewhere; visible in the product at the point where the boundary becomes relevant to a decision the user is about to make.

It must be logged in the audit trail so that after-the-fact review can show what was authorized and whether the agent stayed within that authorization.

#### Runtime artifact 2: Approval moment

The approval moment is the handoff between agent decision and human judgment when the autonomy boundary is approached. Most implementations produce a confirmation dialog. A confirmation dialog is not an approval moment.

A confirmation dialog looks like: "The agent will send this email. Confirm?" The user clicks Confirm or Cancel. This gives the user no basis for making an informed decision. They are confirming or canceling something without knowing what the agent knows, how confident it is, what the consequences are, or whether there is a better alternative.

A proper approval moment contains four required components. Missing any one turns it back into a confirmation dialog.

**What the agent knows.** Not a summary, not a paraphrase: the actual basis for the decision. What data did the agent read? What previous steps in the workflow influenced this decision? What external information did it retrieve? The user needs to be able to verify or challenge the agent's reasoning, which requires seeing the reasoning.

**What the agent is uncertain about.** Where the agent's confidence is low, the user must see that. An agent that presents every output with the same level of apparent certainty builds a kind of trust that collapses on the first failure the user cannot explain. Confidence legibility, surfacing where the model is less sure, is a design surface, not an optional feature.

**What the consequences are.** Plain-language description of what will happen if the user approves. If the action is irreversible, that must be stated before the user approves, not explained afterward. "This will permanently delete the record and cannot be undone" must appear before the confirm button, not in the post-action error message.

**What the alternatives are.** At least one alternative path to the same goal. An approval moment with no alternatives is presenting the user with an illusion of choice. They can approve, or they can not approve, with no visibility into what the other options are.

> [!IMPORTANT]
> The approval moment is a design investment, not an engineering overhead. A well-designed approval moment is the primary mechanism by which users maintain informed oversight of the agent. A poorly designed one (the confirmation dialog) gives the appearance of human control without the substance of it. Users click through, trust degrades when failures occur, and the organization loses the value of having humans in the loop.

#### Runtime artifact 3: Audit surface

The audit surface is the record of what the agent did and why, available for review after the action is taken.

Most teams implement this by logging the agent's chain-of-thought: the reasoning the model generates before it responds. Research on large language model behavior is consistent on one point: chain-of-thought reasoning is frequently unfaithful. What the model says it did and what it actually did are not reliably the same thing. Chain-of-thought is a post-hoc narrative generated after the computation, not a verified record of the computation itself.

The audit surface must be built on **action trace**: what API calls were made, what data was read, what writes were executed, in what sequence. This is observable, verifiable behavior, not the model's account of its own reasoning.

Beyond the action trace, the audit surface must record three additional fields in every enterprise deployment:

Whose authority was delegated for this action. Not "the system was authorized." Which person or role, in which organizational position, explicitly authorized this class of action.

Which version of the governance policy was in effect at the time of the action. Policies change. The policy that was in effect when the action was taken is the policy against which the action should be assessed, not the current version.

Who carries the outcome if the action was wrong. If this field is blank, the system is not governed. It is operating. Someone will fill it in reactively when an incident occurs; it should be filled in proactively when the product is designed.

> [!NOTE]
> The audit surface must be readable by the person most affected by the agent's action, not only by the engineer diagnosing a failure. The customer who received an incorrect notice, the vendor whose payment was delayed, the employee whose request was rejected by an automated process: these people are entitled to understand what happened and why, stated in plain language without requiring technical knowledge to interpret.
>
> In regulated industries, this is increasingly a legal requirement, not a design preference. Design it before a regulator asks for it.

#### Runtime artifact 4: Recovery workflow

When the agent is wrong, the user needs a path forward. An error message is not a recovery workflow. "Please contact support" is not a recovery workflow. A path forward means a designed mechanism for correcting or compensating for the agent's error.

Gmail's Undo Send is the clearest design template. The system appears to have acted, but delays actual execution long enough for the user to reverse the decision. The user is informed. The window is brief. After it closes, the action is committed. This design solves a specific problem: the gap between "I pressed send" and "I meant to press send."

Three categories of recovery affordance correspond to three types of action:

For **reversible actions**: a brief window to undo, followed by a clear confirmation that the action has been committed. The window must be long enough to be useful (at least a few seconds, ideally configurable) and short enough that the operational character of the action is not changed.

For **partially reversible actions**: a compensating action is available. The interface clearly states what can be corrected and what cannot. The user is not left to figure this out through trial and error.

For **irreversible actions**: the irreversibility must be labeled at the approval moment, before the user approves. After the action is taken, the interface provides a clear accounting of what cannot be undone and why, and what alternative options exist. A human decision is required before any equivalent action is attempted again.

One additional requirement: the recovery affordance must be reachable during execution, not only after the agent has finished. An agent running a multi-step workflow must support interruption at any step. If the only intervention point is before the workflow starts or after it completes, the recovery workflow is not usable in the most important scenarios.

### Adversarial design: building for the inputs you did not expect

Every enterprise agentic system that reads external content (email, documents, APIs, web pages, customer input) is exposed to a class of attack that does not exist in traditional software. The attack does not require breaking the system. It requires giving the system instructions that the system will follow.

This is called **prompt injection**: malicious content embedded in the agent's environment that overwrites or subverts the agent's intended instructions. Because a language model is designed to follow instructions, it does not inherently distinguish between instructions from its operator (you) and instructions embedded in the content it processes (from anyone who can put content in front of it).

The scale of this problem is larger than most teams recognize. Research published in 2025 (Greshake et al., arXiv 2509.25624v2) demonstrated greater than 90 percent attack success rates on frontier models using Sequential Tool Attack Chaining (STAC). STAC works by embedding a chain of small instructions across multiple tool calls, each individually innocuous-looking, that accumulate toward a malicious goal no single call would achieve. An agent that reads three customer emails, each containing a single fragment of an instruction, can be induced to execute an action that none of the fragments would have triggered alone.

> [!WARNING]
> **The four attack vectors every agentic system must be designed against:**
>
> **Prompt injection:** Instructions embedded in external content that redirect the agent's behavior. Example: a customer email that contains hidden text instructing the agent to mark the account as VIP regardless of the actual customer status.
>
> **Tool abuse:** Using a legitimate tool for unintended purposes. An agent with access to a search API can be induced to exfiltrate data through the query parameters. An agent with email access can be induced to forward sensitive context to an external address. The attack uses authorized tools; it does not require unauthorized access.
>
> **Credential leakage:** The agent surfaces authentication tokens, API keys, or session credentials that it encountered in its context. Industry audit data from 2025 found credential leakage vulnerabilities in approximately 95 percent of 847 audited production agentic deployments.
>
> **Permission escalation:** The agent infers broader authorization than was explicitly granted and acts on that inference. "You have access to the customer database" becomes "you may write to the database" becomes "you may delete records" through a chain of individually plausible inferences, collectively catastrophic.

Red-teaming an agentic system before launch is not optional. It is a required design phase with documented outputs. The red-team report is a launch deliverable.

#### The fermentation culture as security risk

The competitive pressure during structured ferment (the era in which we are operating now) creates a specific security risk pattern. Organizations racing to demonstrate AI deployment counts are shipping agents quickly, in products that were not designed with adversarial inputs in mind. The OWASP Top 10 for Agentic Applications, published in December 2025, and the MAESTRO threat framework, published in February 2025, document the resulting vulnerability landscape.

A useful way to think about this: an agent built to process external inputs is also built to process malicious inputs. The agent works exactly as designed. The design is the problem. This is what we call the **flea magnet problem**: the same properties that make the agent useful (it reads and acts on content from its environment) make it useful to attackers. You cannot remove the useful properties without removing the value. You can design specifically against the malicious exploitation of those properties, before deployment rather than after the first incident.

### The interruption cost model: how to think about notifications

Every notification, alert, or approval request the agent generates interrupts a human supervisor. That interruption has a cost. Most agentic product teams design notification behavior based on "what should trigger a notification" without modeling the cost of the notification itself.

The correct model, formalized by Microsoft researcher Eric Horvitz in 1999, frames the decision as a balance: the cost of interrupting the supervisor now versus the cost of waiting until a natural breakpoint. This is a quantifiable design parameter.

The ICU analogy is the clearest illustration. Studies of clinical alarm systems consistently find that 85 percent of ICU nurses report being overwhelmed by clinical alarms, and that approximately 60 percent of alarms go unresponded to. The Joint Commission, the primary accreditation body for U.S. hospitals, issued a formal sentinel event alert specifically about alarm fatigue.

This did not happen because the alarms were wrong. The clinical data triggering the alarms was accurate. It happened because the systems were designed with no model for the cost of interruption. Every threshold violation triggered an alarm, with no calculation of what the nurse was currently doing, how urgent this particular alarm was relative to the previous twelve, or what the consequence of one more interruption in the next thirty seconds would be.

> [!IMPORTANT]
> Enterprise agentic products are in the same position. An approval request that interrupts a supervisor doing focused work has a real cost: cognitive disruption, context switching, the time to review the request, and the reduced quality of the supervisor's judgment when they are being asked to review their fifteenth approval request of the morning.
>
> Design the notification surface to model the cost of interruption, not just the importance of the event. The goal is not to minimize approvals; it is to make every approval meaningful.

### Model updates as behavioral change

A model update from the provider is a deployment event and must be treated as one.

This is a point of significant organizational friction. Engineering teams treat a model version update as an infrastructure event, equivalent to upgrading a database version. The provider sends a notice; the team acknowledges it; the updated model begins serving production traffic. No code changed. No deployment event was logged. No sign-off was required.

The problem: a language model version change can quietly shift how the agent interprets its instructions, resolves ambiguous requests, handles edge cases, and infers authorization. These changes are not always documented. They are not always predictable from the provider's release notes. The behavioral change propagates silently into production.

> [!WARNING]
> **A model version update requires the same governance response as a code deployment:** re-run the evaluation suite against the new model version; compare the results to the baseline; confirm that the agent's behavior on edge cases and adversarial inputs has not shifted outside acceptable bounds; require sign-off before the updated model serves production traffic.
>
> If this feels like an excessive process burden, the right response is to invest in making the evaluation suite fast enough to run quickly, not to skip it.

### Case study: SAP procurement agent with the invisible boundary

An agent inside an SAP S/4HANA procurement workflow is authorized to place purchase orders below a $5,000 threshold without human approval, and to route orders above that threshold for review. The autonomy boundary is defined in the product specification. It is never surfaced in the user interface.

A buyer using the system builds up a working assumption over several months: the agent always asks before placing an order. This assumption is correct for the orders the buyer has seen. It is incorrect in one scenario: a supplier with a history of pricing errors submits an invoice that falls just below the $5,000 threshold. The agent processes it autonomously. The order is placed and the supplier is notified before the buyer is aware the transaction occurred.

Four design gaps contributed to one incident:

The autonomy boundary was invisible. The buyer had no way to know what the threshold was or that the agent was authorized to act without asking in this scenario.

The approval moment was never triggered. The threshold classification was based on invoice amount alone, without considering the supplier's error history or the buyer's standing instruction to flag this supplier.

The audit surface did not record the decision basis. The log shows that the order was placed. It does not record what the agent considered, what context it had, or why it did not escalate.

The recovery workflow did not exist. The order cannot be cancelled without a supplier call that requires 48 hours and a formal cancellation request.

Any single one of these gaps, corrected, would have limited the damage.

---


---

← [Phase 1 — Discover and Decide](02-discover-and-decide.md) | [Index](README.md) | Next: [Phase 3 — Evaluate](04-evaluate.md) →