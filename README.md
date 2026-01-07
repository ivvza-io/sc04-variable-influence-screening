# sc04-variable-influence-screening
Systematic screening of additional variables to quantify their incremental predictive value and justify model complexity under a conservative engineering lens
 
 # Study Case 4 - Variable Influence Screening

**Identifying Which Variables Are Worth Adding Before Increasing Model Complexity**

---

## Position in the Portfolio

This study case builds directly on **Study Case 2** and **Study Case 3**, which established and stress-tested a **chemistry-only modeling approach** for predicting UTS at heat level.

By the end of SC3, two conclusions were clear:

1. Chemistry consistently defines the feasible strength envelope.
2. A non-trivial portion of variability remains unexplained within that envelope.

Study Case 4 addresses the natural next industrial question:

> *Before building a full process-aware model, which additional variables are actually worth the added complexity?*

Rather than immediately constructing a large, multi-variable model, this study performs a **controlled variable screening** to identify **high-impact drivers** and discard low-return candidates.

---

## Core Question

**Which additional variables meaningfully reduce prediction error and risk beyond chemistry-only models, and which do not justify their inclusion?**

---

## Thesis

Only a small subset of non-chemical variables is a plausible candidate for delivering **stable, incremental, and industrially meaningful improvements** over chemistry-only models when evaluated under a conservative modeling lens.

Feature engineering choices matter as much as variable selection itself, and improvements should be assessed not only on average error but also on **tail risk**, as captured by high-percentile absolute error.

---

## Screening Philosophy

This study adopts a **conservative screening philosophy**.

The objective is not to uncover every possible interaction or maximize predictive accuracy, but to identify variables whose value:

- is stable across validation folds,
- does not rely on aggressive model flexibility,
- and demonstrably reduces risk rather than only improving average performance.

This philosophy guides both variable selection and model choice throughout the study.

---

## Scope and Constraints

### In scope
- Single alloy system, selected to minimize confounding effects
- Chemistry-only baseline identical to SC2 / SC3
- Incremental addition of candidate variables, one step at a time
- Explicit comparison of alternative feature representations
- Evaluation using both average and tail-error metrics
- Conservative, group-aware validation

### Out of scope (explicit)
- Multi-alloy or cross-system generalization (already addressed in SC3)
- Full process-aware modeling
- Exhaustive feature combinations
- Hyperparameter optimization
- Advanced ML architectures
- Causal interpretation of coefficients or importances

The objective is **screening and prioritization**, not optimization.

---

## System Under Study

This study focuses on a **single alloy system** previously analyzed in earlier study cases, ensuring:

- Stable data semantics
- Controlled metallurgy
- Clear attribution of performance changes to added variables

Generalization across systems is intentionally deferred to later stages.

---

## Data Inputs (from the semantic layer)

- Heat-level chemistry composition
- Heat-level mechanical test results (UTS)
- Selected process variables capturing deformation and thermal history **(not full process routes)**

**Assumptions:**
- One row per heat
- Consistent grain inherited from previous study cases
- Candidate variables are measured with sufficient reliability for screening purposes

---

## Candidate Variables

Based on procces experience and metallurgical reasoning, the following candidates are evaluated:

### Mechanical processing
- Final thickness
- Percentage reduction (engineered feature derived from thickness history)

These first two representations are treated as **alternative encodings of the same physical effect**, not as independent variables.

### Thermal processing
- Coiling temperature   
Coiling temperature represents the exit terminal thermal condition of hot rolling and is evaluated both independently and in combination with deformation proxies.

No assumption is made that all candidates will improve the model.

---

## Method Overview

### Baseline
- Chemistry-only model, frozen from previous study cases
- Identical validation strategy and data splits

### Incremental screening
Models are evaluated in a stepwise manner:

1. Chemistry-only baseline  
2. Chemistry + thickness  
3. Chemistry + percentage reduction  
4. Chemistry + coiling temperature  
5. Chemistry + percentage reduction + coiling temperature  

This design isolates **incremental gain** attributable to each variable or representation.

### Validation
- Group-aware cross-validation
- Out-of-fold predictions only
- Identical folds across all models to ensure comparability

### Evaluation metrics
- Mean Absolute Error (MAE)
- Coefficient of determination (R²)
- **P95 absolute error** (tail-risk indicator)

The P95 metric is used to quantify **worst-case typical errors**, which are often more relevant for industrial risk assessment than average performance alone.

---

## Expected Outputs

### Tables
- Model performance comparison across incremental steps
- Stability metrics across validation folds
- Direct comparison of thickness vs percentage reduction representations

### Figures
- Incremental performance waterfalls (MAE, R², P95 absolute error)
- Error distribution comparisons highlighting tail behavior
- Minimal residual diagnostics supporting interpretation

### Engineering insights
- Identification of variables with potential predictive ROI
- Quantification of whether added variables meaningfully reduce risk or provide only marginal gains
- Clear justification for including or excluding candidate features

---

## Evaluation Criteria

This study is considered successful if it:

- Quantifies the incremental impact of candidate variables under a conservative modeling lens
- Determines whether added variables deliver robust improvements or marginal gains
- Assesses whether any observed improvements are meaningful in terms of tail risk (P95 absolute error)
- Supports an explicit decision on variable inclusion or exclusion for downstream modeling

A lack of clear or consistent improvement is itself considered a valid and informative outcome.


### Not objectives of this study

The following are explicitly **not objectives** of this study and are not used to judge its success:

- Maximizing predictive accuracy
- Expanding feature sets without clear justification
- Chasing marginal metric improvements in isolation
- Introducing additional complexity without clear evidence of risk reduction

The goal is decision support, not metric optimization

---

## Exit Condition

Study Case 4 is complete when:

- The chemistry-only baseline has been extended incrementally and transparently
- The incremental value of candidate variables has been quantified
- Variables that do not justify additional complexity have been explicitly identified
- Feature engineering choices are justified with evidence
- The scope for a process-aware model is clearly defined, including justified exclusions


---

## Relationship to Next Study Case

Study Case 4 defines **what is worth modeling**.

By identifying a minimal, high-value set of non-chemical variables whose contribution is robust and defensible, it establishes a constrained and justified scope for **Study Case 5**, where a full process-aware model can be constructed without unnecessary complexity.
