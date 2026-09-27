# CloudShift Migration Project
## Deliverable 4: Estimation Workbook & PERT Analysis
**Document ID:** CS-EST-2026-V1.0  
**Project:** CloudShift - On-Premise to Cloud Migration  

---

### 1. Initial Given Effort & Duration Calculations

#### 1.1 Input Data (Given Figures)
- **Team Size:** $N = 3$ engineers
- **Given Migration Effort per Application (person-days):**
  - App1: 8 person-days
  - App2: 10 person-days
  - App3: 6 person-days
  - App4: 12 person-days
  - App5: 9 person-days
  - App6: 15 person-days
  - App7 (Undocumented): 20 person-days
  - App8 (Undocumented): 18 person-days
  - App9 (Undocumented): 22 person-days

#### 1.2 Step-by-Step Initial Calculations

**Total Initial Effort ($E_{\text{initial}}$):**
$$E_{\text{initial}} = \sum_{i=1}^{9} \text{App}_i = 8 + 10 + 6 + 12 + 9 + 15 + 20 + 18 + 22 = 120\text{ person-days}$$

**Initial Project Duration ($D_{\text{initial}}$ in working days):**
$$D_{\text{initial}} = \frac{E_{\text{initial}}}{N} = \frac{120\text{ person-days}}{3\text{ engineers}} = 40\text{ working days}$$

---

### 2. Three-Point PERT Re-estimation for Undocumented Apps

#### 2.1 Given PERT Parameters for Undocumented Apps (App7, App8, App9)
For each undocumented app, the three-point estimates are:
- **Optimistic ($O$):** 12 person-days
- **Most Likely ($M$):** 20 person-days
- **Pessimistic ($P$):** 40 person-days

#### 2.2 PERT Expected Effort ($TE$), Variance ($\sigma^2$), and Standard Deviation ($\sigma$) Formulas

$$\text{Expected Effort } TE = \frac{O + 4M + P}{6}$$
$$\text{Standard Deviation } \sigma = \frac{P - O}{6}$$
$$\text{Variance } \sigma^2 = \left(\frac{P - O}{6}\right)^2$$

#### 2.3 Step-by-Step Computation per Undocumented App

$$TE = \frac{12 + 4(20) + 40}{6} = \frac{12 + 80 + 40}{6} = \frac{132}{6} = 22\text{ person-days}$$

$$\sigma = \frac{40 - 12}{6} = \frac{28}{6} \approx 4.667\text{ person-days}$$

$$\sigma^2 = (4.667)^2 = 21.778\text{ person-days}^2$$

#### 2.4 Comparison Matrix: Initial Given Values vs. PERT $TE$

| Application | Status | Initial Given Value | PERT $TE$ Value | Variance ($\sigma^2$) | Absolute Delta | Percentage Change |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **App7** | Undocumented | 20 person-days | 22 person-days | 21.78 | $+2$ person-days | $+10.0\%$ |
| **App8** | Undocumented | 18 person-days | 22 person-days | 21.78 | $+4$ person-days | $+22.2\%$ |
| **App9** | Undocumented | 22 person-days | 22 person-days | 21.78 | $0$ person-days | $0.0\%$ |
| **Subtotal (Apps 7-9)** | Undocumented | **60 person-days** | **66 person-days** | **65.34** | **$+6$ person-days** | **$+10.0\%$** |

#### 2.5 Re-Estimated Total Effort & Duration

$$\text{Effort for Documented Apps (App1–App6)} = 8 + 10 + 6 + 12 + 9 + 15 = 60\text{ person-days}$$
$$\text{Re-Estimated Total Effort } E_{\text{PERT}} = 60 + 66 = 126\text{ person-days}$$
$$\text{Re-Estimated Migration Effort Duration } D_{\text{effort}} = \frac{126\text{ person-days}}{3\text{ engineers}} = 42\text{ working days}$$

---

### 3. Monthly Blackout Window Adjustment

#### 3.1 Input Parameters
- **Total Working Days per Month:** 22 working days
- **Blackout Working Days per Month:** 5 working days (last 5 working days reserved for month-end closing)
- **Effective Working Days Available for Migration per Month ($W_{\text{eff}}$):**
$$W_{\text{eff}} = 22 - 5 = 17\text{ working days/month}$$

#### 3.2 Effective Months Required

**Using Initial Given Figures (40 working days):**
$$\text{Months}_{\text{initial}} = \frac{40\text{ working days}}{17\text{ effective days/month}} = 2.353\text{ months}$$

**Using PERT Re-Estimated Figures (42 working days):**
$$\text{Months}_{\text{PERT}} = \frac{42\text{ working days}}{17\text{ effective days/month}} = 2.471\text{ months}$$

#### 3.3 Calendar Elapsed Schedule Timeline Analysis

- **Month 1:** 17 working days execution + 5 blackout days = 22 total working days elapsed (Cumulative work = 17 days).
- **Month 2:** 17 working days execution + 5 blackout days = 22 total working days elapsed (Cumulative work = 34 days).
- **Month 3:** Remaining 8 working days execution ($42 - 34 = 8$ working days).
- **Total Calendar Elapsed Time:** **2 full calendar months + 8 working days into Month 3** ($\approx 2.5$ calendar months).

#### 3.4 Deadline Feasibility Check
- **Hard Constraint:** On-premise server support contract expires in **7 months**.
- **Calculated Completion:** **2.47 months**.
- **Margin / Buffer:** $7.00 - 2.47 = 4.53\text{ months}$ of safety margin.

---

### 4. Reconciliation: 42-Day Effort Estimation vs. 57-Day Schedule Duration

It is critical to distinguish between **pure migration effort** and **total project schedule duration**:

> **"The 42 working days represent the theoretical productive duration obtained by dividing the total estimated effort of 126 person-days among three engineers. The 57-working-day project schedule is the calendar schedule after applying task dependencies, migration-wave sequencing, blackout windows, and non-critical activities."**

- **42 Working Days ($D_{\text{effort}}$):** Represents the pure active engineering labor duration needed by the 3 engineers working in parallel to perform application migration ($126\text{ person-days} / 3\text{ engineers} = 42\text{ working days}$). This includes only the wave execution tasks T03 (8d), T05 (12d), and T07 (22d).
- **57 Working Days ($D_{\text{schedule}}$):** Represents the comprehensive project schedule duration, adding non-migration project lifecycle overheads:
  - **Setup & Discovery (Tasks T01 & T02):** $+6\text{ working days}$
  - **Wave Staging & Cutovers (Tasks T04, T06, T08):** $+7\text{ working days}$
  - **Project Handover & Decommissioning (Task T09):** $+2\text{ working days}$

$$\mathbf{D_{\text{schedule}} = D_{\text{setup}} (6\text{d}) + D_{\text{effort}} (42\text{d}) + D_{\text{cutover}} (7\text{d}) + D_{\text{handover}} (2\text{d}) = 57\text{ Working Days}}$$

---

### 5. PERT Statistical Confidence Interval Analysis

Total project variance across the 3 independent undocumented applications:
$$\sigma_{\text{total}}^2 = \sigma_7^2 + \sigma_8^2 + \sigma_9^2 = 21.78 + 21.78 + 21.78 = 65.34$$
$$\sigma_{\text{total}} = \sqrt{65.34} \approx 8.083\text{ person-days}$$

- **68.27% Confidence Interval ($\pm 1\sigma$):**
  $$\text{Effort} = 126 \pm 8.08 = [117.92, 134.08]\text{ person-days}$$
  $$\text{Duration} = \frac{[117.92, 134.08]}{3} = [39.31, 44.69]\text{ working days}$$

- **95.45% Confidence Interval ($\pm 2\sigma$):**
  $$\text{Effort} = 126 \pm 2(8.08) = 126 \pm 16.16 = [109.84, 142.16]\text{ person-days}$$
  $$\text{Duration} = \frac{[109.84, 142.16]}{3} = [36.61, 47.39]\text{ working days}$$

---

### 6. Architectural Rationale: Estimate vs. Promise

#### 6.1 Why Software Estimation is an Estimate, Not a Promise
1. **Cone of Uncertainty & Technical Debt:** App7, App8, and App9 are undocumented. The PERT estimate accounts for unknown architectural dependencies, hidden hardcoded configurations, and legacy database schema mismatches. A single deterministic figure cannot capture these unknown risks.
2. **Probabilistic Nature of PERT:** The 42-working-day duration represents the mean of a probability distribution ($TE$), with a 95% confidence spread ranging between 36.6 and 47.4 working days. Treating an estimate as a binding "promise" ignores statistical variance.
3. **Operational Dependencies:** Project timeline depends on zero external delays during month-end blackout windows, continuous CDC sync stability, and cloud vendor API availability.
4. **Conclusion:** The project duration of **42 working days (2.47 effective months)** is a mathematically sound, risk-adjusted **probabilistic estimate**. It provides business stakeholders with realistic bounds rather than an ungrounded deadline promise.

---
