# Stakeholder Map: Platform Decomposition + API Marketplace

> This project touched every function in the organization. Alignment wasn't a one-time meeting. It was a continuous negotiation across stakeholders with fundamentally different incentives.

---

## Why This Project Had Unusual Stakeholder Complexity

Most product initiatives have a clear "customer" and a clear "builder." This one had neither in the traditional sense.

The "customer" was simultaneously: existing enterprise clients who needed reliability fixes, prospective enterprise clients who wanted modular API access, internal teams drowning in operational overhead, and leadership who wanted revenue growth.

The "builder" was an engineering team I was asking to stop building features (what they were measured on) and invest in infrastructure (what nobody gets promoted for).

Every stakeholder had a legitimate perspective. None of them were wrong. The PM job was to find the framing where everyone's interests aligned enough to move forward together.

---

## Stakeholder Map

### Product Leadership (VPs of Product)

**What they cared about**: Roadmap commitments, quarterly OKRs, competitive positioning, customer satisfaction scores.

**Their concern with this project**: "We already promised customers features X, Y, and Z for this year. If we pause feature work, we break those commitments."

**How I managed them**:
- Presented the initiative in the weekly all-hands (which included everyone from junior devs to VPs) as a hypothesis first, not a demand. Got early signal that the idea had support across the org before escalating to a formal proposal.
- Framed the decomposition as *enabling* the roadmap, not competing with it. Once services were isolated, future feature development would be faster because teams wouldn't be blocked by the monolith's shared constraints.
- Showed the revenue numbers from the RFI prospects so this looked like a revenue initiative that happened to also fix infrastructure, not a tech debt cleanup.

**What they needed from me**: A PRD with a clear business case, revenue projections validated by sales, and a concrete list of what current commitments would be affected.

**Alignment mechanism**: Go/no-go review meeting with the full cross-functional group. Written PRD circulated in advance.

---

### Engineering Manager + Dev Team (~15 developers at peak)

**What they cared about**: Technical quality, sustainable pace, interesting problems, not being thrown under the bus when deadlines slipped.

**Their concern with this project**: "We're already overwhelmed with feature requests and production support. Now you want us to rewrite the platform?"

**How I managed them**:
- I sat with the engineering manager almost daily during peak phases. Not to micromanage, but to unblock, to understand where effort estimates were drifting, and to make sure I was translating business pressure accurately without amplifying it.
- When leadership pushed new features during the project, I showed the engineering team the backlog trade-offs: "This new item is coming in, but this other item is coming off your plate. Net effort is the same."
- Gave them ownership of the *how*. I defined the what (which services to isolate, in what order, what the API contracts should look like from a product perspective) and the why (revenue, reliability). They owned the technical architecture, the decomposition approach, and the implementation details.
- Weekly refinement sessions gave devs a recurring safe space to raise concerns, flag unexpected complexity, and push back on timelines. These were not status updates. They were working sessions.

**What they needed from me**: Clear requirements that didn't change mid-sprint. Honest communication about why priorities shifted. Protection from leadership's urge to add scope without removing something else.

**Alignment mechanism**: Daily standups (lightweight), weekly refinements (substantive), and 1:1s with the engineering manager on all live projects.

---

### Implementation Team

**What they cared about**: Customer satisfaction, reducing their own operational burden, not being surprised by platform changes that broke their workflows.

**Their concern with this project**: "Every time you change something, our customer configurations break and we're the ones who get the call at midnight."

**How I managed them**:
- Invited them into the PRD review process from the start. They understood customer needs at a granular level that even account managers didn't always have, because they were the ones configuring the platform daily for each organization.
- Made them early users of every decomposed service in pre-production. They didn't just "review" the feature; they actually used it, configured it, tested it against real customer scenarios.
- The demo documents I created for every feature were primarily for the implementation team. These gave them a click-by-click walkthrough of what changed, what each button did, and what the expected user behavior was. Updated every time anything changed, no matter how small.
- Framed the user management self-service capability (which came out of this project) as "this reduces your ticket queue," not "this replaces your work."

**What they needed from me**: Advance notice of any change that affected customer-facing behavior. Detailed documentation. Testing time in pre-production before anything went live.

**Alignment mechanism**: PRD reviews, pre-production testing cycles, demo docs, and monthly feature enablement sessions.

---

### Account Management Team

**What they cared about**: Customer retention, upselling, hitting revenue targets, maintaining relationships with key contacts at customer organizations.

**Their concern with this project**: "If we slow down on features, our customers will feel neglected. I just promised them feature X in Q3."

**How I managed them**:
- Worked closely with them to understand which customers had the loudest pain points around reliability and downtime. These became the "revenue protection" data points in my business case.
- Involved them in the PRD review process because they understood what each organization expected from the platform at a level of detail nobody else had.
- For the API marketplace, they were the ones who'd validated the RFI revenue projections with prospects. I made sure they felt ownership of the marketplace idea, not that I was "taking" something from their pipeline.
- After launch, the marketplace gave them a new selling motion: modular access for customers who couldn't afford or didn't need the full platform. This turned a potential concern ("you're pausing features for my customers") into an opportunity ("you're giving me a new product to sell").

**What they needed from me**: Talking points for customers about what was changing and why. Reassurance that their key accounts' most critical issues were prioritized in the decomposition sequence.

**Alignment mechanism**: PRD review participation, regular syncs on customer impact, and co-creation of the revenue projections.

---

### Design Team (UX)

**What they cared about**: User experience consistency, having time to do proper research, not being asked to "just make it look nice" at the last minute.

**Their concern with this project**: "Infrastructure work usually means we're an afterthought. We'll be pulled in at the end to polish something that's already been decided."

**How I managed them**:
- Brought them into the process from the PRD stage, not the implementation stage. For features with a user-facing component (user management, marketplace UI, configuration interfaces), the design team was in the go/no-go review alongside engineering and implementation.
- For the platform features that needed UI work, I wrote the use cases and expected behavior; the design team decided how it should look, feel, and behave in the interface. Clear role separation.
- Figma designs were produced as formal artifacts alongside the PRD and technical design document, not after them.

**What they needed from me**: Early involvement, not last-minute polish requests. Clear use cases and user context so they could make informed design decisions.

**Alignment mechanism**: Go/no-go review participation, shared Figma workspace, joint sessions on interaction patterns.

---

### Sales Team

**What they cared about**: Pipeline, deal velocity, competitive differentiation, having something new to pitch.

**Their concern with this project**: "When can I tell prospects the marketplace is available? Can I start selling it before it's built?"

**How I managed them**:
- The sales team was critical for validating the revenue projections. I sat in on the RFI calls with the two enterprise prospects who wanted modular access. This gave me first-hand understanding of what they'd pay for, how they'd use it, and what their deal-breakers were.
- Sales provided the confirmed pipeline numbers that became the centerpiece of the business case to leadership. This gave them ownership of the initiative's success: if the marketplace launched and those deals closed, sales could claim the win.
- I set clear expectations about what could be promised to prospects and when. No selling vaporware. The marketplace would be available after decomposition was complete and security layers were tested.

**What they needed from me**: A timeline they could share with prospects. Clarity on what modules would be available first. Confidence that the product would actually ship.

**Alignment mechanism**: Joint RFI participation, regular pipeline reviews, and shared ownership of the revenue forecast.

---

### Customers (Enterprise Organizations)

**What they cared about**: Platform stability, getting their money's worth, having their specific needs heard.

**Their interaction with this project**: Indirectly, through the implementation team and account managers. Directly, through pre-production beta testing of decomposed services.

**How I managed them**:
- Customer-side account managers were invited to test features in pre-production. This wasn't just QA. It was relationship management: showing customers that their feedback shaped the product.
- For the beta feature flag system, customers could opt-in to new capabilities through the implementation team. This gave them a sense of agency and early access, which strengthened retention.
- The two RFI enterprise prospects were effectively co-design partners for the marketplace. Their requirements directly influenced which modules we packaged first and what the API contracts looked like.

**What they needed from me**: Stability improvements they could feel. Features that matched what they'd asked for. Transparent communication (via account managers) about what was changing and when.

---

## How Stakeholder Dynamics Shifted Over the Project Lifecycle

| Phase | Who Had the Loudest Voice | My Focus |
|-------|--------------------------|----------|
| **Pre-approval** (building the case) | Product leadership + sales | Revenue framing, backlog trade-offs, RFI pipeline data |
| **Early build** (months 1-4) | Engineering team | Unblocking, refining estimates, protecting scope |
| **Mid build** (months 4-8) | Implementation team + account managers | Pre-production testing, demo docs, change communication |
| **Late build + marketplace prep** (months 8-12) | Sales + customers | Module packaging, pricing, prospect communication |
| **Post-launch** | Everyone, but especially implementation | Enablement, self-service rollout, bug triage |

---

## Key Takeaway

No single stakeholder could have killed this project outright. But any two of them pushing back simultaneously would have stalled it. The alignment work was continuous: not a single kickoff meeting, but a year of weekly recalibration, transparent trade-offs, and making sure everyone felt heard even when they didn't get exactly what they wanted.

---

*All company names, client names, and partner names have been anonymized. Stakeholder dynamics and management approaches are accurate as experienced.*
