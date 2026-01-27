# Technical Notes — SC04  
**Variable Influence Screening in Chemistry-Based UTS Models**  
**Audit-Level Methodology, Diagnostics, and Design Decisions**

---

## 1. Scope and Role of This Document

These technical notes document the analytical rationale, methodological decisions, and diagnostic evidence supporting **SC04 — Variable Influence Screening in Chemistry-Based UTS Models**.

They complement:
- the **public README**, which presents conclusions and decision implications,
- and the **notebooks**, which contain the full analytical execution and figures.

This document focuses on **modeling discipline**: how incremental variables were evaluated, and why certain variables were deliberately excluded from the predictive model used to support internal standards.

---

## 2. Analytical Objective

The analytical objective of SC04 is to determine whether **commonly measured, process variables** provide **stable and meaningful incremental predictive value** when added to a **frozen chemistry-only UTS model**.

This study evaluates **incremental signal**, not process relevance.

### Explicit Non-Objectives
This analysis does **not** aim to:
- determine which variables should be measured or controlled in production,
- explain physical mechanisms in detail,
- maximize predictive accuracy through model tuning,
- redesign process standards.

A negative result (no incremental value) is considered **valid and informative**.

---

## 3. Data Contracts and Grain Definition

### 3.1 Analytical Unit (Grain)

- **Grain:** one row per heat  
- **Justification:**
  - Chemistry, process records, and tensile tests are all traceable at heat level.
  - Internal standards are defined at the **alloy and temper level**, while evaluation uses heat-level data.
  - Consistent with SC02 and SC03, preventing leakage.

---

### 3.2 Dataset Construction

- **Source:** SQL semantic layer defined in SC01.
- **System:** AA3105-O.
- **Baseline inputs:**
  - Chemical composition (Mn, Mg).
- **Candidate process variables:**
  - Final thickness,
  - Reduction percentage,
  - Coiling temperature.
- **Target:**
  - UTS (MPa).
- **Aggregation:**
  - Multiple measurements aggregated to heat-level statistics.
- **Exclusions:**
  - None related to data completeness.

> The dataset is identical to SC02 except for the addition of candidate variables, ensuring a controlled comparison.   
All models are trained and evaluated on the same heat set; no rows are dropped due to missing candidate variables.

---

### 3.3 Domain of Validity

All conclusions apply only within the observed chemistry and process ranges present in the historical data.  
No extrapolation beyond these domains is supported.

---

## 4. Validation and Leakage Control

### 4.1 Validation Strategy

- **Method:** Group-aware cross-validation (GroupKFold).
- **Grouping variable:** heat identifier.
- **Rationale:**
  - Prevents leakage from repeated measurements within the same heat.
  - Ensures comparability with SC02 results.

All reported metrics are based exclusively on **out-of-fold predictions**.

---

### 4.2 Leakage Risks Considered

Potential leakage vectors:
- Multiple tensile tests per heat.
- Shared processing context.

Mitigations:
- Heat-level aggregation.
- Group-aware validation.

Residual risks:
- Temporal correlation across heats is not explicitly modeled.

This risk is accepted as representative of real industrial data.

---

## 5. Variable Selection Rationale

Candidate variables were selected based on **industrial and metallurgical relevance**, not opportunistic correlation:

- They are routinely measured in production.
- They follow internal guidelines or typical operating ranges.
- They are known to influence process behavior and downstream quality.

The purpose of this study is **not** to challenge their process importance, but to assess whether they:
- add independent predictive information for UTS,
- reduce tail risk,
- justify increased model complexity.

---

## 6. Modeling Strategy: Frozen Baseline Principle

To isolate incremental value, the chemistry-only model established in SC02 is treated as **frozen**:

- Same model architecture (ridge regression).
- Same regularization.
- Same validation strategy.
- Same evaluation metrics.

Only the **input feature set** changes.

No hyperparameter tuning or model adaptation is performed to “help” new variables.

This ensures that observed effects are attributable solely to the added variables.

---

## 7. Evaluation Metrics and Stability Criteria

### 7.1 Metrics Used

- **Median MAE (MPa):** central error tendency.
- **P95 absolute error (MPa):** tail risk indicator.

Incremental value is assessed using **Δ metrics** relative to the chemistry-only baseline.

---

### 7.2 Acceptance Criteria

A variable (or variable set) is considered to provide meaningful incremental value only if it demonstrates:
- consistent reduction in **P95 absolute error**,
- stability across validation folds,
- improvement that is not limited to isolated folds or narrow subsets.

Improvements in MAE without corresponding tail risk reduction are **not sufficient**.

---

## 8. Diagnostics and Failure Modes

Diagnostic analysis shows that:
- Error distributions for baseline and extended models largely overlap.
- Marginal MAE improvements are inconsistent across folds.
- No candidate variable or combination consistently reduces tail error.

No subgroups or operating regimes showed stable improvement sufficient to justify inclusion.

These results indicate that added variables increase complexity without delivering robust predictive benefit for this modeling objective.

---

## 9. Decision Implications

For AA3105-O and the evaluated objective:

- Chemistry captures the dominant predictive signal for UTS.
- Evaluated process variables do **not** provide robust incremental value.
- Including them in the model would:
  - increase complexity,
  - reduce interpretability,
  - and offer no meaningful reduction in risk.

Importantly:
- Excluding variables from the **model** does **not** imply they are unimportant for the **process**.
- These variables remain critical for operational control, stability, and quality management.
- These results, as presented in this study case, do not imply that the variables lack utility under alternative modeling paradigms or analytical approaches.

---

## 10. Design Decisions Log

Key non-obvious decisions recorded for traceability:

- Accepted a negative result as informative rather than forcing complexity.
- Did not introduce additional variables once initial candidates showed no robust improvement.
- Did not change model architecture to accommodate new variables.
- Prioritized tail risk reduction and stability over marginal accuracy gains.
- Explicitly separated process importance from modeling utility.

---

## 11. Traceability

- **Notebook:** `sc02_chemistry_only_uts_model.ipynb`
- **Data semantics:** SQL semantic layer defined in SC01
- **Toolkit version:** v1.0.0 (pinned in requirements.txt)
- **Dataset:** `data/public/sc04/v_models_analysis_o.csv`

---

### Closing Note

These technical notes document a disciplined screening process that demonstrates **when not to add complexity**.  
This restraint is essential before translating predictive models into conservative, uncertainty-aware engineering decision tools.