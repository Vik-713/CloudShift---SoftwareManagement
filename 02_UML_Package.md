# CloudShift Migration Project
## Deliverable 2: UML Package & Architectural Design
**Document ID:** CS-UML-2026-V1.0  
**Project:** CloudShift - On-Premise to Cloud Migration  

---

### 1. Architectural Overview & Design Principles

To ensure zero downtime during business operations and high availability post-migration, the system architecture adheres to two fundamental software engineering principles:
1. **High Cohesion:** Each service or module encapsulates a single, tightly focused responsibility (e.g., Blackout Guard solely manages time-window policies; Database Replication Engine solely handles change data capture).
2. **Loose Coupling:** Application workloads and shared cloud infrastructure are decoupled via standardized APIs, Infrastructure-as-Code abstractions, and asynchronous event messaging. This ensures that changes to an application's internal code or DB schema do not impact shared infrastructure controllers.

---

### 2. UML Diagrams

#### 2.1 Use Case Diagram
The Use Case diagram illustrates primary actors (Cloud Engineer, System Administrator, CIO, Business User, Automated Blackout Guard) and their interactions with the migration system.

```mermaid
graph TD
    subgraph CloudShift Migration Platform
        UC1([Provision Cloud IaC Infrastructure])
        UC2([Replicate Database CDC Staging])
        UC3([Enforce Month-End Blackout Window])
        UC4([Perform Pre-Cutover Hash Validation])
        UC5([Execute Application Cutover & DNS Switch])
        UC6([Monitor Synthetic Application Health])
        UC7([Trigger Automated Rollback])
        UC8([Generate Migration Compliance Report])
    end

    CloudEng((Cloud Engineer)) --> UC1
    CloudEng --> UC2
    CloudEng --> UC4
    CloudEng --> UC5

    BlackoutBot((Blackout Guard Engine)) --> UC3
    SysAdmin((System Administrator)) --> UC6

    AutoMonitor((Synthetic Monitor)) --> UC7
    CIO((CIO / Executive)) --> UC8

    UC5 .->|includes| UC3
    UC5 .->|includes| UC4
    UC7 .->|extends| UC6
```

---

#### 2.2 Class Diagram
The Class diagram specifies the static structural relationships between core classes in the migration engine framework.

```mermaid
classDiagram
    class MigrationOrchestrator {
        +String appID
        +MigrationState status
        +startMigration()
        +executeCutover()
        +rollback()
    }

    class AppWorkload {
        +String appName
        +int effortDays
        +boolean isDocumented
        +String onPremIP
        +String cloudTargetIP
        +verifyHealth()
    }

    class CloudResource {
        +String resourceID
        +ResourceType type
        +provision()
        +deprovision()
    }

    class DataPipeline {
        +String sourceDB
        +String targetDB
        +double syncLagMs
        +startReplication()
        +validateChecksums() bool
    }

    class BlackoutGuard {
        +Date currentDate
        +boolean isBlackoutActive()
        +lockPipelines()
        +unlockPipelines()
    }

    class HealthMonitor {
        +String endpointURL
        +int responseCode
        +double latencyMs
        +runSyntheticCheck() bool
    }

    class AuditLogger {
        +logEvent(String event, String user)
        +exportTrail()
    }

    MigrationOrchestrator "1" *-- "1" AppWorkload : manages
    MigrationOrchestrator "1" *-- "n" CloudResource : provisions
    MigrationOrchestrator "1" *-- "1" DataPipeline : controls
    MigrationOrchestrator "1" --> "1" BlackoutGuard : queries policy
    MigrationOrchestrator "1" --> "1" HealthMonitor : observes
    MigrationOrchestrator "1" --> "1" AuditLogger : records
```

---

#### 2.3 Sequence Diagram: "Cut Over One Application to the Cloud"
This diagram models the end-to-end cutover procedure for a single application (e.g., App1), including pre-checks, blackout validation, database locking, DNS updates, synthetic validation, and rollback capability.

```mermaid
sequenceDiagram
    autonumber
    actor Engineer as Cloud Engineer
    participant Orch as Migration Orchestrator
    participant Guard as Blackout Guard
    participant DB as Data Pipeline (CDC)
    participant CloudApp as Cloud Compute (K8s/VM)
    participant DNS as Route53 / DNS Switch
    participant Mon as Synthetic Monitor

    Engineer->>Orch: initiateCutover(appID)
    Orch->>Guard: checkBlackoutStatus(currentDate)
    alt Blackout Window Active (Last 5 Days)
        Guard-->>Orch: REJECT (Blackout Active)
        Orch-->>Engineer: Cutover Aborted: Month-End Blackout in effect
    else Window Clear
        Guard-->>Orch: ALLOW (Window Open)
        Orch->>DB: pauseSourceWrites() & flushCDCBuffer()
        DB-->>Orch: Sync Complete (Lag = 0ms)
        Orch->>DB: validateChecksums()
        DB-->>Orch: Hash Match (SHA-256 Valid)
        Orch->>CloudApp: activateProductionContainers()
        CloudApp-->>Orch: Containers Ready
        Orch->>DNS: updateTrafficRouting(OnPrem -> Cloud)
        DNS-->>Orch: DNS Propagation Started
        Orch->>Mon: runSyntheticHealthCheck()
        alt Health Check Passed (HTTP 200, Latency < 200ms)
            Mon-->>Orch: PASS
            Orch-->>Engineer: Cutover SUCCESSFUL (App Live in Cloud)
        else Health Check Failed
            Mon-->>Orch: FAIL
            Orch->>DNS: revertTrafficRouting(Cloud -> OnPrem)
            Orch->>CloudApp: standbyContainers()
            Orch-->>Engineer: Cutover FAILED - Automated Rollback Executed (RTO < 15m)
        end
    end
```

---

#### 2.4 Activity Diagram: Application Migration Lifecycle Workflow
This diagram details the decision steps, concurrency, and validation checkpoints during migration.

```mermaid
flowchart TD
    A([Start Migration Process]) --> B[Inspect Application & DB Dependencies]
    B --> C{Is App Documented?}
    C -- No (App7-9) --> D[Run Reverse Engineering & PERT Re-estimation]
    C -- Yes (App1-6) --> E[Load Standard IaC Template]
    D --> E
    E --> F[Provision Target Cloud Infrastructure]
    F --> G[Initialize CDC Data Sync]
    G --> H{Check Calendar Date}
    H -- Last 5 Working Days --> I[Pause Execution: Blackout Window Active]
    I --> H
    H -- Normal Days --> J[Run Staging Verification & Pre-Cutover Hash Check]
    J --> K{Data Checksum Parity 100%?}
    K -- No --> L[Resync DB Buffer & Alert DBA]
    L --> J
    K -- Yes --> M[Execute Production Traffic Cutover]
    M --> N[Run Synthetic Health Check]
    N --> O{Health Checks Passed?}
    O -- Pass --> P([Complete Migration & Decommission On-Prem])
    O -- Fail --> Q[Trigger Automated Rollback RTO < 15 min]
    Q --> R([Revert Traffic to On-Prem Hardware])
```

---

#### 2.5 State Diagram: Application Migration State Machine
Tracks the state transitions of an application workload through the migration lifecycle.

```mermaid
stateDiagram-v2
    [*] --> Unmigrated
    Unmigrated --> Assessed : Architecture Audit Complete
    Assessed --> Provisioned : IaC Deployed
    Provisioned --> StagingSync : CDC Replication Active
    StagingSync --> BlackoutPaused : Blackout Date Reached
    BlackoutPaused --> StagingSync : Blackout Window Expired
    StagingSync --> CutoverPending : Checksum 100% Validated
    CutoverPending --> CutoverInProgress : DNS Routing Swapped
    CutoverInProgress --> Verified : Synthetic Check Passed
    Verified --> ProductionLive : Handover Complete
    CutoverInProgress --> RolledBack : Synthetic Check Failed
    RolledBack --> StagingSync : Issue Remediated
    ProductionLive --> [*]
```

---

### 3. Cohesion and Coupling Justification

#### 3.1 Loosely Coupled Architecture
- **Infrastructure Abstraction:** Applications do not contain cloud-provider-specific SDK calls. They communicate via generic environment variables injected by Terraform IaC and Kubernetes ConfigMaps.
- **Data Layer Separation:** Change Data Capture (CDC) operates directly at the database transaction log level, ensuring zero invasive changes to application source code.
- **Asynchronous Health Checks:** Synthetic health monitoring operates externally over HTTP/HTTPS endpoints without blocking main application execution threads.

#### 3.2 High Module Cohesion
- **`BlackoutGuard` Module:** Exclusively handles date/time evaluation against the 5-day month-end calendar rules. It does not perform network operations or DB calls.
- **`DataPipeline` Module:** Focuses solely on database schema migration, CDC log streaming, and SHA-256 hash comparison.
- **`MigrationOrchestrator` Module:** Serves as a state machine controller, orchestrating workflow steps without embedding business logic from individual logistics apps.

---
