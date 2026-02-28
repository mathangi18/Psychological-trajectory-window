# Experimental Design and Justification (Through Phase F)

Date: 2026-02-28

## 1) Design Objective

Primary objective:
- Build and validate a synthetic longitudinal framework that can represent psychiatric escalation as a probabilistic, multi-stage cycle.

Secondary objectives:
- Quantify whether instability windows precede admissions.
- Estimate escalation hazard under realistic non-deterministic assumptions.
- Evaluate intervention policy burden/benefit tradeoffs.
- Test whether complexity stacking (multi-diagnosis and medication burden) aligns with higher escalation hazard.

## 2) Conceptual Model

Target process:
1. Initial diagnosis and medication start  
2. Instability accumulates over time  
3. Escalation hazard rises  
4. Admission occurs probabilistically  
5. Intervention modifies trajectory  
6. Patient may re-enter cycle with changed risk

Design status through Phase F:
- Steps 1–6 operationalized, including recurrent post-escalation cycle behavior.

## 3) Population and Time Design

Population:
- 100,000 synthetic patients.

Temporal granularity:
- Daily process in generation engine, with monthly-aligned analytical panels for hazard and complexity analyses.

Follow-up horizon:
- Multi-year trajectory designed to capture accumulation and delayed effects.

Justification:
- Large sample and long horizon are required to estimate low-probability escalation dynamics and intervention tradeoffs with stable summary statistics.

## 4) Phase-Wise Experimental Structure

### Phase A — State Engine Construction and Validation

Design elements:
- Explicit state space and allowed transitions.
- Non-absorbing hospitalization checks.
- Active diagnosis and medication accumulation at panel level.

Justification:
- Ensures trajectory interpretability and structural validity before inferential phases.

Success criteria:
- Transition compliance true.
- Non-absorbing hospitalization true.
- Zero illegal transition rows.

### Phase B — Instability Signal Construction

Design elements:
- Decayed instability trajectory with event increments.
- Anti-drift checks and schema/type contract checks.

Justification:
- Creates a clinically plausible latent burden signal without assuming deterministic progression.

Success criteria:
- Anti-drift check passes.
- Type and cross-verification checks pass.

### Baseline Audit (Bridge to Phase C)

Design elements:
- Overlap audit, threshold activation, permutation collapse, calibration/PR probes.

Justification:
- Establishes whether instability-alone signal is sufficient (it was not), motivating enriched hazard modeling.

### Phase C — Discrete-Time Hazard Calibration

Design elements:
- Hazard predictors: instability, log time-since-medication, incremental burden, time trend.
- Intercept calibrated to first-year annual escalation target (4–6%).

Justification:
- Aligns model behavior to interpretable annual escalation regime while preserving heterogeneity.

Success criteria:
- Annual first-year escalation within 0.04–0.06.
- Probability distribution remains non-degenerate.

### Phase D — Intervention Policy Simulation

Design elements:
- Tau threshold grid and delta reduction grid.
- Outputs: expected prevented events, workload, false positives, frontier.

Justification:
- Converts risk estimates into operational policy analysis and quantifies burden-benefit tradeoffs.

Success criteria:
- Tradeoff and frontier artifacts generated.
- Prevented admissions and burden metrics non-trivial.

### Phase E — Complexity and Stacking Analysis

Design elements:
- Window quantification from instability spikes to admissions.
- Complexity growth curves.
- Hazard by complexity quintile and comorbidity bins.
- Cycle recurrence metrics.

Justification:
- Directly tests thesis claim that cumulative clinical complexity is linked to escalation dynamics.

Success criteria:
- Window and burden-stratified outputs generated.
- Complexity-risk gradient observable.

Observed limitation at end of Phase E:
- Recurrence remained zero, indicating incomplete cycle closure.

### Phase F — Closed-Loop Recurrence and Re-Entry

Design elements:
- Post-admission response-state assignment (`stabilized`, `partial_response`, `nonresponse`).
- State-dependent hazard multipliers and instability shifts with exponential decay.
- Recurrent escalation simulation with cooldown constraints.
- Cycle clock reset (`months_since_cycle_start`) after every Stage F escalation event.
- Recomputed hazard under reset cycle clock.

Justification:
- Converts conceptual cycle closure into explicit operational mechanics.
- Enables quantification of recurrence burden needed before recommendation modeling.

Success criteria:
- Recurrence metrics become non-zero.
- Closed-loop artifacts and proof files are generated.

Observed Phase F outcomes:
- Baseline admissions: 42,773
- Total Stage F escalations: 55,142
- Additional recurrent escalations: 12,369
- Mean cycles per admitted patient: 1.289
- Admission recurrence rate: 0.248

## 5) Outcome Definitions

Primary outcomes:
- Escalation hazard behavior and calibration.
- Admission-related risk summaries.

Secondary outcomes:
- Window prevalence before admission.
- Complexity burden gradients.
- Intervention workload and false-positive burden.

Operational outcomes:
- Policy frontier outputs usable for capacity planning.

## 6) Validation Strategy

Validation layers:
- Structural validation (state transitions, schema contracts).
- Probabilistic validation (distribution checks, permutation collapse).
- Calibration validation (target annual rate).
- Output completeness validation (checklist proof artifacts).

Justification:
- Multi-layer validation reduces risk of reporting artifacts that are numerically correct but structurally or inferentially invalid.

## 7) Design Choices Deferred to Next Stage

Deferred elements:
- Patient-category regimen effectiveness prediction.
- Adherence and treatment-response heterogeneity as explicit transition drivers.

Reason for deferral:
- Recurrence is now implemented; remaining deferred items require category-aware treatment effect and adherence modeling layers.

## 8) Design Adequacy Statement (Current Stage)

The current design is adequate for demonstrating probabilistic escalation, calibration discipline, intervention tradeoffs, complexity-associated risk gradients, and closed-loop recurrence behavior. It is not yet adequate for treatment recommendation claims, which require Stage G/H/I implementation.

