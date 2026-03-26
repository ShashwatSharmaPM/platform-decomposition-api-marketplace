# Prioritization: Platform Decomposition + API Marketplace

> How I prioritized a 1-year infrastructure investment across 21+ services, competing feature requests, and an engineering team that was already overwhelmed.

---

## The Core Tension

This project required convincing leadership to slow down feature development for a year. That meant every item in the prioritization had to answer two questions simultaneously:

1. **Why this service before that one?** (sequencing the decomposition)
2. **Why this project over the 10 features leadership already planned?** (justifying the investment)

I couldn't use a generic framework and hand over a spreadsheet. I had to build a case that connected infrastructure work to revenue, because leadership doesn't fund architecture improvements, they fund revenue outcomes.

---

## Framework: Modified RICE with Revenue Risk Overlay

I used RICE as the transparent scoring layer (so every stakeholder could see how decisions were made), but I added a revenue dimension that standard RICE doesn't capture well: the difference between **revenue protection** and **revenue generation**.

### Scoring Dimensions

| Dimension | What It Measured | How I Estimated It |
|-----------|-----------------|-------------------|
| **Reach** | How many customers/tenants does this service affect? | Platform telemetry: which services were called most frequently, which ones touched the most tenants |
| **Impact** | How severe is the pain when this service fails? | Incident history: SLA breach frequency, average time to identify responsible team (2-3 days was our baseline), customer escalation count |
| **Confidence** | How certain are we about the effort and outcome? | Engineering input during refinement sessions. Higher confidence for services the senior architect had already mapped; lower for services with undocumented dependencies |
| **Effort** | Dev-weeks to isolate this service into an independent microservice | Joint estimation with the engineering manager. Included: code extraction, API contract definition, auth layer, testing, migration of dependent services |
| **Revenue Risk** | What revenue is at stake if we don't fix this? | Two sub-scores (see below) |

### Revenue Risk Sub-Scores

This was the layer that made the business case work for leadership.

**Revenue Protection Score** (scale 1-5):
- How much existing ARR depends on this service being reliable?
- Which strategic customers had escalated issues related to this service?
- Were any customers threatening to churn because of downtime tied to this service?

**Revenue Generation Score** (scale 1-5):
- Could this service, once isolated, be packaged as a marketplace module?
- Had any RFI prospects specifically requested this capability?
- What was the confirmed pipeline value associated with this module?

---

## How I Applied It: Service Prioritization

With ~21-22 services to decompose, I couldn't do them all at once. Here's how they grouped after scoring:

### Tier 1: Decompose First (Months 1-4)
**Criteria**: High reach + high impact + confirmed revenue at risk

These were services that were both causing the most pain AND had the clearest revenue connection.

| Service Category | Why First |
|-----------------|-----------|
| **Job scheduling and orchestration** | Directly responsible for the concurrent job crashes. Largest customers (retailers running 8-10 hour demand recalculation jobs across thousands of locations) were hitting this daily. Revenue protection: 5/5. Two strategic accounts had escalated. |
| **Data crunching engine** | The #1 module requested by both RFI enterprise prospects. Revenue generation: 5/5. Confirmed pipeline. Also the heaviest compute consumer in the monolith, so isolating it immediately relieved pressure on everything else. |
| **Core validation layer** | The validator that checked job conflicts, data integrity, backup/restore safety. Needed first because every other service depended on it. No direct revenue score, but blocking everything else gave it implicit priority. |

### Tier 2: Decompose Next (Months 4-8)
**Criteria**: Medium reach + high impact for specific customer segments

| Service Category | Why Second |
|-----------------|-----------|
| **Messaging / notification service** | SMTP-based, slow (100K+ messages could take an hour), and its compute spikes were starving parallel services in the monolith. Isolating it stopped the blast radius problem. |
| **Retail visualization module** | Included the image processing service. Specifically requested by retail-segment customers. Revenue generation: 4/5 (marketplace module candidate). |
| **User management service** | Large conglomerate with 100K+ users was escalating weekly about access changes. Revenue protection: 4/5. Also an opportunity for self-service (became its own project later). |

### Tier 3: Decompose Last (Months 8-12)
**Criteria**: Lower reach or lower urgency, but still needed for complete decomposition

| Service Category | Why Last |
|-----------------|----------|
| **Reporting and analytics services** | Mostly read-only, rarely caused outages. Lower urgency. |
| **Configuration management** | Stable, rarely changed. Could be isolated with minimal risk. |
| **Remaining internal communication services** | Catch-all for the rest. Lower customer impact, but needed for the monolith to fully become just an orchestrator. |

---

## The Bigger Prioritization: This Project vs. Everything Else

Sequencing the services was the easier problem. The harder one was justifying a year-long infrastructure investment over the existing feature backlog.

### What I Brought to the Leadership Table

**The backlog as a negotiation tool, not a to-do list.**

I didn't walk in saying "we need to do this infrastructure project." I walked in with the full backlog: every item currently in flight, the effort assigned to each, the customer or strategic reason behind it. Then I said:

*"Here's what's planned for the next 12-24 months. Here's the revenue projection for the API marketplace, confirmed by the sales team based on two active RFIs. Here's the compute and reliability cost we're eating from the monolith. If we invest one year, we get both: the reliability fix AND the marketplace revenue. If we don't, we keep firefighting, and we lose the RFI prospects."*

This turned it from "PM wants to do something new" into "here are the trade-offs, you decide what comes off the list."

**Framing infrastructure as revenue protection, not cost.**

Leadership didn't want to hear "our architecture is bad." They wanted to hear "we're losing money because of our architecture, and here's how much we can make by fixing it." Every infrastructure item was framed in revenue terms:

| Infrastructure Work | Revenue Framing |
|-------------------|-----------------|
| Decompose job scheduler | "Strategic account X has escalated 4 times this quarter. They represent $Y in ARR. This fixes their problem." |
| Add security layer per service | "This is what unlocks the marketplace. Without it, we can't sell modules to the two enterprise prospects in our pipeline." |
| Build validator service | "Multi-tenant crashes are causing SLA penalties. This eliminates them." |
| Slow down feature work for 12 months | "We're doing 2 projects in 1. Converting the monolith into microservices AND adding the security layer to expose them as a marketplace. Dual return on a single investment." |

**Combining two projects into one.**

The key insight was that decomposition (reliability) and marketplace (revenue) required the same underlying engineering work. I packaged them as one initiative so leadership saw a single investment with dual returns, not two competing projects asking for the same team's time.

---

## Managing Ongoing Prioritization During the Project

Even after approval, prioritization didn't stop. Leadership frequently pushed new feature requests. Customers raised urgent issues. The process for handling this during the 12-month build:

**When leadership pushed a new feature:**
1. I exposed the current backlog with effort assignments
2. Showed exactly what would slip if we took on the new item
3. Let leadership choose the trade-off: "If this goes in, tell me what comes out"

**When a strategic customer escalated:**
1. Checked if the escalation was related to a service already in our decomposition queue
2. If yes, used it as further justification to accelerate that service's isolation
3. If no, evaluated: one-off fix vs. something that belongs in the prioritization list

**When engineering found unexpected complexity:**
1. Weekly refinement sessions caught most of these early
2. If effort estimates changed materially, I re-scored the affected services and re-sequenced if needed
3. Communicated changes to leadership proactively: "Service X is going to take 3 weeks longer because we found an undocumented dependency. Here's the updated timeline and what it affects."

---

## What I'd Do Differently

1. **Quantify SLA penalty costs in dollars from day one.** I had incident counts and escalation frequency, but I didn't have clean dollar amounts for downtime costs until partway through the pitch. Having that number on slide one would have shortened the approval cycle.

2. **Build a migration dashboard from the start.** I tracked decomposition progress informally. A visible dashboard showing "X of 22 services decomposed, Y% of compute now independently scalable" would have built momentum with leadership and kept engineering motivated through a long project.

3. **Formalize marketplace pricing earlier.** We focused on the technical decomposition first and figured out pricing and packaging later. Having even a rough pricing model earlier would have made the revenue generation projections more credible with leadership.

---

*All company names, client names, and partner names have been anonymized. Metrics and decision frameworks are accurate as applied.*
