# M2 Flow Matching Go/No-Go Evidence

## Environment

- Isolated environment: `/data/chocheng/.venvs/coverage-fm`
- PyTorch: `2.5.1+cu118`
- CUDA device: `Tesla V100-PCIE-32GB`
- Compiled architecture list included `sm_70`; a 1024 by 1024 CUDA matrix multiplication completed with sum `1073741824.0`.

## Dataset

- Dataset: `results/teacher_m2_fixed_r_k1_50/`
- Fixed inputs: footprint radius `0.2`, maximum segments `1`, missed-coverage tolerance `0.05`.
- Fifty instances: ten each of cylinder, hemisphere, saddle, torus, and free-form patch.
- 279 numeric-only feasible teacher candidates; archive audit reported zero bad archives under `allow_pickle=False`.
- Split: 40 train instances and 10 held-out validation instances, stratified as two validation instances per surface family.

## Model and Evaluation

- PointNet surface encoder with conditional path velocity field, cross-attention, straight-path conditional Flow Matching, and Heun ODE sampling.
- Both runs trained for 60 epochs and 1680 optimizer steps.
- Validation uses top-8 generated paths per instance, exact mesh projection, and the same geodesic finite-footprint evaluator as the teacher.
- Smooth-base refinement inserts at most 12 farthest uncovered surface samples into the best generated path.

## Results

Verbatim summary from `results/m2_go_no_go/report.md`:

| Method | Feasible | Mean missed | Median missed | Mean length |
|---|---:|---:|---:|---:|
| FM iid base | 10.0% | 0.1415 | 0.1002 | 63.96 |
| FM smooth base | 10.0% | 0.1907 | 0.1296 | 47.20 |
| FM smooth + repair | 60.0% | 0.1161 | 0.0458 | 51.42 |
| Classical teacher | 100.0% | 0.0146 | 0.0061 | 58.67 |

The smooth-base run remained infeasible after repair on both held-out cylinders and both held-out tori. Raw teacher inspection showed that one instance can mix spiral, orthogonal raster, and phase-shifted targets, creating unresolved waypoint-correspondence ambiguity.

## Verification

- Full regression suite: `25 passed in 15.49s`.
- Compile check: `/data/chocheng/.venvs/coverage-fm/bin/python -m compileall -q src scripts` exited zero.
- Primary machine-readable result: `results/m2_go_no_go/summary.json`.
- Plots: `results/m2_go_no_go/training_loss.png` and `results/m2_go_no_go/missed_fraction_by_surface.png`.

## Decision

M2 is a no-go. Do not advance to radius conditioning, segment-budget conditioning, or manipulator integration until path-sequence alignment and closed-surface mode coverage improve.
