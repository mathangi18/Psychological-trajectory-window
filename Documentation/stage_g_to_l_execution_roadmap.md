# Stage G→L Execution Roadmap

Date: 2026-02-28

## Purpose

This roadmap translates the agreed course-aligned strategy into executable stages with:
- scope boundaries,
- required deliverables,
- acceptance criteria,
- output artifact contracts,
- promotion gates to move to the next stage.

The intent is progressive complexity with controlled comparison, not one-shot model maximization.

## Global Execution Rules (All Stages)

1. Keep one frozen split policy (patient-wise + time-aware where required).
2. Reuse one evaluation protocol across stages.
3. Promote a stage only if primary and safety metrics pass jointly.
4. Keep subgroup and calibration checks mandatory.
5. Record every stage as reproducible artifacts under `Results/tables|figures|reports/<stage_namespace>`.

## Evaluation Protocol (Fixed Across Stages)

Primary metrics:
- AUROC
- PR-AUC
- Brier
- ECE

Safety/robustness metrics:
- Bootstrap CIs
- Subgroup metric spread
- Error analysis tables
- Permutation-collapse sanity check

Stage comparison output:
- Delta table from prior stage (`metric_delta`, `calibration_delta`, `subgroup_delta`, `runtime_delta`).

---

## Stage G — Evaluation Foundation

### Scope
- Establish frozen split governance.
- Add leakage checks and baseline diagnostics.
- Build shared metric runner and report templates.

### Deliverables
- `stage_g_model_stage_comparison.csv`
- `stage_g_label_permutation_collapse.csv`
- `stage_g_feature_permutation_importance.csv`
- `stage_g_data_contract_report.json`
- `stage_g_holdout_freeze.json`
- Stage G summary and proof report

### Acceptance Criteria
- Permutation collapse observed (true-label performance materially above shuffled-label performance).
- Contract completeness check passes.
- Frozen split artifact created.

### Promotion Gate
- Stage G artifacts complete and reproducible on rerun.

---

## Stage H — Baseline Supervised Stack

### Scope
- Baseline explainable models as reference stack.
- Calibrated probability outputs and uncertainty bands.

### Required Models
- Elastic-net logistic regression
- Linear baseline (where target framing allows)

### Deliverables
- Baseline model performance table
- Calibration curve outputs
- Coefficient-based interpretation summary
- Uncertainty summary (bootstrap CIs)

### Acceptance Criteria
- Baselines run on frozen split.
- Calibration report generated.
- Subgroup error table generated.

### Promotion Gate
- Baseline stack stable and interpretable, with no unresolved leakage flags.

---

## Stage I — Bias–Variance and Capacity Sweep

### Scope
- Explicitly measure complexity vs generalization.
- Capacity sweeps and learning curves.

### Deliverables
- Capacity sweep table (`model_capacity`, train/val/test metrics)
- Learning curve artifacts
- Regularization sweep table (L1/L2/elastic-net)
- Bias–variance narrative report

### Acceptance Criteria
- At least one clear underfit and overfit regime demonstrated.
- Selected operating point justified by generalization, not peak train score.

### Promotion Gate
- Chosen model capacity documented with rationale and evidence.

---

## Stage J — Strong Tabular Models

### Scope
- Add stronger tabular learners and compare against H/I baselines.

### Required Models
- RandomForest
- XGBoost and/or LightGBM
- KNN/SVM as diagnostic references (not mandatory winners)

### Deliverables
- Unified comparison matrix across H/I/J
- Runtime and resource profile table
- Calibration and subgroup comparison update

### Acceptance Criteria
- Improvement claims include uncertainty intervals.
- Any accuracy gain that degrades calibration/safety must be explicitly flagged.

### Promotion Gate
- Selected model family passes both performance and safety thresholds.

---

## Stage K — Representation and Unsupervised Layer

### Scope
- Add cohort-shape understanding and representation diagnostics.

### Methods
- PCA / UMAP for structure visualization
- HDBSCAN for dense/irregular subgroup discovery
- Optional embedding/metric-learning block

### Deliverables
- Representation plots
- Cluster profile table and stability notes
- Distance/similarity diagnostics report

### Acceptance Criteria
- Unsupervised findings linked to outcome behavior and not presented in isolation.

### Promotion Gate
- Representation layer provides actionable segmentation signal for Stage L interpretation.

---

## Stage L — Interpretability, Ethics, and Clinical Trust

### Scope
- Trust layer and deployment-facing interpretability package.

### Required Components
- SHAP global and local explanations
- Intrinsic interpretation alignment (for baseline models)
- Counterfactual explanations
- Bias/fairness subgroup audit
- Failure-case catalog

### Deliverables
- Model card draft
- Data/assumption documentation update
- Clinical interpretation update with failure and uncertainty sections

### Acceptance Criteria
- Explanations are consistent across model families where possible.
- Subgroup risk disparities are quantified and explicitly discussed.

### Promotion Gate
- Stage L package ready for thesis reporting and stakeholder review.

---

## Stage Dependencies

- G is prerequisite for all later stages.
- H must complete before I/J comparisons are treated as valid.
- K and L can proceed in parallel after J, but L finalization should include K findings where relevant.

## Branching Policy

When branching is needed:
- Keep branch-specific outputs in distinct stage namespace folders.
- Merge only if branch passes stage promotion gate.
- Preserve all rejected branch reports for auditability.
