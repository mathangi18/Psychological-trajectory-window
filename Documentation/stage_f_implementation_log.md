# Stage F Implementation Log (Closed-Loop Recurrence)

Date implemented: 2026-02-28  
Notebook: `Notebooks/03_phase_e_f_cycle_closure.ipynb` (integrated Phase E+F notebook)  
Execution status: Executed successfully in `ml-ultra` kernel

## 1) Why Stage F was added

Phase E evidence showed:
- `mean_cycles_per_admitted_patient = 1.0`
- `admission_recurrence_rate = 0.0`

This meant the cycle concept was not yet operationally closed. Stage F was implemented to explicitly model post-escalation re-entry and recurrent cycles.

## 2) Functional Additions in Stage F

### A. Post-admission response-state transitions
At each baseline admission event, a response state is assigned:
- `stabilized`
- `partial_response`
- `nonresponse`

Assignment mechanism:
- Nonresponse logit uses diagnosis burden, medication burden, and instability level.
- Probabilities are normalized and sampled stochastically.

### B. State-dependent trajectory modification
Each active response state contributes:
- Hazard multiplier
- Instability shift

Values used:
- `stabilized`: multiplier `0.62`, instability shift `-0.14`
- `partial_response`: multiplier `0.82`, instability shift `-0.05`
- `nonresponse`: multiplier `1.08`, instability shift `+0.03`

Effect decay:
- Exponential decay by months since admission: `exp(-months_since_admission / 6)`

### C. Recurrent escalation simulation
- Starting from adjusted hazard, recurrent escalation events are sampled probabilistically.
- Baseline admission events are preserved.
- A 2-month cooldown is applied after each escalation to avoid unrealistic clustering.
- Recurrence simulation is allowed only after a patient has at least one admission.

### D. Cycle clock reset (re-entry implementation)
After each Stage F escalation event:
- `cycle_id_stage_f` is updated by cumulative event count per patient.
- `months_since_cycle_start` is reset using grouped cumulative counts.
- `log_tsm_stage_f = log1p(months_since_cycle_start)`
- Hazard is recomputed with Stage C coefficients under the reset cycle clock.

This operationalizes cycle re-entry in the longitudinal panel.

## 3) Artifacts Generated

### Tables
- `Results/tables/notebook03_phase_f/phase_f_admission_response_profile.csv`
- `Results/tables/notebook03_phase_f/phase_f_cycle_recurrence_metrics.csv`
- `Results/tables/notebook03_phase_f/phase_f_cycle_hazard_trajectory.csv`
- `Results/tables/notebook03_phase_f/phase_f_transition_dynamics_summary.json`
- `Results/tables/notebook03_phase_f/phase_f_closed_loop_panel.parquet`

### Figures
- `Results/figures/notebook03_phase_f/phase_f_response_mix.png`
- `Results/figures/notebook03_phase_f/phase_f_recurrence_gap_distribution.png`
- `Results/figures/notebook03_phase_f/phase_f_cycle_hazard_trajectory.png`

### Reports and metadata
- `Results/reports/notebook03_phase_f/phase_f_closed_loop_summary.txt`
- `Results/reports/notebook03_phase_f/phase_f_checklist_proof.json`
- `Data/metadata/phase_f_manifest.json`

## 4) Observed Stage F Metrics (Executed Run)

From generated Stage F summary artifacts:
- Rows: `4,900,000`
- Patients: `100,000`
- Baseline admissions: `42,773`
- Total Stage F escalations: `55,142`
- Additional recurrent escalations: `12,369`
- Mean cycles per admitted patient: `1.289178`
- Admission recurrence rate: `0.247960`
- Mean time between cycles: `535.353` days

Response-state mix at baseline admissions:
- `stabilized`: `21,740` (`50.83%`)
- `partial_response`: `8,580` (`20.06%`)
- `nonresponse`: `12,453` (`29.11%`)

## 5) Validation/Proof Status

`phase_f_checklist_proof.json` confirms:
- Post-admission response states assigned
- Re-entry cycle clock reset implemented
- Recurrent escalation simulated
- Recurrence metrics generated
- Transition summary generated
- Closed-loop panel generated
- Manifest generated

All listed checks evaluated to `true`.

## 6) Methodological Caveat to Carry Forward

Stage F recurrence is a simulation layer based on synthetic assumptions and policy-style transition rules. It should be interpreted as mechanistic closure of the cycle hypothesis, not as externally calibrated causal recurrence estimation.

## 7) Immediate Follow-On for Stage G/H

Recommended next implementation sequence:
1. Add explicit intervention action types (switch, augmentation, adherence support)
2. Add patient-category stratification using burden and trajectory history
3. Estimate category-specific expected stabilization to prepare recommendation layer (Stage I)
