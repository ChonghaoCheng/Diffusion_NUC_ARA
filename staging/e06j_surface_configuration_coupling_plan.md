# E06-J surface--configuration coupling capacity gate

Registered: 2026-09-12, before F1/F2/F3 result generation.

Status at registration: REGISTERED / NOT RUN.

## Question and scope

E06-J asks whether explicit optimization over surface path and configuration realization provides
material strict-witness execution benefit beyond the stronger of configuration-only and
surface-only formulations. It is a formulation-capacity test, not a rescue or parameter sweep of
E06-R2. It does not optimize a complete NUC path, alter skeleton topology, use a learned model, or
claim continuous-space global optimality.

All optimized formulations minimize the same verified witness objective
`J_q=sum_i ||q_(i+1)-q_i||_2`. Surface length, `L_G`, manipulability, and `G_exec` are diagnostics
only and cannot select an incumbent.

## Frozen inputs and common contract

Reuse all 60 E06-R2 windows with content hash
`9bd7673f4de735ea5e0aedd985e753145741f402f4f9912497815110e11065c5` over saddle
`T17/T21/T10` and hemisphere `T30/T27/T33`. Keep seven controls, fixed `x0,x1,x5,x6`, free
`x2,x3,x4`, maximum displacement `0.002 m`, unchanged local face-corridor/order rule, and the
same endpoint surface states.

Reuse the UR5e, `attachment_site`, tool-axis convention, axis-symmetric normalized 5D task,
`L_c=0.1 m`, `W=I`, `sigma_safe=0.07237417172157597`, float64 witnesses, maximum dense q step
`0.05 rad`, maximum continuation step `0.1 rad`, position tolerance `0.003 m`, construction
position tolerance `0.0015 m`, axis tolerance `10 deg`, joint limits, collision scene, and E06
mesh-geodesic NUC evaluator. Require relative surface-length change at most `2%`, each of
`E_NUC/E_miss/E_rep` no more than its F0 value plus `0.0297927413`, exact archived start q, and
terminal max-joint mismatch at most `0.05 rad`.

## Frozen formulation solvers

- **F0 fixed:** exact archived surface path and float64 q witness; no optimization.
- **F1 configuration only:** surface positions and axes are immutable. A deterministic layered-q
  beam search starts at exact F0 q and ends at exact F0 q (therefore also satisfies the terminal
  tolerance). At each interior task sample, each retained parent generates IK seeds along the
  one-dimensional normalized-5D-Jacobian null direction with offsets `{-0.12,0,+0.12} rad`.
  Valid children are ranked by cumulative verified discrete `J_q`; beam width is `8`.
- **F2 surface only:** evaluate one frozen bank of 24 nonzero surface-control vectors plus F0.
  Each surface candidate receives exactly one deterministic warm-start continuation from F0 q;
  q is not an independent decision variable. Rank admitted candidates directly by strict-witness
  `J_q`.
- **F3 coupled:** evaluate the identical 24+F0 surface bank. For each surface candidate, run the
  same layered-q beam solver as F1 and rank the admitted joint `(x,q)` candidate directly by
  strict-witness `J_q`. This is partial minimization over an enumerated numerical q graph, not a
  continuous global optimum.

The surface bank uses deterministic scrambled Sobol points in six tangent coordinates, seed
`20260912`, radially clipped per free control to `0.002 m`. It is frozen before F3 execution and
shared exactly by F2/F3. F1/F3 use maximum 80 IK iterations per seed, q-state deduplication radius
`0.01 rad`, and beam width `8`. Solver failures are retained in diagnostics. Candidate rejection
cannot update an incumbent.

This nested formulation is deliberately simple: F3 jointly chooses a surface candidate and an
independently optimized continuous q realization, while F2 has no independent q choice. Efficiency
is not a claim.

## Outcomes and gate

For each window report `Delta_conf=(J0-J1)/J0`, `Delta_surface=(J0-J2)/J0`,
`Delta_joint=(J0-J3)/J0`, and `B_joint=(min(J1,J2)-J3)/J0`, plus paired fractions F3 beats F1,
F2, and both. The statistical unit is the window. Report medians, IQR, 10,000-sample paired
bootstrap intervals (seed `20260912`), surface and anisotropy strata, convergence/failure counts,
and diagnostic integrated `L_G`, median log-kappa, and tangent/eigendirection alignment.

The medium/high GO gate is frozen as all of:

1. F3 beats both F1 and F2 in at least `70%` of windows;
2. median `B_joint >= 5%`;
3. median `Delta_joint >= 8%`;
4. the qualitative conclusion persists after removing the lowest F0-sigma quartile, requiring
   terminal mismatch at most `0.025 rad`, and relative surface-length change at most `1%`.

Classify the evidence as configuration dominated, surface dominated, coupling dominated, or low
local headroom. Do not tune budgets, bounds, or constraints after F3 outcomes. Only coupling
dominated plus the complete gate would justify making the safe surface--configuration manifold the
project's central planning space.

## Planned artifacts and non-claims

Implementation belongs under `src/diffusion_coverage/diagnostics/`, with focused run/summary
scripts, contract tests, frozen candidate bank, raw F0--F3 tables, solver diagnostics, figures, and
reproduction commands under `results/surface_configuration_coupling_gate_v1/`.

E06-J cannot establish global optimality, full NUC planning improvement, hardware performance,
cross-robot generality, C-space topology, Riemannian optimizer benefit, or learned-planner benefit.
