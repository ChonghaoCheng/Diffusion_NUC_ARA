# E06-D: NUC Skeleton Coupling Mechanism Diagnosis

Registered: 2026-09-10

Parent evidence: `evidence/nuc_robot_skeleton_coupling_2026-09-09.md`

## Frozen contract

E06-D retains the E06 task and admission contract without recalibration: `L_c=0.1 m`,
`sigma_safe=0.0723741717`, `delta_NUC=0.0297927413`, maximum dense q step `0.05 rad`,
`W=I`, the temporal geodesic-footprint NUC settings, 5D position plus free-spin tool-axis task,
the same tool site and axis convention, and actual float64 continuation witnesses. D1-D3 prefer
post-processing. Any D4 replay must reproduce archived default-budget metrics before its trace is
admitted.

## Frozen hypotheses and metrics

### D1: Skeleton variability

Hypothesis D1-H: the twenty candidate labels mainly alter traversal order over a substantially
shared transition structure. Measure directed and undirected transition Jaccard similarity,
expansion-tree edge Jaccard similarity, within/cross-parent transition fractions, normalized
sequence edit distance, normalized-arclength ordered path distance, and tangent disagreement.
Subfacet IDs and parent-facet IDs, rather than floating-point coordinates, define structural
identity.

### D2: Witness-cost decomposition

Hypothesis D2-H: most actual joint travel lies on transitions common or frequent across the
candidate library, leaving too little variable-transition cost to permit the E06 ten-percent
gate. Attribute every stored witness increment to its generating directed surface transition and
require the attributed sum to reproduce archived `L_q`. Report within/cross-parent and
library-common/library-variable cost, `eta_cross`, `eta_var`, and accumulated cost on transitions
with library frequency at least 0.50, 0.75, 0.90, and 1.00. Small `eta_var` is not assumed to imply
small total spread; cancellation among similarly priced variable transitions remains an explicit
alternative.

### D3: Local execution metric

Hypothesis D3-H: the normalized 5D metric
`M_task=(Jbar_5 Jbar_5^T)^-1` predicts local and candidate-level witness travel, but its spread may
remain small because expansion-order skeletons expose little robot-geometric freedom. Use minimal
tool-axis transport, no tool-axis spin penalty, stable linear solves, and only samples admitted by
the frozen `sigma_safe`. Report local and candidate Pearson/Spearman correlations, per-scene
normalized correlations, relative spreads, `L_q/L_G`, and residual dependence on singularity and
joint-limit margin. `L_G` is a redundant-task lower-cost diagnostic, not an equality target.

### D4: Hemisphere continuation

Hypothesis D4-H: high neutral pose-wise reachability hides either local transition loss or finite
beam/search exhaustion along long ordered paths. Default failure classes are frozen as:

- `pose_candidate_empty`: independent pose enumeration produces no candidate before safety filters.
- `safety_filter_exhaustion`: candidates exist before filters but none survive the frozen admission filters.
- `transition_graph_empty`: safe candidates exist on both sides, but no validated continuation edge survives.
- `beam_or_search_exhaustion`: assigned only when the preregistered strong search recovers a default failure.
- `unresolved_continuation_failure`: failure does not meet the preceding evidentiary definitions.

Record first failure progress, candidate/filter counts, propagated edges, beam widths, singularity,
joint-limit margin, location, and desired axis. A repeated bottleneck is a numerical geometric or
kinematic observation, never a topology theorem.

## Strong-search sensitivity

After default traces, select independently for P_mid and P_hard: the geometry baseline, earliest
failure, median-failure-progress candidate, and latest failure; deduplicate deterministic ties by
skeleton ID. Append the frozen IDs to this file before strong replay. The strong budget is exactly
32 random restarts, 24 active branches, consistently increased candidate enumeration, and 15 task
edge samples. Task geometry, tolerances, `sigma_safe`, dense checker, NUC evaluator, placement, and
tool geometry remain unchanged. Report candidate and runtime expansion factors.

## Interpretation logic

- Outcome 1: small `eta_var`, small `L_G` spread, and small `L_q` spread closes legal expansion-order selection.
- Outcome 2: high metric correlation but both spreads small narrows the Riemannian line to a diagnostic of a too-narrow skeleton family.
- Outcome 3: large `L_G` spread but small witness spread requires branch/null-space compensation analysis.
- Outcome 4: candidates remain present, transitions repeatedly vanish, and strong search does not materially rescue paths.
- Outcome 5: strong search recovers many failures, identifying the default continuation search as the main limitation.

No outcome automatically authorizes E07 or deformation.

## Planned implementation and outputs

Shared code will live in `src/diffusion_coverage/diagnostics/`. Focused entry points will be
`scripts/diagnose_nuc_skeleton_structure.py`, `scripts/diagnose_nuc_joint_cost_decomposition.py`,
`scripts/diagnose_nuc_execution_metric.py`, `scripts/diagnose_hemisphere_continuation.py`, and
`scripts/summarize_nuc_coupling_diagnosis.py`. Raw CSV/JSONL, figures, summary JSON/Markdown, and
reproduction commands will be written under `results/nuc_robot_coupling_diagnosis_v1/`.

## Claim boundaries

E06-D cannot establish global C-space connectivity or disconnection, global path optimality,
benefit of surface-joint deformation, benefit of Flow Matching/diffusion, physical execution
performance, or generality outside the registered surfaces and placements. It does not reinterpret
E06, introduce a replacement planner, run E07, or change the frozen admission contract.

## Strong-search selected IDs

Frozen 2026-09-10 after the complete default trace and before strong replay:

- P_mid: `S15` geometry baseline, `S19` earliest, `S08` lower-median, `S17` latest.
- P_hard: `S15` geometry baseline, `S17` earliest, `S13` lower-median, `S16` latest.

No IDs were selected using strong-search outcomes. The machine-readable selection is
`results/nuc_robot_coupling_diagnosis_v1/d4_continuation/strong_search_selection.json`.
