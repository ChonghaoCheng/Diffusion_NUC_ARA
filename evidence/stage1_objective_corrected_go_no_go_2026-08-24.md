# Stage 1 Objective-Corrected Go/No-Go

The teacher sanitizer and all proposal ranking now implement the same constrained
objective: minimize path length among candidates satisfying the missed-coverage
tolerance. Missed fraction is not minimized again inside the feasible set.

## Target audit

- Reordering changed 152 of 200 selected targets.
- The previous selected targets were 1.0708 times longer on average.
- The segment-preserving model-input roundtrip was hard-feasible for 40 of 40
  validation targets.
- Mean roundtrip path length was 1.0022 times the serialized target length.

## Best-of-8 validation

| Run | Hard feasibility | Mean missed | Length / corrected teacher | Mean pipeline time |
|:---|---:|---:|---:|---:|
| Old-target warm model, reevaluated | 50.0% | 0.0505 | 1.716 | 3.290 s |
| Correct target, fresh 5k steps | 32.5% | 0.0835 | 1.614 | 3.155 s |
| Correct target, warm 3k steps | 72.5% | 0.0433 | 1.772 | 3.433 s |

The warm corrected-target run reached 27.5%, 50.0%, 65.0%, and 72.5%
feasibility at K=1, 2, 4, and 8 respectively. By surface at K=8 it reached
60% cylinder, 90% free-form patch, 60% hemisphere, and 80% saddle feasibility.

## Decision

The registered gate requires at least 90% hard feasibility and no more than
1.10 times teacher length. The gate remains **NO-GO**. Warm-starting recovers
coverage through denser and longer paths, while fresh training shortens paths
but misses more surface. The current vector field has not learned the
constrained shortest-path frontier.

Raw report and plot:

- `results/stage1_objective_corrected_summary/report.md`
- `results/stage1_objective_corrected_summary/summary.json`
- `results/stage1_objective_corrected_summary/stage1_objective_corrected_diagnostics.png`
- `results/stage1_objective_correct_200/audit_segment_preserving_validation_float64_denorm.json`

The full regression suite passed: 41 tests.
