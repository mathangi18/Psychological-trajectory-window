# Synthetic Data Generation: Design, Procedure, and Justification

Date: 2026-02-28

## 1) Purpose

This document formalizes the synthetic data generation layer used in the project and records the rationale behind generation choices. It is intended as a stable reference for final reporting and for future transition to registry-calibrated data pipelines.

## 2) Generation Objective

Generate a registry-compatible, longitudinal psychiatric cohort that supports:
- Trajectory modeling of instability and escalation risk
- Event-table reconstruction (inpatient, outpatient, medication, diagnosis)
- Downstream hazard calibration and intervention policy simulation
- Complexity and stacking analyses over time

## 3) Core Generative Structure

The generator implements:
- A dynamic instability process with decay and stochastic perturbation
- A probabilistic hazard mapping to escalation/admission events
- Patient-level heterogeneity via frailty/random-effect style variation
- Event emissions that can be represented in relational registry structure

Why this structure was chosen:
- It preserves uncertainty and heterogeneity, which are required for plausible psychiatric trajectory simulation.
- It enables reproducible decomposition into phase-specific analytical panels.

## 4) Data Products and Registry Layout

Primary generated entities include:
- Patient-level table
- Inpatient events
- Outpatient events
- Medication events
- Diagnosis history
- Longitudinal instability/panel artifacts
- Metadata and feature dictionary artifacts

Format discipline:
- Parquet is the authoritative storage format.
- CSV is used only for selected reporting extracts where needed.

Justification:
- Parquet preserves schema and scales better for repeated analytical passes.

## 5) Reproducibility Controls

Controls used:
- Deterministic seed
- Explicit data types for major arrays/columns
- Project-root anchored paths
- Phase-separated output folders
- Check/proof artifacts for structural and output integrity

Justification:
- Ensures reproducibility across sessions and minimizes hidden implementation drift.

## 6) Phase-A/B Enrichment Integrated with Generation

Although raw generation occurs in Notebook 01 baseline engine blocks, this project integrated two critical enrichment layers in the same broad generation phase:

Phase A enrichment:
- Explicit multi-state representation
- Allowed transition validation
- Non-absorbing hospitalization verification

Phase B enrichment:
- Decayed instability construction from event increments
- Anti-drift and type-contract checks
- Export of panel-level instability artifacts used in downstream phases

Why this was necessary:
- Baseline event generation alone is insufficient for clinically interpretable cycle analysis.
- Enrichment creates the intermediate state and signal artifacts required for hazard and intervention modeling.

## 7) Evidence of Generation Adequacy (Current Stage)

Observed output scale is consistent with a large synthetic cohort used in the project:
- 100,000 patients
- Approximately 4.9M panel rows for monthly-level analysis artifacts
- Event tables generated and structurally validated

Structural checks observed through reports:
- Transition compliance passed
- Non-absorbing hospitalization passed
- Schema/type contracts passed

## 8) Known Generation-Layer Constraints

Current constraints to carry into final report:
- Loop closure is now implemented in Stage F, but recurrence remains simulation-based and not yet registry-calibrated.
- Some threshold-derived window indicators are conservative in current outputs (example: unstable-window count in Phase B artifact checks).
- Synthetic distributions are not yet recalibrated with real registry rates/coding variability.

## 9) What This Generation Layer Supports Reliably

Supports reliably:
- Controlled method development
- Hazard calibration workflows
- Intervention tradeoff simulation
- Complexity burden stratification
- Closed-loop recurrence simulation with state-dependent post-admission dynamics

Does not yet support fully:
- Individualized regimen recommendation claims

## 10) Stage F Extension Implemented

Implemented in Notebook 03 Stage F:
- Post-admission response-state assignment at escalation events (`stabilized`, `partial_response`, `nonresponse`)
- State-dependent hazard multipliers and instability shifts with exponential decay
- Recurrent escalation simulation with cooldown constraint
- Cycle clock reset after each Stage F escalation event
- Stage F closed-loop panel and manifest outputs

Observed outputs:
- Baseline admissions: 42,773
- Stage F total escalations: 55,142
- Additional recurrent escalations: 12,369
- Mean cycles per admitted patient: 1.289
- Recurrence rate: 0.248

## 11) Next Generation-Layer Extension (Post-Stage F)

Required additions for Stage G/H readiness:
- Action-specific intervention modeling (switch, augmentation, adherence support)
- Category-level heterogeneity in treatment effects
- Recommendation-layer training labels tied to recurrent-cycle outcomes
- Registry calibration hooks for recurrence and response-state distributions

These additions should be implemented without breaking existing output lineage and validation contracts.