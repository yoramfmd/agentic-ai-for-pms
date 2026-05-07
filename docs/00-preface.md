# Preface: Why This Framework Exists Now

← [Back to index](README.md) | Next: [Phase 0 — AI Literacy](01-ai-literacy.md) →

---

If you have been a product manager for more than five years, you have a system that works. You know how to write a user story. You know how to run a sprint. You know how to prioritize a backlog, run a discovery session, and translate customer feedback into acceptance criteria. You have built features, shipped them, measured them, and iterated. You are good at this.

The problem is that most of what you know was built on one assumption that nobody ever stated explicitly: the software you build does what you tell it to do, every time, in the same way. A billing rule fires when the condition is met. A notification triggers when the threshold is crossed. An API call returns the expected response. The software follows the instructions you wrote, and if it does not, you call it a bug.

Agentic AI breaks that assumption. Not at the margins. At the foundation.

When you put a language model at the center of a product, you are no longer building a system that follows instructions. You are building a system that interprets instructions, makes judgments about what they mean, and takes action based on those judgments. The same input can produce a different output on a different run. The system can be right 95 percent of the time and wrong in ways that compound, rather than fail cleanly. And when it fails, it usually does not announce that it has failed. It produces a confident, fluent, wrong answer and keeps going.

Every PM framework you currently use, including user story templates, acceptance criteria, definition of done, backlog grooming, sprint velocity, was designed for a different kind of system. Not a worse system. A different one. Applying those frameworks unchanged to agentic AI is roughly equivalent to using a road map to navigate at sea. The tool is not broken; it is the wrong tool.

This wiki is the framework built for the system you are actually building now.

---

## The market is moving faster than training programs

There is a structural problem in how organizations are deploying AI. The technology is advancing faster than the skills of the people asked to govern it. A PM who completed a product management certification three years ago learned nothing about probabilistic systems, nothing about token economics, nothing about what it means to design a recovery workflow for an agent that took an irreversible action.

This is not a criticism of the training programs. They could not have anticipated the pace of change. The point is that the gap between what most PMs currently know and what they need to know to ship agentic AI responsibly is real, it is growing, and no one is going to close it through a quarterly training seminar.

!!! note
    **Era of ferment.** Technology markets go through a period called the "era of ferment" before a dominant design emerges and the market consolidates. During ferment, many competing architectures coexist, entry rates are high, and competitive asymmetries form beneath the noise. The term comes from MIT economists Utterback and Abernathy; Harvard Business School professor Karim Lakhani has applied it specifically to the current AI market, calling it "structured ferment."

    The comparison charts that rank AI models by speed and reasoning score tell you what week it is. They do not tell you where the stable ground is. The stable ground is not the model. It is the data, the context architecture, the workflow integration, and the organizational trust that governance structures make possible. Those are product decisions. They are your decisions.


There is also a pricing dynamic worth naming. AI inference costs have fallen roughly a thousand times in three years. But total enterprise AI spending has risen sharply in the same period. The unit gets cheaper; the units deployed multiply.

!!! note
    **LLMflation.** The condition in which AI inference unit costs fall while total AI spending rises, because cost reduction creates applications that were not economical at prior price points. The market expands faster than prices contract. This matters for your business cases, which are covered in [Phase 1](02-discover-and-decide.md).


---

## The questions your old frameworks cannot answer

Traditional PM work is built around questions that assume a human user interacting with a system:

- What does the user want?
- What does the user need?
- What is the user journey from awareness to value?
- What job is the user trying to get done?

These are good questions. They stop being sufficient when the "user" is another agent, an automated trigger, or a system acting on behalf of someone who is not watching.

Agentic AI requires a different set of questions that most PM frameworks have no slot for:

**What is the user journey when there is no real user?** When the agent is acting autonomously on someone's behalf, the journey is a chain of decisions, each producing context that must travel forward to the next step. The journey map is not a funnel; it is a decision tree in which every branch has consequences.

**How do you define jobs-to-be-done when the agent selects its own tools?** A traditional job-to-be-done assumes a human who has a goal and chooses a means. An agent with tool access does not just complete a job; it decides which tools to use, which sequence to attempt, and what to do when the first attempt fails. The scope of the job expands in ways the original specification did not anticipate.

**How do you write acceptance criteria for a system that gives different answers to the same question?** Your current definition of done says the feature does X when Y. For a probabilistic system, done means the feature does X at an acceptable rate across K runs of Y, with a known distribution of failure modes. That is a different kind of specification.

**Who carries the outcome when the agent was wrong?** In traditional software, the developer wrote the rule and the user broke it. In agentic AI, the agent inferred the rule, the platform authorized the action, and the user never saw the decision until after it was executed. Accountability cannot be assumed; it must be designed in.

This framework answers all four questions, for every phase of the product lifecycle.

---

## How this framework is organized

The six phases of the Agentic PM Lifecycle follow the actual sequence of product work, from the initial decision about whether to build at all through to operating and eventually retiring a live system.

Each section follows the same structure: the core concepts a PM needs to understand, the frameworks that make those concepts actionable, the specific deliverables the PM is responsible for, and a case study that shows the concepts working in practice. The case studies alternate between healthcare examples (where the consequences of agentic failure are concrete and well-documented) and enterprise software examples closer to the SAP context most readers work in.

The phases are sequential in the lifecycle but not in practice. Design decisions made in Phase 2 determine what can be measured in Phase 4. If the measurement is missing in Phase 4, the design gap is almost always traceable to Phase 2. Read the full framework before applying any part of it.

---

← [Back to index](README.md) | Next: [Phase 0 — AI Literacy](01-ai-literacy.md) →
