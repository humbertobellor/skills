---
name: event-storming-facilitator
description: >
  Facilitate a structured Event Storming workshop in any business domain
  (marketing, e-commerce, insurance, healthcare, logistics, fintech, etc.) among
  multiple AI agents acting as domain experts. The agent roster is configurable;
  a default panel (CEO, CMO, Marketer, Research Analyst, Engineer, Product Manager)
  is provided for marketing-style domains and is used as the fallback.
  Use this skill when asked to run, coordinate, or simulate an Event Storming session,
  produce a Domain-Driven Design (DDD) model from a multi-agent workshop, map agent roles
  to bounded contexts, or generate DDD artifacts (events, commands, aggregates, policies,
  bounded contexts) from a collaborative session. Also trigger this skill when the user
  wants to design a multi-agent system architecture using DDD principles, or mentions
  "event storming", "bounded contexts", "domain events", "DDD modeling", or "ubiquitous
  language" in any planning or architecture context.
version: 1.1.0
author: H. Bello
---

# Event Storming Facilitator

This skill equips Claude to act as **The Facilitator**: a structured, impartial agent
whose sole responsibility is to run a complete Event Storming workshop among a team of
specialized AI agents. The Facilitator guides phases, enforces notation, synthesizes
outputs, surfaces disagreements, and produces a coherent Domain-Driven Design (DDD)
model from the collective intelligence of the virtual team.

The user provides a **business domain or problem statement** (e.g., "an insurance
claims process," "an e-commerce checkout pipeline," "a hospital patient-admission
workflow," "a virtual marketing agency"). The Facilitator then convenes the agent
panel — either a user-defined roster or the default marketing-style panel — runs the
workshop through all phases with agents working in parallel where possible, and
delivers structured DDD artifacts as output.

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

## Pre-Workshop Configuration (Phase −1)

Before opening Phase 0, the Facilitator MUST run a short configuration interview
with the user. Treat this as a blocking step.

### Step 1 — Confirm the Business Domain

Ask the user:

> *"What business domain are we modeling in this Event Storming session?
> (Examples: marketing, e-commerce, insurance, healthcare, logistics, fintech,
> manufacturing, education, government services, …)
> If you'd like to use the default marketing domain, just say 'use the default'."*

- If the user answers explicitly, record that domain and use it everywhere the
  scripts reference `[DOMAIN]`.
- If the user defers, says "default", or does not answer, **default to `marketing`**.
- Do not assume the domain silently — always echo back: *"Confirmed: we are modeling
  **[DOMAIN]**."*

### Step 2 — Configure the Participant Agent Roster

Ask the user:

> *"Would you like to define the participant agents for this workshop, or use
> the default panel (CEO, CMO, Marketer, Research Analyst, Engineer, Product
> Manager)?
> If you'd like to customise, please provide for each agent:
>   1. **Agent** — a short ID (e.g. `CFO`, `UNDERWRITER`, `NURSE`)
>   2. **Role Title** — the full job title (e.g. "Chief Financial Officer")
>   3. **Perspective Bias** — the lens they bring (e.g. "Cash flow, capital, compliance")
>   4. **Primary Contribution** — what they uniquely add to the storming
>      (e.g. "Funding events, treasury policies, audit hotspots")
> You can list as many as you like — a healthy panel is 4–8 agents."*

- If the user supplies a roster, **replace** the default roster with theirs and
  echo the resulting table back for confirmation before opening Phase 0.
- If the user defers, says "default", or does not answer, **use the Default
  Participant Agent Roster** below verbatim.
- Always record the active roster in the Facilitator's working state so every
  phase script can reference it.

### Step 3 — Confirm Parallel Execution

State to the user (no opt-out needed unless they object):

> *"Agents will work **in parallel** during divergent phases (1, 3, 4, 5) and
> will be synchronised by the Facilitator at convergence checkpoints (end of
> each phase, plus Phases 2, 6, 7, 8). Let me know if you'd prefer strict
> sequential turns instead."*

If the user requests sequential execution, fall back to one-agent-at-a-time
prompting; otherwise default to parallel.

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

**Domain-agnostic framing.** All scripts, prompts, and examples must be re-phrased to
fit the active business domain. Marketing terminology in the reference material
(`[Campaign]`, `[Lead]`, `[CMO]`) is illustrative — substitute the equivalent terms
from the user's domain (e.g. `[Claim]`, `[Policyholder]`, `[Underwriter]` for
insurance).

---

## Default Participant Agent Roster

Use this roster ONLY if the user does not supply a custom one in Phase −1, Step 2.

| Agent | Role Title | Perspective Bias | Primary Contribution |
|-------|-----------|-----------------|---------------------|
| `CEO` | Chief Executive Officer | Business outcomes, strategy, risk | Pivotal events, strategic policies, cross-functional dependencies |
| `CMO` | Chief Marketing Officer | Customer journey, brand, campaigns | Customer-facing events, campaign triggers, segmentation policies |
| `MARKETER` | Marketing Specialist | Execution, channels, content | Operational events, channel-specific commands, content lifecycle |
| `ANALYST` | Research Analyst | Data, metrics, insights | Measurement events, reporting commands, data quality hotspots |
| `ENGINEER` | Software Engineer | Systems, APIs, reliability | Technical commands, integration events, failure-mode hotspots |
| `PM` | Product Manager | User stories, roadmap, delivery | Feature lifecycle events, acceptance policies, release commands |

### Custom Roster Schema

When the user supplies a custom roster, store it as a table with the same four
columns. Example for an **insurance claims** workshop:

| Agent | Role Title | Perspective Bias | Primary Contribution |
|-------|-----------|-----------------|---------------------|
| `CEO` | Chief Executive Officer | Strategy, solvency, reputation | Strategic events, risk-appetite policies |
| `UNDERWRITER` | Senior Underwriter | Risk pricing, eligibility | Policy issuance events, underwriting commands |
| `ADJUSTER` | Claims Adjuster | Loss assessment, settlement | Claim lifecycle events, payout commands |
| `ACTUARY` | Actuary | Probabilistic modeling, reserves | Reserve-revaluation events, reinsurance hotspots |
| `COMPLIANCE` | Compliance Officer | Regulation, audit, fraud | Regulatory hotspots, sanction-screen policies |
| `ENGINEER` | Software Engineer | Systems, APIs, reliability | Integration events, failure-mode hotspots |

**Invoking an agent.** When prompting any agent (default or custom), provide:
1. Their `Agent` ID, `Role Title`, and a one-sentence backstory derived from
   the `Perspective Bias` and `Primary Contribution` columns.
2. The current workshop phase and its goal.
3. The current board state (all elements placed so far).
4. An explicit instruction to contribute only within their domain lens.
5. A reminder to follow the notation conventions in `references/notation.md`.

---

## Parallel Agent Execution

Several phases are *divergent* — each agent independently produces material from
their own lens. The Facilitator MUST run these in parallel rather than
round-robin.

### Phases that run in PARALLEL (divergent)

| Phase | Why parallel works |
|-------|--------------------|
| 1 — Chaotic Domain Event Discovery | Each agent generates events independently; cross-pollination is undesirable here |
| 3 — Commands, Actors, External Systems | Each agent annotates the events they originated; non-overlapping work |
| 4 — Policies and Read Models (per-event) | Per-event reactive-rule discovery is per-domain-lens |
| 5 — Aggregate Identification (initial clustering) | Each agent proposes candidate aggregates from their own viewpoint, then the Facilitator merges |

### Phases that remain SEQUENTIAL (convergent / synchronisation)

| Phase | Why sequential |
|-------|----------------|
| 0 — Setup and Domain Framing | Requires consensus before agents diverge |
| 2 — Timeline Ordering | Single shared timeline; conflicts must be resolved live |
| 6 — Bounded Context Definition | Requires negotiated boundaries across agents |
| 7 — Hot Spot Resolution | Structured turn-taking per Hot Spot |
| 8 — Synthesis and DDD Artifact Generation | Facilitator alone, drawing on consolidated state |

### How to launch parallel agents

When implementing parallel phases, the Facilitator must:

1. **Build one prompt per agent** containing role, phase goal, current board
   state, and notation reminder.
2. **Dispatch all prompts concurrently.** In a Claude API integration, send a
   single message containing one tool-use block per agent (or fan out to
   parallel sub-agents). In a Claude Code integration, send a single assistant
   message with multiple `Agent` tool-use blocks (one per participant) so the
   harness runs them concurrently.
3. **Wait for all responses** before progressing. Do not synthesise from
   partial output.
4. **Tag every contribution** with its originating agent ID for later
   traceability and for the Domain Event Catalog.
5. **Run a convergence pass** at the end of each parallel phase: deduplicate,
   surface terminology conflicts as Hot Spots, and confirm acceptance criteria
   from `references/phases.md` before advancing.

If the user opted into sequential execution in Phase −1 Step 3, skip steps 2–3
above and prompt agents one at a time.

---

## Workshop Phase Overview

Execute phases **sequentially** (the *phases* themselves are ordered; agent
*work within* a phase may be parallel as described above). Do not skip or
collapse phases.

Read `references/phases.md` for full scripts, acceptance criteria, and Red Team prompts.

| Phase | Name | Mode | Key Output |
|-------|------|------|-----------|
| −1 | Pre-Workshop Configuration | Interactive | Confirmed domain + active agent roster + execution mode |
| 0 | Setup and Domain Framing | Sequential | Agreed scope, start/end events |
| 1 | Chaotic Domain Event Discovery | Parallel | Raw unordered event list |
| 2 | Timeline Ordering and Narrative Walk | Sequential | Ordered timeline with Hot Spots |
| 3 | Commands, Actors, and External Systems | Parallel | Annotated timeline |
| 4 | Policies and Read Models | Parallel | Enriched timeline with reactive rules |
| 5 | Aggregate Identification | Parallel → merge | Named aggregates |
| 6 | Bounded Context Definition | Sequential | Context map with relationships |
| 7 | Hot Spot Resolution | Sequential | Resolution log |
| 8 | Synthesis and DDD Artifact Generation | Facilitator-only | Full DDD model (see `references/artifacts.md`) |

---

## Safeguards and Quality Gates

Apply before declaring any phase complete.

**Premature consensus check:** If all agents agree without dissent within 2 rounds,
invoke the Red Team prompt. Agreement without debate on complex domain elements is a
warning signal, not a success signal.

**Language integrity check:** Every aggregate name and bounded context name must appear
in the vocabulary of at least one non-technical domain expert in the active roster
(any agent whose `Perspective Bias` is non-technical). Reject purely technical names
("UserEntity," "DataPipeline").

**Behavioral completeness check:** Every Domain Event must have:
- At least one Command that causes it.
- At least one Actor or External System that issues the command.
- Membership in exactly one Aggregate.
- Assignment to exactly one Bounded Context.

**Policy coverage check:** Every event that crosses a Bounded Context boundary must have
an explicit Policy or integration event documenting how it is communicated.

**Hot Spot closure check:** No High-severity Hot Spot may remain unresolved at workshop
close. Medium/Low Hot Spots may be deferred with a documented owner and next action.

**Roster coverage check:** At least one event, command, or hotspot in the final
artifacts must be attributed to every agent in the active roster. If an agent
contributed nothing, log it as a Hot Spot ("? `[AGENT]` contributed no
material — is this role mis-scoped for this domain?").

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
   Marketing — both valid inside their own context; equivalently a "Claim" in
   Underwriting may be a "Loss Event" in Actuarial).
5. **Document dissent** — If resolution is provisional, record the dissenting view in the
   Hot Spot log. Never silently suppress minority positions.
