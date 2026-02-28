# Decisions and Justifications (Through Phase F)

Date: 2026-02-28

## Scope Note

This document records structural, analytical, and operational decisions made up to and including Phase E. The focus is on why each decision was made, what alternatives were considered, and what constraints were accepted.

## D-01 — Use a Synthetic Registry Before Real Registry Integration

Decision:
- Build and validate the full deterioration workflow in synthetic data first.

Justification:
- Enables rapid iteration of structural logic, risk calibration, and validation checks before schema/quality variability from real registries is introduced.
- Reduces data-governance and access bottlenecks during model architecture stabilization.

Tradeoff:
- Clinical realism is approximate until real-registry calibration stage.

## D-02 — Preserve Probabilistic (Non-Deterministic) Escalation

Decision:
- Keep escalation modeling stochastic and avoid deterministic admission rules.

Justification:
- Baseline audits indicate weak separability from instability alone, which matches expected heterogeneity in psychiatric trajectories.
- Deterministic escalation would overstate certainty and reduce external plausibility.

Evidence:
- Baseline AUROC close to 0.5 with permutation collapse near random.

## D-03 — Freeze Notebook 01 as Generation Layer

Decision:
- Keep generation architecture in Notebook 01 frozen once core outputs were stable.

Justification:
- Protects upstream reproducibility and avoids moving targets during downstream hazard and intervention validation.
- Enforces clear phase separation (generation vs enrichment vs policy analysis).

Tradeoff:
- Some later insights require follow-on notebooks rather than retrofitting generation logic.

## D-04 — Adopt Parquet-First Data Discipline

Decision:
- Persist all core artifacts in Parquet and avoid CSV for primary storage.

Justification:
- Better schema consistency, type stability, and IO performance at project scale.
- Supports reproducible downstream loading contracts.

## D-05 — Explicit Numeric Type Discipline

Decision:
- Enforce float32/int8/int16 typing in generation and panel transformations.

Justification:
- Limits silent numeric drift and memory overhead.
- Improves reproducibility across large synthetic panels.

## D-06 — Add Multi-State Representation (Phase A)

Decision:
- Introduce explicit state-space representation and transition checks.

Justification:
- Clinical trajectories are stage-like; state transitions are more interpretable than unstructured event lists.
- Validation showed allowed transition compliance and non-absorbing hospitalization behavior.

## D-07 — Build Instability as Decayed Event-Density Signal (Phase B)

Decision:
- Construct instability trajectory using decay plus event increments, and verify anti-drift.

Justification:
- Captures accumulation and partial resolution over time.
- Anti-drift checks prevent pathological long-horizon behavior.

Observed nuance:
- Current unstable-window flag count is zero; threshold/window strategy likely needs refinement with recurrence implementation.

## D-08 — Calibrate Hazard to First-Year 4–6% Escalation Band (Phase C)

Decision:
- Use discrete-time hazard with coefficient constraints and intercept search for annual incidence calibration.

Justification:
- Establishes a clinically interpretable baseline event regime.
- Constrained coefficients preserve probabilistic spread and prevent deterministic collapse.

## D-09 — Include Medication Timing in Hazard (`log(1 + time_since_med)`)

Decision:
- Integrate recency of medication event as a hazard covariate.

Justification:
- Clinically motivated: treatment timing can influence near-term instability and escalation risk.
- Adds temporal treatment context beyond static burden counts.

## D-10 — Model Intervention Policy via Tau/Delta Sweep (Phase D)

Decision:
- Simulate intervention triggers by instability threshold and expected risk reduction magnitudes.

Justification:
- Provides operationally interpretable tradeoff curves (prevented escalation vs workload and false positives).
- Enables policy frontier analysis without claiming individualized treatment causality.

## D-11 — Prioritize Complexity Stacking and Comorbidity Stratification (Phase E)

Decision:
- Quantify hazard and escalation behavior by burden strata and complexity quintiles.

Justification:
- Aligns with thesis focus that deterioration is shaped by multi-diagnosis and medication stacking.
- Produces interpretable cohort-level risk gradients for clinical narrative.

## D-12 — Defer Recommendation Layer Until Closed-Loop Recurrence Exists

Decision:
- Delay patient-category medication recommendation model until recurrence is represented.

Justification:
- Recommendation quality depends on trajectory feedback and repeated-cycle outcomes.
- At the time of decision, Phase E metrics showed zero recurrence, so loop closure was prerequisite.

## D-13 — Stage F Priority

Decision:
- Implement post-admission/intervention state re-entry and recurrence before renaming/restructuring notebooks.

Justification:
- Scientific completeness of cycle logic is higher priority than presentation-layer renaming.
- Stage naming should reflect final functional boundaries after recurrence is implemented.

## D-14 — Implement Response-State Transition Layer in Stage F

Decision:
- Introduce three post-admission response states (`stabilized`, `partial_response`, `nonresponse`) with state-specific hazard multipliers and instability shifts.

Justification:
- Creates clinically interpretable heterogeneity in post-escalation trajectories.
- Enables recurrence to emerge from differentiated response dynamics rather than uniform reset.

## D-15 — Add Cooldown-Constrained Recurrent Escalation Simulation

Decision:
- Simulate recurrent escalation events using adjusted hazard with a 2-month cooldown after each escalation.

Justification:
- Prevents unrealistic event clustering while preserving probabilistic recurrence.
- Provides a practical closed-loop mechanism suitable for Stage G extensions.

## D-16 — Reset Cycle Clock After Every Stage F Escalation

Decision:
- Recompute cycle index and `months_since_cycle_start` after each Stage F escalation event and use this in hazard recomputation.

Justification:
- Operationalizes trajectory re-entry explicitly.
- Aligns with the conceptual requirement that intervention/escalation events alter subsequent risk evolution.

## D-17 — Preserve Phase E Outputs and Write Stage F to Parallel Artifact Namespace

Decision:
- Keep existing Phase E outputs unchanged and write Stage F outputs to `notebook03_phase_f` directories with separate manifest/proof artifacts.

Justification:
- Maintains lineage and comparability between pre-closure and post-closure stages.
- Supports thesis reporting with clear before/after evidence.

## D-18 — Implement Executable Stage H Artifact Namespace

Decision:
- Convert Notebook 05 from design-only to design+execution by adding baseline supervised implementation outputs under `notebook05_stage_h`.

Justification:
- Roadmap dependencies require H completion before later stage comparisons are treated as valid.
- Missing Stage H namespace artifacts created a traceability gap for thesis closure.

## D-19 — Use Notebook 01 Registry Outputs as Upstream Source of Diagnosis/Medication Signals

Decision:
- Reuse `Data/diagnosis_history.parquet` and `Data/medication_event.parquet` generated by Notebook 01 instead of rebuilding synthetic generation logic in late stages.

Justification:
- Preserves Notebook 01 freeze principle while enabling recommendation-layer enrichment.
- Avoids destabilizing upstream generation when downstream integration is sufficient.

## D-20 — Stage L Explainability Must Prefer SHAP-Native Paths

Decision:
- Enforce SHAP-first execution with runtime logging and dependency alignment to avoid silent fallback-only explainability.

Justification:
- Stage L roadmap explicitly requires SHAP global/local explanations.
- Runtime logging provides auditability for method selection and fallback conditions.

## D-21 — Introduce Registry-Backed Multi-Diagnosis Progression and Category Recommendation Layer

Decision:
- Build Stage L multi-diagnosis progression using registry-backed diagnosis history when available, with inferred fallback only when required by schema availability.

Justification:
- Aligns implementation with thesis core intent (multi-diagnosis trajectory + recommendation support).
- Keeps robustness across schema variants without blocking pipeline execution.

