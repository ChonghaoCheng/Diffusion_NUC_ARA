# Architecture

## A01: Use one hard checker for workspace-first and direct C-space planning
- **Design**: Both pipelines consume the same surface, robot, footprint, tolerance, segment budget, base placement, and hard feasibility checker. Workspace-first methods produce surface paths and invoke post-hoc continuous IK; the proposed method generates configuration-space paths directly.
- **Interface**: Surface samples define desired tool poses; pose-wise IK candidates and continuity edges expose colour/component structure; exact graph completion or continuous optimization certifies a lift. Direct C-space samples pass through forward kinematics and the identical finite-footprint checker.
- **Status**: active
- **Provenance**: user-revised
- **Code refs**: [`src/diffusion_coverage/robot/ur5e_mujoco.py`, `scripts/benchmark_ur5e_liftability.py`, `scripts/summarize_ur5e_liftability.py`]
- **Last revised**: 2026-08-24 (2026-08-24_001#2)

## A02: Preserve variable geometric bandwidth with padded variable-token paths
- **Design**: Determine waypoint count from known surface area and footprint scale, retain every expert source-segment boundary, distribute additional samples within each segment by intrinsic length, then pad and mask within a batch. Deterministic endpoint/direction gauges remove representation ambiguity without merging homotopy, winding, pattern-family, or IK-component modes.
- **Interface**: Each segment stores points, mask, actual normalized intrinsic arclength, total intrinsic length, and canonicalization metadata. Inference token count depends only on known task geometry. Fixed-control-point B-splines remain a measured compression baseline rather than the default representation.
- **Status**: active
- **Provenance**: user
- **Code refs**: [`src/diffusion_coverage/representation/variable_token.py`, `src/diffusion_coverage/representation/bspline.py`, `src/diffusion_coverage/evaluation/representation_audit.py`, `scripts/benchmark_representation_stage0.py`]
- **Last revised**: 2026-08-24 (2026-08-24_001#3)

## A03: Calibrated NUC skeleton-robot coupling gate
- **Design**: Generate only legal variants of the upstream NUC facet-tree expansion, evaluate ordered temporal footprint episodes, enumerate multiple UR5e IK branches, propagate float64 continuation witnesses, and choose the minimum actual `L_q` over the finite layered graph. Compare a geometry-only selector with an execution-aware oracle under a frozen NUC-equivalence threshold.
- **Interface**: Every method passes one strict checker that independently reports kinematics, coverage, and timing status. Kinematics uses dense q interpolation, joint/collision limits, task tracking, and normalized 5D `sigma_min`; coverage uses the final FK workspace trace and revisit-aware NUC evaluator.
- **Status**: active for E06; local deformation is blocked pending the E06 gate.
- **Provenance**: user
- **Code refs**: [`src/diffusion_coverage/coverage/nuc_evaluator.py`, `src/diffusion_coverage/robot/task_kinematics.py`, `src/diffusion_coverage/robot/strict_execution.py`, `src/diffusion_coverage/nuc/adapter.py`, `src/diffusion_coverage/nuc/robot_lift.py`, `scripts/benchmark_nuc_robot_coupling.py`]
- **Last revised**: 2026-09-09
