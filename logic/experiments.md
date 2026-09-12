# Experiments

## E00: Representation audit
Generate 200-500 expert plans on cylinder, hemisphere, saddle, and free-form surfaces over a footprint-radius range. Measure geometry reconstruction error, missed-coverage change, and intrinsic-length change for fixed-control-point B-splines. Compare against variable-token arclength paths with deterministic gauge fixing. Stop if representation error becomes material at small footprint radii.

**Corrected result (2026-08-24):** After restoring the archived quadrature and robustly rechecking serialized targets, 200 expert paths still reject fixed-control-point compression. At 128 control points, p95 normalized geometric error was `0.329126`, p95 absolute missed-coverage change was `0.025391`, and p95 absolute relative-length change was `0.038799`. Segment-preserving padded variable-token paths are retained. Earlier Stage 0 numbers are diagnostic-only because they used a reconstructed quadrature.

## E01: Pre-training UR5e liftability motivation gate
Generate hundreds of classical 3D surface coverage paths before training a new model. For multiple base placements and orientation tolerances, enumerate pose-wise UR5e IK branches and test whether a continuous branch/component assignment exists. Report continuous-lift success and decompose failures into reachability, joint limits, singularity, orientation tolerance, collision, and colour/component discontinuity.

**Result (2026-08-24):** Across 200 workspace-first plans, continuous-lift success was `52%` overall and `53.3%` among the 195 instances with 100% sparse pose-wise IK calibration. Success was strongly surface-dependent: cylinder `2%`, hemisphere `6%`, saddle `100%`, and free-form patch `100%`. A stronger fixed-placement check on five cylinder and five hemisphere failures (2.5 mm sampling, beam 24, 32 restarts) remained `0/10` liftable. This passes the motivation gate but does not certify C-space disconnection: the checker is finite-beam numerical continuation, workpiece collision is absent, and detailed failure labels are solver-sensitive.

## E02: Surface-only infrastructure
Condition on `(S,r,k)` and compare raster, contour/spiral, multi-start optimization, diffusion, FM, and an adapted Flow Matching Ergodic Coverage baseline. Test footprint scaling and best feasible cost versus equal wall-clock budget. This is supporting evidence, not the headline result.

**Stage 1 result (2026-08-24):** After correcting candidate selection to minimize length subject to the missed-coverage constraint, the strongest K=8 run reached `72.5%` hard feasibility but required `1.771741` times the corrected teacher length. Fresh training reached `32.5%` feasibility and `1.614422` times teacher length. Both fail the registered `90%` feasibility and `1.10x` length gate; E03 and direct C-space FM remain blocked on surface-planner quality.

**Single-family and representation ablation (2026-08-24):** On a restricted 35-instance validation cohort using only `raster_u_phase_0.00`, the best balanced XYZ loss reached `74.3%` feasibility at `1.440137` times teacher length; the shortest tested XYZ variant reached `65.7%` at `1.348028` times teacher length. Analytic-UV targets passed the unchanged hard checker on all 35 instances with a `0.998347` mean length ratio, and the UV model completed training, but its formal K=8 generated-plan evaluation was interrupted and remains pending. The Stage 1 status therefore remains no-go.

**Structured-control update (2026-08-24):** Dense analytic UV reached `77.1%` feasibility at `1.535950x` teacher length; adding length/tangent pressure reached `1.078910x` but collapsed feasibility to `17.1%`. A raster stroke-control representation reduced outputs to 10-32 geometry-derived tokens. On 33 held-out unrepaired `raster_u_phase_0.00` instances, K=8 reached `31/33 = 93.9%` feasibility and `0.926890x` mean teacher length (`0.930716x` on feasible instances), passing the registered point-estimate gate. The Wilson lower confidence bound remains below 90%, and the result does not cover multiple pattern families or repaired paths. E03 may proceed as a structured multimodality experiment; direct C-space claims remain premature.

## E03: Multimodality
After canonicalization, draw 100 candidates per instance, cluster with a geometry-aware symmetric path distance, and report feasible modes, mode entropy, best-of-M cost, and expert-mode coverage.

**Corrected result (2026-08-30):** The former absolute-control five-mode result remains invalid as evidence of generative multimodality: it replayed deterministic analytic templates, and a genuine multi-start replacement initially exposed an absolute-UV no-go. A footprint-normalized template-residual representation then passed a complete float32 roundtrip contract on an expanded 160-instance, 2,055-candidate corpus. On one fixed 128/32 split, three independent-coupling seeds recovered all 93 validation modes at K=8; candidate feasibility was `92.47%`, `81.32%`, and `81.32%`, and mean feasible length ratios were `1.0498`, `1.0430`, and `1.0403` relative to the best same-mode teachers. The deterministic template was `1.1208x` the best teacher length on average, while generated proposals were approximately `0.933-0.940x` template length. E03 therefore passes the conditioned best-of-eight surface gate for the admitted structured families, but it does not establish autonomous discovery of discrete modes and single-sample feasibility remains seed-sensitive. Work advances to E04 synthetic 3D liftability; the UR5e C-space claim remains deferred.

**Vocabulary-expansion update (2026-08-30):** A missing raster-v family was added for cylinder and hemisphere after a controlled cross-axis audit. The canonical v10 corpus contains 2,455 candidates and passes 2,455/2,455 float32 residual hard checks plus complete Dataset-index iteration. On the same fixed instance split, the validation vocabulary grew from 93 to 109 instance-mode tasks. Three 5,000-update seeds recovered `108/109`, `108/109`, and `109/109` modes at K=8; candidate feasibility was `86.81%`, `83.72%`, and `82.91%`, and mean feasible length ratios were `1.0417`, `1.0439`, and `1.0413`. The conditioned generator therefore remains viable after the discrete vocabulary expansion, with two single-mode K=8 failures retained rather than hidden.

## E04: Synthetic 3D liftability colours
Assign controlled overlapping colour sets to 3D surface samples and vary component disappearance, bridges, nesting, and boundary curvature. Measure the segment-budget length frontier under known connectivity ground truth.

**Finite-library go/no-go result (2026-08-30):** Colour availability was grounded in the analytic charts of 160 actual cylinder, hemisphere, saddle, and free-form instances. Exact fixed-path colour completion evaluated 24,660 candidate-field pairs over 1,920 fields. Relative to selecting the shortest workspace path, exact colour-aware selection from the same candidate library improved lift success by `19.53` percentage points at easy `k=4`, `18.28` points at medium `k=16`, and `17.50` points at hard `k=32`. Instance-cluster bootstrap 95% intervals for paired rescue were `[15.31,24.06]`, `[14.22,22.50]`, and `[13.44,21.88]` points. The corresponding per-instance feasible length ratios were `1.0218`, `1.0265`, and `1.0477`. Segment counts were identical at UV sample steps `0.02`, `0.01`, and `0.005` on 3,192 matched checks, and the exact dynamic program matched exhaustive assignment on random tiny paths. This passes the synthetic workspace-first motivation gate, but the frontier is globally exact only over the finite candidate library. E04 remains active while a colour-aware multi-start teacher tests whether direct constrained search improves that reference.

**Colour-aware search and proposal audit (2026-08-30):** A lexicographic colour-aware multi-start teacher produced no feasibility rescues at the matched four-restart, 12-step budget, although augmenting the workspace library reduced feasible length by `1.62-4.12%`. A targeted 16-restart, 48-step run rescued three of four selected near-boundary failures, showing that the objective can cross a segment-budget boundary at higher cost. The larger population effect instead came from a missing discrete raster-v family. After canonical cross-axis expansion, paired rescue reached `39.22` points at easy `k=4`, `33.91` points at medium `k=16`, and `17.50` points at hard `k=32`, with instance-cluster intervals `[35.16,43.28]`, `[30.00,37.97]`, and `[13.44,21.88]`. Corresponding rescued-plan length ratios were `1.1088`, `1.1228`, and `1.0477`. E04 now supports the claim that workspace-shortest and colour-liftable plans differ, while also demonstrating that the measured gap is sensitive to discrete proposal coverage.

## E05: UR5e workspace-first versus C-space generation
Compare surface planning plus post-hoc IK against direct C-space FM under `(S,r,tau,k,T_base)`. Primary metrics are continuous-lift success, failure decomposition, length frontier, finite-footprint quality, and best feasible liftable plan versus wall-clock time.

**Current gate and next control (2026-08-30):** E01 already establishes the real UR5e motivation gap and its denser fixed-placement sensitivity check. Before implementing direct C-space FM, the next registered control holds each UR5e base transform and continuation checker fixed while comparing the geometry-shortest workspace proposal against best-of-mode proposals from the expanded vocabulary. This test separates a missing workspace proposal-family explanation from a genuinely configuration-space planning explanation.

**Fixed-placement control and real-component update (2026-08-30):** The registered workspace-vocabulary control is complete. Exhausting all 1,339 hard-feasible teacher candidates over 80 difficult cylinder/hemisphere instances produced only three apparent rescues, of which two survived a stronger continuation check. A new numerical UR5e surface IK graph then enumerated local candidates, checked neighbour transitions, and extracted components on forty paired 8x8 grids. Under nested cone enumeration, increasing tool-axis tolerance from 3 to 10 degrees raised mean largest-component coverage from `60.94%` to `85.16%` and reduced the exact mean full-cover component count from `7.45` to `2.175`; all forty paired segment frontiers were monotone. A GPU candidate MPNN did not improve this coarse selection problem: held-out coverage relative to exact was `89.82%`, versus `94.58%` for component-size ordering and `100%` for marginal greedy. The component-selection learning branch is closed. E05 now advances to constructing short continuous q-space coverage teachers inside selected real IK components; direct C-space FM remains unimplemented.

**Strict-continuation correction (2026-08-30):** The preceding 8x8 component frontier is now
diagnostic only: its edges did not enforce task-space surface tracking between endpoint poses.
A dense recheck likewise rejected all six formerly feasible 32x10 q-teacher labels. The graph
contract now stores float64 IK-continuation witnesses and the hard checker independently
interpolates them in q space. Under this corrected contract, a 24x8 pilot passed all six
surface/budget combinations. At `k=16`, missed coverage was `40.430%` on the cylinder and
`8.798%` on the hemisphere, exposing strict cylinder fragmentation as the current bottleneck.
The legacy 20-instance teacher corpus is not training data. Direct C-space FM remains
unimplemented; the next gate is a calibrated strict teacher corpus.

## E06: NUC skeleton-robot coupling motivation gate

Hold finite-footprint coverage, remeshing, root condition, robot, placement, and numerical
lifting budget fixed while varying only legal expansion choices in the upstream NUC skeleton
construction. For saddle and hemisphere surfaces at three neutrally calibrated rigid
placements, compare geometry-only skeleton selection against an execution-aware oracle over
20 skeleton candidates. The oracle minimizes actual float64 continuation-witness joint length
`L_q` subject to the frozen NUC-equivalence tolerance and normalized 5D task-singularity
threshold.

**Result (2026-09-09):** NO-GO. The registered 120-candidate run found zero of six scenes with
at least 10% coverage-equivalent `L_q` spread, versus four required. The four scenes with a
valid geometry baseline/oracle pair had only `0.3985%` median `L_q` reduction, versus 10%
required. Saddle skeleton rankings changed substantially across placement but only within a
roughly 1% cost band. Hemisphere P_mid/P_hard had no continuous lift found under the fixed
numerical budget despite high neutral pose-wise reachability. E07 remains blocked; no learned
model or deformation method was run.

## E07: Incremental benefit of fixed-topology surface-joint deformation

On a small subset of E06 scenes, compare fixed-surface robot refinement, isotropic tangential
surface perturbations, and normalized-5D robot-metric-guided perturbations at equal proposal
budget. Keep skeleton topology fixed and admit candidates lexicographically by strict robot
feasibility, frozen NUC equivalence, positive task-singularity margin, then witness `L_q`.

**Status (2026-09-10):** blocked after the E06 NO-GO and not run. E06-D did not authorize it:
the local metric was predictive, but the tested skeleton degree of freedom had only sub-1.57%
metric spread and strong continuation sensitivity was placement dependent.

## E06-D: NUC skeleton coupling mechanism diagnosis

Post-process the registered E06 skeletons and float64 continuation witnesses to diagnose why
legal expansion-order choices changed robot-dependent ranking but produced only a sub-1.1%
execution-cost spread. Measure exact transition-set and ordered geometric variability, attribute
actual witness `L_q` to canonical skeleton transitions, and test whether the normalized 5D local
task metric predicts witness motion. Replay the 40 archived hemisphere P_mid/P_hard failures under
the unchanged E06 contract to localize candidate, safety, transition, or finite-search exhaustion;
then rerun only a deterministically selected diagnostic subset under the preregistered stronger
search budget.

**Status (2026-09-10):** registered and active. This is a diagnosis of the failed E06 mechanism,
not a new planner or a revision of the E06 NO-GO. E07 remains blocked and must not run.

**Result (2026-09-10):** resolved as **Outcome 2**, with a mixed Outcome 4/5 hemisphere
diagnosis. Candidate-level normalized-5D metric correlations with witness `L_q` were high
(Pearson `0.8984-0.9998`, Spearman `0.9338-1.0000`) while both metric and witness spreads stayed
below `1.57%`. Strictly variable transitions carried substantial cost, but transitions appearing
in at least half the library carried median `91.23%` (saddle) and `97.95%` (hemisphere) of `L_q`;
candidate differences therefore mostly reorder a high-frequency local-motion vocabulary whose
costs cancel at plan level. Default hemisphere replay reproduced all forty E06 failures. Strong
search recovered two of four P_mid cases, delayed two others, and recovered zero of four P_hard
cases. Legal expansion-order selection is closed as a useful optimization degree of freedom;
the Riemannian line is retained only as a diagnostic. E07 remains blocked and was not run.
# E06-R: Riemannian Anisotropy Planning-Utility Gate

Registered: 2026-09-12

Status: REGISTERED / RUNNING. E07 remains BLOCKED / NOT RUN.

Parent evidence: `evidence/nuc_robot_coupling_diagnosis_2026-09-10.md`

E06-R tests directional planning utility without optimizing a coverage path. At the same
admitted contact state `(x,q)`, it compares equal-intrinsic-length surface probes along both
signs of the minimum- and maximum-cost eigenvectors of the normalized 5D robot-induced surface
metric. R0 selects low/mid/high-anisotropy, fully liftable scenes without observing directional
probe outcomes; R1 then measures the sign-averaged actual joint-length ratio `R_q` against the
local prediction `R_G0`.

The frozen robot contract remains `L_c=0.1 m`, `sigma_safe=0.0723741717`, `W=I`, maximum dense
q interpolation step `0.05 rad`, the E06 position/tool-axis tolerances, float64 witnesses, the
same MuJoCo UR5e model, `attachment_site`, and tool-axis index/sign. Before any probe table was
written, the requested numerical-resolution audit rejected `0.002 m` because it was only twice
the internal `0.001 m` IK stopping tolerance. Probe lengths are therefore frozen at `0.004 m`
and `0.008 m` (`0.50` and `1.00` times the frozen tool radius).

The preregistered GO gate requires pooled Spearman correlation at least `0.70`, at least `0.80`
of eligible medium/high-anisotropy anchors with `R_q>1`, high-anisotropy median `R_q>=1.20`,
matched intrinsic lengths, and persistence after removing the lowest-sigma anchor quartile.
Passing only authorizes registration of a separate fixed-topology local-deformation experiment;
it does not run E07 or path deformation.

Detailed frozen hypotheses, selection rules, metrics, controls, and non-claims are in
`staging/e06r_riemannian_anisotropy_utility_plan.md`.

Result: GO. Across 90 frozen independent anchors, the sign- and length-aggregated directional
ratio had pooled `Spearman(log R_G0,log R_q)=0.9471`; medium/high scenes placed the predicted
expensive direction above the cheap direction at `96.67%` of anchors, and the high-level median
was `R_q=1.7157`. All 720 signed probes passed the unchanged strict kinematic checker. See
`evidence/riemannian_anisotropy_utility_2026-09-12.md`. This result changes neither E06/E06-D nor
the status of the historical E07.

# E06-R2: Fixed-topology local Riemannian deformation validation

Registered: 2026-09-12

Status: COMPLETED / NO-GO (2026-09-12). This is not the previously blocked E07. The full
pre-result contract is frozen in `staging/e06r2_local_deformation_plan.md`.

Motivation: E06-R passed its directional planning-utility gate. A subsequent experiment may now
compare fixed-surface refinement, isotropic tangent proposals, and metric-guided tangent proposals
from identical path/witness initializations, equal proposal budgets, the same strict checker, and
the frozen NUC-equivalence constraint. Registration does not establish full-path benefit and no
deformation was run in E06-R. E06-R2 freezes the six E06-R scenes, 60 deterministic windows,
`0.048 m` window length, seven controls with three free interior controls, `0.002 m` displacement
bound, equal 60-proposal M1/M2 budgets, unchanged strict task contract, matched length/coverage
admission, paired outcomes, and the GO/NO-GO gate before generating deformation results.

Result: the metric-guided deformation did not provide a practically meaningful incremental
benefit. In 40 medium/high windows, M2 beat M1 in `52.5%`, median `A_R` was `0.0040%`, and median
M2 improvement over M0 was `2.3349%`, against frozen gates of `75%`, `5%`, and `8%`. The metric
still tracked candidate cost (`Spearman=0.9858` over admitted M2 candidates), but this fine-scale
ranking did not improve the selected local deformation enough. A larger NUC-constrained planner
is not authorized by E06-R2. See `evidence/riemannian_local_deformation_2026-09-12.md`.

# E06-J: Surface--Configuration Coupling Capacity Gate

Registered: 2026-09-12

Status: COMPLETED / NO-GO (2026-09-12).

E06-J reuses the 60 frozen E06-R2 windows but asks a different formulation question. It compares
fixed F0, configuration-only F1, true-witness-ranked surface-only F2, and joint surface--q F3.
Every optimized method minimizes actual verified `J_q`; `G_exec` is diagnostic only. F2/F3 share
one frozen surface candidate bank, while F1/F3 share one frozen layered-q solver. The complete
pre-result contract and practical gate are recorded in
`staging/e06j_surface_configuration_coupling_plan.md`. E06-R2 remains NO-GO and historical E07
remains blocked.

Result: all 60 F0 witnesses replayed exactly. In the 40 medium/high-anisotropy windows, median
`Delta_conf=0`, median `Delta_surface=1.7162%`, median `Delta_joint=1.6605%`, and median
`B_joint=0`. F3 beat both decoupled formulations in only `45%` of windows; all frozen gates
failed. F2 and F3 selected the same surface candidate in all 60 windows, while their independent
q realizations differed by less than `0.1%` of F0 cost. The result is Outcome D: low local
headroom. It does not justify making the surface--configuration manifold the central planning
space under this local fixed-topology parameterization. See
`evidence/surface_configuration_coupling_gate_2026-09-12.md`.

# E06-G: Global Coverage-Layout Capacity Gate

Registered: 2026-09-12

Status: COMPLETED / NO-GO (G0); G1 NOT AUTHORIZED.

E06-G moves from failed local fixed-topology optimization to a finite global-layout capacity test.
It reuses the six qualified E06-R scenes and varies only eight physical root locations and four
same-surface planning-remesh realizations while fixing `upstream_first`, refinement, robot, NUC
evaluation, and strict execution contracts. All 32 layouts per surface are evaluated on one common
physical reference mesh, then under three frozen placements. G0 measures verified `J_q`, `C_q`,
length-controlled headroom, numerical liftability, and root/remesh effects. G1 is blocked unless
the preregistered G0 cost or strong-confirmed feasibility gate passes. Full definitions and
non-claims are frozen in `staging/e06g_global_layout_capacity_plan.md`.

Result: the frozen library contained 64 geometrically distinct object-frame layouts and produced
107 verified strict witnesses across 192 scene-layout evaluations. The coverage-equivalent set
collapsed to one layout for saddle and contained eight layouts but zero verified witnesses for
hemisphere. Consequently no scene had an evaluable coverage-equivalent `S_q>=10%`, median
`Delta_global=0`, and no strong-confirmed feasibility rescue existed. G0 is NO-GO and G1 was not
run. Non-equivalent layouts did show substantial raw robot-cost and liftability variation, so this
result closes only the tested root/remesh NUC family under the frozen NUC-equivalence and numerical
continuation contracts. See `evidence/global_layout_capacity_gate_2026-09-12.md`.

# E06-G2: Symmetry-Preserving Global Layout Gate

Registered: 2026-09-12

Status: REGISTERED / NOT RUN.

E06-G2 isolates pure robot-induced global orientation preference by applying exact object-frame
self-isometries to one frozen upstream/default NUC path per surface. Hemisphere uses a 24-member
15-degree rotational orbit; saddle admits only analytically and numerically validated bounded-domain
symmetries. Path order, topology, activity, intrinsic segment lengths, total length and temporal
finite-footprint metrics must remain invariant before robot execution. The six E06-R placements and
strict 5D robot contract remain fixed. A secondary `G_exec` diagnostic is blocked unless the
preregistered symmetry-orbit cost or strong-search feasibility gate passes. See
`staging/e06g2_symmetry_preserving_global_layout_plan.md`.
