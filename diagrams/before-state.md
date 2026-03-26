# Before state: monolithic architecture

> All 21+ services in one codebase, shared compute, no ownership of the communication layer.

```mermaid
%%{init: {'theme': 'neutral'}}%%
graph TD
    FE[Frontend teams] --> MONO
    BE[Backend teams] --> MONO

    subgraph MONO["MONOLITH — single codebase, shared compute"]
        direction TB
        SVC1[Job scheduler — 8-10hr crash jobs]
        SVC2[Data crunching engine — heaviest compute]
        SVC3[Messaging — 100K+ msgs per batch]
        SVC4[User management — manual via tickets]
        SVC5[Retail visualization — image processing]
        SVC6[Reporting — read-heavy queries]
        SVC7[Configuration management]
        SVC8[Other services — all unowned]
    end

    MONO --> CUST[Customers — full platform or nothing]
```

### What this architecture caused

```mermaid
%%{init: {'theme': 'neutral'}}%%
graph TD
    A[Heavy job runs in Tenant A] --> SHARED[Shared compute pool]
    B[Parallel job runs in Tenant B] --> SHARED
    SHARED --> CRASH[Platform crash — resources starved]
    CRASH --> SLA[SLA penalties + customer escalations]
    SLA --> CHURN[Strategic accounts threaten to leave]
```

### Pain points

| Problem | Impact |
|---------|--------|
| **No ownership of inter-service communication** | 2-3 days just to find which team should investigate a failure |
| **Shared compute** | One customer's 8-10 hour demand recalculation job starves all parallel workloads |
| **No modular access** | Enterprise prospects wanted specific modules with their own front-end. Architecture made it impossible. |
| **Can't scale independently** | A spike in SMTP load (100K+ notifications) pulls compute from every unrelated service |
| **SLA penalties** | Weekly downtimes in multi-tenant environments. Strategic accounts threatening to leave. |
