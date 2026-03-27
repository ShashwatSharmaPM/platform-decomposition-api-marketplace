# After state: microservices + API marketplace

> Monolith reduced to orchestrator. ~20 services, ~50+ APIs, each independently scalable. Security layer per service. External marketplace access for modular purchase. Team grew from 3 to 18.

```mermaid
graph TD
    INT[Internal teams] --> GW[API gateway]
    MKT[API marketplace buyers] --> GW
    ENT[Enterprise clients<br/>with own front-ends] --> GW

    GW --> ORCH[Orchestrator<br/>former monolith, routing only]

    ORCH --> JS[Job scheduler<br/>own compute]
    ORCH --> DC[Data crunching engine<br/>own compute]
    ORCH --> MSG[Messaging service<br/>own SMTP]
    ORCH --> UM[User management<br/>self-service RBAC]
    ORCH --> RV[Retail visualization<br/>image tunneling]
    ORCH --> RPT[Reporting<br/>own compute]
    ORCH --> CFG[Configuration mgmt<br/>tenant-scoped]
    ORCH --> MORE[Other services<br/>all independent]

    classDef gateway fill:#2d9d78,color:#fff,stroke:#1d7a5c
    classDef orch fill:#777,color:#fff,stroke:#555
    classDef svc fill:#a3dcc8,color:#0a2a1e,stroke:#5dbfa0
    classDef ext fill:#4a8bc2,color:#fff,stroke:#3670a0
    classDef mkt fill:#7b6bb5,color:#fff,stroke:#5f519a

    class GW gateway
    class ORCH orch
    class JS,DC,MSG,UM,RV,RPT,CFG,MORE svc
    class INT ext
    class MKT mkt
    class ENT gateway
```

### API marketplace: modular purchase

```mermaid
graph TD
    BUYER[Marketplace buyer<br/>can't afford or doesn't need full platform] --> GW[API gateway]
    GW --> M1[Data crunching module]
    GW --> M2[Retail visualization module]
    GW --> M3[Planning engine module]
    GW --> M4[Additional modules]

    M1 --> OWN[Buyer pushes output<br/>to their own UI]
    M2 --> OURS[Buyer uses<br/>our reporting UI]
    M1 --> OURS[Buyer uses<br/>our reporting UI]
    M3 --> OURS[Buyer uses<br/>our reporting UI]
    M3 --> OWN

    classDef buyer fill:#7b6bb5,color:#fff,stroke:#5f519a
    classDef gw fill:#2d9d78,color:#fff,stroke:#1d7a5c
    classDef mod fill:#a3dcc8,color:#0a2a1e,stroke:#5dbfa0
    classDef out fill:#777,color:#fff,stroke:#555

    class BUYER buyer
    class GW gw
    class M1,M2,M3,M4 mod
    class OWN,OURS out
```

### Validator service: progressive job conflict resolution

```mermaid
graph TD
    JOB_A[User A starts<br/>demand recalculation] --> VAL{Validator checks<br/>what data each job touches}
    JOB_B[User B starts<br/>parallel job] --> VAL

    VAL -->|Same data touched| BLOCK[Block parallel job<br/>until first completes]
    VAL -->|Different data regions| ALLOW[Allow both jobs<br/>to run simultaneously]

    BLOCK --> SAFE[Data integrity preserved]
    ALLOW --> SAFE

    SAFE --> EXPAND[Validator expanded to cover<br/>backup/restore conflicts<br/>and other shared-resource scenarios]

    classDef job fill:#4a8bc2,color:#fff,stroke:#3670a0
    classDef val fill:#c68a2e,color:#fff,stroke:#a06e1e
    classDef action fill:#a3dcc8,color:#0a2a1e,stroke:#5dbfa0
    classDef outcome fill:#2d9d78,color:#fff,stroke:#1d7a5c

    class JOB_A,JOB_B job
    class VAL val
    class BLOCK,ALLOW action
    class SAFE,EXPAND outcome
```

### Image processing service: solving the IP trust problem

```mermaid
graph TD
    RETAILER[Retailer has 100K+ SKUs<br/>images change constantly] --> REFUSE{Will they share<br/>images with us?}
    REFUSE -->|No — IP concerns<br/>future product launches at risk| TUNNEL[Build tunneling service<br/>connect to retailer's image database]
    TUNNEL --> MAP[Import SKU-to-image mapping<br/>via CSV from retailer]
    MAP --> PROCESS[Pull, resize, process images<br/>in real-time per report load]
    PROCESS --> DISPLAY[Display inline with<br/>demand planning data]
    DISPLAY --> DISCARD[Image discarded after display<br/>never stored on our servers]

    classDef retailer fill:#4a8bc2,color:#fff,stroke:#3670a0
    classDef problem fill:#c68a2e,color:#fff,stroke:#a06e1e
    classDef solution fill:#a3dcc8,color:#0a2a1e,stroke:#5dbfa0
    classDef outcome fill:#2d9d78,color:#fff,stroke:#1d7a5c

    class RETAILER retailer
    class REFUSE problem
    class TUNNEL,MAP solution
    class PROCESS,DISPLAY outcome
    class DISCARD outcome
```

## Before vs. after comparison

| Dimension | Before | After |
|-----------|--------|-------|
| **Architecture** | Single monolith, all services in one codebase | ~20 independent microservices, ~50+ APIs. Monolith is just an orchestrator. |
| **Compute** | Shared. One heavy job starves everything. | Independent per service. Job scheduler scaling doesn't affect messaging. |
| **Failure isolation** | 2-3 days to find the responsible team | Each service independently monitored. Failures scoped to one service. |
| **External access** | Full platform or nothing | API marketplace: buy individual modules (data crunching, retail viz, planning engine) |
| **Concurrent jobs** | Two parallel jobs = data corruption and crashes | Validator service checks for conflicts. Progressive relaxation based on what data each job touches. |
| **User management** | Every access change = ticket to implementation team | Self-service RBAC. User Manager role with tenant-scoped access. |
| **Image processing** | Not possible (retailers wouldn't share images) | Tunneling service pulls, processes, displays, and discards. Never stores. |
| **Team** | 3 people (PM + architect + junior dev) | 18 people (3 PMs, 15 devs) |
| **Market access** | Enterprise-only, full platform deals | SMBs and modular buyers via marketplace. New market segment unlocked. |
| **Customer growth** | Constrained by reliability and packaging limitations | 700% growth in number of customers supported |

## Key architectural decisions

**Why the monolith became an orchestrator, not deleted:**
Rewriting from scratch would have taken 2+ years with massive risk. Keeping the monolith as a thin routing layer meant we could decompose incrementally, one service at a time, with rollback capability at each step. By the end, the monolith did nothing but route requests to the right microservice.


**Why the validator started restrictive and relaxed over time:**
The initial version blocked all concurrent jobs within a tenant. This introduced delays but stabilized the system immediately. Then we progressively relaxed the rules based on data analysis: if Job A only touches demand data in APAC and Job B only touches supply data in Europe, let them run simultaneously. Starting permissive and adding restrictions after failures would have been a worse user experience than starting strict and relaxing as we gained confidence.

**Why the image service tunnels instead of stores:**
Retailers refused to upload product images to our servers. The IP risk (future product launches, packaging changes) was too high for their legal teams. Tunneling directly to the retailer's image database, processing on the fly, and never storing anything was the only design that passed their security review. Higher compute cost per request, but it unlocked the entire retail module.
