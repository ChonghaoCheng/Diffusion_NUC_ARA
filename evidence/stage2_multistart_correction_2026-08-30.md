# Stage 2 Template Audit and Multi-Start Correction

## Deterministic-template audit

Source: `results/stage2_structured_mode_freedom_audit/metrics.json`

- Candidates audited: `491`
- Source instances: `187`
- All source `refinement_iterations` were zero: `true`
- Exact template-match rate at a `1e-6 r` threshold: `0.9409368635437881`
- Mean control RMS divided by footprint radius: `1.5995468543953167e-07`
- Maximum control error divided by footprint radius: `2.6924640258485312e-06`
- Fraction of controls above `0.01 r`: `0.0`
- Deterministic analytic-template hard-feasible rate: `1.0`
- Mean template/source length ratio: `0.998153350153845`

The former five-mode Stage 2 dataset therefore contained no material within-mode
continuous variation. Its Flow Matching result measures noisy template reproduction,
not generative multimodality.

## Multi-start teacher pilot

Source: `results/stage2_multistart_teacher_pilot/metrics.json`

- Instances: `8`
- Instance-mode tasks: `19`
- Tasks with at least one diverse hard-feasible alternative: `19/19`
- Mean retained alternatives per task: `3.789473684210526`
- Mean maximum alternative/template control distance: `0.4710976639598864 r`
- Mean best alternative/template length ratio: `0.8816624462546`
- Explicit hard evaluations: `1007`

This pilot established that the tasks do possess nontrivial feasible continuous control
freedom; the old teacher simply did not sample it.

## Corrected dataset contract

Sources:

- `results/stage2_multistart_structured_40_v4/summary.json`
- `results/stage2_multistart_structured_40_v4/roundtrip_audit.json`

The corrected teacher uses derivative-free multi-start search, hard feasibility,
distance filtering, and model-input float32 control quantization. Original optimized
controls are archived directly rather than recovered from dense 3D paths.

- Balanced instances: `40`
- Candidates: `503`
- Explicit hard evaluations: `5406`
- Build time: `114.23842407669872` seconds
- Mean alternatives per instance-mode: approximately `3.83-4.00`
- Mean alternative/template control distance by mode: `0.348-0.384 r`
- Complete model-input roundtrip hard-feasible rate: `1.0`
- Maximum roundtrip missed-fraction change: `0.0`
- Mean roundtrip length ratio: `1.0`

An intermediate v2 archive inferred controls from dense paths and failed on hemisphere
chart/pole cases. An intermediate v3 archive stored direct float64 controls but retained
one float32-threshold-fragile candidate. Both are diagnostic only. V4 is the admitted
dataset.

## Three-seed Flow Matching replication

Sources:

- `results/stage2_multistart_summary/summary.json`
- `results/stage2_multistart_summary/report.md`
- `results/stage2_multistart_summary/stage2_multistart_seed_summary.png`

Protocol: fixed 32/8 instance split, fixed surface subsampling, three training seeds,
5,000 optimization steps, 32 Heun steps, and K=8 per explicitly conditioned mode.

| Seed | Per-sample feasible | K=8 mode recovery | All modes recovered | Feasible length ratio |
|---:|---:|---:|---:|---:|
| 0 | 0.26704545454545453 | 0.5454545454545454 | 0.375 | 1.0616935234164706 |
| 1 | 0.3068181818181818 | 0.5909090909090909 | 0.375 | 1.0747033770153036 |
| 2 | 0.056818181818181816 | 0.2727272727272727 | 0.25 | 1.119543648583601 |

Aggregate:

- Mean per-sample feasibility: `0.2102272727272727`
- Sample standard deviation: `0.13433625474473737`
- Mean K=8 mode recovery: `0.46969696969696967`
- Sample standard deviation: `0.17208813169091738`
- Mean feasible length ratio: `1.0853135163384584`
- Deterministic template feasible rate: `1.0`
- Full regression suite: `51 passed in 9.55s`

## Decision

The previous Stage 2 interpretation is withdrawn. On the corrected multi-start
distribution, standard independently coupled conditional Flow Matching is unstable and
does not beat the deterministic template baseline. The next experiment must change the
coupling or conditional representation and expand the instance cohort. Synthetic colour
and direct C-space stages remain deferred.
