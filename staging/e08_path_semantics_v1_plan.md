# E08-R1 path-semantics repair v1 — frozen plan

Registered before the eight-candidate measurement on 2026-09-16. Parent snapshots are Code
`14c7fbdaa1441f5dbab244938912a57881503ccf` and ARA
`fc35b78c591f05ef6183d584057b5ea06bad40cd`; the working branch is
`exp/e08-path-semantics-repair-v1` in both repositories.

The experiment evaluates exactly four saddle and four hemisphere geometries already frozen by
E08, once per condition and without robot-placement repetition. Original overlap, phase,
waypoint spacing, segment grouping, footprint radius 0.008 m, miss limit 0.02, and repeat limit
0.10 remain fixed. No path optimization, IK, real motion graph, real S0/S1 comparison, local
deformation, Flow Matching, or hardware execution is allowed.

Three conditions are separated. L reproduces legacy mesh shortest-path reconstruction and its
mesh-edge footprint approximation. P samples every original waypoint interval by E08's
chord-then-analytical-projection convention and applies the same legacy footprint backend without
route reconstruction. R keeps that prescribed trace and uses exact spherical angular distance or,
on the saddle, the endpoint chord lower bound and lifted straight-chart arclength upper bound.
Uncertain saddle memberships use a two-state dynamic program for minimum and maximum episode
counts; independent all-in/all-out run counts are forbidden.

The deterministic reference schedule is (1.0 mm,Q0), (0.5 mm,Q0), (0.25 mm,Q0),
(0.25 mm,Q1), and (0.25 mm,Q2). Analytical-domain cells halve on each quadrature level. Saddle
cell counts are 48x48, 96x96, and 192x192. Hemisphere counts are 48x24, 96x48, and 192x96 in
azimuth and polar angle. Midpoint locations are used; hemisphere cell areas are exact and saddle
weights use midpoint analytical surface density. No further refinement is permitted.

Q2 accepts only when both upper error bounds meet the frozen thresholds, Q1 and Q2 classifications
agree, and every final bound changes by at most 0.002. It rejects only when a lower bound exceeds a
threshold under the same stability checks. Threshold-straddling uncertainty or instability is
unresolved. These are sampled geometry decisions, not continuous-time or robot-execution
certificates.

Before measurement, repository tests must cover the planar two-triangle detour, mesh-independent
analytical trace semantics, temporal episode corner cases, sphere seam/pole behavior, saddle bound
enclosure, uncertainty-DP brute force, edge-summary equivalence, shared segment-budget reachability,
a positive repeat-bound obstruction, and a cyclic full-count oracle. A failure blocks scientific
interpretation but must remain documented.

The Code config `configs/e08_path_semantics_v1.json` is the machine-readable contract. Frozen input
arrays, hashes, quadrature definitions, environment details, results, corrected IK interpretation,
readiness evidence, and reproduction commands will be written only under
`results/e08_path_semantics_v1/`. Historical E08 files remain unchanged.
