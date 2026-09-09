# Stage 1 Structured Control-Token Gate

## Dense analytic-UV baselines

Formal K=8 results on the 35-instance single-family validation cohort:

- Dense analytic UV: hard feasibility `0.7714285714285715`, mean length ratio `1.5359495195459056`.
- UV plus length/tangent: hard feasibility `0.17142857142857143`, mean length ratio `1.0789100917111767`.
- The six feasible length/tangent instances were a subset of the 27 dense-UV feasible instances.

Sources:

- `results/flow_matching_stage1_single_family_uv_run1/eval_k8/metrics.json`
- `results/flow_matching_stage1_single_family_uv_length_tangent_run1/eval_k8/metrics.json`

This isolates a coverage-length trade-off: scalar intrinsic-length pressure reaches the
length gate by deleting required coverage strokes.

## Hard-feasibility-preserving shortcut probe

On four cylinder instances, insertion plus shortcutting repaired one infeasible proposal
and substantially shortened two feasible proposals. Final per-instance length ratios were
`1.888`, `1.198`, `2.198`, and `1.015`. This distinguishes removable local redundancy
from genuine extra loops/strokes, but is too costly and incomplete to satisfy the gate.

Source: `results/flow_matching_stage1_single_family_uv_run1/eval_k8_shortcut_probe4/metrics.json`.

## Raster control-token representation

The representation stores alternating coverage-stroke endpoints in unwrapped analytic UV.
Token count is derived from known surface geometry, footprint radius, overlap, and sweep
axis. The decoder densifies coverage strokes at the teacher spacing but leaves connectors
direct, then maps to 3D and invokes the unchanged hard checker.

Target contract on all 163 unrepaired `raster_u_phase_0.00` instances:

- Token count: 10 to 32; all 163 counts matched the geometry-derived count.
- Hard feasibility: 100%.
- Mean decoded/source length ratio: `0.9965958645581788`.
- p95 decoded/source length ratio: `1.0055890663925369`.

Formal validation target audit on the seed-0 split:

- Instances: 33.
- Hard feasibility: 100%.
- Mean target roundtrip length ratio: `0.9979253711925614`.

Source: `results/flow_matching_stage1_raster_control_run1/target_audit_validation.json`.

## Formal K=8 result

From `results/flow_matching_stage1_raster_control_run1/eval_k8/metrics.json`:

- Hard feasibility: `0.9393939393939394` (`31/33`).
- Mean length / teacher: `0.9268901999483329`.
- Feasible-only mean length / teacher: `0.9307162889351336`.
- Mean path tokens: `18.303030303030305`.
- Mean total pipeline time: `1.8454350625926799` seconds.
- K=4 already reached the same `0.9393939393939394` feasibility.
- Failures: `cylinder_0027` at missed fraction `0.302734375`, and
  `hemisphere_0016` at missed fraction `0.05461213365754858`.

The point estimate passes the registered 90%-feasibility / 1.10-length gate. The 95%
Wilson interval for 31/33 is reported in `results/stage1_structured_summary/report.md`; its
lower bound remains below 90%, so larger-cohort replication remains required.

## Verification

```text
................................................                         [100%]
48 passed in 12.21s
```

Summary artifacts:

- `results/stage1_structured_summary/report.md`
- `results/stage1_structured_summary/summary.json`
- `results/stage1_structured_summary/stage1_feasibility_length.png`
