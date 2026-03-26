# After state: microservices + API marketplace

> Monolith reduced to orchestrator. ~20 services, ~50+ APIs, each independently scalable. Security layer per service. External marketplace access for modular purchase.

```mermaid
graph TB
    subgraph Consumers["Who accesses the platform"]
        direction LR
        INT["Internal teams"]
        MKT["API marketplace<br/><i>Modular purchase</i>"]
        ENT["Enterprise clients<br/><i>Own front-ends</i>"]
    end

    GW["API gateway + auth per service"]

    ORCH["Orchestrator<br/><i>Former monolith — routing only</i>"]

    subgraph Services["Independent microservices (~20 services, ~50+ APIs)"]
        direction TB
        subgraph Tier1["Tier 1 (months 1-4)"]
            direction LR
            JS["Job scheduler<br/><i>Own compute</i>"]
            DC["Data crunching<br/><i>Own compute</i>"]
            MSG["Messaging<br/><i>Own SMTP</i>"]
        end

        subgraph Tier2["Tier 2 (months 4-8)"]
            direction LR
            UM["User mgmt<br/><i>Self-service RBAC</i>"]
            RV["Retail viz<br/><i>Image tunneling</i>"]
            RPT["Reporting<br/><i>Own compute</i>"]
        end

        subgraph Tier3["Tier 3 (months 8-12)"]
            direction LR
            CFG["Config mgmt<br/><i>Tenant-scoped</i>"]
            MORE["Other services<br/><i>All independent</i>"]
        end
    end

    VAL["Validator service<br/><i>Job conflicts, data integrity, backup/restore</i>"]

    subgraph Modules["API marketplace modules (purchasable)"]
        direction LR
        M1["Data crunching<br/>module"]
        M2["Retail viz<br/>module"]
        M3["Planning engine<br/>module"]
    end

    INT --> GW
    MKT --> GW
    ENT --> GW
    GW --> ORCH
    ORCH --> JS
    ORCH --> DC
    ORCH --> MSG
    ORCH --> UM
    ORCH --> RV
    ORCH --> RPT
    ORCH --> CFG
    ORCH --> MORE

    JS -.-> VAL
    DC -.-> VAL
    UM -.-> VAL

    DC --- M1
    RV --- M2

    style GW fill:#EAF3DE,stroke:#639922,stroke-width:2px
    style ORCH fill:#F1EFE8,stroke:#888780
    style VAL fill:#FAEEDA,stroke:#BA7517,stroke-width:1.5px
    style JS fill:#E1F5EE,stroke:#1D9E75
    style DC fill:#E1F5EE,stroke:#1D9E75
    style MSG fill:#E1F5EE,stroke:#1D9E75
    style UM fill:#E1F5EE,stroke:#1D9E75
    style RV fill:#E1F5EE,stroke:#1D9E75
    style RPT fill:#E1F5EE,stroke:#1D9E75
    style CFG fill:#E1F5EE,stroke:#1D9E75
    style MORE fill:#E1F5EE,stroke:#1D9E75
    style MKT fill:#EEEDFE,stroke:#7F77DD
    style ENT fill:#E1F5EE,stroke:#0F6E56
    style INT fill:#E6F1FB,stroke:#378ADD
    style M1 fill:#EEEDFE,stroke:#7F77DD
    style M2 fill:#EEEDFE,stroke:#7F77DD
    style M3 fill:#EEEDFE,stroke:#7F77DD
```

## What changed

| Dimension | Before | After |
|-----------|--------|-------|
| **Architecture** | Single monolith, all services in one codebase | ~20 independent microservices, ~50+ APIs. Monolith is just an orchestrator. |
| **Compute** | Shared. One heavy job starves everything. | Independent per service. Job scheduler scaling doesn't affect messaging. |
| **Failure isolation** | 2-3 days to find the responsible team | Each service independently monitored. Failures are scoped to one service. |
| **External access** | Full platform or nothing | API marketplace: buy individual modules (data crunching, retail viz, planning engine) |
| **Concurrent jobs** | Two parallel jobs in same tenant = data corruption and crashes | Validator service checks for conflicts. Progressive relaxation based on what data each job touches. |
| **User management** | Every access change = ticket to implementation team | Self-service RBAC. User Manager role with tenant-scoped access. |
| **Image processing** | Not possible (retailers wouldn't share images) | Tunneling service pulls, processes, displays, and discards. Never stores. |
| **Team** | 3 people (PM + architect + junior dev) | 18 people (3 PMs, 15 devs) |
| **Market access** | Enterprise-only, full platform deals | SMBs and modular buyers via marketplace. New market segment unlocked. |
| **Customer growth** | Constrained by reliability and packaging limitations | 700% growth in number of customers supported |

## Key architectural decisions

**Why the monolith became an orchestrator, not deleted:**
Rewriting everything from scratch would have taken 2+ years and introduced massive risk. Keeping the monolith as a thin routing layer meant we could isolate services incrementally, one at a time, with rollback capability at each step.

**Why auth per service, not a central auth gateway:**
Central auth creates a single point of failure and doesn't support the marketplace model (different clients need different service-level permissions). Per-service auth meant we could grant marketplace customers access to specific modules without exposing the full platform.

**Why the validator is a cross-cutting service, not embedded in each microservice:**
Job conflict detection requires a global view of what's running across the tenant. Embedding validation logic in each service would have created consistency problems (each service making its own concurrency decisions without knowing what other services are doing). A dedicated validator service has the full picture.
