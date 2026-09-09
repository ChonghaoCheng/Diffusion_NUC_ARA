# Heuristics

## H01: Preserve surface topology while resampling path segments
- **Rationale**: Ambient-space chord interpolation followed by independent closest-point projection can switch surface sheets; reconstructing the mesh shortest path first keeps evaluator sources on one topology-consistent surface route.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: unknown
- **Code ref**: [`src/diffusion_coverage/surface/geodesic.py:shortest_surface_polyline`, `src/diffusion_coverage/surface/geodesic.py:resample_projected_polyline`]
- **Last revised**: 2026-08-23 (2026-08-23_001#2)

## H02: Refuse infeasible learned-planner targets
- **Rationale**: Exact coverage evaluation is the target-admission boundary; retaining a teacher proposal that violates the configured missed-coverage constraint trains the model toward an explicitly invalid solution.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: low
- **Code ref**: [`src/diffusion_coverage/coverage/dataset.py:TeacherDatasetWriter.write`, `scripts/generate_surface_teacher_dataset.py`]
- **Last revised**: 2026-08-23 (2026-08-23_001#3)

## H03: Preserve the hard-checker data contract through serialization
- **Rationale**: Reconstructing a new evaluation quadrature or changing path precision can alter discrete finite-footprint labels, so archives must carry the original quadrature and serialized targets must pass the complete model-input roundtrip.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/coverage/dataset.py:surface_from_teacher_archive`, `scripts/sanitize_teacher_dataset.py`, `scripts/audit_learning_targets.py`]
- **Last revised**: 2026-08-24 (2026-08-24_001#3)

## H04: Preserve source-segment boundaries during variable-token resampling
- **Rationale**: Global resampling may reconnect samples with a different mesh shortest path and shortcut intentional coverage turns; allocating interpolation within each source segment preserves those turns without repeated-token weighting artifacts.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/representation/variable_token.py:_resample_preserving_waypoints`]
- **Last revised**: 2026-08-24 (2026-08-24_001#3)

## H05: Rank coverage plans by the constrained planning objective
- **Rationale**: Once a candidate satisfies the hard missed-coverage tolerance, selection should minimize path length; minimizing missed fraction again biases targets toward over-coverage and unnecessary travel. Teacher generation, sanitization, learned-proposal evaluation, and refinement must call the same constrained-objective helper.
- **Sources**: []
- **Status**: active
- **Provenance**: user-revised
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/coverage/objective.py:constrained_coverage_key`, `scripts/sanitize_teacher_dataset.py`, `scripts/evaluate_flow_matching.py`]
- **Last revised**: 2026-08-24 (2026-08-24_001#4)

## H06: Admit candidate-specific targets through the complete learning roundtrip
- **Rationale**: Filtering a proposal family only after dataset splitting can change cohort composition, while archive feasibility alone does not guarantee that coordinate conversion and model dtype reconstruction preserve the hard label. Select the exact canonical family before splitting and hard-check the complete learning-input roundtrip.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/learning/teacher_dataset.py:filter_manifest_by_candidate_name`, `scripts/sanitize_teacher_dataset.py:learning_roundtrip_metrics`]
- **Last revised**: 2026-08-24 (2026-08-24_001#5)

## H07: Generate coverage-stroke controls instead of redundant dense samples
- **Rationale**: Finite-footprint raster plans carry their principal combinatorial structure in stroke count, endpoint order, and connectors. Generate those controls, derive their count from known task geometry when possible, and perform deterministic topology-aware densification before the unchanged hard checker.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/coverage/patterns.py:simplify_parameter_polyline`, `src/diffusion_coverage/coverage/patterns.py:raster_control_token_count`, `src/diffusion_coverage/coverage/patterns.py:decode_raster_parameter_controls`]
- **Last revised**: 2026-08-24 (2026-08-24_001#6)

## H08: Factor discrete pattern modes from continuous path controls
- **Rationale**: Pattern family, sweep axis, and phase define discrete correspondence classes, while stroke endpoints and connector geometry vary continuously. Expose the former as an explicit mode condition and balance those modes during training so one continuous generator is not asked to average incompatible path structures.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/coverage/patterns.py:PATTERN_MODE_NAMES`, `src/diffusion_coverage/learning/teacher_dataset.py:TeacherPathDataset`, `scripts/train_flow_matching.py`, `scripts/evaluate_stage2_multimodal.py`]
- **Last revised**: 2026-08-24 (2026-08-24_001#7)

## H09: Archive model-space controls instead of inverse-recovered paths
- **Rationale**: Surface chart projection and dense-path decoding need not be invertible, and hard feasibility can change at model precision. Preserve the variables actually optimized by the teacher, convert them to the model input dtype before admission, and run the unchanged hard checker on that representation.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/coverage/structured_teacher.py:MultiStartStructuredTeacher`, `scripts/build_multistart_structured_dataset.py`, `src/diffusion_coverage/learning/teacher_dataset.py:TeacherPathDataset`]
- **Crystallized via**: artifact-commitment
- **From staging**: O29
- **Last revised**: 2026-08-30 (2026-08-30_001#2)

## H10: Parallelize controlled runs before enlarging batches
- **Rationale**: Larger batches improve hardware throughput but also change optimizer-update count and gradient noise. Use available GPUs for concurrent seeds and ablations first; when changing batch size, hold and report update count and sample exposure separately and retain the unchanged hard evaluation.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`scripts/train_flow_matching.py`, `scripts/evaluate_stage2_multimodal.py`, `scripts/summarize_flow_ablation.py`]
- **Crystallized via**: artifact-commitment
- **From staging**: O31
- **Last revised**: 2026-08-30 (2026-08-30_001#2)

## H11: Learn physical residuals around structured coverage templates
- **Rationale**: A deterministic structured template already encodes stroke count, ordering, and nominal surface geometry. Subtract it and scale intrinsic-axis displacement into footprint-radius units so the vector field models local hard-feasible variation instead of reconstructing the entire absolute plan.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/coverage/patterns.py:structured_controls_to_residual`, `src/diffusion_coverage/coverage/patterns.py:structured_residual_to_controls`, `src/diffusion_coverage/learning/teacher_dataset.py`, `scripts/evaluate_stage2_multimodal.py`]
- **Last revised**: 2026-08-30 (2026-08-30_001#3)

## H12: Filter full-roundtrip failures at candidate granularity
- **Rationale**: A multi-start instance may contain one threshold-fragile candidate while its other modes and alternatives remain valid. Run the coordinate-and-dtype audit over the complete corpus, remove only failing candidate records, preserve any still-supported instance-mode group, and rerun the complete audit before training.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`scripts/audit_learning_targets.py`, `scripts/filter_dataset_by_audit.py`]
- **Last revised**: 2026-08-30 (2026-08-30_001#3)

## H13: Ground synthetic liftability in 3D surface position
- **Rationale**: A random colour graph tests combinatorial solver behavior but omits the coupling between coverage orientation and spatial IK-sheet availability. Define colours over actual surface charts, densify each 3D path under a checked resolution, and use exact fixed-colour segment completion before interpreting liftability.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/liftability/synthetic_colours.py`, `scripts/benchmark_synthetic_liftability.py`, `tests/test_synthetic_liftability.py`]
- **Last revised**: 2026-08-30 (2026-08-30_001#4)

## H14: Bootstrap repeated colour fields at the surface-instance level
- **Rationale**: Multiple colour fields evaluated on the same surface and candidate library share geometry and proposal errors. Preserve those repetitions as one cluster when estimating uncertainty so the interval reflects independent 3D task variation rather than treating every field as a new surface instance.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`scripts/analyze_synthetic_liftability.py`]
- **Last revised**: 2026-08-30 (2026-08-30_001#4)

## H15: Audit discrete proposal vocabulary before enlarging local search
- **Rationale**: A segment-count objective changes discontinuously when a path crosses colour boundaries. If a sweep orientation is absent from the proposal set, increasing local residual-search effort is an inefficient way to discover it. Audit analytic or enumerated discrete families first, then allocate continuous refinement within each supported family.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/coverage/patterns.py:generate_pattern_proposals`, `scripts/audit_periodic_cross_axis.py`]
- **Crystallized via**: artifact-commitment
- **From staging**: O45
- **Last revised**: 2026-08-30 (2026-08-30_001#5)

## H16: Apply hard admission to analytic templates
- **Rationale**: Closed-form construction does not guarantee that serialization, model dtype, decoding, and the finite-footprint checker preserve feasibility. Pass analytic templates through the same model-space roundtrip and hard admission boundary as optimized candidates before archiving or training on them.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/coverage/structured_teacher.py:MultiStartStructuredTeacher.solve`, `scripts/audit_structured_residual_dataset.py`]
- **Crystallized via**: artifact-commitment
- **From staging**: O46
- **Last revised**: 2026-08-30 (2026-08-30_001#5)

## H17: Fix singular chart gauges before defining structured token topology
- **Rationale**: Inverse parameterization at an azimuth-undefined pole can introduce numerical control points and nondeterministic simplification. Construct known templates directly in the analytic chart and assign singular endpoints to adjacent path tracks with a deterministic gauge before extracting or comparing controls.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/coverage/patterns.py:_raster_template_parameter_controls`, `src/diffusion_coverage/coverage/patterns.py:_fix_hemisphere_pole_gauge`]
- **Crystallized via**: artifact-commitment
- **From staging**: O47
- **Last revised**: 2026-08-30 (2026-08-30_001#5)

## H18: Pair stochastic IK comparisons by random stream
- **Rationale**: When proposals for one fixed robot task receive different random restart streams, solver luck is confounded with proposal quality. Derive restart seeds from the instance and reuse them for every paired proposal; exclude proposal preprocessing decisions from exact-search counters.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`scripts/benchmark_ur5e_workspace_modes.py`]
- **Crystallized via**: artifact-commitment
- **From staging**: O52
- **Last revised**: 2026-08-30 (2026-08-30_001#6)

## H19: Cap nested thread pools under process parallelism
- **Rationale**: A process pool multiplied by implicit BLAS or OpenMP threads oversubscribes fixed CPU resources, reducing throughput and making runtime comparisons depend on scheduler contention. Pin numerical thread pools to one per process when workers already span the available cores.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: medium
- **Code ref**: [`scripts/benchmark_ur5e_workspace_modes.py`, `scripts/benchmark_ur5e_surface_ik_graph.py`]
- **Crystallized via**: artifact-commitment
- **From staging**: O53
- **Last revised**: 2026-08-30 (2026-08-30_001#6)

## H20: Enumerate the feasible orientation cone
- **Rationale**: A looser inverse-kinematics convergence threshold still targets the same center axis and therefore does not represent the extra configuration freedom granted by tool-axis tolerance. Sample or optimize target axes inside the cone, then hard-filter achieved axes against its center and aperture.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/robot/ur5e_mujoco.py:sample_axis_cone`, `src/diffusion_coverage/robot/ur5e_mujoco.py:UR5eKinematics.enumerate_ik`]
- **Crystallized via**: artifact-commitment
- **From staging**: O56
- **Last revised**: 2026-08-30 (2026-08-30_001#6)

## H21: Nest candidate sets for tolerance monotonicity tests
- **Rationale**: Independently resampling each orientation cone can replace useful inner candidates and confound physical tolerance with finite enumeration. Preserve a bounded inner candidate set verbatim, then append candidates sampled only for the wider condition before comparing reachability or segment frontiers.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/robot/ur5e_mujoco.py:UR5eKinematics.enumerate_ik`, `tests/test_ur5e_kinematics.py:test_nested_orientation_cone_preserves_inner_candidates`]
- **Crystallized via**: artifact-commitment
- **From staging**: O57
- **Last revised**: 2026-08-30 (2026-08-30_001#6)

## H22: Carry exact continuation witnesses on compatibility edges
- **Rationale**: A boolean endpoint-compatibility edge is insufficient for continuous robot planning. Preserve the numerical IK-continuation path that established compatibility, in float64, and reuse that exact witness during routing and independent hard checking.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/robot/surface_ik_graph.py`, `src/diffusion_coverage/robot/qspace_teacher.py`]
- **Crystallized via**: artifact-commitment
- **From staging**: O60
- **Last revised**: 2026-08-30 (2026-08-30_001#7)

## H23: Separate construction tolerance from final admission
- **Rationale**: Numerical construction needs margin against interpolation and serialization error. Solve IK and continuation under tighter task-space tolerances, then densely interpolate in joint space and apply the unchanged final checker independently.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/robot/surface_ik_graph.py`, `src/diffusion_coverage/robot/qspace_teacher.py`]
- **Crystallized via**: artifact-commitment
- **From staging**: O61
- **Last revised**: 2026-08-30 (2026-08-30_001#7)

## H24: Hard-audit adaptive variable-token q plans
- **Rationale**: Simplify each continuous q-space teacher to its own geometric bandwidth, then rerun the full task-space checker. This avoids padding every plan to the longest dense witness while preserving failures that uniform subsampling can hide.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/robot/qspace_teacher.py`, `scripts/audit_qspace_teacher_representation.py`]
- **Crystallized via**: artifact-commitment
- **From staging**: O62
- **Last revised**: 2026-08-30 (2026-08-30_001#7)
## H25: Share the ordered surface-trace backend across planning and evaluation
- **Rationale**: Temporal revisit counts depend on the ordered trace, not only endpoint geometry. Robot continuation targets and the NUC evaluator must therefore use the same projected mesh-geodesic path construction; otherwise a backend mismatch can look like a coverage-quality change even when robot tracking is accurate.
- **Sources**: []
- **Status**: active
- **Provenance**: ai-suggested
- **Sensitivity**: high
- **Code ref**: [`src/diffusion_coverage/nuc/robot_lift.py`, `src/diffusion_coverage/coverage/nuc_evaluator.py`]
- **Last revised**: 2026-09-09 (2026-09-09_001#5)
