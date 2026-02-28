# Clinical Interpretation (Through Phase F)

Date: 2026-02-28  
Project: Psychiatric escalation cycle in synthetic longitudinal data

## 1) Clinical Question and Interpretation Frame

This project asks whether a patient-level deterioration process can be represented as a repeated escalation cycle rather than a single-step event. Clinically, the cycle of interest is:

1. Initial diagnosis and treatment start  
2. Instability signals accumulate  
3. Hazard of escalation increases  
4. Hospital admission risk materializes  
5. Intervention attempts to alter trajectory  
6. Patient re-enters trajectory with changed risk state

Through Phase F, all six conceptual steps are now represented, including post-admission re-entry and recurrent-cycle behavior.

## 2) Interpretation of Phase A (State Engine)

Phase A established the longitudinal state engine and transition validity.

Clinical interpretation:
- The model supports an interpretable disease-course representation (state progression and transitions rather than static snapshots).
- Non-absorbing hospitalization behavior was validated, which is clinically appropriate because admission is not terminal in psychiatric trajectories.
- Active diagnosis and medication counts were explicitly tracked, enabling complexity burden interpretation instead of binary diagnosis status.

Evidence summary:
- Allowed transition compliance: true  
- Non-absorbing hospitalization: true  
- Illegal transition rows: 0  
- Scale: 100,000 patients; 4.9M monthly panel rows

## 3) Interpretation of Phase B (Instability Construction)

Phase B created instability as a decayed, event-driven trajectory with anti-drift controls.

Clinical interpretation:
- Instability behaves as a dynamic burden signal rather than a fixed trait.
- Anti-drift behavior indicates the trajectory does not explode numerically, supporting plausible longitudinal behavior.
- Positive and negative month-to-month changes coexist, consistent with waxing/waning clinical behavior.

Important nuance:
- `unstable_windows = 0` in current artifact checks. This suggests instability thresholding/window definitions may currently be conservative or misaligned with expected event prevalence, and should be revisited when implementing recurrence in Stage F.

## 4) Interpretation of Baseline Audit (Pre-Phase C)

Baseline audit showed weak discriminative signal when instability is used in isolation.

Clinical interpretation:
- AUROC near 0.5 indicates that instability alone is insufficient as a deterministic triage signal.
- Permutation collapse supports that observed signal is not due to leakage.
- This result justifies structural enrichment (time context, medication timing, complexity burden, and intervention policy modeling).

Implication:
- The project correctly moved toward enriched, probabilistic risk modeling rather than forcing deterministic thresholds.

## 5) Interpretation of Phase C (Hazard Model)

Phase C introduced a discrete-time hazard function using instability, medication timing (`log(1 + time_since_med)`), event increments, and time progression.

Clinical interpretation:
- Calibration to a first-year annual escalation target band of 4–6% gives a clinically interpretable baseline incidence regime.
- Hazard spread (`p01≈0.0035`, `p50≈0.0050`, `p99≈0.0082`) confirms non-deterministic risk assignment, aligned with psychiatric heterogeneity.
- Incorporating time-since-med is clinically meaningful because treatment recency often modifies short-term risk behavior.

Conclusion:
- Phase C transforms instability into calibrated probabilistic risk, enabling policy analysis in subsequent phases.

## 6) Interpretation of Phase D (Intervention Simulation)

Phase D simulated threshold-triggered intervention policies over tau/delta grids and quantified expected prevented admissions, workload, and false-positive burden.

Clinical interpretation:
- Interventions have measurable expected benefit, but benefit is workload-dependent and accompanied by operational burden.
- This captures a realistic clinical operations tension: broader intervention policies can prevent more events but increase unnecessary touchpoints.
- The frontier analysis is suitable for service planning discussions (capacity-limited intervention design).

Interpretive caution:
- Phase D uses policy simulation assumptions; it is not yet a patient-specific causal effect model.

## 7) Interpretation of Phase E (Complexity and Stacking)

Phase E quantified escalation windows and complexity burden across diagnosis/medication strata.

Clinical interpretation:
- 72.0% of admissions were preceded by an instability spike within 180 days, supporting clinically relevant pre-escalation windows.
- Mean hazard increases with complexity burden, consistent with real-world multimorbidity/polypharmacy risk stacking.
- Complexity growth curves demonstrate cumulative burden over follow-up, supporting the need for early trajectory-aware intervention.

Critical gap identified in Phase E outputs:
- `mean_cycles_per_admitted_patient = 1.0` and `admission_recurrence_rate = 0.0`.

Interpretation:
- Phase E alone captured escalation and burden stratification but not repeated recurrence.

## 8) Interpretation of Phase F (Closed-Loop Recurrence)

Phase F implemented and executed post-admission response-state transitions, cycle-clock resets, and recurrent escalation simulation.

Clinical interpretation:
- The model now represents trajectory re-entry after escalation and can generate repeated cycles.
- Recurrence prevalence is non-zero and substantial (`admission_recurrence_rate ≈ 24.8%`), which is consistent with the intended cyclic hypothesis.
- Mean cycles per admitted patient increased to `1.289`, confirming loop closure in the synthetic process.
- Additional recurrent escalations (`12,369`) indicate that post-index trajectories now contribute materially to burden.

Response-state mix interpretation:
- Stabilized: 50.8%
- Partial response: 20.1%
- Nonresponse: 29.1%

This response-state structure provides clinically interpretable heterogeneity for next-stage intervention modeling.

## 9) What Is Clinically Supported at This Stage

Supported:
- Probabilistic deterioration and escalation risk representation  
- Complexity-associated risk gradient  
- Early warning window quantification  
- Operational tradeoff framing for intervention thresholds  
- Closed-loop recurrence dynamics after escalation/intervention

Not yet supported (pending Stage G+):
- Explicit adherence-response trajectories  
- Patient-category-specific medication effectiveness recommendations

## 10) Report-Ready Positioning Statement

At the current stage, the project demonstrates a probabilistic, complexity-dependent escalation system with identifiable pre-admission windows, policy-sensitive intervention burden, and implemented closed-loop recurrence dynamics. The next scientific step is to move from recurrence-capable simulation to patient-category treatment recommendation and adherence-aware intervention modeling.

## 11) Stage H and Stage L Clinical Closure Update

Stage H (baseline explainable layer) is now operational with explicit namespace artifacts under `notebook05_stage_h`, including calibration, subgroup error table, and bootstrap confidence intervals.

Stage L trust layer is also operational with:
- SHAP-native explainability execution (`shap_tree`),
- intrinsic alignment outputs,
- fairness subgroup audit,
- failure-case catalog,
- counterfactual examples,
- multi-diagnosis progression outputs,
- patient-category recommendation artifacts.

## 12) Current Clinical Assumptions and Limitations

Assumptions:
- Synthetic trajectory dynamics are informative proxies for deterioration patterns.
- Category-level recommendations are used for policy exploration, not direct individualized prescription.
- Response-state and complexity burden remain valid organizing dimensions for clinical interpretation.

Limitations:
- Medication recommendation remains non-causal (no treatment-effect identification).
- Medication event table currently lacks rich medication label fields in this environment; fallback policy mapping is used.
- External validity to real registry populations remains pending Stage H4/real-adapter validation.

## 13) Updated Positioning Statement

The project now supports an end-to-end thesis narrative from cycle generation and recurrence through explainable trust outputs and category-level recommendation policy. The remaining translational step is real-registry validation and causal treatment policy testing, not additional synthetic-only structural completion.

