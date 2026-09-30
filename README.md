# Case Study No. 92: CloudShift - On-Premise to Cloud Web Application Migration
**Course:** Software Engineering & Project Management (Semester III)  
**Program:** B.Tech Computer Science & Engineering (2025–29) | ITM Skills University - School of Future Tech  
**Project Name:** CloudShift  
**Case Study ID:** Case Study No. 92  

---

## Executive Summary & Index of Deliverables

This repository contains the complete, rigorous software engineering solution and project management deliverables for **Case Study No. 92: CloudShift**. 

A logistics enterprise currently runs 9 internal web applications on ageing on-premise office hardware with support contracts expiring in 7 months. To prevent a recurrence of last year's 2-day billing application outage and ensure zero interference with month-end financial closing, all 9 applications are systematically analyzed, re-estimated, scheduled, tested, and migrated to a modern cloud provider.

> **Case Study Simulation Note:**  
> *"The following closure results represent the simulated completion of the CloudShift case study based on the defined requirements, estimates, test evidence, and project artifacts."*

---

## Repository Deliverables Summary Matrix

| Deliverable # | Title & Repository Artifact | Key Contents & Artifacts Included | Core Metrics / Outcomes |
| :--- | :--- | :--- | :--- |
| **Deliverable 1** | [01.CloudShift - SRS-Document.pdf](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/01.CloudShift%20-%20SRS-Document.pdf) | IEEE 830 SRS Standard, 12 Functional Requirements (FR-01 to FR-12), 8 Measurable NFRs (NFR-01 to NFR-08), MoSCoW Matrix, Requirements Traceability Matrix (RTM) | Cloud Uptime $\ge 99.95\%$, API P95 $\le 200\text{ ms}$, RPO = 0s, RTO $\le 15\text{ m}$, Cutover Downtime $\le 30\text{ m}$ |
| **Deliverable 2** | [UML Package (Diagrams)](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29) | 5 High-Resolution Architectural UML Diagrams:<br>• [Use Case Diagram](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/Use%20Case.png)<br>• [Class Diagram](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/Class.png)<br>• [Sequence Diagram](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/Sequence.png)<br>• [Activity Diagram](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/Activity.png)<br>• [State Diagram](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/State.png) | High cohesion in micro-modules; Loose coupling via Infrastructure-as-Code (Terraform), Change Data Capture (CDC), and externalized REST APIs |
| **Deliverable 3** | [03. CloudShift - Project Plan.pdf](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/03.%20CloudShift%20-%20Project%20Plan.pdf) | 5-Level Work Breakdown Structure (WBS), 3-Engineer Allocation Matrix, CPM Network Analysis (T01–T10), Blackout-Adjusted Gantt Chart, 5 Project Milestones | CPM Active Duration = **62 working days**; Non-critical Task T10 Float = **24 working days**; Milestone gates embedded outside blackout windows |
| **Deliverable 4** | [04. CloudShift - Estimation Workbook.pdf](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/04.%20CloudShift%20-%20Estimation%20Workbook.pdf) | Baseline Single-Point Estimates ($120\text{ pd}$ / $40\text{d}$), PERT 3-Point Model ($O=12, M=20, P=40$), Capacity Planning, Monthly Blackout Adjustments, 95% Statistical Range | Baseline Effort = 120 pd; PERT Re-estimate = **126 pd**; Productive Effort Duration = **42 working days** (2.47 effective months $\ll 7.0$ months limit) |
| **Deliverable 5** | [05. CloudShift - Test Plan and Evidence.pdf](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/05.%20CloudShift%20-%20Test%20Plan%20and%20Evidence.pdf) | Test Strategy, 12 FR Test Cases (TC-FR-01 to 12), Boundary Value Analysis (BVA) for Blackout Window ($W \in [1, 22]$), Go/No-Go Decision Table, 20-Defect Log | Defect Removal Efficiency (DRE) = **90.0%**; Defect Density = **2.22 defects/app** & **0.159 defects/person-day** |
| **Deliverable 6** | [06. CloudShift - Risk Register and Closure Note.pdf](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/06.%20CloudShift%20-%20Risk%20Register%20and%20Closure%20Note.pdf) | 5x5 Probability-Impact Grid ($E = P \times I$), Risk Register (R-01 to R-06), RMMM Plans for Top 3 Exposure Risks (R-01, R-02, R-04), Historical Issue Log (ISS-01, ISS-02), Closure Note | Top Risks: R-01 ($E=20$, High), R-02 ($E=15$, High), R-04 ($E=12$, Medium); R-03 ($E=10$, Medium); Historical Issues resolved; 100% apps live |
| **Viva Guide** | [CloudShift_Project_Explanation_and_Viva.pdf](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/CloudShift_Project_Explanation_and_Viva.pdf) | Comprehensive Solution Summary, System Architecture Breakdown, Step-by-Step Implementation Walkthrough, and 10 Viva Voce Questions & Answers | Oral defense preparation document covering software engineering, PERT math, BVA testing, CPM scheduling, and cloud architecture |

---

## Detailed Deliverable Breakdowns

### 1. Software Requirements Specification (SRS Document)
- **IEEE 830 Compliance:** Structured according to international software requirements engineering standards.
- **12 Functional Requirements (FR-01 to FR-12):**
  - **FR-01 (Data Sync & Staging):** Continuous background replication from on-premise DBs to cloud staging instances with zero data mutation.
  - **FR-02 (Schema Migration):** Automated schema conversion and validation for target cloud database engines.
  - **FR-03 (Traffic Rerouting & DNS Cutover):** Low-TTL Route53 DNS manipulation and ALB target group switching for cutover.
  - **FR-04 (Blackout Enforcement):** Automated `BlackoutGuard` middleware service locking deployment pipelines during month-end financial freeze (last 5 working days of each 22-day month).
  - **FR-05 (Session Persistence):** Managed Redis session state cache to prevent user logout during cutover windows.
  - **FR-06 (Automated IaC Provisioning):** Terraform IaC scripts for idempotent landing zone deployment.
  - **FR-07 (Configuration & Secrets Management):** Integration with AWS Secrets Manager / Parameter Store for dynamic configuration injection.
  - **FR-08 (Cloud IAM & RBAC Security):** Least-privilege IAM policies, multi-factor authentication, and role-based access control.
  - **FR-09 (Synthetic Health Monitoring):** Post-cutover synthetic transaction bots validating application availability.
  - **FR-10 (Automated Rollback Trigger):** Automatic DNS failback and container rollback if health checks fail within 15 minutes.
  - **FR-11 (Audit Logging & Compliance):** Immutable log spooling to S3 bucket with CloudTrail compliance tracking.
  - **FR-12 (Real-Time Executive Dashboard):** Executive console rendering real-time migration progress, countdown timers, and system status.
- **8 Non-Functional Requirements (NFR-01 to NFR-08):**
  - **NFR-01 (Availability):** Cloud uptime $\ge 99.95\%$ per calendar month (max unplanned downtime $\le 21.9$ minutes/month).
  - **NFR-02 (Cutover Window):** Maximum cutover downtime window $\le 30$ minutes per application during scheduled maintenance.
  - **NFR-03 (API Latency):** 95th percentile (P95) HTTP API response latency $\le 200\text{ ms}$ under 1,000 req/sec.
  - **NFR-04 (DB Latency):** 99th percentile (P99) transaction execution time $\le 50\text{ ms}$.
  - **NFR-05 (Data Loss / RPO):** Recovery Point Objective (RPO) = 0 seconds (zero transaction loss).
  - **NFR-06 (Data Integrity):** Pre-cutover checksum validation with 100% SHA-256 hash parity match between source and target DBs.
  - **NFR-07 (Security Standards):** 100% data at rest encrypted with AES-256, all data in transit encrypted using TLS 1.3.
  - **NFR-08 (Maintainability / RTO):** Recovery Time Objective (RTO) $\le 15$ minutes automated rollback & 100% IaC zero drift.

---

### 2. Software Architecture & UML Diagram Package
The [UML Package (Diagrams)](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29) folder contains five high-resolution diagrams illustrating system structure and dynamics:
1. **[Use Case Diagram](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/Use%20Case.png):** Visualizes user roles (System Admin, DevOps Engineer, Business User, Executive) interacting with core migration use cases including IaC deployment, staging sync, blackout check, cutover trigger, and rollback.
2. **[Class Diagram](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/Class.png):** Defines core object structures: `MigrationController`, `BlackoutGuard`, `CDCEngine`, `IaCManager`, `RollbackController`, `SyntheticMonitor`, and `SecretsStore`, emphasizing high cohesion and loose coupling.
3. **[Sequence Diagram](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/Sequence.png) ('Cut Over One Application'):** Traces exact execution sequence during an application cutover, detailing pre-cutover SHA-256 hash audit, BlackoutGuard verification, low-TTL DNS rerouting, synthetic probe validation, and automated rollback fallback.
4. **[Activity Diagram](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/Activity.png):** Maps workflow logic from initial discovery and IaC setup through continuous CDC data synchronization, blackout enforcement, production cutover, health check validation, and hardware decommissioning.
5. **[State Diagram](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/State.png):** Details lifecycle state transitions of a target application: `OnPremiseActive` $\rightarrow$ `StagingSyncing` $\rightarrow$ `PreCutoverValidated` $\rightarrow$ `CutoverPending` $\rightarrow$ `CloudActive` (or `RollbackTriggered` $\rightarrow$ `OnPremiseActive`).

---

### 3. Project Management & CPM Network Scheduling

#### Critical Path Method (CPM) Task Breakdown (Tasks T01 to T10)

| Task ID | Task Description | Duration ($D$) | Early Start (ES) | Early Finish (EF) | Late Start (LS) | Late Finish (LF) | Total Float (TF) | Critical Path? |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **T01** | Discovery & Legacy Codebase Assessment | 3d | 0 | 3 | 0 | 3 | 0d | **Yes** |
| **T02** | Cloud Landing Zone & IaC Setup | 3d | 3 | 6 | 3 | 6 | 0d | **Yes** |
| **T03** | Wave 1 Migration Execution (App1–App3) | 10d | 6 | 16 | 6 | 16 | 0d | **Yes** |
| **T04** | Wave 1 Cutover & Verification | 2d | 16 | 18 | 16 | 18 | 0d | **Yes** |
| **T05** | Wave 2 Migration Execution (App4–App6) | 15d | 18 | 33 | 18 | 33 | 0d | **Yes** |
| **T06** | Wave 2 Cutover & Verification | 2d | 33 | 35 | 33 | 35 | 0d | **Yes** |
| **T07** | Wave 3 Migration Execution (App7–App9 PERT) | 22d | 35 | 57 | 35 | 57 | 0d | **Yes** |
| **T08** | Wave 3 Cutover & Verification | 3d | 57 | 60 | 57 | 60 | 0d | **Yes** |
| **T09** | Decommissioning & Project Handover | 2d | 60 | 62 | 60 | 62 | 0d | **Yes** |
| **T10** | Non-blocking Security Audit | 5d | 6 | 11 | 30 | 35 | **24d** | No |

- **Critical Path Chain:** $\text{T01} \rightarrow \text{T02} \rightarrow \text{T03} \rightarrow \text{T04} \rightarrow \text{T05} \rightarrow \text{T06} \rightarrow \text{T07} \rightarrow \text{T08} \rightarrow \text{T09}$
- **CPM Active Critical Path Duration:** $3 + 3 + 10 + 2 + 15 + 2 + 22 + 3 + 2 = \mathbf{62\text{ Working Days}}$.
- **Non-Critical Path Float:** Task T10 has a **Total Float of 24 working days** ($LS = 30, ES = 6 \rightarrow TF = 30 - 6 = 24\text{d}$).

---

### 4. Software Estimation Workbook & PERT Math

#### Baseline vs. PERT 3-Point Estimate Comparison
- **Documented Applications (App1–App6):** Supplied single-point baseline efforts total $8 + 10 + 6 + 12 + 9 + 15 = \mathbf{60.00\text{ person-days}}$.
- **Undocumented Legacy Applications (App7–App9):** Initial single-point estimates totaled $20 + 18 + 22 = \mathbf{60.00\text{ person-days}}$.
- **Initial Baseline Project Total:** $60 + 60 = \mathbf{120.00\text{ person-days}}$ ($\text{Duration} = 120 / 3 = \mathbf{40\text{ working days}}$).

#### PERT Three-Point Calculation for App7, App8, App9
Given Optimistic $O = 12.00\text{ pd}$, Most Likely $M = 20.00\text{ pd}$, Pessimistic $P = 40.00\text{ pd}$ per app:

1. **Expected Effort ($TE$):**
   $$TE = \frac{O + 4M + P}{6} = \frac{12 + 4(20) + 40}{6} = \frac{132}{6} = \mathbf{22.00\text{ person-days per app}}$$

2. **Standard Deviation ($\sigma$) & Variance ($\sigma^2$):**
   $$\sigma = \frac{P - O}{6} = \frac{40 - 12}{6} = \frac{28}{6} = \mathbf{4.67\text{ person-days}}$$
   $$\sigma^2 = (4.67)^2 = \mathbf{21.78\text{ person-days}^2\text{ per app}}$$

3. **Re-estimated Total Project Effort:**
   $$\text{Total Effort} = 60\text{ (Documented)} + (22.00 \times 3)\text{ (Undocumented)} = 60 + 66 = \mathbf{126.00\text{ person-days}}$$
   *(Introduces a variance of $+6.00\text{ person-days}$ or $+5.0\%$ over baseline effort).*

4. **Statistical Confidence & Planning Range (95% CI):**
   $$\text{Planning Range (per app)} = TE \pm 1.96 \sigma = 22.00 \pm 1.96(4.67) = 22.00 \pm 9.15\text{ days } (\mathbf{12.85\text{ to } 31.15\text{ days}})$$
   $$\sigma_{\text{total}} = \sqrt{3 \times 21.78} = \sqrt{65.33} = \mathbf{8.08\text{ person-days}}$$

#### Capacity Planning & Reconciliation of Durations
- **Team Allocation:** 3 full-time migration engineers.
- **Pure Productive Engineering Migration Duration ($D_{\text{effort}}$):**
  $$D_{\text{effort}} = \frac{126\text{ person-days}}{3\text{ engineers}} = \mathbf{42.00\text{ working days}}$$
- **Monthly Blackout Adjustment ($W_{\text{eff}}$):**
  $$W_{\text{eff}} = 22\text{ gross working days/month} - 5\text{ blackout days} = \mathbf{17.00\text{ effective days/month}}$$
- **Effective Duration in Months:**
  $$\text{Effective Months} = \frac{42.00\text{ working days}}{17.00\text{ days/month}} = \mathbf{2.47\text{ effective months}}$$
- **Deadline Feasibility:** $2.47\text{ months} \ll 7.0\text{ months}$ server EOL deadline ($4.53$ months safety buffer).
- **Reconciliation Note:** The **42 working days** ($D_{\text{effort}}$) represent active engineering labor effort duration. The **62 working days** ($D_{\text{cpm}}$) represent the active CPM network schedule including lifecycle overheads (T01 Discovery=3d, T02 Setup=3d, cutover verifications=7d, handover=2d).

---

### 5. Quality Assurance, Test Evidence & Metrics

#### Boundary Value Analysis (BVA) & Equivalence Partitioning (EP)
For working day variable $W \in [1, 22]$ in each month:
- **Equivalence Partition 1 (Valid Migration Window):** $W \in [1, 17]$. Cutover permitted.
- **Equivalence Partition 2 (Invalid Blackout Freeze Window):** $W \in [18, 22]$. Cutover blocked.
- **BVA Boundary Values:**
  - $W = 17$ (Last Permitted Day, $18 - 1$): System ALLOWS migration cutover.
  - $W = 18$ (First Blackout Day, min blackout boundary): System BLOCKS cutover with error `Blackout Active`.
  - $W = 22$ (Max Blackout Day): Cutover BLOCKED.

#### Go/No-Go Cutover Decision Table

| Condition / Rule | Rule 1 | Rule 2 | Rule 3 | Rule 4 | Rule 5 | Rule 6 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **C1: SHA-256 Checksum Parity Match** | True | False | True | True | True | True |
| **C2: Month-End Blackout Active** | False | False | **True** | False | False | False |
| **C3: Synthetic Health Probes Passed** | True | True | True | **False** | True | True |
| **C4: Automated Rollback Target Verified**| True | True | True | True | **False** | True |
| **Outcome Action** | **APPROVE CUTOVER** | **ABORT & RE-SYNC** | **BLOCK (BLACKOUT)**| **TRIGGER ROLLBACK**| **BLOCK (UNSAFE)** | **APPROVE CUTOVER** |

#### Defect Metrics & Quality Computations
- **Pre-Release Staging Defects ($E_{\text{pre}}$):** 18 defects (DEF-01 through DEF-18).
- **Post-Migration Warranty Defects ($E_{\text{post}}$):** 2 defects (POST-01, POST-02).
- **Total Defect Count ($E_{\text{total}}$):** $18 + 2 = \mathbf{20\text{ defects}}$.

1. **Defect Removal Efficiency (DRE):**
   $$DRE = \left(\frac{E_{\text{pre}}}{E_{\text{pre}} + E_{\text{post}}}\right) \times 100\% = \left(\frac{18}{18 + 2}\right) \times 100\% = \left(\frac{18}{20}\right) \times 100\% = \mathbf{90.0\%}$$

2. **Defect Density per Application:**
   $$\text{Defect Density}_{\text{app}} = \frac{20\text{ defects}}{9\text{ applications}} = \mathbf{2.22\text{ defects/application}}$$

3. **Defect Density per Person-Day of Migration Effort:**
   $$\text{Defect Density}_{\text{effort}} = \frac{20\text{ defects}}{126\text{ person-days}} = \mathbf{0.159\text{ defects/person-day}}$$

---

### 6. Risk Register, RMMM & Closure Summary

#### Risk Exposure Ranking Matrix ($E = P \times I$)

| Risk ID | Description | Category | Probability ($P$) | Impact ($I$) | Exposure ($E$) | Exposure Rating | Response Strategy | Risk Owner |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **R-01** | Undocumented Legacy Code (App7–9) | Technical Debt | 4 | 5 | **20** | **HIGH** | Mitigate (Containerize, PERT, Profiling) | Eng 3 / Lead |
| **R-02** | Month-End Blackout Window Violation | Operational | 3 | 5 | **15** | **HIGH** | Avoid (BlackoutGuard Pipeline Lock) | DevOps Lead |
| **R-04** | On-Premise Server Hardware Failure | Infrastructure | 3 | 4 | **12** | **MEDIUM** | Mitigate (Wave Sequencing, IaC Ready) | Infra Lead |
| **R-03** | Data Loss During Migration Cutover | Data Integrity | 2 | 5 | **10** | **MEDIUM** | Mitigate (CDC Real-time Sync, Hash Audit) | Eng 1 / DBA |
| **R-05** | DNS Propagation Lag | Networking | 2 | 3 | **6** | **LOW** | Mitigate (Reduce Route53 TTL to 60s) | Network Lead |
| **R-06** | Cloud Operating Cost Overrun | Financial | 2 | 2 | **4** | **LOW** | Accept (CloudWatch Budget Alerts) | CIO |

#### RMMM Summary for Top 3 Risks
- **R-01 (Undocumented Code, $E=20$):** Dynamic memory profiling, Docker containerization to bypass legacy OS dependencies, PERT schedule buffer ($TE=22\text{d}$), automated code scanner. Fallback to VM re-hosting if refactoring stalls.
- **R-02 (Blackout Violation, $E=15$):** Embed `BlackoutGuard` middleware into CI/CD pipeline triggering automated lock on Day 18. Real-time dashboard countdown. Auto-abort cutover at 23:59 on Day 17.
- **R-04 (Hardware Failure, $E=12$):** Sequence high-risk applications in Wave 1 and Wave 2, maintain pre-configured IaC landing zone, perform daily offsite backups and SMART disk health monitoring.

#### Historical Issue Log (Isolated Past Incidents)
- **ISS-01 (Billing App Outage - Prior Year):** 2-day outage due to on-premise RAID controller failure. *Resolution:* Replaced single physical server with cloud Multi-AZ Auto Scaling Group ($\ge 99.95\%$ uptime).
- **ISS-02 (Power Supply Degradation - 6 Months Ago):** 4-hour outage due to UPS battery failure. *Resolution:* Migrated infrastructure to public cloud with multi-region backup.

#### Simulated Project Closure Note
- 100% of 9 applications operational in cloud infrastructure.
- All 8 NFR targets verified via load testing and synthetic benchmarks.
- On-premise office servers powered down and prepped for disposal. Operational handoff completed.

---

## Instructions for Repository Navigation & Verification

1. **View SRS Requirements & RTM:** Read [01.CloudShift - SRS-Document.pdf](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/01.CloudShift%20-%20SRS-Document.pdf) for complete functional and non-functional specifications.
2. **Inspect Architectural Diagrams:** Open the [UML Package (Diagrams)](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29) folder to review [Use Case](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/Use%20Case.png), [Class](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/Class.png), [Sequence](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/Sequence.png), [Activity](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/Activity.png), and [State](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/UML%20Package%20%28Diagrams%29/State.png) diagrams.
3. **Review WBS & CPM Schedule:** Consult [03. CloudShift - Project Plan.pdf](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/03.%20CloudShift%20-%20Project%20Plan.pdf) for task dependency tables, forward/backward pass calculations, and float values.
4. **Examine PERT Estimation Workbook:** Check [04. CloudShift - Estimation Workbook.pdf](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/04.%20CloudShift%20-%20Estimation%20Workbook.pdf) for step-by-step mathematical proofs and capacity planning.
5. **Analyze QA & Test Evidence:** Inspect [05. CloudShift - Test Plan and Evidence.pdf](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/05.%20CloudShift%20-%20Test%20Plan%20and%20Evidence.pdf) for the 20-defect log, BVA blackout analysis, and DRE calculations.
6. **Review Risk Register & Closure Note:** Read [06. CloudShift - Risk Register and Closure Note.pdf](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/06.%20CloudShift%20-%20Risk%20Register%20and%20Closure%20Note.pdf) for 5x5 exposure scores, RMMM plans, and closure state.
7. **Oral Viva Voce Preparation:** Study [CloudShift_Project_Explanation_and_Viva.pdf](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/CloudShift_Project_Explanation_and_Viva.pdf) for 10 comprehensive questions and answers covering software engineering and project management concepts.
