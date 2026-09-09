# NUC robot coupling v1: pre-implementation audit

Date: 2026-09-09
Branches: `exp/nuc-robot-coupling-v1` in both repositories
Upstream NUC commit: `f28c9a0b182d3e7b6b6223972ce7682e7e3b1300`

## Reusable code

- `coverage/CoveragePlan` preserves active segment and waypoint ordering.
- `coverage/evaluator.py` supplies the historical intrinsic disk-footprint backend, projected
  geodesic path length, swept union, missed fraction, and coverage efficiency.
- `surface/SurfaceInstance` supplies deterministic area-weighted samples and triangle metadata.
- `robot/UR5eKinematics` supplies MuJoCo FK, site Jacobians, IK enumeration, joint limits,
  numerical collision state, and position/tool-axis continuation.
- `robot/SurfaceIKGraph` supplies layered IK candidates, float64 continuation witnesses, graph
  serialization, and surface-grid connectivity.
- `robot/qspace_coverage_teacher.py` supplies q-space densification, witness reuse, and
  workspace reconstruction from checked q paths.
- Upstream `nuc.cpp` supplies the reference breadth-first facet spanning tree and cyclic
  three-subfacet traversal; `main.py` documents its flat triangle/vertex Python interface.

## Contract mismatches

- The historical evaluator collapses time into a swept union and cannot count leave/return
  episodes. It must remain unchanged while a new revisit-aware evaluator reuses its footprint
  backend.
- Current robot admission uses the product of all six singular values of the full 6D site
  Jacobian. The new axis-symmetric task has only five controlled directions and requires a
  characteristic-length-normalized `J5`, `sigma_min_5`, and `mu_bar`.
- Existing workspace lift and q-space hard checks expose different success semantics and failure
  labels. E06 requires one checker with separate kinematics, coverage, and timing status.
- Existing q route edge weights apply additional `2*pi` wrapping and endpoint differences. E06
  execution cost must integrate the actual stored continuous witness without extra wrapping.
- `attachment_site` is a flange attachment site on `wrist_3_link`, at local position
  `[0, 0.1, 0]`; no separate physical contact tool is present. E06 therefore freezes
  `site_name=attachment_site`, local tool axis `+z`, and explicitly limits physical-contact claims.
- The existing `ClassicalTeacherPlanner` is not NUC. The upstream C++ implementation is a
  deterministic breadth-first expansion over directed triangle adjacency. Its build currently
  lacks pybind11 in the isolated project environment; no substitute may be labelled NUC unless
  upstream equivalence is executed.
- Old coarse graph frontiers and old q-teacher labels that failed dense interpolation remain
  diagnostic only and are excluded from E06 admission and calibration.

## Files to modify or add

Code repository:

- add `src/diffusion_coverage/coverage/nuc_evaluator.py`
- add `src/diffusion_coverage/robot/task_kinematics.py`
- add `src/diffusion_coverage/robot/strict_execution.py`
- add `src/diffusion_coverage/robot/execution_cost.py`
- add `src/diffusion_coverage/nuc/` adapter, policies, validation, and upstream-equivalence wrapper
- minimally extend package `__init__.py` exports
- add `scripts/calibrate_nuc_robot_contracts.py`
- add `scripts/benchmark_nuc_robot_coupling.py` and E06 plotting support
- add `configs/nuc_robot_coupling_v1*.json`
- add focused evaluator, task-geometry, strict-checker, cost, and NUC-adapter tests

ARA repository:

- append E06 and blocked E07 registrations without rewriting E00-E05
- append calibration and E06 evidence files with commit/config/result provenance
- append trace decisions, experiments, failed branches, and interpretation boundaries

## Files explicitly not to modify

- `src/diffusion_coverage/coverage/evaluator.py` and historical metric meanings
- historical E00-E05 experiment text and evidence files
- Flow Matching, diffusion, MPNN, GNN, dataset-training, and learned-model code
- upstream `/data/chocheng/Code/NUC_upstream` source files
- ROS 2, hardware execution, force/contact dynamics, timing optimization, or real-time control
- old result directories and old teacher labels

## Exact experiment stages

1. Add and unit-test temporal revisit-aware intrinsic-footprint NUC metrics.
2. Add and numerically validate normalized five-dimensional position/tool-axis task geometry.
3. Add a single dense strict execution checker with independent kinematics/coverage/timing status.
4. Add witness-integrated weighted joint travel and resampling-invariance tests.
5. Build upstream NUC in the isolated environment, implement legal expansion policies, and pass
   an upstream-first equivalence regression before using any candidate.
6. Run a small contract calibration to freeze coverage density, q interpolation density,
   characteristic length, `sigma_safe`, and `delta_NUC` before method comparison.
7. Freeze neutral easy/mid/hard placements, generate structurally valid paired skeletons, and run
   E06 over saddle and hemisphere scenes with equal lifting/refinement budgets.
8. Apply the pre-registered E06 gate. Run no E07 code or experiment unless both spread and paired
   improvement criteria pass.

## Expected outputs

- `results/nuc_robot_contract_calibration_v1/{config.json,raw.csv,summary.json,README.md,*.png}`
- `configs/nuc_robot_coupling_v1_placements.json` with neutral selection provenance
- `results/nuc_robot_coupling_v1/{config.json,candidates.csv,scenes.csv,summary.json,README.md,*.png}`
- coverage/robot/compute fields requested by E06, including explicit numerical-search failures
- ARA evidence pages that state branch, code commit, configuration, support, and non-support
- an explicit E07 GO/NO-GO decision; absent E06 results must be labelled
  `IMPLEMENTED BUT NOT EXPERIMENTALLY VALIDATED`

