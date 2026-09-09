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
