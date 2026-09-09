# Stage 2 template-residual representation and expanded cohort

## Representation contract

- Coordinate system: subtract the deterministic structured template and scale intrinsic
  UV displacement by surface extent divided by footprint radius.
- The first residual implementation did not preserve the hard label for every target
  because its float64 transform was not the arithmetic used by the float32 model path.
- The corrected transform performs subtraction, scaling, inverse scaling, and template
  addition in float32 model arithmetic.
- The admitted 160-instance v6 cohort contains 2,055 candidates. Its complete residual
  roundtrip audit reports 2,055/2,055 hard-feasible candidates.

Sources:

- `results/stage2_multistart_structured_160_v6/residual_roundtrip_audit.json`
- `src/diffusion_coverage/coverage/patterns.py`
- `scripts/filter_dataset_by_audit.py`

## Expanded three-seed result

The formal protocol used one fixed 128/32 instance split, batch 32, 5,000 optimizer
updates, independent coupling, 32 Heun steps, and eight samples per explicitly conditioned
teacher-supported mode. Each validation split contains 93 instance-mode tasks.

Verbatim result table from `results/stage2_residual_160_v6_multiseed/report.md`:

> | seed0 | 92.47% | 100.00% | 100.00% | 1.0498 | 0.9401 | 83/93 |
> | seed1 | 81.32% | 100.00% | 100.00% | 1.0430 | 0.9352 | 82/93 |
> | seed2 | 81.32% | 100.00% | 100.00% | 1.0403 | 0.9330 | 80/93 |

Columns are candidate hard-feasible rate, K=8 mode recovery, all-mode recovery, generated
length divided by same-mode best teacher length, generated length divided by deterministic
template length, and modes shorter than the deterministic template.

The deterministic template baseline on these 93 validation modes is 1.120776 times the
same-mode best teacher length on average and is strictly longer in 89 modes.

Sources:

- `results/stage2_residual_160_v6_multiseed/residual_diagnosis.json`
- `results/flow_matching_stage2_residual_160_v6_b32_5k_seed0/eval_validation_k8/metrics.json`
- `results/flow_matching_stage2_residual_160_v6_b32_5k_seed1/eval_validation_k8/metrics.json`
- `results/flow_matching_stage2_residual_160_v6_b32_5k_seed2/eval_validation_k8/metrics.json`
- `results/stage2_residual_160_v6_multiseed/expanded_stage2.png`

## Batch/update control

At approximately equal sample exposure, batch 8 with 20,000 updates trained in 332.04 s,
reached 89.11% candidate feasibility and a 1.0444 mean length ratio. Batch 32 with 5,000
updates trained in 95.97 s, reached 92.47% candidate feasibility and a 1.0498 mean length
ratio. Both recovered all 93 modes at K=8. Batch 32 is retained for multi-seed throughput;
batch 8 remains the slightly better seed-0 length control.

Source: `results/stage2_residual_160_v6_batch_ablation/report.md`.

## Finite-teacher diversity diagnosis

At the originally chosen 0.15-footprint-radius residual-control threshold, exact
generated-to-teacher point coverage is low, but teacher leave-one-out coverage is lower.
For seed 0, the threshold sweep reports:

> | 0.15 | 12.51% | 2.37% |
> | 0.50 | 95.48% | 93.98% |
>
> Mean teacher-to-generated nearest distance: 0.3119r
> Mean teacher leave-one-out nearest distance: 0.3413r
> Mean generated/teacher energy distance: 0.1633r

Thus a single 0.15r exact-match fraction is dominated by sparse, separated finite teacher
alternatives and is not by itself evidence of mode collapse. Energy distance and the full
threshold curve remain reported because they still detect distribution mismatch.

Sources:

- `results/flow_matching_stage2_residual_160_v6_b32_5k_seed0/eval_validation_k8/control_diversity.md`
- `scripts/analyze_control_diversity.py`
- `src/diffusion_coverage/evaluation/control_diversity.py`

## Verification

The complete unit test suite passed: 57 tests.

