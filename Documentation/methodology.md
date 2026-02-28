# Methodology and Justification (Through Phase F)

Date: 2026-02-28

## 1) Methodological Philosophy

The methodology follows a staged engineering-science workflow:

1. Build a reproducible synthetic longitudinal data generator.
2. Validate structural integrity before modeling.
3. Introduce probabilistic hazard modeling with explicit calibration.
4. Quantify intervention policy tradeoffs.
5. Analyze complexity and pre-escalation windows.

This order was chosen to prevent inferential modeling on unverified structure.

## 2) Data and Compute Discipline

Method choices:
- Parquet-first storage for all core artifacts.
- Explicit numeric types (float32/int8/int16 where applicable).
- Deterministic random seed for reproducibility.
- Vectorized transformations for high-volume panel operations.

Justification:
- Improves reproducibility, IO performance, and memory behavior.
- Reduces schema drift and unintended precision changes.

## 3) Synthetic Generation Layer (Notebook 01)

Method:
- Simulate instability recursion with decay and stochastic perturbation.
- Compute logistic hazard from instability and covariates.
- Simulate admissions stochastically.
- Export registry-like event tables and metadata artifacts.

Justification:
- Provides controlled, inspectable data-generating process for method development before registry integration.

## 4) State Engine and Complexity Structure (Phase A/B)

### Phase A methods
- Derive monthly state panel from generated events.
- Attach diagnosis growth and medication stacking dynamics.
- Validate transition legality and non-absorbing hospitalization behavior.

Why this method:
- Supports clinically interpretable trajectory states and safeguards against invalid transition artifacts.

### Phase B methods
- Construct instability signal with decay + event increment logic.
- Run anti-drift checks and contract checks.

Why this method:
- Balances dynamic accumulation with long-horizon numeric stability.

Observed issue:
- Unstable-window count currently zero; thresholding strategy requires refinement in next stage.

## 5) Baseline Signal Audit Method

Methods used:
- Group overlap checks.
- Threshold activation analysis.
- Permutation test for leakage collapse.
- AUROC/PR and calibration probes.

Why this method:
- Baseline audit establishes whether instability-alone signal supports discrimination and whether artifacts may reflect leakage.

Result-informed methodological decision:
- Because baseline discrimination is weak, move to enriched hazard design instead of threshold-only strategy.

## 6) Hazard Modeling Method (Phase C)

Model form:
- Discrete-time logistic hazard on panel rows.

Core predictors:
- Instability level.
- Log time-since-medication.
- Incremental burden proxy.
- Time trend.

Calibration approach:
- Binary search over intercept to match first-year annual escalation target (~5%, bounded 4–6%).

Why this method:
- Directly aligns model output scale to clinically interpretable annual incidence constraints while preserving patient-level heterogeneity.

## 7) Intervention Simulation Method (Phase D)

Policy simulation:
- Trigger intervention when instability exceeds threshold tau.
- Apply hazard reduction factor delta for intervened rows.
- Summarize expected prevented escalations, workload, false positives, and burden-per-benefit metrics.

Why this method:
- Provides operationally relevant policy analysis before individualized treatment recommendation modeling.

Methodological caveat:
- This is a policy-effect simulation layer, not a causal identification framework.

## 8) Complexity and Window Analysis Method (Phase E)

Methods:
- Define instability spikes via quantile threshold.
- Compute lag from latest spike to admission.
- Estimate complexity growth trajectories over time.
- Stratify hazard by complexity quintile and comorbidity bins.
- Compute recurrence metrics based on repeated admissions.

Why this method:
- Tests the thesis that escalation risk rises with cumulative burden and that actionable pre-escalation windows can be quantified.

Observed limitation at end of Phase E:
- Recurrence metrics were zero, so loop closure remained incomplete.

## 9) Closed-Loop Recurrence Method (Phase F)

Methods implemented:
- Assign post-admission response states at baseline escalation events.
- Apply state-specific instability shifts and hazard multipliers with decay over months since admission.
- Simulate recurrent escalations with probabilistic hazard and cooldown constraints.
- Recompute cycle index and months-since-cycle-start after each Stage F escalation event.
- Recompute hazard using reset cycle clock (`log_tsm_stage_f`).

Why this method:
- Operationalizes post-event trajectory re-entry and recurrence while keeping behavior probabilistic.
- Produces measurable recurrence burden required for downstream recommendation stages.

Observed Stage F metrics:
- Mean cycles per admitted patient: 1.289
- Recurrence rate: 0.248
- Additional recurrent escalations: 12,369

## 10) Artifact-Centric Reproducibility Method

Method:
- Every phase writes explicit tables, figures, and proof/checklist reports.
- Metadata manifests capture input/output lineage.

Why this method:
- Produces auditable traceability needed for thesis reporting and defense.

## 11) Quality Controls in Place

Controls implemented:
- Structural rule checks (transitions).
- Type/schema contracts.
- Drift checks.
- Calibration target checks.
- Output existence proofs.

Rationale:
- Reduces risk of presenting incomplete or structurally inconsistent phase outputs.

## 12) Methodological Limitations (Current Stage)

Limitations:
- No explicit adherence-response mechanism.
- No treatment recommendation model by patient category.
- Synthetic assumptions not yet recalibrated against real registry distributions.

## 13) Method Readiness for Next Stage

The methodology is ready to extend into Stage G/H by adding adherence-aware intervention actions, patient-category stratification, and recommendation-layer estimation while preserving current validation and artifact standards.

