# CloudShift Migration Project
## Deliverable 1: Software Requirements Specification (SRS) Document
**Document ID:** CS-SRS-2026-V1.0  
**Project:** CloudShift - On-Premise to Cloud Migration  
**Target Systems:** 9 Internal Logistics Web Applications (App1 to App9)  
**Standard Compliance:** IEEE Std 830-1998 (System Requirements Specification)  

---

### 1. Introduction & Scope

#### 1.1 Purpose
This document defines the functional, non-functional, interface, and operational requirements for project **CloudShift**. The project's objective is to migrate 9 mission-critical internal web applications operating on ageing office-bound hardware to a high-availability, scalable public cloud infrastructure.

#### 1.2 Scope
The scope encompasses:
- Full migration of application binaries, configuration files, and database schemas for Applications 1 through 9.
- Implementation of automated Infrastructure as Code (IaC), continuous data synchronization, automated cutover mechanisms, and instant rollback capabilities.
- Strict adherence to business continuity rules, specifically avoiding any deployment or cutover activity during the month-end blackout window (the final 5 working days of each calendar month).
- Resolution of technical debt and risk associated with legacy, undocumented systems (App7, App8, App9).

#### 1.3 Stakeholders & Elicitation Methodology
Requirements were elicited through:
1. **Document Analysis:** Audit of legacy server configurations, database schemas, and codebase inspection for App1–App6.
2. **Stakeholder Workshops:** Conducted with the Chief Information Officer (CIO), Lead Infrastructure Engineers, DevOps Team, and Business Unit Leaders (Billing, Logistics Dispatch, Inventory, Fleet Management).
3. **Historical Outage Post-Mortem:** Analysis of the prior year's 2-day outage of the billing application to engineer preventative non-functional guardrails.

---

### 2. General Description & Constraints

#### 2.1 System Constraints
- **C-01 (Hardware EOL):** All on-premise hardware support contracts expire in exactly 7 months. All applications must be 100% operational in the cloud prior to contract expiration.
- **C-02 (Month-End Blackout):** No migration execution, downtime, or risk-bearing cutover activities may occur during the last 5 working days of any month (22 working days total per month).
- **C-03 (Resource Limit):** Project execution is strictly constrained to a core team of 3 engineering staff.
- **C-04 (Legacy Codebase):** Applications 7, 8, and 9 lack source code documentation and original developer support.

---

### 3. Functional Requirements (FR)

The system shall fulfill the following 12 Functional Requirements:

| Requirement ID | Requirement Name | Description | Stakeholder Origin |
| :--- | :--- | :--- | :--- |
| **FR-01** | **Data Sync & Staging** | The system shall continuously replicate database transactions from on-premise databases to staging cloud databases in real time using Change Data Capture (CDC). | Cloud Engineer / DevOps |
| **FR-02** | **Schema Migration** | The system shall automatically transform legacy database schemas into target cloud-native database engine formats without structural data loss. | Database Administrator |
| **FR-03** | **Traffic Rerouting & DNS Cutover** | The system shall update DNS routing tables and load balancer target groups to direct end-user application traffic from on-premise endpoints to cloud endpoints. | Network Admin / CIO |
| **FR-04** | **Blackout Enforcement** | The system shall automatically lock all automated deployment pipelines and prevent cutover execution during the designated last 5 working days of each month. | Business Teams / Billing |
| **FR-05** | **Session Persistence** | The system shall migrate active user sessions to a centralized cloud session cache (e.g., Redis cluster) prior to cutover to prevent session drop-outs. | Business User / UX Lead |
| **FR-06** | **Automated IaC Provisioning** | The system shall deploy cloud compute, networking, storage, and security groups using declarative Infrastructure-as-Code (IaC) scripts. | Cloud Engineer |
| **FR-07** | **Config Management** | The system shall externalize application configuration parameters into a centralized cloud parameter store, injecting environment secrets dynamically at launch. | Security Lead / Engineer |
| **FR-08** | **Cloud IAM & RBAC** | The system shall enforce role-based access control (RBAC) integrated with corporate SSO (Single Sign-On) for all 9 application administrative interfaces. | Security Administrator |
| **FR-09** | **Synthetic Health Monitoring** | The system shall perform automated synthetic transaction checks against staging and production endpoints every 30 seconds to verify functional health. | System Monitor / Operations |
| **FR-10** | **Automated Rollback Trigger** | The system shall automatically revert DNS traffic back to on-premise infrastructure within 15 minutes if post-cutover synthetic checks detect failure threshold breaches. | CIO / Business Teams |
| **FR-11** | **Audit Logging & Compliance** | The system shall log all migration actions, cutover triggers, configuration edits, and user access events to an immutable append-only cloud audit storage. | Compliance / Auditor |
| **FR-12** | **Real-Time Migration Dashboard** | The system shall provide a unified management dashboard showing real-time replication lag, cutover status, blackout window countdown, and application health. | Project Manager / CIO |

---

### 4. Non-Functional Requirements (NFR) with Measurable Targets

In accordance with strict quality assurance standards, every NFR below includes an explicit, quantifiable metric. *Qualitative statements such as "the system shall be fast" are prohibited.*

| NFR ID | Category | Requirement Description | Measurable Target / Metric | Verification Method |
| :--- | :--- | :--- | :--- | :--- |
| **NFR-01** | **Availability** | Overall Cloud Infrastructure Uptime | Post-migration cloud availability shall be $\ge 99.95\%$ per calendar month (max unplanned downtime $\le 21.9$ minutes/month). | CloudWatch / SLA Monitor |
| **NFR-02** | **Availability** | Maximum Cutover Downtime Window | Unplanned downtime during application cutover shall not exceed **30 minutes** per application during scheduled maintenance. | Downtime Timer / Log Audit |
| **NFR-03** | **Performance** | API Response Latency | 95th percentile (P95) HTTP API response time shall be $\le 200\text{ ms}$ under a load of 1,000 concurrent requests/sec. | JMeter Load Test |
| **NFR-04** | **Performance** | Database Query Latency | 99th percentile (P99) transaction execution time for billing/dispatch queries shall be $\le 50\text{ ms}$. | APM Query Profiler |
| **NFR-05** | **Data Integrity** | Recovery Point Objective (RPO) | Data loss during migration cutover shall be **RPO = 0 seconds** (zero transaction loss). | CDC Audit Log Comparison |
| **NFR-06** | **Data Integrity** | Pre-Cutover Checksum Validation | 100% data integrity parity match ($\text{SHA-256}$ hash verification) between on-premise source DB and target DB before cutover authorization. | Hash Verification Script |
| **NFR-07** | **Security** | Data Encryption Standards | 100% of data at rest encrypted with **AES-256** and all data in transit encrypted using **TLS 1.3**. | Cloud Security Scanner |
| **NFR-08** | **Reliability** | Recovery Time Objective (RTO) | Automated rollback execution complete and production fully restored on-premise within **RTO $\le 15$ minutes** of failure trigger. | Simulated Disaster Recovery |

---

### 5. MoSCoW Prioritization

Requirements are categorized using the MoSCoW prioritization matrix:

```
+-----------------------------------------------------------------------------------+
| MUST HAVE (Critical for MVP & Safety)                                              |
| FR-01 (Data Sync), FR-03 (DNS Rerouting), FR-04 (Blackout Locking),               |
| FR-06 (IaC Provisioning), FR-10 (Rollback Trigger)                                |
| NFR-02 (Cutover Downtime <=30m), NFR-05 (RPO=0), NFR-06 (Data Hash Parity),       |
| NFR-08 (RTO <=15m)                                                                |
+-----------------------------------------------------------------------------------+
| SHOULD HAVE (High Value, Essential for Scale)                                     |
| FR-02 (Schema Migration), FR-05 (Session Persistence), FR-07 (Config Mgmt),      |
| FR-08 (IAM/RBAC), FR-09 (Synthetic Checks), FR-11 (Audit Logging)                 |
| NFR-01 (99.95% Uptime), NFR-03 (P95 API <=200ms), NFR-04 (P99 DB <=50ms)           |
+-----------------------------------------------------------------------------------+
| COULD HAVE (Desirable Enhancements)                                               |
| FR-12 (Real-Time Management Dashboard), NFR-07 (TLS 1.3 / AES-256 automated scan) |
+-----------------------------------------------------------------------------------+
| WON'T HAVE (Out of Scope for initial 7-month migration window)                    |
| Application refactoring to Microservices Architecture, Serverless Rewrites        |
+-----------------------------------------------------------------------------------+
```

---

### 6. Requirements Traceability Matrix (RTM)

The matrix below links every Functional Requirement to its corresponding Non-Functional Requirements, Architecture Modules, and Test Case Identifiers:

| FR ID | Functional Requirement Summary | Related NFR | Architecture Module | Test Case ID |
| :--- | :--- | :--- | :--- | :--- |
| **FR-01** | Data Sync & Staging | NFR-05, NFR-06 | Data Pipeline / CDC Engine | TC-FR-01 |
| **FR-02** | Schema Migration | NFR-05, NFR-06 | Database Transformer | TC-FR-02 |
| **FR-03** | Traffic Rerouting & DNS Cutover | NFR-02 | Traffic Orchestrator / DNS | TC-FR-03 |
| **FR-04** | Blackout Enforcement | NFR-02 | Blackout Guard Service | TC-FR-04 |
| **FR-05** | Session Persistence | NFR-03 | Session State Cache | TC-FR-05 |
| **FR-06** | Automated IaC Provisioning | NFR-01 | Terraform IaC Modules | TC-FR-06 |
| **FR-07** | Config Management | NFR-07 | Cloud Secrets Manager | TC-FR-07 |
| **FR-08** | Cloud IAM & RBAC | NFR-07 | Security IAM Service | TC-FR-08 |
| **FR-09** | Synthetic Health Monitoring | NFR-01, NFR-03 | Synthetic Monitor Bot | TC-FR-09 |
| **FR-10** | Automated Rollback Trigger | NFR-08 | Rollback Controller | TC-FR-10 |
| **FR-11** | Audit Logging & Compliance | NFR-07 | Audit Logger Service | TC-FR-11 |
| **FR-12** | Real-Time Migration Dashboard | NFR-01 | Management UI Console | TC-FR-12 |

---
