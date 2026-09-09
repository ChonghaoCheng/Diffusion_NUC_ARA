# Colour-aware search, cross-axis vocabulary, and v10 learning

## Colour-aware multi-start search

The matched-budget pilot evaluated 60 tasks with the original four-restart,
12-step teacher budget. It produced no direct feasibility rescues, while augmenting
the original library with direct-search candidates reduced feasible path length by
1.62%, 1.80%, and 4.12% for easy, medium, and hard tasks respectively.

Source: `results/colour_aware_teacher_pilot_v1/summary.json`.

A targeted 16-restart, 48-step run rescued three of four selected near-boundary
failures. This is mechanism evidence only, not a population estimate.

Source: `results/colour_aware_teacher_targeted_v1/summary.json`.

## Missing cross-axis proposal

The analytic raster-v audit identified a missing discrete proposal family on periodic
surfaces. On cylinders it raised finite-library success from 61.88% to 100.00% at easy
difficulty and from 35.62% to 65.00% at medium difficulty. On hemispheres the initial
numerical-chart implementation raised success from 59.38% to 71.25% and from 35.62%
to 39.38%.

Source: `results/periodic_cross_axis_audit_v1/report.md`.

## Contract failures and canonical v10 corpus

- v7 is diagnostic-only: fixed-width NumPy string construction truncated proposal names.
- v8 is diagnostic-only: 14 analytic hemisphere templates failed after the float32
  model-control roundtrip because analytic templates were admitted without hard checking.
- v9 passed hard residual reconstruction but exposed nondeterministic hemisphere pole-chart
  control counts during full `TeacherPathDataset` iteration.
- v10 fixes the hemisphere pole gauge and constructs raster templates directly in the
  analytic chart. It contains 160 instances and 2,455 candidates, including 400 new
  cross-axis candidates.

The full v10 audit reports 2,455/2,455 hard-feasible candidates after float32 residual
roundtrip. All 1,915 train and 540 validation candidate indices were readable, and the
maximum canonical control count was 92.

Sources:

- `results/stage2_multistart_structured_160_v10/summary.json`
- `results/stage2_multistart_structured_160_v10/residual_roundtrip_audit.json`
- `src/diffusion_coverage/coverage/patterns.py`
- `src/diffusion_coverage/coverage/structured_teacher.py`

## Revised synthetic frontier

With the canonical cross-axis library, the selected paired-rescue operating points were:

| Difficulty | k | Workspace success | Colour-aware success | Paired rescue | Length ratio |
|---|---:|---:|---:|---:|---:|
| easy | 4 | 58.91% | 98.12% | 39.22% [35.16%, 43.28%] | 1.1088 |
| medium | 16 | 33.91% | 67.81% | 33.91% [30.00%, 37.97%] | 1.1228 |
| hard | 32 | 25.78% | 43.28% | 17.50% [13.44%, 21.88%] | 1.0477 |

Cylinder and hemisphere account for the improvement over v1; free-form and saddle
results are unchanged under the same colour-field seed.

Sources:

- `results/synthetic_liftability_160_v3/cluster_bootstrap.md`
- `results/synthetic_liftability_160_v3/summary.json`
- `results/synthetic_liftability_160_v3/synthetic_liftability.png`

## Three-seed v10 learning result

The v10 validation split contains 109 instance-mode tasks. All runs use batch 32,
5,000 optimizer updates, eight samples per explicitly conditioned mode, and 32 Heun
steps.

| Run | Candidate feasible | K=8 recovery | Generated / best teacher | Generated / template |
|---|---:|---:|---:|---:|
| seed0 | 86.81% | 99.08% (108/109) | 1.0417 | 0.9398 |
| seed1 | 83.72% | 99.08% (108/109) | 1.0439 | 0.9406 |
| seed2 | 82.91% | 100.00% (109/109) | 1.0413 | 0.9391 |

The complete unit suite passed: 69 tests.

Sources:

- `results/stage2_residual_160_v10_multiseed/report.md`
- `results/stage2_residual_160_v10_multiseed/residual_diagnosis.json`
- `results/stage2_residual_160_v10_multiseed/expanded_stage2.png`

## Next falsification

The pre-existing 200-plan UR5e gate already establishes a large workspace-first liftability
gap, especially on cylinders and hemispheres. The next controlled test must hold the UR5e
checker and base placement fixed while comparing the geometry-shortest workspace proposal
against best-of-mode workspace proposals. This determines how much of the real-robot gap is
explained by workspace proposal vocabulary before direct C-space generation is credited.

