# Before state: monolithic architecture with no ownership

> All 21+ services in one codebase, shared compute, no ownership of the communication layer. 2-3 days to identify which team should fix a failure. Enterprise prospects walking away because we couldn't sell modules.

```mermaid
graph TD
    FE[Frontend teams] --> MONO
    BE[Backend teams] --> MONO

    subgraph MONO[" Monolith — single codebase, shared compute, no service ownership "]
        direction TB
        JS[Job scheduler]
        DC[Data crunching engine]
        MSG[Messaging service]
        UM[User management]
        RV[Retail visualization]
        RPT[Reporting]
        CFG[Configuration mgmt]
        MORE[+14 more services]
    end

    MONO --> CUST[Customers<br/>full platform or nothing]

    classDef mono fill:#d94f4f,color:#fff,stroke:#b33a3a
    classDef svc fill:#e8a0a0,color:#2a0a0a,stroke:#c27070
    classDef ext fill:#4a8bc2,color:#fff,stroke:#3670a0
    classDef cust fill:#777,color:#fff,stroke:#555

    class MONO mono
    class JS,DC,MSG,UM,RV,RPT,CFG,MORE svc
    class FE,BE ext
    class CUST cust
```

### How concurrent jobs crashed the platform

```mermaid
graph TD
    CUST_A[Customer A starts<br/>demand recalculation job] --> SHARED[Job consumes shared<br/>compute resources]
    CUST_B[Customer B starts<br/>parallel job in same environment] --> SHARED

    SHARED --> STARVE[Parallel workloads starves resources]
    STARVE --> CRASH[Platform crashes<br/>in multi-tenant environment]
    CRASH --> INCIDENT[Incident raised]
    INCIDENT --> BLAME[2-3 days to identify<br/>which team is responsible]
    BLAME --> SLA[SLA penalties and<br/>customer escalations]

    classDef trigger fill:#4a8bc2,color:#fff,stroke:#3670a0
    classDef problem fill:#e8a0a0,color:#2a0a0a,stroke:#c27070
    classDef bad fill:#d94f4f,color:#fff,stroke:#b33a3a

    class CUST_A,CUST_B trigger
    class SHARED,STARVE problem
    class CRASH,INCIDENT,BLAME,SLA bad
```

### Why enterprise prospects walked away

```mermaid
graph TD
    PROSPECT[Enterprise prospect<br/>wants specific modules only] --> REQ{Can we sell<br/>just data crunching<br/>or just planning engine?}
    REQ -->|No| ARCH[Architecture is monolithic<br/>everything is coupled]
    ARCH --> FULL[Full platform purchase<br/>or nothing]
    FULL --> LOST[Prospect lost<br/>revenue opportunity missed]

    classDef start fill:#4a8bc2,color:#fff,stroke:#3670a0
    classDef step fill:#c68a2e,color:#fff,stroke:#a06e1e
    classDef problem fill:#e8a0a0,color:#2a0a0a,stroke:#c27070
    classDef bad fill:#d94f4f,color:#fff,stroke:#b33a3a

    class PROSPECT start
    class REQ,ARCH step
    class FULL problem
    class LOST bad
```

## Pain points summary

| Problem | Impact |
|---------|--------|
| **No ownership of inter-service communication** | 21+ services talking to each other with no team owning the connections. 2-3 days just to identify which team should investigate a failure. |
| **Shared compute resources** | One customer's 8-10 hour demand recalculation job starves all parallel workloads. Platform crashes in multi-tenant environments. |
| **No modular access** | Enterprise prospects wanted specific modules (data crunching, planning engine) with their own front-end. Architecture made this impossible. Full platform or nothing. |
| **Scaling = scaling everything** | Can't scale messaging independently of reporting. A spike in SMTP load (100K+ notifications) pulls compute from unrelated services. |
| **SLA penalties accumulating** | Weekly downtimes in multi-tenant environments. Strategic accounts threatening to leave. Penalties mounting while teams debate ownership. |
| **No external API access** | Platform was internal-only. No way to expose individual services to external customers or marketplaces. |
