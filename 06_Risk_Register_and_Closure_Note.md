# CloudShift Migration Project
## Deliverable 6: Risk Register, RMMM, Issue Log & Closure Note
**Document ID:** CS-RISK-2026-V1.0  
**Project:** CloudShift - On-Premise to Cloud Migration  

---

### 1. Probability-Impact Risk Matrix (5x5 Grid)

The matrix below maps project risks across a 5x5 Probability ($P \in [1, 5]$) vs. Impact ($I \in [1, 5]$) grid. Exposure score is calculated as $E = P \times I$.

- **High Exposure ($E = 15\text{–}25$):** Requires immediate proactive RMMM mitigation plan.
- **Medium Exposure ($E = 8\text{–}12$):** Requires active monitoring and contingency triggers.
- **Low Exposure ($E = 1\text{–}6$):** Acceptable risk; monitored via standard logs.

```
       +-------------------------------------------------------------------------+
       |                        IMPACT RATING (1 to 5)                           |
       +------------------+------------------+------------------+----------------+
PROB.  | 1: Negligible    | 2: Minor         | 3: Moderate      | 4: Major       | 5: Critical
-------+------------------+------------------+------------------+----------------+------------------+
5: Very| Score: 5 (LOW)   | Score: 10 (MED)  | Score: 15 (HIGH) | Score: 20(HIGH)| Score: 25 (HIGH) |
High   |                  |                  |                  |                |                  |
-------+------------------+------------------+------------------+----------------+------------------+
4: High| Score: 4 (LOW)   | Score: 8 (MED)   | Score: 12 (MED)  | Score: 16(HIGH)| [R-01]           |
       |                  |                  |                  |                | Score: 20 (HIGH) |
-------+------------------+------------------+------------------+----------------+------------------+
3: Med | Score: 3 (LOW)   | Score: 6 (LOW)   | Score: 9 (MED)   | [R-04]         | [R-02]           |
       |                  |                  |                  | Score: 12 (MED)| Score: 15 (HIGH) |
-------+------------------+------------------+------------------+----------------+------------------+
2: Low | Score: 2 (LOW)   | [R-06]           | [R-05]           | Score: 8 (MED) | [R-03]           |
       |                  | Score: 4 (LOW)   | Score: 6 (LOW)   |                | Score: 10 (MED)  |
-------+------------------+------------------+------------------+----------------+------------------+
1: V.Low| Score: 1 (LOW)  | Score: 2 (LOW)   | Score: 3 (LOW)   | Score: 4 (LOW) | Score: 5 (LOW)   |
-------+------------------+------------------+------------------+----------------+------------------+
```

---

### 2. Risk Register Table

Exposure Score ($E = P \times I$) where Probability ($P \in [1, 5]$) and Impact ($I \in [1, 5]$). The top 3 ranked risks by exposure are **R-01 ($E=20$)**, **R-02 ($E=15$)**, and **R-04 ($E=12$)**.

| Risk ID | Risk Description | Category | Probability ($P$) | Impact ($I$) | Exposure ($E = P \times I$) | Response Strategy | Risk Owner |
| :--- | :--- | :--- | :---: | :---: | :---: | :--- | :--- |
| **R-01** | **Undocumented Legacy Code (App7–9):** Hidden hardcoded dependencies or missing source files delay migration. | Technical Debt | 4 | 5 | **20 (HIGH)** | **Mitigate** (Reverse eng, PERT estimation, containerization) | Eng 3 / Lead |
| **R-02** | **Blackout Window Violation:** Migration or cutover bleeds into last 5 working days of month, interrupting billing. | Operational | 3 | 5 | **15 (HIGH)** | **Avoid** (Automated Blackout Guard lock service) | Eng 2 / Infra |
| **R-04** | **On-Premise Server Hardware Failure:** Ageing servers crash before 7-month contract expiry. | Infrastructure | 3 | 4 | **12 (MEDIUM)** | **Mitigate** (Prioritize high-risk apps in early waves) | Cloud Eng |
| **R-03** | **Data Loss during Cutover:** Database replication desynchronization causes missing transactions. | Data Integrity | 2 | 5 | **10 (MEDIUM)** | **Mitigate** (CDC real-time sync, SHA-256 pre-cutover hash audit) | Eng 1 / DBA |
| **R-05** | **DNS Propagation Lag:** Extended DNS cached TTL causes temporary access drop for end users. | Networking | 2 | 3 | **6 (LOW)** | **Mitigate** (Reduce Route53 TTL to 60s 48h prior to cutover) | Network Lead |
| **R-06** | **Cloud Cost Overrun:** Provisioned cloud resources exceed initial monthly operating budget. | Financial | 2 | 2 | **4 (LOW)** | **Accept** (Set up CloudWatch budget alerts and auto-scaling) | CIO |

---

### 3. RMMM Plans for Top 3 Ranked Risks (R-01, R-02, R-04)

#### 3.1 Risk 1 (R-01): Undocumented Legacy Codebase (App7, App8, App9)
- **Risk Exposure:** $E = 4 \times 5 = 20$ (Rank #1)
- **Risk Mitigation:**
  1. Conduct static code analysis and dynamic memory profiling to discover runtime dependencies.
  2. Encapsulate legacy runtimes in Docker container images to bypass OS-level library incompatibilities.
  3. Apply 3-point PERT estimation ($TE = 22\text{ person-days}$) with a 95% confidence buffer.
- **Risk Monitoring:** Track daily execution velocity against PERT estimates. If reverse engineering exceeds 15 person-days per app, trigger secondary senior developer review.
- **Risk Management / Contingency:** Fall back to re-hosting (lift-and-shift VM encapsulation) if code refactoring stalls beyond threshold.

#### 3.2 Risk 2 (R-02): Month-End Blackout Window Violation
- **Risk Exposure:** $E = 3 \times 5 = 15$ (Rank #2)
- **Risk Mitigation:**
  1. Program `BlackoutGuard` microservice directly into the CI/CD deployment pipeline.
  2. Implement automated pipeline locking triggered on Day 18 of every working month.
- **Risk Monitoring:** Real-time countdown timer displayed on the CloudShift Executive Dashboard (FR-12).
- **Risk Management / Contingency:** If cutover is incomplete by 23:59 on Day 17, automatically abort deployment and defer cutover to Day 1 of the following month.

#### 3.3 Risk 3 (R-04): On-Premise Server Hardware Failure
- **Risk Exposure:** $E = 3 \times 4 = 12$ (Rank #3)
- **Risk Mitigation:**
  1. Prioritize migration sequence for mission-critical apps on oldest hardware servers in Wave 1 and Wave 2.
  2. Maintain pre-configured IaC cloud landing zone ready for emergency instant workload deployment.
  3. Perform daily full database and image backups of all remaining on-premise hosts.
- **Risk Monitoring:** Daily automated SMART health monitoring of disk arrays and CPU temperature logs on legacy office servers.
- **Risk Management / Contingency:** If on-premise hardware experiences critical failure prior to scheduled wave cutover, trigger emergency accelerated lift-and-shift migration to cloud staging environment.

---

### 4. Separate Historical Issue Log (Past Occurrences)

This log isolates problems that **have already occurred** in legacy on-premise operations prior to project CloudShift.

| Issue ID | Incident Description | Occurrence Date | Impact / Outage | Root Cause Analysis | Preventative CloudShift Guardrail | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **ISS-01** | **Billing Application Server Failure** | Prior Year | **2-Day Total Outage** of billing app | Unhandled hardware RAID controller failure on ageing office server without failover. | Replaced single server with multi-AZ cloud auto-scaling group (NFR-01: 99.95% availability). | **RESOLVED** |
| **ISS-02** | **On-Premise Power Supply Degradation** | 6 Months Ago | 4-Hour Intermittent Outage | UPS battery failure during office building power fluctuation. | Cloud migration eliminates local power dependency; cloud multi-region backup implemented. | **RESOLVED** |

---

### 5. Project Closure Note & Lessons-Learned Summary

> **"The following closure results represent the simulated completion of the CloudShift case study based on the defined requirements, estimates and test evidence."**

#### 5.1 Key Lessons Learned
1. **Automated Policy Enforcement is Essential:** Relying on manual developer promises to avoid month-end blackouts failed in past projects. Hardcoded programmatic pipeline locks (`BlackoutGuard`) eliminate human error.
2. **PERT Estimation Mitigates Technical Debt:** Undocumented legacy applications (App7–9) carry fat-tailed risk distributions ($P = 40\text{ days}$). PERT three-point calculations successfully provided realistic schedule buffers ($TE = 22\text{ days}$).
3. **Automated Rollback Prevents Outages:** Designing an automated RTO $\le 15\text{ min}$ rollback mechanism guarantees that failed cutovers do not replicate last year's 2-day outage.

#### 5.2 Operational Handoff Checklist
- [x] 100% of 9 applications live in public cloud infrastructure.
- [x] All 8 NFR targets verified via synthetic tests and load benchmarks.
- [x] On-premise office hardware powered down and prepped for disposal.
- [x] Support contracts formally transferred to Cloud Operations Team.

---
