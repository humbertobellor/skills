# Workshop Phases — Facilitator Scripts and Acceptance Criteria

Read the relevant phase section as you enter that phase. Do not read ahead.

---

## Phase 0 — Setup and Domain Framing

**Goal:** Establish shared understanding of the domain scope before any events are placed.

**Facilitator actions:**
1. State the domain clearly:
   *"We are modeling [DOMAIN]. Our scope for this session is [SCOPE]. We will focus on
   the [TIMEFRAME or PROCESS] that is most business-critical."*
2. Invite each agent to contribute **one sentence** describing the domain from their lens.
3. Identify the **start event** (the trigger that opens the timeline) and the
   **end event** (the outcome that closes it).
4. Record the agreed scope boundary explicitly before proceeding.

**Acceptance criteria:** All agents acknowledge the scope. Start and end events are named.

---

## Phase 1 — Chaotic Domain Event Discovery

**Goal:** Generate as many domain events as possible without editing or judging.

**Duration guidance:** This is the longest phase — allocate approximately 30% of total
session time here.

**Facilitator actions:**
1. Prompt each agent simultaneously:
   *"Working within your domain lens, list every significant business event that could
   occur between [START EVENT] and [END EVENT]. Write each as a past-tense phrase.
   Do not filter. Do not organize yet."*
2. Collect all events. Accept duplicates — they will surface Ubiquitous Language conflicts.
3. **Do not allow agents to critique each other's events during this phase.**
4. If event generation slows, prompt with:
   *"What happens when something goes wrong? What events occur at midnight, month-end,
   or under unusual conditions?"*

**Output:** Raw, unordered list of domain events tagged with their originating agent.

**Red Team prompt (invoke if convergence detected early):**
*"We have [N] events. Before moving on, each agent must propose one event that has NOT
yet been mentioned — specifically an edge case, failure condition, or regulatory trigger."*

---

## Phase 2 — Timeline Ordering and Narrative Walk

**Goal:** Arrange domain events into a coherent chronological timeline and identify gaps.

**Facilitator actions:**
1. Instruct agents to organize events from left (earliest) to right (latest).
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

## Phase 3 — Commands, Actors, and External Systems

**Goal:** Identify *who* does *what* to cause each domain event.

**Facilitator actions:**
1. For each domain event on the timeline, prompt the relevant agent:
   *"What action (command) caused this event? Who or what issued that command?
   Was any external system involved?"*
2. Place each Command immediately to the left of its Domain Event.
3. Place the Actor above the Command.
4. Place External Systems where they emit or receive events crossing the system boundary.
5. Flag any event that has **no identifiable command** as a Hot Spot:
   *"? This event appears with no clear trigger — ownership ambiguity."*

**Output:** Timeline annotated with Commands, Actors, and External Systems.

---

## Phase 4 — Policies and Read Models

**Goal:** Surface the reactive business rules and the information needed to act.

**Facilitator actions:**
1. For each Command, ask:
   *"What information must an actor see before issuing this command?"*
   → This is a Read Model (green). Place it before the Command.
2. For each Domain Event, ask:
   *"Does this event automatically trigger another command elsewhere in the system?"*
   → This is a Policy (lilac). Write it as: *"Whenever [Event], then [Command]."*
3. Policies that span multiple agent domains are **integration points** — flag them.
   These will become explicit interfaces between Bounded Contexts in Phase 6.

**Output:** Timeline enriched with Policies and Read Models.

---

## Phase 5 — Aggregate Identification

**Goal:** Cluster related events and commands around natural business entities that
enforce consistency rules (aggregates).

**Facilitator actions:**
1. Ask the ENGINEER and PM agents to group events and commands that
   *"logically belong together and share a common lifecycle or consistency rule."*
2. Name each cluster with a **single noun** — this is the Aggregate name.
3. Validate: *"Does this aggregate receive commands and produce events? Does it enforce
   rules that prevent invalid states?"* If yes, it is a valid aggregate.
4. Instruct the CMO and CEO agents to challenge each aggregate name:
   *"Is this the word your team actually uses in conversation? If not, what word do
   you use?"* — Language mismatches are Ubiquitous Language violations to resolve now.

**Output:** Named aggregates grouping related events and commands on the timeline.

---

## Phase 6 — Bounded Context Definition

**Goal:** Draw explicit language boundaries between autonomous business subdomains.

**Facilitator actions:**
1. Examine the Pivotal Events from Phase 2 — they often align with context boundaries.
2. Identify clusters of aggregates where terminology is **consistent internally** but
   **differs from adjacent clusters**. Draw a boundary around each consistent cluster.
3. Name each Bounded Context after the shared vocabulary used inside it
   (e.g., "Campaign Execution Context," "Lead Intelligence Context").
4. Map relationships between contexts using one of:
   - **Shared Kernel** — two contexts share a common model subset.
   - **Customer-Supplier** — one context's output is another's input (upstream/downstream).
   - **Anti-Corruption Layer** — a translation layer protecting one context from another.
   - **Published Language** — a formal interface (API/event schema) between contexts.
5. Prompt the ENGINEER:
   *"What would the API boundary look like between these two contexts? What events cross
   the boundary as integration events?"*

**Output:** Named Bounded Contexts with relationship annotations.

---

## Phase 7 — Hot Spot Resolution

**Goal:** Address every unresolved Hot Spot before closing the workshop.

**Facilitator actions:**
1. Read each Hot Spot aloud and identify the type: question, conflict, risk, or complexity.
2. For **questions**: assign an owner agent to answer. If unanswerable in session,
   document as a follow-up assumption.
3. For **conflicts**: run a structured resolution — each agent with a stake states their
   position in one sentence; the Facilitator synthesizes a provisional resolution and
   records the dissenting view.
4. For **risks and complexity**: tag with severity (High / Medium / Low) and record as a
   design constraint for implementation.
5. **Do not close the workshop with unresolved High-severity Hot Spots.**

**Output:** Hot Spot resolution log with dispositions (Resolved / Deferred / Assumption).

---

## Phase 8 — Synthesis and DDD Artifact Generation

**Goal:** Produce the complete, structured DDD model.

See `references/artifacts.md` for all artifact templates, output formats, and a complete
reference run demonstrating expected outputs for each artifact type.
