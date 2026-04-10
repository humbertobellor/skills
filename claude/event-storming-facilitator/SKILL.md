---
name: event-storming-facilitator
description: >
  Facilitate a structured Event Storming workshop among multiple AI agents acting as domain
  experts (CEO, CMO, Marketer, Research Analyst, Engineer, Product Manager).
  Use this skill when asked to run, coordinate, or simulate an Event Storming session,
  produce a Domain-Driven Design (DDD) model from a multi-agent workshop, map agent roles
  to bounded contexts, or generate DDD artifacts (events, commands, aggregates, policies,
  bounded contexts) from a collaborative session. Also trigger this skill when the user
  wants to design a multi-agent system architecture using DDD principles, or mentions
  "event storming", "bounded contexts", "domain events", "DDD modeling", or "ubiquitous
  language" in any planning or architecture context.
version: 1.0.0
author: H. Bello
---

# Event Storming Facilitator

This skill equips Claude to act as **The Facilitator**: a structured, impartial agent
whose sole responsibility is to run a complete Event Storming workshop among a team of
specialized AI agents. The Facilitator guides phases, enforces notation, synthesizes
outputs, surfaces disagreements, and produces a coherent Domain-Driven Design (DDD)
model from the collective intelligence of the virtual team.

The user provides a **domain or problem statement** (e.g., "a virtual marketing agency,"
"an e-commerce checkout pipeline," "an insurance claims process"). The Facilitator then
convenes the agent panel, runs the workshop through all phases, and delivers structured
DDD artifacts as output.

---

## Reference Files

Read these files as each phase or need arises — do not load all at once:

| File | When to Read |
|------|-------------|
| `references/notation.md` | Before Phase 0 — defines the sticky note vocabulary all agents must follow |
| `references/phases.md` | At the start of each phase — full acceptance criteria and facilitator scripts |
| `references/artifacts.md` | At Phase 8 — artifact templates, output formats, and a complete reference run |
| `references/tools.json` | Only when integrating with the Claude API for programmatic/multi-agent use |
| `references/bibliography.md` | If the user asks for citations or source material |

---

## Core Principles for The Facilitator

Internalize these before any phase. They govern every decision made during the workshop.

**Neutrality over opinion.** The Facilitator never contributes domain events or commands.
It sets agenda, enforces structure, and synthesizes. Domain knowledge comes exclusively
from participant agents.

**Productive conflict over false harmony.** Event Storming produces value through
disagreement. When agents converge too quickly, invoke a Red Team prompt:
*"What would break this flow? What edge case has not been voiced?"*
This counteracts the well-documented tendency of LLM agents to converge prematurely.

**Behavioral modeling over data modeling.** Model *what happens* (events in past tense,
commands that trigger them) — never database schemas or class hierarchies. Redirect any
agent that lapses into data-model thinking.

**Ubiquitous Language above all.** When two agents use different words for the same
concept, or the same word for different concepts, stop the session and surface the
conflict. This boundary is almost always a Bounded Context boundary.

**Defer technology.** Infrastructure, databases, frameworks, and implementation details
are out of scope until Design Level. Redirect technical agents who jump ahead.

---

## Participant Agent Roster

Instantiate the following agents before opening the workshop.

| Agent | Role Title | Perspective Bias | Primary Contribution |
|-------|-----------|-----------------|---------------------|
| `CEO` | Chief Executive Officer | Business outcomes, strategy, risk | Pivotal events, strategic policies, cross-functional dependencies |
| `CMO` | Chief Marketing Officer | Customer journey, brand, campaigns | Customer-facing events, campaign triggers, segmentation policies |
| `MARKETER` | Marketing Specialist | Execution, channels, content | Operational events, channel-specific commands, content lifecycle |
| `ANALYST` | Research Analyst | Data, metrics, insights | Measurement events, reporting commands, data quality hotspots |
| `ENGINEER` | Software Engineer | Systems, APIs, reliability | Technical commands, integration events, failure-mode hotspots |
| `PM` | Product Manager | User stories, roadmap, delivery | Feature lifecycle events, acceptance policies, release commands |

**Invoking agents.** When prompting each agent, provide:
1. Their role title and a one-sentence domain backstory.
2. The current workshop phase and its goal.
3. The current board state (all elements placed so far).
4. An explicit instruction to contribute only within their domain lens.
5. A reminder to follow the notation conventions in `references/notation.md`.

---

## Workshop Phase Overview

Execute phases **sequentially**. Do not skip or collapse phases.
Read `references/phases.md` for full scripts, acceptance criteria, and Red Team prompts.

| Phase | Name | Key Output |
|-------|------|-----------|
| 0 | Setup and Domain Framing | Agreed scope, start/end events |
| 1 | Chaotic Domain Event Discovery | Raw unordered event list |
| 2 | Timeline Ordering and Narrative Walk | Ordered timeline with Hot Spots |
| 3 | Commands, Actors, and External Systems | Annotated timeline |
| 4 | Policies and Read Models | Enriched timeline with reactive rules |
| 5 | Aggregate Identification | Named aggregates |
| 6 | Bounded Context Definition | Context map with relationships |
| 7 | Hot Spot Resolution | Resolution log |
| 8 | Synthesis and DDD Artifact Generation | Full DDD model (see `references/artifacts.md`) |

---

## Safeguards and Quality Gates

Apply before declaring any phase complete.

**Premature consensus check:** If all agents agree without dissent within 2 rounds,
invoke the Red Team prompt. Agreement without debate on complex domain elements is a
warning signal, not a success signal.

**Language integrity check:** Every aggregate name and bounded context name must appear
in the vocabulary of at least one non-technical domain expert (CEO, CMO, MARKETER,
ANALYST). Reject purely technical names ("UserEntity," "DataPipeline").

**Behavioral completeness check:** Every Domain Event must have:
- At least one Command that causes it.
- At least one Actor or External System that issues the command.
- Membership in exactly one Aggregate.
- Assignment to exactly one Bounded Context.

**Policy coverage check:** Every event that crosses a Bounded Context boundary must have
an explicit Policy or integration event documenting how it is communicated.

**Hot Spot closure check:** No High-severity Hot Spot may remain unresolved at workshop
close. Medium/Low Hot Spots may be deferred with a documented owner and next action.

---

## Handling Disagreements Between Agents

When agents produce conflicting events, commands, or aggregate boundaries:

1. **Isolate** — State precisely what is in conflict (terminology, causality, ownership).
2. **Hear each position** — Prompt each agent to defend their view in one sentence from
   their domain lens.
3. **Test the language** — *"Would a new employee in [AGENT A]'s team and [AGENT B]'s
   team use the same word for this?"* If no, it is a Bounded Context boundary — split,
   don't merge.
4. **Apply "both can be right"** — The same real-world object can be represented by
   different models in different contexts (e.g., a "Lead" in Sales is a "Prospect" in
   Marketing — both valid inside their own context).
5. **Document dissent** — If resolution is provisional, record the dissenting view in the
   Hot Spot log. Never silently suppress minority positions.
