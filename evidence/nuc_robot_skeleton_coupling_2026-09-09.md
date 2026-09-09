# E06 NUC skeleton-robot coupling (2026-09-09)

## Provenance

- Code commit: `78876d52d5313c0e99978700ff3cb7de02e2d0a5`
- Branch: `exp/nuc-robot-coupling-v1`
- Result directory: `results/nuc_robot_skeleton_coupling_v1/`
- ARA result snapshot: `evidence/runs/nuc_robot_skeleton_coupling_v1/`
- Main configuration: `configs/nuc_robot_coupling_v1.json`
- Frozen placements: `configs/nuc_robot_coupling_v1_placements.json`
- Experiment config hash: `800217b19a3f0dc54ab2271b884962ac8fbe2f9fd25158be9aea8f8f6c452726`
- Command: `MPLCONFIGDIR=/data/chocheng/.cache/matplotlib /data/chocheng/.venvs/coverage-fm/bin/python scripts/benchmark_nuc_robot_coupling.py`
- Scale: 2 surfaces x 3 fixed placements x 20 legal unique NUC skeletons = 120 candidate executions.
- Wall time: 870.286 s.

No Flow Matching, diffusion, GNN, MPNN, ROS 2, hardware execution, contact-force dynamics, or
timing optimization was run.

## Frozen admission contract

- Temporal geodesic-footprint NUC evaluation: 48 samples/face, 0.002 m path spacing.
- NUC equivalence: `delta_NUC=0.0297927413`, fixed before E06.
- Axis-symmetric task: 3 position + 2 tool-axis directions, `L_c=0.1 m`.
- Singularity admission: `sigma_min_5 >= 0.0723741717`.
- Dense q interpolation: at most 0.05 rad per checked step.
- Numerical lifting: 8 random restarts, at most 6 active branches, 7 minimum task-edge samples.
- Execution cost: actual float64 witness `L_q` with `W=I`, no extra `2*pi` wrapping.
- Local geometry budget: four identical surface-projection iterations; topology was unchanged.

## Registered result

| Surface | Placement | Geometry skeleton | Geometry `L_q` | Oracle skeleton | Oracle `L_q` | Reduction | Equivalent feasible | Relative spread |
|---|---|---|---:|---|---:|---:|---:|---:|
| saddle | P_easy | S16 | 23.6379 | S05 | 23.5315 | 0.450% | 20 | 0.969% |
| saddle | P_mid | S16 | 23.7836 | S06 | 23.6707 | 0.475% | 20 | 0.754% |
| saddle | P_hard | S16 | 27.3653 | S01 | 27.3247 | 0.148% | 20 | 0.691% |
| hemisphere | P_easy | S15 | 111.5114 | S07 | 111.1246 | 0.347% | 17 | 1.031% |
| hemisphere | P_mid | S15 | not found | none | not found | NA | 0 | NA |
| hemisphere | P_hard | S15 | not found | none | not found | NA | 0 | NA |

Gate A required at least four scenes with at least 10% spread; observed `0/6`. Gate B required
at least 10% median paired improvement; the median over four valid pairs was `0.3985%`.
The registered decision is **NO-GO**. E07 was not run.

## Placement coupling and safety

The best saddle skeleton changed from S05 to S06 to S01 across easy/mid/hard placements.
Spearman rank correlations were `-0.950` (easy/mid), `-0.755` (easy/hard), and `0.602`
(mid/hard). Thus placement changed ranking, but only inside a sub-1% cost spread and therefore
did not produce practically exploitable selection space under this construction.

All 60 saddle candidates and all 20 hemisphere/P_easy candidates found a continuous numerical
lift. Three hemisphere/P_easy frontier variants exceeded the frozen NUC-equivalence admission;
all 40 hemisphere/P_mid and P_hard candidates returned
`continuous_lift_not_found_under_budget`. This is a finite numerical-search outcome, not a
claim of C-space disconnection. Selected feasible paths had minimum `sigma_min_5` between
0.1718 and 0.7606, above the safety threshold. Saddle P_mid/P_hard witnesses reached zero
reported joint-limit margin while remaining within the checker's inclusive limits, so the
experiment does not establish positive joint-limit clearance.

## Dependency and contract findings

1. The originally implemented per-parent edge-order policy has only 16 unique hemisphere
   topologies under the fixed root, even over 10,000 seeds. Four additional legal unique
   candidates required a seeded frontier-facet selection policy. Upstream-first remains an
   exact regression match to upstream commit `f28c9a0b...` on the test meshes.
2. A smoke implementation that used projected Euclidean chords for robot transitions changed
   saddle temporal `E_NUC` by about 0.05 despite sub-millimeter tracking. Reusing the evaluator's
   mesh-geodesic path backend reduced the planned/executed discrepancy to about 0.0003. The
   discarded smoke result is not E06 evidence.
3. Neutral pose-wise reachability was 100% for hemisphere P_mid and 95.8% for P_hard, yet no
   complete NUC lift was found there. Pose-wise placement calibration is therefore insufficient
   to predict long-path continuation feasibility.

## What this supports

Under the tested scenes, robot placement changes NUC skeleton ranking, but the available legal
skeleton variation does not provide the preregistered material execution-cost improvement.
The result also shows that neutral pose-wise reachability can overestimate full-path numerical
continuation feasibility.

## What this does not support

This does not establish global optimality, continuous C-space topology or disconnection,
generality beyond the two surfaces and six placements, superiority of any learned planner,
physical execution performance, timing performance, or equivalence to the original T-Mech
contact model. The execution oracle is optimal only over the finite propagated candidate graph,
and collision checks are limited to the loaded standalone UR5e scene.
