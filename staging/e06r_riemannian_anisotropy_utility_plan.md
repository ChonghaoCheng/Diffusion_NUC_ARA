# E06-R Riemannian anisotropy planning-utility plan

Registered: 2026-09-12, before R0/R1 implementation or directional-probe execution.

## Question and hypotheses

E06-R asks whether the eigenstructure of the normalized 5D robot-induced surface metric has a
causally useful directional signal at a shared feasible contact state. The primary hypothesis is
that equal physical surface displacements along the high-cost eigenvector require more actual
continuous witness motion than displacements along the low-cost eigenvector.

- R0-H: safely fully liftable placements span measurable low/medium/high scene anisotropy.
- R1-H1: sign-averaged `R_q` is greater than one when `R_G0` is appreciably greater than one.
- R1-H2: `log R_G0` ranks `log R_q` across paired anchors.
- R1-H3: low-anisotropy scenes act as a negative control and have small directional differences.
- R1-H4: the shorter probe agrees more closely with local theory than the longer probe.

## Frozen contract

Reuse the E06-D contract without recalibration: axis-symmetric 5D position plus tool-axis task,
`L_c=0.1 m`, `sigma_safe=0.0723741717`, `W=I`, maximum dense q interpolation step `0.05 rad`,
E06 axis and position tolerances, float64 witnesses, the same MuJoCo UR5e model,
`attachment_site`, tool-axis index `2` and sign `+1`, and the mesh-geodesic surface backend.

`B_h` contains physical tangent translation normalized by `L_c` and the derivative of the
surface-normal/tool-axis field represented in the task's perpendicular axis basis. Define
`H_task=(Jbar_5 Jbar_5^T)^-1`, `G_exec=B_h^T H_task B_h`, ordered eigenvalues
`lambda_min<=lambda_max`, `kappa_R=lambda_max/lambda_min`, and
`R_G=sqrt(kappa_R)`. Stable solves are mandatory. R1 is blocked if basis invariance,
finite-difference `B_h`, positivity, or E06-D D3 consistency fails.

## R0 scene calibration

- Surfaces: frozen E06 saddle and hemisphere meshes.
- Candidate count: 36 deterministic rigid placements per surface, seed `20260912`.
- Candidate generation: include the E06 working `P_easy` transform, then deterministic bounded
  perturbations around it (translation up to `0.06 m`, roll/pitch up to `10 deg`, yaw up to
  `20 deg`); no scaling.
- Neutral path: one `upstream_first` NUC skeleton per surface, unchanged across placements.
- Search budget: the frozen E06 default continuation budget. A candidate is admissible only if
  the complete path is found, passes the unchanged strict checker, has
  `sigma_min_5>=sigma_safe`, and minimum joint-limit margin greater than `0.02 rad`.
- Scene score: median pathwise `log(kappa_R)`; also archive P75/P90/max and fractions with
  `R_G>=1.1,1.2,1.5,2.0`.
- Selection: among admissible placements sorted by `(A_scene, candidate_id)`, choose the lowest,
  lower median, and highest as `P_low`, `P_mid`, and `P_high`. No directional `R_q` quantity is
  computed before this selection is written to `configs/riemannian_anisotropy_scenes_v1.json`.

## R1 anchors and probes

- Up to 15 anchors per selected surface/placement, approximately uniform in baseline witness
  arclength after deterministic eligibility filtering.
- Eligibility: at least `0.0045 m` from the mesh boundary, joint margin above `0.03 rad`,
  `sigma_min_5 >= 1.10*sigma_safe`, local baseline turning angle below `30 deg`, a valid local
  surface neighbourhood through `0.004 m`, positive-definite `G_exec`, and
  `kappa_R<=1e6`. Ties and spacing are resolved by witness index.
- Probe lengths: exactly `ell_1=0.002 m` and `ell_2=0.004 m`, fixed from the `0.008 m` E06 tool
  radius before results.
- Directions: both signs of physical unit directions obtained from `v_min` and `v_max` at the
  same anchor. All four probes start from the identical stored `q_i`.
- Curve construction: face-traversing tangent advance with projection/parallel tangent update;
  ambient chord-and-independent-projection is prohibited. Requested/actual intrinsic length must
  differ by at most `2%` and paired min/max lengths by at most `2%`.
- Continuation: frozen task definition, tolerances, safety threshold and strict dense checker;
  no independent branch enumeration by direction and no imputation of failed probes.

For each eligible sign-complete anchor/length, compute sign-averaged directional costs and
`R_q=cbar_q(v_max)/cbar_q(v_min)`. Report directional feasibility asymmetry independently.
Integrated `L_G` may use each successful actual witness but is diagnostic; `R_G0` at the shared
anchor is the causal prediction.

## Statistics and controls

Report median/quartiles and fractions `R_q>1,1.1,1.2`, pooled and per-level Spearman correlation,
the fit `log R_q=a+b log R_G0`, confidence intervals and `R^2`, dose response, probe-length
sensitivity, sign asymmetry, intrinsic-length mismatch, residual association with minimum
`sigma_min_5` and minimum joint-limit margin, integrated `L_G` against actual `L_q`, and the
low-anisotropy negative control.

The GO gate is frozen as all of:

1. pooled `Spearman(log R_G0,log R_q)>=0.70`;
2. `P(R_q>1)>=0.80` over eligible medium/high-anisotropy anchors;
3. high-anisotropy median `R_q>=1.20`;
4. paired intrinsic-length mismatch remains at most `2%`;
5. conclusions persist after excluding the lowest-sigma quartile of anchors.

If this gate fails, no Riemannian deformation experiment is registered. If it passes, only a new
fixed-topology local-deformation experiment may be registered; it is not run automatically.

## Planned files and outputs

Implementation: `src/diffusion_coverage/geometry/robot_surface_metric.py`, focused geometry and
experiment helpers under `src/diffusion_coverage/geometry/` or
`src/diffusion_coverage/diagnostics/`, `scripts/calibrate_riemannian_anisotropy_scenes.py`,
`scripts/run_riemannian_directional_probe.py`, and
`scripts/summarize_riemannian_anisotropy_utility.py`. Results are rooted at
`results/riemannian_anisotropy_utility_v1/` with raw CSV/JSONL source tables for every plot,
frozen scenes and anchors, configuration, summaries, and reproduction commands.

## Interpretation boundaries

E06-R does not optimize a full NUC path and cannot establish improvement of NUC coverage,
superiority of Riemannian deformation, global trajectory optimality, C-space connectivity or
disconnection, Flow Matching/diffusion benefit, physical hardware performance, or generality
beyond the selected numerical surfaces and placements. E06/E06-D remain unchanged and E07
remains blocked/not run.

## Frozen R1 anchors

Frozen 2026-09-12 after R0 scene selection and deterministic anchor eligibility, before any
directional continuation result was computed. Machine-readable source:
`results/riemannian_anisotropy_utility_v1/frozen_anchors.json`; content hash
`39e45120d456aaf341be3d72678c33cd2619b0717481f828b5e7283ceaf30f29`.

- saddle/P_low, P_mid, P_high: `A00@1, A01@233, A02@452, A03@671, A04@897,
  A05@1115, A06@1355, A07@1581, A08@1807, A09@2033, A10@2258, A11@2491,
  A12@2710, A13@2950, A14@3169`.
- hemisphere/P_low, P_mid, P_high: `A00@1, A01@399, A02@773, A03@1172,
  A04@1558, A05@1969, A06@2355, A07@2754, A08@3140, A09@3614, A10@4000,
  A11@4423, A12@4797, A13@5208, A14@5595`.

Each saddle scene had 256 eligible samples from 450 deterministic candidates; each hemisphere
scene had 300. The selected IDs are identical across placements of one surface because the path,
geometry filters, and arclength targets are shared and these samples passed every placement's
frozen safety filters. No `R_q` or directional success value existed at freeze time.

## Pre-result numerical-resolution correction

The first R1 process was interrupted before it completed and before it wrote any probe-result
table. During that run's implementation audit, the original `0.002 m` short probe was found to be
only twice the unchanged internal IK stopping tolerance (`0.001 m`), making its joint increment
susceptible to solver stopping quantization. This activates the preregistered allowance to choose
nearby fixed physical lengths when the tool-relative preference is below numerical resolution.
The final R1 lengths are frozen as `0.004 m` and `0.008 m`; minimum surface-boundary clearance is
correspondingly `0.0085 m`. Anchors are regenerated under that clearance and the superseded hash
above must not be used for R1. This correction used no `R_q`, directional cost, or completed probe
outcome; the interrupted process produced no result artifact.

Final post-correction anchor freeze, before the restarted R1: content hash
`4fdc4cbdc76d2efa767627b271a5754d89453de0c73ddd3fa4556978849db79b`.

- saddle/P_low, P_mid, P_high: `A00@22, A01@233, A02@466, A03@685, A04@904,
  A05@1136, A06@1355, A07@1581, A08@1807, A09@2033, A10@2251, A11@2477,
  A12@2696, A13@2922, A14@3147`.
- hemisphere/P_low, P_mid, P_high: `A00@1, A01@399, A02@773, A03@1172,
  A04@1558, A05@1969, A06@2355, A07@2754, A08@3140, A09@3614, A10@4000,
  A11@4423, A12@4797, A13@5208, A14@5595`.

The final eligible pools were 228 samples per saddle scene and 293 per hemisphere scene. This is
the only anchor freeze admitted for the restarted R1.
