# Case Study No. 92: CloudShift - On-Premise to Cloud Web Application Migration
**Course:** Software Engineering & Project Management (Semester III)  
**Program:** B.Tech CSE (2025–29) | ITM Skills University - School of Future Tech  
**Project Name:** CloudShift  

---

## Executive Summary & Index of Deliverables

This repository contains the complete, rigorous software engineering solution and project management deliverables for **Case Study No. 92: CloudShift**. 

A logistics enterprise currently runs 9 internal web applications on ageing on-premise office hardware with support contracts expiring in 7 months. To prevent a recurrence of last year's 2-day billing application outage and ensure zero interference with month-end financial closing, all 9 applications are systematically analyzed, re-estimated, scheduled, tested, and migrated to a modern cloud provider.

> **Case Study Simulation Note:**  
> *"The following closure results represent the simulated completion of the CloudShift case study based on the defined requirements, estimates and test evidence."*

---

## Deliverables Summary Matrix

| Deliverable # | Title | Key Artifacts Included | Core Metrics / Outcomes |
| :--- | :--- | :--- | :--- |
| **Deliverable 1** | [01_SRS_Document.md](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/01_SRS_Document.md) | IEEE 830 SRS Standard, 12 Functional Requirements, 8 Measurable NFRs, MoSCoW Matrix, RTM | NFR Uptime $\ge 99.95\%$, API P95 $\le 200\text{ ms}$, RPO = 0s, RTO $\le 15\text{ m}$ |
| **Deliverable 2** | [02_UML_Package.md](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/02_UML_Package.md) | Use Case, Class, Sequence ('Cut Over One Application'), Activity, State Diagrams, Architectural Justification | High cohesion in micro-modules; Loose coupling via IaC, CDC, & REST APIs |
| **Deliverable 3** | [03_Project_Plan.md](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/03_Project_Plan.md) | 5-Level WBS, 3-Engineer Allocation Matrix, Visual CPM Network Diagram, Gantt Chart with 22d Working-Day Cycles | Critical Path = 57 working days; Milestones embedded as $D=0$ checkpoints; T10 Float = **29d** |
| **Deliverable 4** | [04_Estimation_Workbook.md](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/04_Estimation_Workbook.md) | PERT 3-Point Estimate ($O=12, M=20, P=40$), Step-by-Step Math, Blackout Adjustment, 42d vs 57d Reconciliation | Initial Effort = 120 pd (40d); PERT Effort = 126 pd (42d / 2.47 effective months) |
| **Deliverable 5** | [05_Test_Plan_and_Evidence.md](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/05_Test_Plan_and_Evidence.md) | Test Strategy, 12 FR Test Cases, BVA for Blackout Window, EP, Cutover Decision Table, 20-Defect Log (18 Pre + 2 Post) | Defect Removal Efficiency (DRE) = **90.0%**; Defect Density = **2.22 defects/app** |
| **Deliverable 6** | [06_Risk_Register_and_Closure_Note.md](file:///Users/viketh/Desktop/Software%20Management%20-%20CloudShift/06_Risk_Register_and_Closure_Note.md) | Visual 5x5 Probability-Impact Grid, Risk Register ($P \times I$), Top 3 RMMM Plans (R-01, R-02, R-04), Issue Log, Closure Note | Top 3 Risks: R-01 ($E=20$), R-02 ($E=15$), R-04 ($E=12$); R-03 = Medium ($E=10$) |

---

## Quick Reference: Core Key Formulas & Numerical Results

### 1. Initial Effort & Duration
- **Initial Sum of Effort:** $8 + 10 + 6 + 12 + 9 + 15 + 20 + 18 + 22 = 120\text{ person-days}$
- **Initial Duration (3 Engineers):** $120 / 3 = 40\text{ working days}$

### 2. PERT Re-estimation for Undocumented Apps (App7, App8, App9)
- **PERT Expected Effort ($TE$):**
  $$TE = \frac{O + 4M + P}{6} = \frac{12 + 4(20) + 40}{6} = \frac{132}{6} = 22\text{ person-days/app}$$
- **Standard Deviation ($\sigma$):** $\sigma = \frac{40 - 12}{6} = 4.67\text{ person-days}$
- **Re-estimated Total Effort:** $60\text{ (Documented)} + 66\text{ (Undocumented)} = 126\text{ person-days}$
- **Re-estimated Migration Effort Duration (3 Engineers):** $126 / 3 = 42\text{ working days}$

### 3. Reconciliation: 42-Day Effort Estimation vs. 57-Day Schedule Duration
> *"The 42 working days represent the theoretical productive duration obtained by dividing the total estimated effort of 126 person-days among three engineers. The 57-working-day project schedule is the calendar schedule after applying task dependencies, migration-wave sequencing, blackout windows, and non-critical activities."*

- **Pure Engineering Migration Duration ($D_{\text{effort}}$):** $126\text{ person-days} / 3\text{ engineers} = \mathbf{42\text{ working days}}$ (Tasks T03, T05, T07).
- **Total Project Schedule Duration ($D_{\text{schedule}}$):** Adds Setup ($+6\text{d}$), Cutovers ($+7\text{d}$), and Handover ($+2\text{d}$):
  $$\mathbf{D_{\text{schedule}} = D_{\text{setup}} (6\text{d}) + D_{\text{effort}} (42\text{d}) + D_{\text{cutover}} (7\text{d}) + D_{\text{handover}} (2\text{d}) = 57\text{ Working Days}}$$

### 4. Monthly Blackout Window Policy
> *"Blackout windows represent the last five working days of each month, based on the case-study assumption of 22 working days per month."*

- **Effective Working Days/Month:** $22 - 5 = 17\text{ working days/month}$
- **Effective Months Required:**
  $$\text{Effective Months} = \frac{42\text{ working days}}{17\text{ days/month}} = 2.47\text{ months}$$
- **Deadline Feasibility:** $2.47\text{ months} \ll 7.0\text{ months}$ (Support contract deadline met with 4.53 months safety buffer).

### 5. Risk Exposure Ranking & Top 3 RMMM
- **Exposure Formula:** $E = P \times I$ ($P \in [1, 5], I \in [1, 5]$).
- **Top 3 Exposure Order:**
  1. **R-01 — Undocumented Legacy Code:** $P=4, I=5 \rightarrow E=\mathbf{20\text{ (HIGH)}}$
  2. **R-02 — Month-End Blackout Violation:** $P=3, I=5 \rightarrow E=\mathbf{15\text{ (HIGH)}}$
  3. **R-04 — On-Premise Server Hardware Failure:** $P=3, I=4 \rightarrow E=\mathbf{12\text{ (MEDIUM)}}$
- **R-03 Rating Correction:** Data Loss during Cutover ($P=2, I=5 \rightarrow E=10$) is categorized as **MEDIUM** (Medium range $= 8\text{–}12$).

### 6. Quality Assurance Metrics & Evidence Match
- **Pre-Release Dry-Run Defects ($E_{\text{pre}}$):** 18 defects (DEF-01 through DEF-18)
- **Post-Migration Warranty Defects ($E_{\text{post}}$):** 2 defects (POST-01, POST-02)
- **Defect Removal Efficiency (DRE):**
  $$DRE = \left(\frac{E_{\text{pre}}}{E_{\text{pre}} + E_{\text{post}}}\right) \times 100\% = \left(\frac{18}{18 + 2}\right) \times 100\% = \mathbf{90.0\%}$$
- **Defect Density per Application:** $20 / 9 = \mathbf{2.22\text{ defects/app}}$
