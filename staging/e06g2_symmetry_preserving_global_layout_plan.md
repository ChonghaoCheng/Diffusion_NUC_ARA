# E06-G2 symmetry-preserving global layout gate

Registered on 2026-09-12 before symmetry-orbit robot outcomes were generated.

Status at registration: REGISTERED / NOT RUN.

## Question and historical audit

E06-G produced physically diverse paths and large raw robot-cost spread, but remeshing changed NUC
quality and length enough to collapse the admitted library. E06-G2 varies only an exact
self-isometry of one physical path while robot/object placement stays fixed.

The qualified E06-R path source is the E06 `S00`, `upstream_first` strict witness at
`results/nuc_robot_skeleton_coupling_v1/witnesses/{surface}_P_easy_S00.npz`, produced by code commit
`78876d52d5313c0e99978700ff3cb7de02e2d0a5`. E06-R converted this same object-frame task trace to
each placement. Audit revealed that these source points lie on the E06 coarse triangular mesh,
whereas E06-G2 requires an exact analytical-surface orbit. Therefore Stage A applies one
deterministic analytical projection to the extracted object-frame trace and freezes that result as
E06-G2 `Gamma_0`. The source-to-canonical displacement is reported. `theta=0` must reproduce this
frozen canonical path byte-for-byte. No robot outcome may select or alter it.

## Frozen geometry proposal

Hemisphere uses object-frame rotations `theta_k=15*k deg`, `k=0,...,23`, about analytical z. Points
are radial-normalized to the fixed `0.14 m` hemisphere only during canonicalization; transformed
normals are the analytical outward normals. Saddle uses `z=1.5(x^2-y^2)` on the centered
`0.24 x 0.24 m` square. Candidate symmetries are identity, x reflection, y reflection and 180-degree
z rotation. A saddle member is retained only if code verifies analytical surface, bounded-domain,
normal covariance, ordered segment-length and evaluator invariance; arbitrary rotations are banned.

The canonical trace is one active segment. Rigid transforms preserve sample count, order, activity,
topology and Euclidean segment sequence exactly. The common physical evaluators are deterministic,
symmetry-compatible reference meshes fixed in Stage A. Hemisphere uses the existing 24-azimuth
structured reference so each 15-degree rotation is a sample/mesh automorphism. Saddle may use a
cell-centred four-triangle reference realization if required to make all exact analytical
reflections evaluator automorphisms; this is an evaluator representation correction, not a planning
variable, and it must be frozen before robot execution.

Stage A freezes a single numerical invariance tolerance equal to the maximum observed orbit error
rounded upward, with a hard ceiling of `1e-10` for each of `E_miss`, `E_rep`, `E_NUC`, total path
length and per-segment length. Any larger discrepancy stops the experiment; scientific coverage
admission is never widened.

## Frozen scenes and robot search

Reuse saddle `T17/T21/T10` and hemisphere `T30/T27/T33` as low/mid/high. Keep UR5e,
`attachment_site`, tool-axis convention, normalized axis-symmetric 5D task, `L_c=0.1 m`, `W=I`,
`sigma_safe=0.07237417172157597`, maximum dense q step `0.05 rad`, position tolerance `0.003 m`,
axis tolerance `10 deg`, collision contract and joint limits.

Every orbit member independently enumerates start-state IK with eight deterministic random restarts,
at most six candidates and five orientation-cone samples. Each start candidate receives the same
full dense-path continuation with `0.1 rad` maximum continuation step and `0.0015 m` construction
position tolerance; retain the minimum verified witness `J_q`. Seeds are a fixed function of
surface and placement only, deliberately independent of angle and execution order. No orbit member
may warm-start another. This is the qualified E06-R full-path numerical continuation pattern, not a
global q-space optimum.

If a scene mixes default successes and failures, rerun every failed member with 32 restarts, 24
candidates, 15 orientation-cone samples and otherwise unchanged task/admission. This strong budget
is frozen before default outcomes; no successful member is rerun and no failure subset is selected
post hoc.

## Metrics and gate

For each strict float64 witness compute `J_q=sum ||q_(i+1)-q_i||_2`, `C_q=J_q/L_S`, and transition
cost over first 5%, middle 90% and last 5% by transition-index arclength. Per scene report verified
count, `S_sym=(J_max-J_min)/J_min`, and canonical gain
`Delta_sym=(J_q(theta=0)-J_min)/J_q(theta=0)` when canonical verifies.

Cost GO requires at least two of three hemisphere scenes to have `S_sym>=10%` and median
`Delta_sym>=8%` over canonical-verified hemisphere scenes. If cost GO fails, feasibility GO requires
at least two of all six scenes to retain a mixed symmetry-orbit success/failure split after the
frozen strong rerun. Otherwise E06-G2 is NO-GO.

Only after either capacity gate passes may Stage D post-process unchanged witnesses with the
validated E06-R `G_exec`, reporting integrated `L_G`, eigendirection alignment, `L_G/J_q`
correlations, metric-selected angle and finite-orbit recovery. Stage D cannot modify paths or claim
a planner/global optimum.

## Outputs and non-claims

Add focused symmetry geometry, execution and summary code; contract tests; frozen canonical/orbit
artifacts; raw geometry/execution/sensitivity tables; summaries and figures under
`results/symmetry_preserving_global_layout_v1/`.

E06-G2 cannot establish global path or q-space optimality, C-space connectivity/disconnection,
hardware performance, general non-symmetric-surface parameterization, planner superiority,
cross-robot generality or Flow Matching/diffusion benefit. It tests one finite exact symmetry orbit
under finite deterministic numerical continuation budgets.

## Frozen Stage A/B artifacts

Stage A initially exposed a representation defect before robot execution: independently asking the
mesh backend for a shortest polyline between every adjacent source point selected different
equal-length routes at symmetry-related mesh ties, changing temporal revisit counts. The finalized
evaluator representation densifies the canonical analytical path once at the frozen `0.002 m`
spacing, evaluates its ordered footprint membership once with the same mesh-edge geodesic disk
backend, verifies that each symmetry is a mesh/sample/weight automorphism, and transports that
membership over the verified permutation. This removes vertex-ID tie breaking without widening the
coverage criterion or changing path order.

The symmetry orbit was frozen at code commit `b1cf1458ec85b9ce5c393d2faab24d5a9cd43711`.
The orbit hash is `479f4e0497e4ddd771ab7f445c3505aaaa5da4543895cc82296110abbfc4fdd1` and the
pre-robot invariance tolerance is `1e-12`. All 28 members have identical `E_miss`, `E_rep`,
`E_NUC`, total discrete physical length, ordered segment-length sequence, sample count/activity,
and topology within that tolerance.

Saddle `Gamma_0` comes from 3,171 E06 S00 source samples; analytical projection moves a point by at
most `0.00020390625 m`, preserves 3,171 samples, and yields path hash
`dfd59b447091aeaff80631c22c8666107b122dd297597c00c927793677f70da0`. Hemisphere starts from
5,597 source samples; analytical projection plus deterministic spacing densification gives 6,340
samples, maximum source projection `0.00617877480 m`, and path hash
`989323dd6ebdd3206fcad6f593ad0404d7075517abc48dbe00fc29dfed5b360c`. These displacements are
an explicit limitation: E06-G2 uses an analytically normalized derivative of the E06-R path, not a
byte-identical replay of its coarse-mesh target trace.

The frozen E06-G2 geometry values are saddle `E_NUC=0.7565340822`, `L_S=6.0139313520 m` and
hemisphere `E_NUC=0.4033100677`, `L_S=10.4904846652 m`. They must not be substituted for historical
E06 metrics because the common symmetry-compatible evaluator representation differs. Robot Stage C
had not run when these artifacts and values were committed.
