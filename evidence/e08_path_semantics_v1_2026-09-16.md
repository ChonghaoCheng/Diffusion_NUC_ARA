# E08-R1 path-semantics repair — evidence report

Registered and executed: 2026-09-16

Branch: `exp/e08-path-semantics-repair-v1`

Code result commit: `cc98cdc8117c7a170e2e03a08fee620f45cd7c30`

Code report: `results/e08_path_semantics_v1/report.md`

## 1. Scope and completion

The bounded freeze, tests, geometry, readiness, and report stages completed. Exactly eight E08
geometries were regenerated once with their original parameters and saved with hashes. They were
evaluated once per L/P/R condition, not once per robot placement. No path was optimized, no
threshold was relaxed, and no extra temporal or spatial resolution was added.

L reproduced the historical mesh reconstruction and mesh-edge footprint approximation. P kept
the same footprint backend and area samples but sampled the prescribed chord-then-analytical-
projection trajectory directly. R used deterministic analytical-domain quadrature, exact sphere
distance, and saddle chord/straight-chart distance bounds with a two-state episode dynamic
program. The five R settings were 1.0 mm/Q0, 0.5 mm/Q0, 0.25 mm/Q0, 0.25 mm/Q1, and
0.25 mm/Q2.

## 2. Reproduction and implementation repair

All eight L rows matched `canonical_qualification.csv` exactly for E_miss and E_rep; no 1e-8
replay warning fired. The archived ordered sources show why the evaluated path differed from the
prescribed one. On saddle, L lengths were 6.68--6.84 m while P lengths were 3.89--4.02 m. On
hemisphere, L lengths were 14.68--22.55 m while P lengths were 7.85--12.53 m. Every candidate's
repeat error decreased under P. This directly supports a substantial trajectory-reconstruction
artifact.

P intentionally retained the old footprint approximation. Its large residual miss values cannot
be read as an analytical-surface result. L-to-R changes additionally include physical sample
locations, analytical area weights, and distance semantics; they are not attributed solely to the
route reconstruction repair.

## 3. Eight-candidate results

Values are E_miss/E_rep. R entries are Q2 lower--upper bounds.

| Candidate | L | P | R Q2 | Decision |
|---|---:|---:|---:|---|
| saddle raster-u | 0.116651 / 0.511352 | 0.146340 / 0.125260 | 0.026564--0.026675 / 0.005929 | unresolved |
| saddle raster-v | 0.151239 / 0.460590 | 0.142941 / 0.134586 | 0.026564--0.026675 / 0.005929 | unresolved |
| saddle spiral | 0.144951 / 0.471449 | 0.168640 / 0.012181 | 0.038599 / 0.012259 | unresolved |
| saddle raster-u k2 | 0.117529 / 0.511352 | 0.147218 / 0.125260 | 0.026564--0.026675 / 0.005488 | unresolved |
| hemisphere raster-u phase 0 | 0.128660 / 0.589181 | 0.136591 / 0.031522 | 0 / 0.028614 | accepted_under_reference_checks |
| hemisphere raster-u phase 0.25 | 0.127929 / 0.630148 | 0.136591 / 0.031699 | 0 / 0.028614 | accepted_under_reference_checks |
| hemisphere raster-v | 0.016274 / 1.235340 | 0.027621 / 0.386086 | 0 / 0.576860 | unresolved |
| hemisphere spiral | 0.147563 / 0.528890 | 0.156223 / 0.011734 | 0.012865 / 0.020482 | accepted_under_reference_checks |

The three accepted hemisphere geometries were stable between Q1 and Q2. Hemisphere raster-v has
a clear sampled repeat defect at Q2, but its final change was 0.006384 and the registered rule
therefore makes it unresolved. Saddle Q2 miss lower bounds exceed 0.02, supporting genuine
uncovered gaps, but final changes were 0.006019--0.010651, so all four are unresolved. No stable
reference rejection was issued, and no percentage allocation between artifacts and path defects
is supported.

## 4. Correctness and status repair

The focused suite passed 20 tests. The complete repository suite passed 182 tests with one
existing skip and 14 warnings. It covers the two-triangle sqrt(8) mm planar path and legacy detour,
stationary/return/OFF episodes, shared endpoints, sphere seam and pole cases, saddle bound
enclosure, uncertainty-DP brute force including gap merging, identical whole-trace/edge-summary
composition, segment-budget reachability, a positive repeat-bound obstruction, shared
bottlenecks, and a cyclic bounded full-count oracle.

The corrected IK view preserves `ik_comparison.csv` and interprets `complete_lift` only as
`target_sequence_solved`. T30 has minimum sampled task singular values about 0.02696 and T33 about
0.07172, both below 0.07237417172157597 for both backends. T27 and all saddle rows pass the logged
sampled numeric checks. Dense transitions, coverage, and overall execution are NOT_RUN.

## 5. Graph readiness and interpretation boundary

S0 and S1 now share ordinary reachability on `(node, used_on_segments)`; tests distinguish that
prune from S1's finite repeat-bound prune. Endpoint membership is recomputed and checked instead
of overwritten. A readiness guard rejects absent task-preserving ON connections or node activity.
The old cross-chain builder only supplied OFF reconfigurations. Workpiece, complete tool, and
environment collisions remain unmodeled beyond the historical MuJoCo self-collision scope.

No robot requalification, real motion graph, real-surface S0/S1 comparison, final robot plan
validation, or FM training ran. Runtime benefit, pruning efficacy, and real planning success are
N/A. The accepted labels concern sampled geometry only; the saddle bounds concern sampled
membership only. Nothing here is a continuous-time certificate, global infeasibility result,
planner comparison, FM result, or RSS claim.
