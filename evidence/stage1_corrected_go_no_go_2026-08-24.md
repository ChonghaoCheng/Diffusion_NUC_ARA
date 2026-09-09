# Contract-Corrected Stage 0/1 Evidence (2026-08-24)

## Data contract

- Reconstructed each surface from archived vertices plus its original quadrature
  (`sample_face_indices`, barycentric coordinates, normals, and area weights).
- Stored candidate waypoints in float64 and hard-rechecked serialized candidates.
- Required a `0.01` missed-coverage robustness margin during dataset sanitization.
- Segment-preserving variable-token reconstruction retained every source-segment boundary.
- Validation target roundtrip: `40/40` feasible, mean length ratio `1.0088002646779`.

Sources: `results/stage1_robust_200/audit_segment_preserving_density23_validation.json`,
`results/stage1_robust_200/manifest.jsonl`.

## Corrected representation audit

The audit covered 200 expert plans. At 128 B-spline control points:

| Metric | p95 |
|:---|---:|
| Geometry error / footprint radius | 0.3291257416845484 |
| Absolute missed-fraction change | 0.025390624999999896 |
| Absolute relative-length change | 0.03879898207854012 |

No tested fixed control-point count passed all registered thresholds. The selected default
remains a padded variable-token path.

Source: `results/stage0_representation_audit_corrected/summary.json`.

## Corrected Stage 1 K=8 runs

| Run | Best validation loss | Feasible | Mean missed | Length / teacher | Mean pipeline time |
|:---|---:|---:|---:|---:|---:|
| Segment-preserving, 3.2k updates | 0.204002 | 22.5% | 0.083210 | 1.471756 | 3.1235 s |
| Longer training | 0.170226 | 32.5% | 0.073063 | 1.587072 | 3.4611 s |
| Smooth base, 10% correlation length | 0.056648 | 50.0% | 0.050234 | 1.634870 | 3.5359 s |

The smooth-base anytime feasible rates were `17.5%`, `30.0%`, `40.0%`, and `50.0%`
for K=`1`, `2`, `4`, and `8`. The classical teacher remained `100%` feasible with mean
solve time `4.6726 s` and median `2.6316 s` on the same validation instances.

Sources:

- `results/flow_matching_stage1_corrected_run3_segment_preserving/eval_k8_anytime/metrics.json`
- `results/flow_matching_stage1_corrected_run4_long_train/eval_k8_anytime/metrics.json`
- `results/flow_matching_stage1_corrected_run5_smooth_base/eval_k8_anytime/metrics.json`
- `results/stage1_corrected_summary/report.md`
- `results/stage1_corrected_summary/stage1_corrected_diagnostics.png`

## Decision

The Stage 1 gate requires at least 90% feasibility and mean generated length no more than
1.10 times teacher length. The selected run achieved 50.0% and 1.634870 times teacher
length. Stage 1 is therefore a no-go; Stage 2 multimodality and C-space FM do not start
under the current model/data configuration.

Earlier Stage 1 run directories are retained as diagnostic-only because their evaluation
reconstructed a different surface quadrature and admitted threshold-fragile float32 targets.

