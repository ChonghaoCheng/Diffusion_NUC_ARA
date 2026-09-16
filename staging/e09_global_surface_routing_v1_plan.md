# E09 full-surface robot-aware coverage routing — frozen plan

Registered 2026-09-16 (Australia/Sydney), before robot graph construction or planner outcomes.

E09 tests full analytical-hemisphere routing at frozen placements T30, T27, and T33 for ON-segment
budgets 1 and 2. Its immutable geometry bank comes only from E08-R1 accepted hemisphere raster-u
phase 0, raster-u phase 0.25, and spiral phase 0. The paths are partitioned into approximately
0.10 m macro-arcs, both directions are retained, and up to six deterministic nonadjacent ports
within intrinsic distance 0.032 m receive shortest-sphere connector proposals. This bank is frozen
before robot outcomes.

Every E09 pose solve uses the task5 backend. The common start is the first raster-u phase-0 point
and at most eight frozen seeds are tried. Robot graphs cap 2,048 states, 8,000 ON attempts, 4,000
OFF attempts, and 1,000,000 IK calls per placement. Verified OFF reconfiguration uses 0.02 m normal
retreats and includes its full joint cost. Missing numerical connections are not physical
infeasibility evidence.

F keeps one registered template direction while selecting graph q states. G0 searches the common
history-aware graph. G1 differs only by the prospective repeat-action lower bound. A common
initializer of at most 30 seconds is charged equally. Each timed method is capped at 300 seconds or
200,000 expanded labels, with checkpoints at 10/30/60/120/300 seconds. The order is
`(used reconfigurations, -covered area, Jq, serial)`.

All plans use the 0.008 m intrinsic footprint, E_miss <= 0.02, E_rep <= 0.10, 0.1 mm position,
0.1 degree tool-axis, and normalized task5 sigma >= 0.07237417172157597 requirements. Final
validation recomputes actual-FK ordered episodes on Q1/Q2. A Q1/Q2 change over 0.002 or a threshold
crossing is unresolved. Collision claims are limited to contacts modeled by the pinned MuJoCo XML.

The machine-readable contract is `configs/e09_global_surface_routing_v1.json`. No saddle campaign,
FM training, hardware execution, path tuning, or threshold relaxation is part of E09.
