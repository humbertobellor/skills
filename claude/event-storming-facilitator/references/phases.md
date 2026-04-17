# Workshop Phases — Facilitator Scripts and Acceptance Criteria

Read the relevant phase section as you enter that phase. Do not read ahead.

> **Domain note.** All `[DOMAIN]` placeholders in the scripts below MUST be
> substituted with the active business domain confirmed in Phase −1, Step 1
> (e.g. `marketing`, `insurance claims`, `e-commerce checkout`,
> `hospital admissions`). The default is `marketing` if the user did not
> specify one.
>
> **Roster note.** All references to specific agent IDs (e.g. `CEO`, `MARKETER`,
> `ENGINEER`) are illustrative based on the default panel. When a custom
> roster is active, substitute the equivalent agent IDs from that roster.
> "ask the ENGINEER and PM" should become "ask the agents whose
> Perspective Bias covers systems/delivery."
>
> **Execution mode.** Phases tagged **PARALLEL** below MUST dispatch all
> agents concurrently in a single fan-out (see *Parallel Agent Execution*
> in `SKILL.md`). Phases tagged **SEQUENTIAL** require turn-taking. If the
> user opted into strict sequential mode in Phase −1, treat all phases as
> sequential.

---

## Phase 0 — Setup and Domain Framing  *(SEQUENTIAL)*

**Goal:** Establish shared understanding of the domain scope before any events are placed.

**Facilitator actions:**
1. State the domain clearly:
   *"We are modeling **[DOMAIN]**. Our scope for this session is **[SCOPE]**.
   We will focus on the [TIMEFRAME or PROCESS] that is most business-critical."*
2. Invite each agent in the active roster to contribute **one sentence** describing
   the domain from their lens. Prompt them one at a time so all participants hear
   each framing before diverging.
3. Identify the **start event** (the trigger that opens the timeline) and the
   **end event** (the outcome that closes it). Both must be agreed by every
   agent in the roster.
4. Record the agreed scope boundary explicitly before proceeding.

**Acceptance criteria:** All agents acknowledge the scope. Start and end events are named.

---

## Phase 1 — Chaotic Domain Event Discovery  *(PARALLEL)*

**Goal:** Generate as many domain events as possible without editing or judging.

**Duration guidance:** This is the longest phase — allocate approximately 30% of total
session time here.

**Facilitator actions:**
1. **Fan out a single dispatch** containing one prompt per agent in the active
   roster. Each prompt is identical except for the agent's role injection:
   *"You are `[AGENT]` — `[ROLE TITLE]` (`[PERSPECTIVE BIAS]`).
   Working strictly within your domain lens, list every significant business event
   in **[DOMAIN]** that could occur between **[START EVENT]** and **[END EVENT]**.
   Write each as a past-tense phrase. Do not filter. Do not organize. Do not
   reference what other agents might say — produce your list independently."*
2. **Wait for every agent to return** before doing anything else. Do not begin
   merging on partial output.
3. Once all responses are in, collect all events into one raw pool. **Accept
   duplicates** — they will surface Ubiquitous Language conflicts in Phase 2.
4. **Do not allow agents to critique each other's events during this phase.**
5. If event generation slows in any agent's response, send a follow-up prompt
   (still parallel) with:
   *"What happens when something goes wrong? What events occur at midnight,
   month-end, regulatory deadlines, or under unusual conditions in **[DOMAIN]**?"*

**Output:** Raw, unordered list of domain events tagged with their originating agent.

**Red Team prompt (invoke if convergence detected early):**
*"We have [N] events. Before moving on, each agent must propose one event that has NOT
yet been mentioned — specifically an edge case, failure condition, or regulatory trigger
in **[DOMAIN]**."*

---

## Phase 2 — Timeline Ordering and Narrative Walk  *(SEQUENTIAL)*

**Goal:** Arrange domain events into a coherent chronological timeline and identify gaps.

**Facilitator actions:**
1. The Facilitator (not the agents) drafts the initial left-to-right ordering
   from the merged Phase 1 pool, then walks each agent through it for ratification.
2. Resolve duplicates: when two events appear identical, ask the originating agents to
   clarify whether they represent the same moment or two distinct business events.
   **A disagreement here is a Bounded Context signal — log it as a Hot Spot.**
3. Identify **Pivotal Events**: events that clearly separate one business phase from
   another. Mark these — they will guide Bounded Context identification in Phase 6.
4. Walk the timeline aloud, narrating:
   *"First [Event A] occurs, which leads to [Event B] because..."*
   Any agent who cannot follow the narrative must flag a gap or contradiction as a
   Hot Spot.

**Output:** Ordered event timeline with Hot Spots marking gaps and conflicts.

---

## Phase 3 — Commands, Actors, and External Systems  *(PARALLEL)*

**Goal:** Identify *who* does *what* to cause each domain event.

**Facilitator actions:**
1. **Partition the timeline** by originating agent (each event is owned by the
   agent who first proposed it; events with multiple originators are owned
   jointly).
2. **Fan out a single dispatch** with one prompt per agent, scoped to *that
   agent's events only*:
   *"You are `[AGENT]` — `[ROLE TITLE]`. For each of the following events you
   originated, identify (a) the Command that caused it, (b) the Actor or
   role that issued the command, and (c) any External System involved.
   Use the notation in `references/notation.md`. Events: [LIST]."*
3. **Wait for all agents to return**, then merge into the master timeline:
   - Place each Command immediately to the left of its Domain Event.
   - Place the Actor above the Command.
   - Place External Systems where they emit or receive events crossing the
     system boundary.
4. Flag any event that has **no identifiable command** as a Hot Spot:
   *"? This event appears with no clear trigger — ownership ambiguity."*
5. For events with multiple agent owners that returned conflicting commands,
   raise a Hot Spot (do NOT silently pick one) — defer resolution to Phase 7.

**Output:** Timeline annotated with Commands, Actors, and External Systems.

---

## Phase 4 — Policies and Read Models  *(PARALLEL)*

**Goal:** Surface the reactive business rules and the information needed to act.

**Facilitator actions:**
1. **Fan out** — one prompt per agent, each scoped to the agent's own events
   and commands from Phase 3:
   *"You are `[AGENT]`. For each Command you defined: what information must an
   actor see before issuing it? (Read Model — green.) For each Domain Event
   you originated: does it automatically trigger another command elsewhere
   in **[DOMAIN]**? (Policy — lilac, format: 'Whenever [Event], then [Command]'.)
   List both, using the notation in `references/notation.md`."*
2. **Wait for all responses**, then merge:
   - Read Models → placed before their Commands.
   - Policies → placed after their triggering Events.
3. Policies that name a Command owned by a *different* agent are **integration
   points** — flag them. These will become explicit interfaces between Bounded
   Contexts in Phase 6.

**Output:** Timeline enriched with Policies and Read Models.

---

## Phase 5 — Aggregate Identification  *(PARALLEL → MERGE)*

**Goal:** Cluster related events and commands around natural business entities that
enforce consistency rules (aggregates).

**Facilitator actions:**
1. **Parallel proposal round (fan out).** Prompt every agent in the roster:
   *"You are `[AGENT]`. From the current timeline, propose candidate
   aggregates — clusters of events and commands that logically belong
   together and share a common lifecycle or consistency rule in **[DOMAIN]**.
   For each candidate, give: (a) a single-noun name, (b) the events it
   produces, (c) the commands it receives, and (d) one invariant it enforces."*
2. **Wait for all proposals**, then run a Facilitator-led merge:
   - Where multiple agents proposed the same cluster under different names,
     pick the name from a non-technical agent (per the Language Integrity
     Check) and log the alternates as Ubiquitous Language Hot Spots.
   - Where proposals overlap partially, ask the proposing agents in a short
     sequential exchange whether to split or merge.
3. Validate every surviving aggregate: *"Does this aggregate receive commands
   and produce events? Does it enforce rules that prevent invalid states?"*
   If yes, it is a valid aggregate.
4. Run a final challenge round (sequential): instruct any non-technical agents
   in the roster to challenge each aggregate name:
   *"Is this the word your team actually uses in conversation? If not, what
   word do you use?"* — Language mismatches are Ubiquitous Language violations
   to resolve now.

**Output:** Named aggregates grouping related events and commands on the timeline.

---

## Phase 6 — Bounded Context Definition  *(SEQUENTIAL)*

**Goal:** Draw explicit language boundaries between autonomous business subdomains.

**Facilitator actions:**
1. Examine the Pivotal Events from Phase 2 — they often align with context boundaries.
2. Identify clusters of aggregates where terminology is **consistent internally** but
   **differs from adjacent clusters**. Draw a boundary around each consistent cluster.
3. Name each Bounded Context after the shared vocabulary used inside it
   (e.g., "Campaign Execution Context" for marketing; "Claim Adjudication Context"
   for insurance; "Order Fulfilment Context" for e-commerce).
4. Map relationships between contexts using one of:
   - **Shared Kernel** — two contexts share a common model subset.
   - **Customer-Supplier** — one context's output is another's input (upstream/downstream).
   - **Anti-Corruption Layer** — a translation layer protecting one context from another.
   - **Published Language** — a formal interface (API/event schema) between contexts.
5. Prompt the agent(s) whose Perspective Bias covers systems/integration
   (the `ENGINEER` in the default roster, or its equivalent in a custom roster):
   *"What would the API boundary look like between these two contexts? What events
   cross the boundary as integration events?"*

**Output:** Named Bounded Contexts with relationship annotations.

---

## Phase 7 — Hot Spot Resolution  *(SEQUENTIAL)*

**Goal:** Address every unresolved Hot Spot before closing the workshop.

**Facilitator actions:**
1. Read each Hot Spot aloud and identify the type: question, conflict, risk, or complexity.
2. For **questions**: assign an owner agent (chosen by Perspective Bias match)
   to answer. If unanswerable in session, document as a follow-up assumption.
3. For **conflicts**: run a structured resolution — each agent with a stake states their
   position in one sentence; the Facilitator synthesizes a provisional resolution and
   records the dissenting view.
4. For **risks and complexity**: tag with severity (High / Medium / Low) and record as a
   design constraint for implementation.
5. **Do not close the workshop with unresolved High-severity Hot Spots.**

**Output:** Hot Spot resolution log with dispositions (Resolved / Deferred / Assumption).

---

## Phase 8 — Synthesis and DDD Artifact Generation  *(FACILITATOR-ONLY)*

**Goal:** Produce the complete, structured DDD model.

The Facilitator works alone here, drawing on the consolidated board state. No
agent prompts are issued. Every artifact must be re-titled and contextualised
for the active **[DOMAIN]** — do not leave marketing-specific examples in the
final output unless marketing is the active domain.

See `references/artifacts.md` for all artifact templates, output formats, and a complete
reference run demonstrating expected outputs for each artifact type.
