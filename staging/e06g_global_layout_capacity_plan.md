# E06-G global coverage-layout capacity gate

Registered: 2026-09-12, before root/remesh smoke testing or robot-performance evaluation.

Status at registration: REGISTERED / NOT RUN.

## Audit and question

The existing NUC adapter exposes physical `root_face` selection and reproduces the separately
built upstream `upstream_first` implementation. E06's `minimum_cost_nuc_lift` provides the
qualified multi-branch full-path continuation contract, float64 transition witnesses, and the
strict 5D task checker. E06-R freezes six fully liftable placements. These components are reused.

Missing contracts are: deterministic same-surface remesh realizations, physical roots independent
of face IDs, common-reference evaluation across remeshes, global-layout diversity metrics, and a
path adapter allowing E06 continuation over ordered paths mapped to that reference surface. The
adapter is an execution backend, not a new coverage planner.

E06-G asks whether globally distinct but finite-footprint-equivalent layouts produce material
verified joint-cost or numerical continuous-liftability differences. It does not tune E06-R2 or
E06-J, optimize paths, change expansion order, or introduce learning.

## Frozen physical test bed

Reuse saddle placements `T17/T21/T10` and hemisphere placements `T30/T27/T33` as low/mid/high.
Keep the E06 UR5e, `attachment_site`, tool axis, axis-symmetric normalized 5D task, `L_c=0.1 m`,
`W=I`, `sigma_safe=0.07237417172157597`, `0.05 rad` dense q interpolation, position/axis
tolerances, collision scene, joint limits, footprint radius `0.008 m`, and
`delta_NUC=0.0297927413`.

One common evaluation surface per physical object is frozen independently of planning meshes:
saddle uses an analytic `12 x 12` grid with 12 samples per face; hemisphere uses analytic
`24 x 10` azimuth/polar resolution with 8 samples per face. Every layout is projected to and
evaluated on this surface for NUC, intrinsic length, diversity, visualization, and robot task
targets. A candidate is never evaluated on its own planning mesh.

## Frozen remesh rule

Planning resolution remains exactly E06's saddle `6 x 6` cells and hemisphere `12 x 5` rings.
All vertices lie on the same analytic surface and boundaries are unchanged.

- `M00`: exact canonical E06 mesh.
- Saddle `M01/M02/M03`: opposite, checkerboard, and x-stripe cell diagonals on the same vertices.
- Hemisphere `M01`: half-azimuth phase with canonical diagonals; `M02`: zero phase with opposite
  quad diagonals; `M03`: half phase with checkerboard diagonals.

All use `upstream_first` and the same four projection-refinement iterations. Admission requires
identical vertex/face counts within a surface, mean edge length within 10% of M00, edge-length CV
within 15% absolute of M00, maximum analytic/reference approximation error at most `0.006 m`, and
analytic boundary residual at most `1e-10 m`. No scaling or placement change is permitted.

## Frozen physical root rule

`R00` is the physical centroid of canonical planning face zero projected to the common reference
surface. `R01`--`R07` use deterministic approximate intrinsic farthest-point sampling over common
reference samples, with ID tie-breaking. These eight object-frame coordinates are shared across
all remesh IDs. Each root maps to the nearest planning facet by deterministic projection; mapping
error must not exceed `0.006 m`. A failed pair is rejected, not replaced by a favourable root.

## Stage A smoke contract

Before freezing the 64-layout library, run saddle `{R00,R01} x {M00,M01}`. Stop if: canonical
`R00/M00` differs from direct `generate_nuc_skeleton(..., upstream_first)`; physical roots depend
on remesh; remesh validation fails; root mapping is nondeterministic/out of tolerance; any
candidate is evaluated on a non-reference surface; expansion policy differs; or structural NUC
validation fails. Smoke geometry is contract evidence, not robot-performance evidence.

Candidate-family diversity is considered failed only if, on a surface, both the median pairwise
ordered-path distance is below `0.25*r_tool = 0.002 m` and median tangent disagreement is below
`5 deg`. Labels alone do not establish diversity.

## Frozen factorial library and geometry admission

Freeze `8 roots x 4 remeshes` for each surface: 64 object-frame layouts and 192 placement-layout
executions. The only varying factors are physical root and remesh. Expansion policy is always
`upstream_first`; local refinement and subdivision are fixed.

Select `Gamma_geo` independently for each physical surface, before robot evaluation, by
lexicographic `(E_NUC, intrinsic surface length, root ID, remesh ID)`. Retain `R00/M00` as
`Gamma_canonical`. A layout is coverage-equivalent when
`E_NUC <= E_NUC(Gamma_geo)+0.0297927413`; report `E_miss` and `E_rep` separately.

## Frozen full-path execution backend

For each remesh/placement, enumerate one shared safe IK catalog over its canonical subfacet
waypoint set with E06 budgets: eight random restarts, six retained IK candidates, five orientation
cone samples, six active branches, seven task-edge samples, `0.1 rad` maximum continuation step,
and `0.0015 m` construction position tolerance. All eight root orderings reuse the catalog and
transition cache. Ordered transitions are constructed on the common reference surface. Final
admission uses the unchanged strict checker and actual float64 witness
`J_q=sum_i ||q_(i+1)-q_i||_2`. Failure wording is `continuous_lift_not_found_under_budget`.

For a default apparent rescue (`Gamma_geo` fails, an equivalent alternative succeeds), strong
sensitivity deterministically selects `Gamma_geo` and the lowest-default-`J_q` alternative, once
per scene. Both receive 32 random restarts, 24 candidates/active branches, 15 task-edge samples,
and otherwise unchanged task/admission thresholds. No other candidate may be added post hoc.

## G0 metrics and frozen gate

For each scene compute coverage-equivalent verified count, `J_min/J_max/median/IQR`,
`S_q=(J_max-J_min)/J_min`, finite-library oracle `Gamma_q*`, and geometry-baseline gain
`Delta_global`. Also report `C_q=J_q/L_S` and the fixed 2% length-matched subset/gain. Describe
root, remesh, and root-by-remesh ranges/variance contributions for `J_q`, `C_q`, lift success,
`E_NUC`, and `L_S`.

Cost GO requires both: at least four of six scenes have `S_q>=10%`, and median `Delta_global>=8%`
over scenes with verified `Gamma_geo`. Robot-specific support additionally requires visible `C_q`
spread or nontrivial length-matched gain, which is reported but not assigned a post-hoc theorem
threshold. Feasibility GO applies only if cost GO fails and at least two scenes show a
strong-search-confirmed geometry-baseline failure with an equivalent alternative success.

If neither gate passes, stop after G0 and do not run G1. If either passes, G1 may only post-process
the frozen verified witnesses using existing `G_exec`; it cannot alter geometry. G1 reports
`L_G/J_q` correlations, cheap/expensive tangent alignment versus `C_q`, metric-selected layout,
and oracle-gap recovery when the denominator is material.

## Planned files and non-claims

Add `configs/global_layout_capacity_gate_v1.json`,
`src/diffusion_coverage/diagnostics/global_layout.py`, focused freeze/run/summary scripts, contract
tests, frozen library artifacts, raw geometry/execution/effect/diversity tables, plots and source
tables under `results/global_layout_capacity_gate_v1/`.

E06-G cannot establish continuous global optimality, C-space connectivity/disconnection, full
physical NUC contact equivalence, hardware performance, cross-robot generality, planner
superiority, or Flow Matching/diffusion benefit. A finite library and finite continuation budget
bound every conclusion.

## Stage A pre-result tolerance correction

Before any robot execution or complete library freeze, the initial remesh contract test showed that
the canonical E06 hemisphere mesh `M00` itself has maximum projection discrepancy
`0.0060700861 m` against the newly densified common reference surface. All four hemisphere
realizations had the same discrepancy within `1e-14 m`; saddle discrepancies were
`0.0005224118 m`. Therefore the provisional `0.006 m` absolute ceiling incorrectly rejected M00
without distinguishing remesh quality. The ceiling is corrected once to `0.0061 m` before Stage A
smoke completion and before any `J_q`/lift result. Root mapping, relative edge statistics, physical
boundary, and every robot/coverage threshold remain unchanged. No further adjustment is allowed
after smoke execution.

## Frozen Stage B artifacts

Stage A passed all canonical-path, physical-root, reference-surface, fixed-policy, mapping, and
structural checks. Stage B was frozen at code commit `4bdbaa1` before any robot execution. Artifact
hashes are: roots `8547418f72c3a46e08f70bfaa06378ac1f8ea884e20801ccd29f6f352ad003dd`,
remeshes `3bb2170272191537772602724db94c3d587c0c55f460d43c46d76cf6783b99b7`, and 64-layout
library `ad897a3249e4f39aaac606812e4501ecc7f28046475763b1075c472d94b6bc09`.

The median ordered-path distance/tangent disagreement is `0.129189 m / 89.959 deg` for saddle and
`0.146989 m / 89.849 deg` for hemisphere, so the preregistered diversity-failure criterion does
not fire. Geometry-only baselines are saddle `R01_M03` and hemisphere `R00_M03`. Under the frozen
`delta_NUC`, saddle has one coverage-equivalent layout and hemisphere has eight. This means the
cost-spread gate cannot reach four scenes from the admitted library, but the library and threshold
remain unchanged; Stage C is retained to measure numerical liftability and feasibility-rescue
evidence under the frozen rule.
