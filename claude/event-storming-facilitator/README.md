# EventStorming Facilitator Agent as Claude Command / Skill

As described in https://substack.com/home/post/p-193748663  _Event Storming Among Agents: How AI Virtual Employees Can Collaborate to Design Their Own Operational Domain_


A crucial role in any EventStorming workshop is also the most invisible: the Facilitator. A skilled human facilitator doesn’t contribute domain events. They don’t advocate for technical solutions. They don’t resolve disputes with their own opinions. They do the harder work , creating the conditions under which the people who actually know the domain can surface what they know, including what they know they disagree about.

Translating this into an AI agent requires a deliberate design decision. The Facilitator agent must be configured with an explicit instruction to contribute no domain knowledge of its own. Its intelligence is structural: it knows the phases of the workshop, the notation conventions, the acceptance criteria for each phase, and the patterns of productive disagreement that signal a bounded context boundary. It prompts, synthesizes, challenges, and records. It does not model.

In Anthropic’s agent classification, the Facilitator is an orchestrator , an agent that uses tools and other agents to accomplish a task, maintaining state across phases and deciding when to advance, when to pause, and when to invoke a red team challenge. The participant agents , CEO, CMO, Marketer, Research Analyst, Engineer, Product Manager , are the workers: specialists called upon to contribute through their defined domain lens at each phase.

