# CloudShift Migration Project
## Deliverable 5: Test Plan, BVA, Decision Tables & QA Evidence
**Document ID:** CS-TEST-2026-V1.0  
**Project:** CloudShift - On-Premise to Cloud Migration  

---

### 1. Test Strategy & Scope

The testing strategy validates that all 9 applications migrate cleanly to the cloud with zero data corruption, full blackout compliance, and instant rollback execution if cutover failures occur.

---

### 2. System Test Cases for Functional Requirements (FR-01 to FR-12)

| Test Case ID | Requirement | Test Objective | Test Input / Action | Expected Result | Pass/Fail Criteria |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **TC-FR-01** | FR-01 (CDC Sync) | Validate real-time CDC replication lag between DBs | Insert 5,000 DB records into on-prem DB | Target cloud DB mirrors records with lag $< 100\text{ ms}$ | PASS if lag $< 100\text{ ms}$ |
| **TC-FR-02** | FR-02 (Schema Trans) | Verify automated schema mapping accuracy | Run schema transformation tool | 100% table schemas, constraints, and indexes migrated | PASS if 0 schema errors |
| **TC-FR-03** | FR-03 (DNS Reroute) | Test DNS cutover and load balancer swap | Execute DNS update script | Traffic routes 100% to cloud IP within propagation TTL | PASS if traffic on cloud IP |
| **TC-FR-04** | FR-04 (Blackout Lock) | Test deployment pipeline locking during blackout | Trigger migration script on Day 19 of month | Execution blocked with error message: "Blackout Active" | PASS if execution blocked |
| **TC-FR-05** | FR-05 (Session Migr) | Validate active user session migration to Redis | User logged in on-prem, trigger session sync | User remains authenticated post-cutover without login | PASS if session active |
| **TC-FR-06** | FR-06 (IaC Provision) | Test Terraform script provisioning | Run `terraform apply` | VPC, Subnets, Security Groups, and K8s cluster created | PASS if 0 IaC errors |
| **TC-FR-07** | FR-07 (Config Mgmt) | Verify dynamic secret injection | Launch app container with parameter store | DB credentials loaded securely from parameter store | PASS if app connects |
| **TC-FR-08** | FR-08 (IAM & RBAC) | Validate SSO RBAC authorization | Log in as Non-Admin & Admin | Non-Admin denied migration triggers; Admin allowed | PASS if RBAC enforced |
| **TC-FR-09** | FR-09 (Synthetic Mon) | Test automated 30s health check bot | Inject simulated 500 error in staging | Synthetic monitor detects failure within $\le 30\text{ s}$ | PASS if alert fired |
| **TC-FR-10** | FR-10 (Auto Rollback) | Verify automated DNS rollback execution | Fail health check post-cutover | DNS automatically reverted to on-prem within $\le 15\text{ min}$ | PASS if RTO $\le 15\text{ min}$ |
| **TC-FR-11** | FR-11 (Audit Log) | Validate append-only audit trail | Perform cutover action | Audit log records event, timestamp, user ID, SHA256 hash | PASS if log immutable |
| **TC-FR-12** | FR-12 (Dashboard) | Verify management UI dashboard metrics | Open dashboard during CDC sync | Displays real-time replication lag, blackout timer, status | PASS if metrics match |

---

### 3. Boundary Value Analysis (BVA) for Month-End Blackout Window

#### 3.1 Field Specification
- **Input Variable:** Working Day of the Month ($W \in [1, 22]$)
- **Blackout Window Policy:** Last 5 working days of the month ($W \in [18, 22]$) are BLACKOUT DAYS (cutover disabled). Days $W \in [1, 17]$ are PERMITTED DAYS (cutover enabled).

#### 3.2 BVA Test Cases

| BVA Case ID | Working Day Value ($W$) | Boundary Classification | Expected System Behavior | Test Result |
| :--- | :--- | :--- | :--- | :--- |
| **BVA-01** | $W = 0$ | Below Min (Invalid) | Input Error / Rejected | PASS |
| **BVA-02** | $W = 1$ | Min Valid Working Day | Cutover ALLOWED (Normal Operation) | PASS |
| **BVA-03** | $W = 2$ | Min + 1 | Cutover ALLOWED (Normal Operation) | PASS |
| **BVA-04** | $W = 16$ | Nominal Non-Blackout Day | Cutover ALLOWED (Normal Operation) | PASS |
| **BVA-05** | $W = 17$ | **Boundary: Last Permitted Day** ($18 - 1$) | Cutover ALLOWED (Normal Operation) | PASS |
| **BVA-06** | $W = 18$ | **Boundary: First Blackout Day** (Min Blackout) | Cutover BLOCKED (Blackout Lock Active) | PASS |
| **BVA-07** | $W = 19$ | Inside Blackout Window | Cutover BLOCKED (Blackout Lock Active) | PASS |
| **BVA-08** | $W = 21$ | Inside Blackout Window | Cutover BLOCKED (Blackout Lock Active) | PASS |
| **BVA-09** | $W = 22$ | **Boundary: Last Blackout Day** (Max Blackout) | Cutover BLOCKED (Blackout Lock Active) | PASS |
| **BVA-10** | $W = 23$ | Above Max Working Days (Invalid) | Input Error / Rejected | PASS |

---

### 4. Equivalence Partitioning (EP) for Migration Window

| Partition ID | Working Day Range | Partition Class | System Expected Behavior |
| :--- | :--- | :--- | :--- |
| **EP-01** | $W < 1$ | Invalid Low | System Rejects Request (Invalid Day Error) |
| **EP-02** | $1 \le W \le 17$ | Valid Permitted Window | System Permits Migration & Cutover |
| **EP-03** | $18 \le W \le 22$ | Valid Blackout Window | System Locks Cutover Pipeline (Blackout Enforcement) |
| **EP-04** | $W > 22$ | Invalid High | System Rejects Request (Invalid Day Error) |

---

### 5. Decision Table for Cut-over Go/No-Go Rules

#### 5.1 Condition Definitions
- **$C_1$ (Checksum Match):** Pre-cutover DB SHA-256 checksum parity $= 100\%$.
- **$C_2$ (Blackout Active):** Current calendar date falls within last 5 working days of month.
- **$C_3$ (Health Check Pass):** Synthetic test returns HTTP 200 & API P95 latency $\le 200\text{ ms}$.
- **$C_4$ (Rollback Verified):** Automated rollback target and DB backup verified.

#### 5.2 Decision Matrix

| Rule ID | $C_1$ Checksum 100% | $C_2$ Blackout Active | $C_3$ Health Check Pass | $C_4$ Rollback Verified | Decision Outcome / Action |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **R-01** | **True** | **False** | **True** | **True** | **GO:** Execute Traffic Cutover to Cloud |
| **R-02** | **True** | **True** | True | True | **NO-GO:** Defer Cutover (Blackout Window Active) |
| **R-03** | **False** | False | True | True | **NO-GO:** Abort Cutover (Data Checksum Mismatch) |
| **R-04** | True | False | **False** | True | **NO-GO:** Trigger Automated Rollback to On-Prem |
| **R-05** | True | False | True | **False** | **NO-GO:** Abort Cutover (Rollback Plan Unverified) |
| **R-06** | False | True | False | False | **NO-GO:** Abort Cutover (Multiple Safety Failures) |

---

### 6. Comprehensive Defect Log (Pre-Release & Post-Release Evidence)

#### 6.1 Pre-Release Dry-Run & Staging Defect Evidence ($E_{\text{pre}} = 18\text{ defects}$)

| Defect ID | Associated App | Summary / Description | Severity | Discovery Phase | Status | Root Cause & Resolution |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **DEF-01** | App1 | CDC replication buffer overflow during load test | Major | Staging Sync | Closed | Buffer size increased from 128MB to 512MB |
| **DEF-02** | App2 | DNS TTL propagation delayed beyond 5 minutes | Medium | Wave 1 Cutover | Closed | TTL lowered from 86400s to 60s in Route53 |
| **DEF-03** | App7 | Legacy hardcoded DB connection string crashed app | Critical | Wave 3 PERT | Closed | Replaced with environment parameter injection |
| **DEF-04** | App4 | Blackout Guard allowed trigger on 18th working day | Critical | Integration Test | Closed | Corrected boundary operator from `>` to `>=` |
| **DEF-05** | App3 | Redis session token missing expiration header | Low | Wave 1 Staging | Closed | Added default 24h TTL policy in Redis |
| **DEF-06** | App8 | Deprecated PHP 5.6 module incompatible with cloud OS | High | Reverse Eng | Closed | Containerized legacy PHP runtime in Docker |
| **DEF-07** | App5 | P95 latency reached 340ms under 1,000 req/s | Medium | Load Testing | Closed | Added read-replica DB & index tuning |
| **DEF-08** | App9 | Missing table primary key caused CDC checksum failure | High | Data Validation | Closed | Created synthetic unique index on target schema |
| **DEF-09** | App6 | Automated rollback script timed out at 18 minutes | Critical | DR Simulation | Closed | Parallelized container shutdown (RTO now 8m) |
| **DEF-10** | App2 | Audit logger dropped logs during network partition | Low | Security Audit | Closed | Implemented local disk queue for log spooling |
| **DEF-11** | App1 | SSL/TLS certificate handshake mismatch on ALB | Medium | Security Testing | Closed | Updated IAM certificate ARN binding |
| **DEF-12** | App3 | Heap memory leak during 24-hour continuous CDC sync | Major | Endurance Test | Closed | Fixed unclosed DB connection pool handle |
| **DEF-13** | App4 | Dynamic secrets parameter fetch timeout on cold start | High | Integration Test | Closed | Added SDK retry policy with exponential backoff |
| **DEF-14** | App5 | Concurrent session token collision under traffic spikes | Medium | Performance Test | Closed | Updated session token generator to UUIDv4 |
| **DEF-15** | App6 | Synthetic health check bot false positive alert | Low | Monitoring Test | Closed | Adjusted synthetic probe retry threshold to 3 |
| **DEF-16** | App7 | Legacy hardcoded file path missing on Linux instance | Critical | Staging Execution | Closed | Refactored path to S3 object bucket adapter |
| **DEF-17** | App8 | Missing DB index on invoice timestamp query | Medium | DB Audit | Closed | Created B-tree index on `invoice_created_at` |
| **DEF-18** | App9 | System daemon failed to auto-restart on reboot | High | System Test | Closed | Configured systemd unit file with auto-restart |

#### 6.2 Post-Migration Warranty Defects ($E_{\text{post}} = 2\text{ defects}$)

| Defect ID | Associated App | Summary / Description | Severity | Discovery Phase | Status | Root Cause & Resolution |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **POST-01** | App7 | Minor UI alignment artifact on legacy report page | Low | Post-Migration Warranty | Closed | CSS rule updated in hotfix patch |
| **POST-02** | App2 | Intermittent 503 on legacy batch export route | Minor | Post-Migration Warranty | Closed | Increased container worker thread limit |

---

### 7. Quality Metrics Computation

#### 7.1 Given Metrics Log Data
- **Pre-Release Dry-Run Defects ($E_{\text{pre}}$):** 18 defects
- **Post-Migration Warranty Defects ($E_{\text{post}}$):** 2 minor defects
- **Total Applications ($N_{\text{apps}}$):** 9 applications
- **Total Project Effort ($E_{\text{total}}$):** 126 person-days

#### 7.2 Defect Removal Efficiency (DRE) Calculation

$$\text{DRE} = \left( \frac{E_{\text{pre}}}{E_{\text{pre}} + E_{\text{post}}} \right) \times 100\%$$

$$\text{DRE} = \left( \frac{18}{18 + 2} \right) \times 100\% = \left( \frac{18}{20} \right) \times 100\% = \mathbf{90.0\%}$$

#### 7.3 Defect Density Calculations

1. **Defect Density per Application:**
$$\text{Defect Density}_{\text{app}} = \frac{E_{\text{pre}} + E_{\text{post}}}{N_{\text{apps}}} = \frac{20\text{ defects}}{9\text{ applications}} = \mathbf{2.22\text{ defects/application}}$$

2. **Defect Density per Person-Day of Migration Effort:**
$$\text{Defect Density}_{\text{effort}} = \frac{E_{\text{pre}} + E_{\text{post}}}{E_{\text{total}}} = \frac{20\text{ defects}}{126\text{ person-days}} = \mathbf{0.159\text{ defects/person-day}}$$

---
