# Stage G Implementation Checklist

Date: 2026-02-28
Status: Ready for iterative execution

## A) Data & Split Governance

- [ ] Confirm frozen split policy is loaded from `stage_g_holdout_freeze.json`
- [ ] Confirm no patient overlap across train/val/test
- [ ] Run time-leakage sanity checks (future columns not used at prediction horizon)
- [ ] Save split audit table

## B) Contract & Adapter

- [ ] Validate canonical contract columns and dtypes
- [ ] Run synthetic adapter normalization
- [ ] Keep registry adapter interface stub callable (no registry data required yet)
- [ ] Save adapter validation report

## C) Messy Data Story Layer

- [ ] Generate mechanism-aware missingness summary
- [ ] Generate missingness-indicator contribution view
- [ ] Generate extreme-value event-risk comparison
- [ ] Save extreme story samples for manual review

## D) Baseline Model Ladder (G-level)

- [ ] Run S0 baseline feature set
- [ ] Run S1 clinical feature set
- [ ] Run S2 interactions/extremes feature set
- [ ] Export model stage comparison table

## E) Permutation Diagnostics

- [ ] Run label permutation collapse check
- [ ] Run feature permutation importance
- [ ] Save importance plot and table

## F) Stage Report Pack

- [ ] Generate Stage G summary report
- [ ] Generate Stage G checklist proof JSON
- [ ] Update phase manifest

## G) Promotion Decision (G -> H)

- [ ] Confirm permutation collapse is present
- [ ] Confirm contract and split audits pass
- [ ] Confirm story outputs (missingness/extremes) are generated
- [ ] Record go/no-go decision for Stage H in work log

## Notes

- Stage G is considered complete only when governance + diagnostics + storytelling + comparison artifacts all exist.
- High headline metrics alone are not sufficient for stage promotion.
