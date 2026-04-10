# Phase 8 Artifacts — Output Templates and Reference Run

Produce all six artifacts below at the close of Phase 8. Use the reference run section
as a guide for expected depth and format.

---

## Artifact Templates

### 1. Domain Event Catalog

| Event Name | Aggregate | Bounded Context | Triggering Actor |
|------------|-----------|-----------------|-----------------|
| Campaign Published | Campaign | Campaign Execution Context | Marketing Specialist |
| Lead Scored | Lead | Lead Intelligence Context | System (ML Model) |
| … | … | … | … |

---

### 2. Command Catalog

| Command Name | Issues Event | Aggregate | Actor |
|-------------|-------------|-----------|-------|
| Publish Campaign | Campaign Published | Campaign | Marketing Specialist |
| Score Lead | Lead Scored | Lead | System |
| … | … | … | … |

---

### 3. Aggregate Definitions

For each aggregate, produce a structured block:

```
Aggregate: [Name]
Bounded Context: [Context Name]
Commands received: [list]
Events produced: [list]
Invariants:
  - [Business rule that the aggregate enforces]
  - [Additional invariant if applicable]
```

---

### 4. Policy Register

| Policy | Source Context | Target Context |
|--------|---------------|---------------|
| Whenever Lead Scored, then Notify Sales | Lead Intelligence | Sales Engagement |
| Whenever Campaign Published, then Trigger Analytics Sync | Campaign Execution | Reporting & Analytics |
| … | … | … |

---

### 5. Bounded Context Map

Produce a textual diagram of all Bounded Contexts and their directional relationships:

```
[Context A]
  ├── DOWNSTREAM → [Context B]  (Published Language: EventName)
  ├── DOWNSTREAM → [Context C]  (Customer-Supplier)
  └── SHARED KERNEL → [Context D]

[Context B]
  ├── DOWNSTREAM → [Context E]  (Published Language: EventName)
  └── UPSTREAM ← [Context A]
```

---

### 6. Open Assumptions and Deferred Hot Spots

| Hot Spot | Type | Severity | Disposition | Owner Agent | Next Action |
|----------|------|----------|-------------|-------------|-------------|
| ? Who owns lead scoring thresholds? | Question | High | Deferred | ANALYST | Schedule stakeholder review |
| … | … | … | … | … | … |

---

## Reference Run: Virtual Marketing Agency Workshop

**Domain:** Digital marketing campaign lifecycle, from brief to performance reporting.
**Scope:** End-to-end — Campaign Brief Submitted → Campaign Performance Reported.

---

### Sample Phase 1 Output (selected events by agent)

```
CEO:       "Annual Budget Approved", "Strategic Direction Set", "Agency Pitch Won"
CMO:       "Campaign Brief Submitted", "Creative Direction Approved", "Campaign Launched"
MARKETER:  "Audience Segment Defined", "Ad Copy Drafted", "Campaign Published", "A/B Test Started"
ANALYST:   "Performance Data Collected", "Anomaly Detected", "Weekly Report Generated"
ENGINEER:  "Ad Platform Connected", "Tracking Pixel Deployed", "API Rate Limit Hit"
PM:        "Campaign Brief Accepted", "Campaign Milestone Reached", "Campaign Closed"
```

---

### Sample Aggregate Identification (Phase 5)

```
Aggregate: Campaign
  Events:   Campaign Brief Submitted, Campaign Brief Accepted, Campaign Launched,
            Campaign Published, Campaign Milestone Reached, Campaign Closed
  Commands: Submit Campaign Brief, Accept Brief, Launch Campaign, Publish Campaign,
            Close Campaign

Aggregate: Audience Segment
  Events:   Audience Segment Defined, Audience Segment Refined, Segment Archived
  Commands: Define Audience Segment, Refine Segment, Archive Segment

Aggregate: Performance Report
  Events:   Performance Data Collected, Anomaly Detected, Weekly Report Generated
  Commands: Collect Performance Data, Generate Weekly Report, Flag Anomaly
```

---

### Sample Bounded Context Map (Phase 6)

```
[Campaign Management Context]         — CEO, CMO, PM lens
  Aggregates: Campaign, Brief

[Campaign Execution Context]          — MARKETER, ENGINEER lens
  Aggregates: Audience Segment, Ad Creative, Ad Platform Connection

[Analytics & Reporting Context]       — ANALYST lens
  Aggregates: Performance Report, Anomaly, Attribution Model

Relationships:
  Campaign Management → Campaign Execution   (Customer-Supplier)
  Campaign Execution  → Analytics & Reporting (Published Language: CampaignPublishedEvent)
```
