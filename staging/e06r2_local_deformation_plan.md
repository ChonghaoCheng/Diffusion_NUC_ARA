# E06-R2 fixed-topology local Riemannian deformation plan

Registered: 2026-09-12, before window freezing or deformation-result generation.

Status at registration: REGISTERED / NOT RUN. This is not historical E07.

## Scientific question

E06-R2 tests whether the validated robot-induced surface metric can guide a bounded local
deformation to lower **actual strict-witness** joint length more than the same optimizer guided
only by intrinsic surface length. It does not optimize a complete NUC path or alter skeleton
topology.

The paired methods are `M0` (archived fixed window), `M1` (intrinsic Euclidean surface-length
objective), and `M2` (integrated `G_exec` objective). M1 and M2 use the same variables, proposal
sequence, evaluation limit, acceptance constraints, continuation, and strict checker. Only the
ranking objective differs.

## Frozen scenes and windows

Reuse the six E06-R scenes without recalibration: saddle `T17/T21/T10` and hemisphere
`T30/T27/T33`, corresponding to `P_low/P_mid/P_high`. Use the final E06-R anchor freeze with hash
`4fdc4cbdc76d2efa767627b271a5754d89453de0c73ddd3fa4556978849db79b`.

Select ten windows per scene deterministically from the fifteen anchors. A window is eligible
when a contiguous archived path/witness interval of total arclength `L_window=0.048 m` can be
formed about the anchor, contains no discontinuity, has fixed task endpoints, retains positive
joint margin, and has no archived continuation failure. Among eligible anchors, select ten
approximately uniformly in baseline arclength, breaking ties by witness index. Selection uses no
deformation result. Archive all selected/rejected anchors and a content hash in
`results/riemannian_local_deformation_v1/frozen_windows.json`.

## Parameterization and bounds

- Seven ordered controls `x0...x6` are sampled at approximately uniform baseline arclength.
- `x0`, `x1`, `x5`, and `x6` remain exactly fixed for splice regularity; only `x2`, `x3`, and
  `x4` have two-dimensional tangent displacements.
- Each free-control physical displacement is bounded by `0.002 m = 0.25*r_tool`.
- A piecewise-linear displacement field over baseline arclength is retracted sample-by-sample
  with the existing face-traversing mesh retraction. The fixed path order is retained, every
  adjacent sample must remain in a connected local face corridor, and candidate turning is capped
  at `max(45 deg, baseline maximum turn + 15 deg)`.
- Ambient chord projection and waypoint permutation are forbidden.

## Equal-budget optimizer

Use deterministic derivative-free incumbent search with common random numbers. Each M1/M2 run
evaluates exactly 60 proposed parameter vectors after evaluating M0, permits at most 12 accepted
updates, and uses proposal radii `0.002/0.001/0.0005 m` for evaluations `1-20/21-40/41-60`.
Seeds are deterministically derived from the experiment seed `20260912` and window identity.
Rejected candidates never update the incumbent. Best feasible actual `L_q` is recorded against
strict-evaluation count.

M1 ranks admitted candidates by intrinsic surface length and cannot access `G_exec`, `Jbar_5`,
or `L_G`. M2 ranks by integrated `L_G`; for every admitted candidate its continuation witness is
used to recompute `G_exec` along the current path. Both methods nevertheless report actual
float64-witness `L_q`, surface length, NUC metrics, and safety margins.

## Frozen robot and admission contract

Reuse unchanged: UR5e model and `attachment_site`, tool axis index/sign, axis-symmetric 5D task,
`L_c=0.1 m`, `W=I`, `sigma_safe=0.07237417172157597`, dense q interpolation step `0.05 rad`,
position tolerance `0.003 m`, construction position tolerance `0.0015 m`, axis tolerance `10 deg`,
maximum continuation step `0.1 rad`, float64 witnesses, collision contract, and the E06 mesh
geodesic evaluator.

Every admitted candidate must satisfy all of:

- unchanged endpoint surface states, ordering, and local topology;
- `abs(L_surface-L_surface0)/L_surface0 <= 0.02`;
- `E_NUC <= E_NUC0+0.0297927413`;
- `E_miss <= E_miss0+0.0297927413` and `E_rep <= E_rep0+0.0297927413`;
- continuation begins at the exact archived `q_start`;
- `max(abs(q_end-q_end0)) <= 0.05 rad`;
- strict kinematics, joint limits, collision, position/axis tolerances, and
  `sigma_min_5 >= sigma_safe`.

The local baseline is reconstructed directly from the archived witness. Its transition-sum
`L_q0` must match the same archived slice at numerical tolerance before the window can run.

## Outcomes and statistics

For each independent window report `Delta_E=(L_q0-L_qE)/L_q0`,
`Delta_R=(L_q0-L_qR)/L_q0`, and incremental benefit `A_R=Delta_R-Delta_E`, plus
`C_q=L_q/L_surface`. Report median, IQR, paired bootstrap 95% confidence intervals using 10,000
resamples and seed `20260912`, stratified by surface and anisotropy level. Fit
`A_R=alpha+beta*A_window+error` and report Spearman correlation, where `A_window` is baseline
median `log(kappa_R)`.

The frozen GO gate requires all of:

1. medium/high fraction `L_qR < L_qE >= 0.75`;
2. medium/high median `A_R >= 0.05`;
3. medium/high median `Delta_R >= 0.08`;
4. `Spearman(A_window,A_R) >= 0.40` or a clearly positive preregistered regression slope;
5. the qualitative conclusion persists after removing the lowest-sigma quartile, restricting
   surface-length mismatch to at most 1%, and restricting terminal mismatch to at most 0.025 rad.

## Planned artifacts and interpretation boundary

Implementation will be focused under `src/diffusion_coverage/diagnostics/`, with one freeze/run
script, one summary script, contract tests, configuration, raw candidate history, window-level
results, machine-readable plot sources, figures, and reproduction commands under
`results/riemannian_local_deformation_v1/`.

E06-R2 can support only a tested local, fixed-topology, matched-contract incremental deformation
claim. It cannot establish whole-surface NUC planner superiority, global optimality, hardware
performance, Flow Matching benefit, cross-robot generality, or C-space connectivity. A NO-GO
will not trigger post-hoc increases in radius, budget, or relaxed admission thresholds.

## Frozen windows

Frozen before any deformation evaluation at code commit `56af57a`. Machine-readable source:
`results/riemannian_local_deformation_v1/frozen_windows.json`; content hash
`9bd7673f4de735ea5e0aedd985e753145741f402f4f9912497815110e11065c5`.

- Saddle uses anchors `A00,A02,A03,A05,A06,A08,A09,A11,A12,A14` for every placement.
- Hemisphere uses anchors `A01,A02,A04,A05,A06,A08,A09,A10,A12,A13` for every placement.
- Hemisphere endpoint anchors `A00/A14` were excluded because a centered `0.048 m` interval did
  not exist. All remaining exclusions were the preregistered uniform-arclength subselection.
- Every scene has exactly ten windows; all 60 passed the pre-result extent, control-sample,
  positive joint-margin, and sampled `sigma_safe` eligibility checks.

No M1/M2 candidate, objective change, or deformation outcome existed when this freeze was
committed.
