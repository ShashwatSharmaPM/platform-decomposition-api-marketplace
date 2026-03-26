# Before state: monolithic architecture

> All 21+ services in one codebase, shared compute, no ownership of the communication layer.

```mermaid
graph TB
    subgraph External["External consumers"]
        FE["Frontend teams"]
        BE["Backend teams"]
    end

    subgraph Monolith["MONOLITH — shared compute, shared resources"]
        direction TB

        subgraph Tier1["Services (all tightly coupled)"]
            direction LR
            JS["Job scheduler<br/><i>8-10hr crash jobs</i>"]
            DC["Data crunching<br/><i>Heaviest compute</i>"]
            MSG["Messaging<br/><i>100K+ msgs/batch</i>"]
            UM["User mgmt<br/><i>Manual via tickets</i>"]
        end

        subgraph Tier2["More services (all unowned)"]
            direction LR
            RV["Retail visualization<br/><i>Image processing</i>"]
            RPT["Reporting<br/><i>Read-heavy</i>"]
            CFG["Config mgmt<br/><i>Tenant settings</i>"]
            MORE["Other services<br/><i>No team owns these</i>"]
        end

        JS <-.-> DC
        DC <-.-> MSG
        MSG <-.-> RV
        JS <-.-> RPT
        DC <-.-> CFG
        UM <-.-> MORE
        RV <-.-> JS
        CFG <-.-> MSG
    end

    FE --> Monolith
    BE --> Monolith
    Monolith --> CUST["Customers: full platform only"]

    style Monolith fill:#FCEBEB,stroke:#E24B4A,stroke-width:2px
    style JS fill:#FAECE7,stroke:#D85A30
    style DC fill:#FAECE7,stroke:#D85A30
    style MSG fill:#FAECE7,stroke:#D85A30
    style UM fill:#FAECE7,stroke:#D85A30
    style RV fill:#FAECE7,stroke:#D85A30
    style RPT fill:#FAECE7,stroke:#D85A30
    style CFG fill:#FAECE7,stroke:#D85A30
    style MORE fill:#FAECE7,stroke:#D85A30
    style FE fill:#E6F1FB,stroke:#378ADD
    style BE fill:#E6F1FB,stroke:#378ADD
    style CUST fill:#F1EFE8,stroke:#888780
```

## Pain points

| Problem | Impact |
|---------|--------|
| **No ownership of inter-service communication** | 2-3 days just to identify which team should investigate a failure |
| **Shared compute resources** | One customer's 8-10 hour demand recalculation job starves all parallel workloads. Platform crashes in multi-tenant environments. |
| **No modular access** | Enterprise prospects wanted specific modules (data crunching, planning engine) with their own front-end. Architecture made this impossible. Full platform or nothing. |
| **Scaling = scaling everything** | Can't scale messaging independently of reporting. A spike in SMTP load (100K+ notifications) pulls compute from unrelated services. |
| **SLA penalties accumulating** | Weekly downtimes in multi-tenant environments. Customers escalating. Strategic accounts threatening to leave. |
