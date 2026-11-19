# CAREER VENTURE LAB — STREAMLIT BUILD PROMPT (STAGE 1)

## 0. Team & Build Configuration
- **team_id:** [ENTER_YOUR_TEAM_ID]
- **pathway_id:** NUR-2
- **selected_course:** Precalculus
- **selected_level:** Advanced
- **teacher_approved_skills:** [ENTER_APPROVED_SKILL_NAME, e.g., Rational Average Cost, Function Composition & Seasonal Trigonometric Demand]
- **target_phase:** Stage 1 ONLY (inputs, deterministic math engine, dated ledger, Save/Load JSON, and fixture acceptance tests). Do not generate Stage 2-4 modules yet unless stubbed clearly as unimplemented.

**Instruction before coding:** Summarize the resolved requirements, formulas, and file list first before generating `app.py`, `requirements.txt`, `README.md`, `sample_data/monthly_fixture.csv`, and `tests/`.

---

## 1. Common Streamlit Dashboard Requirements & Data Contract
Build a working Python Streamlit app for Career Venture Lab using `app.py`, `requirements.txt`, `README.md`, `sample_data/`, and `tests/`. Students already use GitHub and Render. Do not deploy or publish automatically. Deliver complete files and setup instructions, not only a visual mockup.

Implement staged modules: Overview; Inputs and Funding; Math Skills and Levels; Cross-Course Connections; Customers; Operations; Finance; AI Recommendations; Marketing Studio; Report Center; Assessment; Decisions; optional Storefront. Mark unfinished features clearly. **Stage 1 must work without paid APIs:** deterministic math, synthetic sample data, manual snapshot export/import, and durable Save/Load JSON. Do not simulate an AI response and label it live.

Expose startup funding mix, itemized equipment/setup/inventory/reserve, sales offerings, requested demand, completed sales, variable and fixed expense lines, staffing, and capacity. Use one dated ledger for daily/monthly/yearly aggregation. Separate actual simulated transactions from projected scenarios. Implement cash, profit, interest, principal, and capital-equipment rules described below. Do not use arbitrary tax assumptions.

Show course skill, level, equation, units, domain, assumptions, and interpretation prompt on every analysis page. Foundation/Applied/Advanced must change the task and evidence. Build linked plots, data tables, sliders, feasible boundaries, zero line, forecast-versus-actual, and scenario comparisons. All graphs must be printable/exportable with assumptions and data. Add accessible labels and non-color-only indicators.

Financial and operations reports must reconcile to the same ledger and snapshot. Recommendations are pending until a student approves; stale imports are rejected. Record before/after state and a decision memo. Implement reproducible random seeds and duplicate-run protection. Treat input changes as scenarios until approved.

Do not expose teacher assessment keys in the student app. Use school LMS or authenticated server authorization for graded release; hiding a tab is insufficient. Use fictional data and no real payments. Include validation, named test cases, restart/reload test, and a manual acceptance checklist. Explain any required secret or service and provide a no-API fallback.

### Data & Integration Contract
- **Snapshot:** `team_id`, `course`, `pathway_id`, `snapshot_id`, `state_version`, `simulation_date`, `base_period`, `working_days`, `currency`, `level_by_skill`, `assumptions[]`, `sources[]`, `offerings[]`, `funding[]`, `cost_lines[]`, `capacity{}`, `ledger[]`, `campaigns[]`, `decisions[]`.
- **Offering:** `offering_id`, `name`, `unit`, `price`, `demand_rule`, `capacity_rule`.
- **Ledger:** `transaction_id`, `date`, `event_type`, `category`, `amount`, `quantity`, `unit_price`, `offering_id`, `campaign_id`, `financing_type`, `state_version`.
- **Recommendation:** `role`, `snapshot_id`, `state_version`, `question`, `evidence[]`, `method`, `results{}`, `alternatives[]`, `uncertainty`, `proposed_changes{}`, `checks_needed[]`, `approval_status`.
- **Decision:** `decision_id`, `snapshot_id`, `alternatives`, `report_ids[]`, `student_prediction`, `calculation`, `visual_id`, `chosen_action`, `owner`, `checker`, `approval`, `expected_outcome`, `actual_outcome`, `reflection`.
- **Rules for data transfer:** Export JSON for structured snapshots and CSV for human inspection. On import, validate required fields, types, nonnegative quantities, valid dates, unique IDs, `team_id`, and expected `state_version`. Reject stale versions and wrong team IDs. Do not silently overwrite the ledger. Each accepted simulation step has a unique `run_id` and `seed`; clicking twice, refreshing, or reopening a report must not duplicate transactions.
- **Common field rules:** Currency USD; two-decimal stored money using `Decimal` or integer cents; integer counts; nonnegative sales and expenses; rates in `[0,1]`; real-valued continuous model views explicitly labeled. All fixture rows are monthly and dated on the last calendar day. Store days/month for daily modeled allocations, and distinguish working days from calendar days. At baseline, borrowing, interest, refunds, depreciation, and sales tax are disabled and shown as not modeled.

---

## 2. NUR Business Package: Nurse-Led Health Education Simulation
Implement every field, default, validation, cost row, algorithm, fixture, and expected result in this NUR package. All values are fictional editable teaching defaults.

### Exact Inputs & Baselines
- `base_price = 25` (USD per workshop seat; `> 0` for demand model)
- `D0 = 80` (requested units/month at base price; integer `>= 0`)
- `staff_hours = 30` (productive hours/month; number `>= 0`)
- `hours_per_unit = 0.5` (productive hours/unit; `> 0`)
- `opening_cash = 4000` (post-startup spendable cash; USD `>= 0`)
- `venue_seats = 60` (integer `>= 0` seats/month)
- `no_show_rate = 0` (number in `[0,1]`)
- `satisfaction_mean = 4` (number in `[1,5]`; supplied survey summary)
- **Additional control fields:** `team_id`, `pathway_id`, `selected_course`, `selected_level`, `snapshot_id`, `state_version`, `simulation_date`, `run_id`, `random_seed`, `refund_amount = 0`, `loan_principal = 0`, `interest = 0`, `additional_capex = 0`. Funding and capex are not sales or ordinary operating expenses.

### Startup Financing & Opening Balances
- Owner contribution: `$6,000`
- Equipment assets: `$1,500` (Demonstration equipment `$1,000` + accessibility materials `$500`)
- Pre-opening expense: `$500` (setup)
- Opening inventory: `$0`
- Post-startup cash: `$4,000`
- Retained earnings begin at `-$500`. Opening assets = owner equity minus pre-opening expense; do not expense equipment or inventory again.

### Editable Cost Rows
- **Per delivered/attended unit:** `educator_delivery` `$2`; `materials` `$2`; `cleaning_consumables` `$1`. Total = `$5/unit`.
- **Monthly fixed:** `venue` `$250`; `scheduling_software` `$100`; `accessibility_support` `$200`; `admin_insurance_assumption` `$150`. Total = `$700/month`.
- **Advertising:** Additional campaign line, initially `$0`.

### Deterministic Monthly Algorithm & Accounting Rules
- **Capacity & Fulfillment:**
  - `capacity = min(floor(staff_hours / hours_per_unit), venue_seats)`
  - `booked = min(requested, floor(staff_hours / hours_per_unit), venue_seats)`
  - `attended = floor(booked * (1 - no_show_rate))`
  - `sold = attended`
  - Collect payment and incur per-seat variable expenses **only for attended seats** (`sold`) in this simplified model.
- **Profit, Cash & Balance Rules:**
  - `revenue = price * sold`
  - `variable_expense = sold * variable_per_unit` (baseline `$5 * sold`)
  - `fixed_expense = sum(monthly_fixed_rows)` (baseline `$700`)
  - `operating_profit = revenue - variable_expense - fixed_expense - own_marketing_expense`
  - `net_profit = operating_profit - interest - enabled_depreciation`
  - Refunds reduce revenue and cash once.
  - `ending_cash = opening_cash + revenue - variable_cash_cost - fixed_cash_cost - marketing - interest - principal - capex + new_owner_cash + loan_cash`
- **Business-Specific Treatment:**
  - Do not infer health outcomes from attendance/satisfaction.
  - Extra accessibility spending must be an explicit line item.
  - Model fictional general education only—never diagnosis, clinical treatment, or patient data.
  - Base offerings: `General wellness education workshop` (primary offering for acceptance), `Caregiver information session`, `First-aid awareness demonstration`. Extra offerings need separate costs/capacity allocation and cannot duplicate revenue.
  - Operating view must show **booking versus attendance, educator utilization, and access cost**.
  - Normal mode derives capacity from resources (`staff_hours / hours_per_unit` and `venue_seats`). Fixture-test mode uses explicit `capacity_override` only while replaying the supplied sample; show a warning and do not mix modes in a run.
  - Forecasting multipliers: baseline/upside/downside demand multipliers `1.00 / 1.10 / 0.90` unless changed; label assumptions and keep forecast scenarios out of the actual ledger.

### Sample Fixture (`sample_data/monthly_fixture.csv`) & Acceptance Tests
Create `sample_data/monthly_fixture.csv` with exact rows:
```csv
month,requested,capacity_override,ad_cost
2027-01,80,60,0
2027-02,88,60,100
2027-03,72,45,0
```
- **Expected Fixture Replay Results** (reset to post-startup opening cash `$4,000` before replaying all 3 rows; February includes a `$100` own-business campaign charged once):
  - `2027-01`: Sold = `60`, Revenue = `$1,500`, Total Expense = `$1,000` (`$300` var + `$700` fixed), Profit = `$500`, Closing Cash = `$4,500`
  - `2027-02`: Sold = `60`, Revenue = `$1,500`, Total Expense = `$1,100` (`$300` var + `$700` fixed + `$100` ad), Profit = `$400`, Closing Cash = `$4,900`
  - `2027-03`: Sold = `45`, Revenue = `$1,125`, Total Expense = `$925` (`$225` var + `$700` fixed), Profit = `$200`, Closing Cash = `$5,100`
- **Business-Specific Reset Test:** Reset before testing; set `no_show_rate = 0.20` (with baseline `requested = 80`, `capacity = 60`): `booked = 60`, `attended = 48`, `revenue = $1,200`, `variable = $240`, `profit = $260`. Do not record all 60 bookings as revenue.
- **Common Numerical Checks:** Normal baseline staff-hours capacity is `60`. At zero requested sales (`requested = 0`), `revenue = 0`, `variable_expense = 0`, `profit = -$700`, and no division-by-zero crash. Money tolerance `$0.01`; counts exact.

---

## 3. Course Build Adapter: Precalculus & Pathway NUR-2
Build a persistent **Math Skills & Challenge Level** panel visible from the overview and beside every related graph/report. Use `D0 = 80`, `p0 = 25`, `v = 5`, `F = 700`, and derived `capacity` (`60` at baseline). Keep all three levels selectable (with teacher-approval lock/preview workflow), defaulting to **Advanced**:

- **Foundation Mode:**
  - Define $C(s) = F + v \cdot s = 700 + 5s$ and Average Cost $A(s) = \frac{C(s)}{s} = \frac{700}{s} + 5$ for $0 < s \le \text{capacity}$ ($0 < s \le 60$).
  - Display vertical asymptote $s = 0$ and horizontal asymptote $y = v$ ($y = 5$) with a practical-domain warning; at $s = 0$ show `undefined` (no zero-division crash).
- **Applied Mode:**
  - Compose price-demand $q(p) = \max\left(0,\; 2D_0 - \frac{D_0}{p_0}p\right) = \max(0,\; 160 - 3.2p)$ on $0 \le p \le 2p_0$ ($0 \le p \le 50$).
  - Compose realized sales $s(p) = \min(\text{capacity},\; q(p))$, Revenue $R(p) = p \cdot s(p)$, Cost $C(s(p)) = F + v \cdot s(p)$, and Profit $P(p) = R(p) - C(s(p))$.
  - Show input-output mapping table and the capacity breakpoint $p = p_0 \left(2 - \frac{\text{capacity}}{D_0}\right)$ (at baseline: $p = 25(2 - 60/80) = \$31.25$) when within domain. Explicitly distinguish continuous analysis from integer transaction rounding (`floor`).
- **Advanced Mode (Active Default):**
  - Include Foundation and Applied capabilities plus a selectable extension toggle (enable **one extension at a time**, not both simultaneously):
    1. **Seasonal Trigonometric Model** (after trigonometry is taught): $D(t) = D_0 \left(1 + 0.20 \sin\left(\frac{2\pi(t - 1)}{12}\right)\right)$, where $t$ is month index ($1 \dots 12$), period is 12 months, peak at $t = 4$ ($1.2 \cdot D_0 = 96$), and minimum at $t = 10$ ($0.8 \cdot D_0 = 64$). Clip realized sales at `capacity`, compare original seasonal demand vs. realized sales, and label assumed seasonality.
    2. **Exponential/Logarithmic Growth Alternative** (after exponentials/logarithms are taught): solve for time $t$ to reach a positive growth target with a justified growth rate.
  - Display assumptions, units, and extrapolation limits. Include linked composed-function graphs, average-cost curve with asymptotes, input-output table, and seasonal/growth comparison chart.

### Precalculus Acceptance Checks
- $A(0)$ is `undefined`; $A(\text{capacity}) = F/\text{capacity} + v$ (at baseline `capacity = 60`: $700/60 + 5 \approx \$16.67$).
- With the seasonal extension at baseline $D_0 = 80$: demand at month $t = 4$ is $1.2 \cdot 80 = 96$ (clipped to `60` realized seats at baseline capacity) and at month $t = 10$ is $0.8 \cdot 80 = 64$ (clipped to `60` at baseline capacity).

### Pathway NUR-2 Curricular Context
- **Cross-Course Evidence:** Evaluate an official health-education source and revise a fictional workshop flyer for clarity. No diagnosis, treatment, or patient data.
- **Required Disruption Scenario:** Venue availability falls by 20% (`venue_seats` drops from `60` to `48`); add accessible delivery without exceeding the budget.
- **Individual Mastery Check:** Explain the difference between $C(60)$ and $A(60)$ with units, and identify what the average-cost graph does near $q = 0$ and why $q = 0$ is excluded.

---

## 4. Stage 1 Execution & Verification Checklist
1. Generate `app.py`, `requirements.txt`, `README.md`, `sample_data/monthly_fixture.csv`, and `tests/test_nur2.py`.
2. Run tests if execution is available; otherwise provide exact `pytest` / `streamlit run app.py` commands and mark `NOT RUN`.
3. Include automated unit tests verifying: (a) 3-month fixture replay, (b) `no_show_rate = 0.20` reset test, (c) zero-sales handling & $A(0)$ undefined check, (d) Precalculus seasonal peaks ($t=4, t=10$) and capacity breakpoint ($p=\$31.25$), (e) duplicate `run_id` protection, and (f) JSON export/restore + stale `state_version` / wrong `team_id` rejection.