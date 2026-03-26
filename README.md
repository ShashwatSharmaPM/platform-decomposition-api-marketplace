# platform-decomposition-api-marketplace
Case study: Breaking a monolith into 50+ APIs and launching a modular marketplace. Grew from 3-person team to 18. Multi-billion dollar market entry.

# Platform Decomposition + API Marketplace Launch

> Breaking a monolith into 50+ APIs, launching a modular marketplace, and opening up an entirely new market segment, all driven bottom-up from a 3-person team.

## Context

**Company type**: Global supply chain SaaS platform (demand and supply planning)
**My role**: Lead Product Manager, APIs
**Team**: Started with 3 (me, a senior architect, a junior developer). Grew to 18 (15 devs, 3 PMs).
**Timeline**: ~1 year for the core decomposition; marketplace launched alongside

---

## The Problem

The platform was built as a single monolith. Every service, from data crunching to messaging to reporting, lived in one codebase with shared resources.

This caused three cascading problems:

**1. Nobody owned the communication layer.**
Over 20 internal services were talking to each other via APIs, but no team owned these connections. When something broke (and it broke regularly), each team pointed at the others. Just identifying which team should investigate took 2-3 days. Customers were experiencing downtimes weekly. We were eating SLA penalties.

**2. The monolith couldn't handle concurrent workloads.**
Our largest customers ran demand recalculation jobs that processed data from thousands of retail locations, distribution centers, and regional HQs, forecasting up to 365 days into the future. These jobs ran for 8-10 hours. In multi-tenant environments, when two customers ran parallel jobs, compute resources were shared. One heavy job would starve everything else. The platform would crash.

**3. Enterprise prospects wanted modular access, but we only sold the full platform.**
Two very large enterprise prospects came through RFI processes wanting specific modules (just data crunching, or just the planning engine) with their own front-end. Our architecture made this impossible. We had to say no to what turned out to be a massive untapped market segment.

---

## What I Did

### Phase 1: Take Ownership of the Mess

I was originally on the UI/UX side, building reporting widgets. But I kept seeing the same pattern: the front end would work fine, and then something in the middle layer would silently break. I proposed setting up a dedicated API team to own the inter-service communication layer.

Leadership didn't mandate this. I drove it.

Started with 3 people. We began owning services one by one, first just triaging who should fix what, then actually owning the code. Within months, we owned ~21-22 services used by every front-end and back-end team in the organization.

### Phase 2: See the Bigger Opportunity

Once we owned the middle layer, I could see two things clearly:

1. If we decomposed the monolith into independent microservices, each service could scale independently. No more one heavy job killing the entire platform.
2. If we added a security/authentication layer on top of those microservices, we could expose them externally as a modular API marketplace.

One initiative, two outcomes. I built a 1-year plan covering:
- Which services to isolate first (prioritized by customer pain and revenue risk)
- The security layer architecture (auth per service, not per monolith)
- Revenue projections for the API marketplace (validated with the sales team from the two enterprise RFIs)
- What feature work we'd need to slow down, and why the trade-off was worth it

### Phase 3: Convince Leadership to Pause Feature Work

This was the hardest part. The engineering team was already overwhelmed with feature requests. I was asking them to slow down and invest in infrastructure for a year.

I brought three things to the leadership table:
- **Revenue numbers**: Confirmed pipeline from just two enterprise prospects was enough to justify the investment. If we opened it to the broader market, the upside was significantly larger.
- **The backlog as leverage**: I showed leadership exactly what was currently in flight, the effort assigned to each item, and what would need to be deprioritized. No vague "we need more time," just concrete trade-offs.
- **The dual benefit**: This wasn't just a marketplace play. It was also a reliability play. Decomposing the monolith directly fixed the downtime and scalability problems that were already costing us customers.

Leadership approved. The full team aligned.

### Phase 4: Build It

The decomposition turned the monolith into an orchestrator. Everything else was broken out into independent microservices (~20 services, ~50+ APIs).

Key sub-projects within this:

**Job Validator Service**
For the concurrent job problem, I built a validator API that initially blocked all parallel jobs within a tenant. Then we progressively relaxed the rules based on data analysis: if Job A only touches demand data in APAC and Job B only touches supply data in Europe, let them run simultaneously. We expanded this validator into a full service covering backup/restore conflicts, data integrity checks, and more.

**Image Processing Service**
Retail customers had hundreds of thousands of SKUs that changed constantly (new packaging = new SKU). Planners wasted hours scrolling through SKU numbers. We built a real-time image processing service that tunneled directly into the retailer's image database, pulled and resized product images on the fly, and displayed them inline with planning reports. The catch: retailers refused to share images with us for storage (IP concerns around future product launches). So we never stored anything. Pull, process, display, discard.

**API Marketplace Packaging**
We packaged the microservices into purchasable modules: data crunching module, retail visualization module, planning engine module, etc. Customers could buy individual modules and connect them to their own front-ends via our APIs. This was the first time the platform offered anything other than the full end-to-end solution.

---

## How I Worked With Engineering

My collaboration with the engineering manager was tight, sometimes daily calls during peak phases.

**Process**:
- I wrote the initial PRD with business context, problem statement, and success criteria
- We held a cross-functional review (implementation team, account managers, engineering, design, product leadership) for go/no-go
- Once approved, I created an overarching product requirement ticket in Jira, broken into enablers (each enabler = one capability needed to fulfill the requirement)
- The EM created epics and tasks within each enabler
- Weekly refinement sessions to address questions, unblock devs, and handle any technical limitations discovered during implementation
- Daily standups for status visibility
- Pre-production testing with implementation teams and customer-side account managers before any production release

**Feature flags**: We ran alpha, beta, and production tiers. Beta features could be enabled in production via system-level configuration, allowing customers to opt-in through the implementation team.

**Demo docs**: For every feature (big or small), I created a click-through demo document with screenshots and video walkthroughs showing exactly how users should interact with each element. This was unusual for the organization and became a standard practice.

---

## Outcomes

- **Team scale**: 3 people to 18 (15 devs, 3 PMs reporting to me)
- **Customer growth**: Supported 700% growth in number of customers
- **New market**: API marketplace launched, unlocking modular access to a multi-billion-dollar market segment for the first time
- **Reliability**: Multi-tenant downtime incidents dropped dramatically after microservice isolation (each service could scale independently)
- **Job conflicts**: Validator service eliminated data corruption from concurrent jobs, then progressively relaxed restrictions to minimize user friction
- **Revenue**: Confirmed enterprise pipeline converted; marketplace opened revenue streams from SMBs who couldn't afford the full platform

---

## What I Learned

1. **Bottom-up initiatives need top-down air cover.** I drove this from the ground, but it only worked because I built the business case in the language leadership cared about: revenue numbers, not architecture diagrams.

2. **Decomposition is a product problem, not just an engineering problem.** Which services to isolate first, how to package them for the market, what to charge, what the onboarding experience looks like: these are PM decisions that directly determine whether the engineering investment pays off.

3. **Progressive relaxation beats big-bang launches.** The job validator worked because we started restrictive (block everything) and relaxed incrementally based on real data. If we'd tried to ship the "smart" version first, we'd still be building it.

4. **Own the mess nobody wants.** Nobody wanted to own the communication layer. That's exactly why owning it gave me leverage to drive the largest platform initiative in the company.

---

*All company names, client names, and partner names have been anonymized. Metrics and technical details are accurate as described.*
