# Stage 1 Single-Family and Analytic-UV Evidence

## Scope

- Canonical proposal: `raster_u_phase_0.00`.
- Full model-input-roundtrip curated dataset: `results/stage1_single_family_raster_u_roundtrip`.
- Eligible instances: 178; fixed seed-0 validation cohort: 35.
- Registered gate: at least 90% hard feasibility and at most 1.10 times teacher length.

## XYZ single-family ablation

Verbatim aggregate fields from `results/stage1_single_family_summary/summary.json`:

| Run | Hard feasibility | Mean missed fraction | Mean length / teacher |
|---|---:|---:|---:|
| Velocity only | 0.5714285714285714 | 0.0668758551498989 | 1.3627668915847977 |
| Coverage-heavy | 0.7428571428571429 | 0.03319255426661838 | 1.8385985055946243 |
| Coverage + length | 0.7428571428571429 | 0.04659621015802751 | 1.4401373733023868 |
| + tangent | 0.6571428571428571 | 0.04827543493972491 | 1.3480275603602416 |
| + surface | 0.6 | 0.04912416142266529 | 1.3594510327791136 |

No restricted single-family run passed the joint gate. Coverage loss raised feasibility by
adding travel; length and tangent penalties recovered some path quality but did not remove
common failures.

## Analytic UV representation

Analytic parameter inversion was implemented for plane, cylinder, hemisphere, saddle,
free-form patch, and torus surfaces. The model generates chart coordinates that are mapped
back to the three-dimensional surface before the unchanged hard checker.

Verbatim validation target audit from
`results/flow_matching_stage1_single_family_uv_run1/target_audit_validation.json`:

- `instances`: 35
- `feasible_rate`: 1.0
- `mean_absolute_missed_change`: 0.0028912764166940316
- `maximum_absolute_missed_change`: 0.027277701153101463
- `mean_length_ratio`: 0.9983466474034378

Training history from `results/flow_matching_stage1_single_family_uv_run1/history.json`:

- 5,000 optimizer steps in 235.03344229608774 seconds.
- Best validation velocity loss: 0.04142387993633747 at epoch 255 / step 4590.
- Checkpoint: `results/flow_matching_stage1_single_family_uv_run1/best.pt`.

The formal K=8 generation evaluation was interrupted before producing an output file.
There is no UV feasibility or path-quality result yet.

## Verification

Twelve targeted tests passed across learning-dataset batching, Flow Matching, and analytic
surface parameterization:

```text
............                                                             [100%]
12 passed in 4.73s
```

Relevant code includes `coverage/patterns.py`, `learning/teacher_dataset.py`, the path
vector-field/noise/sampling modules, and the sanitize/train/evaluate/audit scripts.
