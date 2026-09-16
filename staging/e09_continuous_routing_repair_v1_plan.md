# E09-R1 synchronized continuous routing repair — frozen plan

Registered 2026-09-17 00:20 Australia/Sydney, before repaired graph construction or planner outcomes.

E09-R1 retains the complete radius-0.14 m analytical hemisphere, placements T30/T27/T33,
ON-segment budgets one and two, and E09 geometry hash
`01050368f57eca9a904a04106dc705aec89cab65248c508462ad232328976047`. The bank remains 238 ports,
470 directed source arcs, and 1,120 connector proposals. Coverage, task5, sigma5, joint, collision,
construction, search, and memory thresholds remain unchanged.

The repair introduces a synchronized motion trace whose q, curve parameter, declared position,
axis, and activity arrays share one parameterization. Endpoint-targeted tails retain all samples;
shape mismatches, endpoint discrepancies, activity mismatches, and recomputed-membership mismatches
are rejected. All eight deterministic seeds are evaluated per port. Up to four effective q1:5
states are retained by frozen deduplication and farthest-point selection while full unwrapped q is
stored. A single-state induced subgraph is derived without additional IK.

Cold-start methods are F (fixed route with `(repeat,Jq)` Pareto and correct OFF progress), G0
(global history search), G1 (the same graph/search plus the existing repeat bound), and S (G0 on
the induced one-state graph). All 24 cells are capped at 300 seconds or 200,000 expansions with
the existing 30,000-resident-label and 6 GiB guards.

Selected witnesses first undergo exact same-sample edge-summary composition checks, then the
fixed T0/Q1--Q4, T1/Q4, and antithetic T1/Q4a schedule. Acceptance retains the 0.02 miss, 0.10
repeat, and 0.002 resolution thresholds and is labeled sampled rather than continuous.

No geometry tuning, added family, placement change, threshold relaxation, Q5, saddle campaign,
hardware, Flow Matching, or lower-bound optimization is authorized. Negative, limited, or
unresolved outcomes will be retained and published.
