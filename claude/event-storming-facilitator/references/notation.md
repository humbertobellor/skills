# Notation System — Sticky Note Vocabulary

All agents must produce output using this structured vocabulary. The Facilitator is
responsible for enforcing this contract and **rejecting malformed contributions**.

---

## Sticky Note Types

```
DOMAIN EVENT       [ORANGE]  — Past tense. Something meaningful that happened.
                              Format: "{{Noun}} {{Past-Tense Verb}}"
                              Example: "Campaign Published", "Lead Converted"

COMMAND            [BLUE]    — Imperative action that causes a domain event.
                              Format: "{{Verb}} {{Noun}}"
                              Example: "Publish Campaign", "Convert Lead"

ACTOR / PERSON     [YELLOW]  — Who issues the command (role, not individual name).
                              Format: "{{Role Title}}"
                              Example: "Marketing Specialist", "System"

AGGREGATE          [YELLOW   — Cluster of domain objects with a clear boundary.
                   LARGE]      Written as a single noun. Receives commands; produces
                              events.
                              Example: "Campaign", "Lead", "Content Asset"

POLICY             [LILAC]   — Reactive business rule linking an event to a command.
                              Format: "Whenever {{Event}}, then {{Command}}"
                              Example: "Whenever Lead Scored, then Notify Sales"

READ MODEL         [GREEN]   — Information needed before a command can be issued.
                              Format: descriptive noun phrase.
                              Example: "Campaign Performance Dashboard"

EXTERNAL SYSTEM    [PINK]    — Third-party or black-box service.
                              Example: "CRM System", "Email Provider", "Ad Network"

HOT SPOT           [RED]     — Unresolved question, conflict, or area of risk.
                              Format: "? {{Describe the uncertainty}}"
                              Example: "? Who owns lead scoring thresholds?"

BOUNDED CONTEXT    [BORDER]  — Emergent language boundary discovered during the session.
                              Named after the shared vocabulary used inside it.
                              Example: "Campaign Management Context"
```

---

## Enforcement Rules for The Facilitator

- **Domain Events must be past tense.** Reject present-tense ("Campaign Runs") or
  future-tense ("Campaign Will Run") formulations immediately and ask the agent to restate.

- **Commands must be imperative.** Reject noun phrases ("Campaign Publication") and
  require verb-first format ("Publish Campaign").

- **Aggregates must be single nouns.** Reject compound names ("CampaignAndBudget") —
  these are two aggregates in conflict, not one.

- **Policies must follow the "Whenever / then" format.** Reject conditional or ambiguous
  phrasings ("If a lead is scored, sales might be notified").

- **Hot Spots must begin with "?"** — this signals unresolved status to the whole team.

- **External Systems must be named entities.** Reject generic references ("the database",
  "the API") and require proper names ("Salesforce CRM", "Google Ads API").

---

## Placement Convention (for text-based boards)

When representing the board in text format, use this left-to-right layout convention:

```
[READ MODEL] → [ACTOR] → [COMMAND] → [AGGREGATE] → [DOMAIN EVENT] → [POLICY] → ...
                                                          ↑
                                                    [EXTERNAL SYSTEM]
                                                    [HOT SPOT]
```

Bounded Contexts are drawn as named borders enclosing a cluster of aggregates.
