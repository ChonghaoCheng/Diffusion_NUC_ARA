# E09-R1: synchronized continuous connections and multi-state routing

Date: 2026-09-17 (Australia/Sydney)

Code result: `31c6bb2068c789205924aacd3bab6dba5d8bebbf` on
`exp/e09-continuous-routing-repair-v1`.

## Scope and implementation

E09-R1 retained the complete radius-0.14 m analytical hemisphere, placements T30/T27/T33,
budgets k=1/2, geometry semantic hash
`01050368f57eca9a904a04106dc705aec89cab65248c508462ad232328976047`, and the established
coverage and robot thresholds. It did not add geometry families, alter starts, relax thresholds,
run hardware, train Flow Matching, or optimize the prospective bound.

The corrected connection API stores q, curve parameter, declared position and axis, and activity
at the same samples. Endpoint-targeted tails retain the terminal interval. Edge admission checks
stored endpoint q, endpoint activity, recomputed endpoint membership, task5 residuals, sigma5,
joint limits, and modeled collisions. The builder evaluated all eight scheduled seeds at every
port and retained up to four effective q1:5 states while preserving full unwrapped q. F was
corrected to preserve the repeat resource in Pareto dominance and to update progress across OFF
relocations.

## Tests

The focused suite passed 45/45 tests. This includes synchronized tail sizes, nonuniform
parameters, reverse traces, endpoint retention, shape mismatch rejection, incompatible endpoint
rejection, task5 propagation, F repeat/Jq Pareto behavior, OFF progress, cyclic reachability and
repeat-bound checks, and indexed/dense episode equivalence.

The literal repository suite reported 196 passed, 1 skipped, and 10 failed. The ten failures are
individually preserved in `tests.txt`; they require two absent historical E06 inputs
(`saddle_T17.npz` and `nuc_robot_skeleton_coupling_v1/config.json`). The full suite is therefore
dependency-limited and is not recorded as passing.

Archived rejected E09 endpoint-state graph arrays were not published, so the optional paired
old/new replay of up to twelve rejected connectors was NOT_RUN. The corrected global experiment
did not depend on a positive historical replay.

## Repaired graphs

| scene | states | edges | multi-state ports | accepted cross-port ON | root-reachable cross ON | OFF edges | build s |
|---|---:|---:|---:|---:|---:|---:|---:|
| T30 | 865 | 5,870 | 237 | 1,529 | 1,118 | 3,511 | 716.46 |
| T27 | 897 | 6,091 | 238 | 1,573 | 1,120 | 3,732 | 723.08 |
| T33 | 724 | 6,056 | 238 | 1,622 | 1,120 | 3,491 | 703.09 |

This repairs E09's zero-cross-ON representation. All scheduled seeds were recorded for all 238
ports in every placement. The three graph NPZ files total about 227 MB and remain local with
SHA256 hashes and reconstruction commands; compact graph summaries, every edge attempt, and the
selected lossless witnesses are published.

## Six tasks and four methods

Cells show the independently refined result, followed by search termination.

| scene | k | F | G0 | G1 | S |
|---|---:|---|---|---|---|
| T30 | 1 | no plan / exhausted | no plan / resident-label limit | coverage failed / 300 s | no plan / resident-label limit |
| T30 | 2 | no plan / exhausted | no plan / resident-label limit | no plan / 300 s | no plan / resident-label limit |
| T27 | 1 | accepted / exhausted | no plan / resident-label limit | no plan / 300 s | no plan / resident-label limit |
| T27 | 2 | accepted / exhausted | no plan / resident-label limit | no plan / 300 s | no plan / resident-label limit |
| T33 | 1 | no plan / exhausted | no plan / resident-label limit | no plan / 300 s | no plan / resident-label limit |
| T33 | 2 | no plan / exhausted | no plan / resident-label limit | no plan / 300 s | no plan / resident-label limit |

The T27 k=1/k=2 rows share one content-hashed 77-edge spiral template-prefix witness with one ON
segment. It has Jq 90.011360 (ON 89.946565, OFF 0, entry 0.064795), minimum sigma5 0.109752,
maximum position error 3.91e-5 m, maximum axis error 0.0214 degrees, and passes modeled collision
and joint checks. At T1/Q4a, E_miss=0.018332 and E_rep=0.019571. Q3/Q4, temporal, and phase
changes are all within 0.002. The label is
`accepted_under_E09_R1_refined_sampled_checks`, not continuous certification.

T30/k=1 G1 returned an 88-edge cross-family route containing nine cross-port ON connections.
Jq is 95.837506 and all sampled robot checks pass. The graph Q2 scores meet the contract, but
T0/Q3 E_miss=0.020274 exceeds 0.02 and the Q3/Q4 change is within 0.002. It is therefore
`coverage_contract_failed_under_refined_checks`; it is not an accepted global route.

Every returned route used rank-0 endpoint states. Although the graph retained multiple effective
states, G0 and S both stopped at the resident-label safeguard without plans, so no finite-graph
benefit from state multiplicity was established.

## Prospective repeat bound

Across six G1 cells, 7,772 prospective-repeat prunes were recorded. Bound computation consumed
1,747.7 of 1,800.7 search seconds (97.1%). G1 found the T30/k=1 graph route at 143.1 seconds,
whereas G0 reached its resident-label guard without a route, but the G1 route failed refined
coverage and the arms had different budget termination mechanisms. This is not evidence of net
computational saving. G1 remains slower as an implementation in this experiment.

A natural finite positive obstruction was observed: past repeat 0.099946 plus future lower bound
0.013447 gives 0.113392 while ordinary reachability remains available. This supports informative
pruning within the frozen graph, not global physical infeasibility.

## Retrospective and interpretation

The three accessible unique parent-E09 q witnesses were checked on the new finite schedule without
replanning. Their motion checks pass, but all remain `numerically_unresolved` because Q4-to-Q4a
repeat changes exceed 0.002. Historical E09 statuses are unchanged.

Q1 has a placement-conditional answer: T27 has one accepted complete fixed-route witness at both
budgets, but no accepted globally recombined witness was produced. Q2 is unsupported: G0 did not
improve on F. Q3 was not meaningfully exercised by returned routes. Q4 is negative for net time:
the bound pruned but dominated runtime.

Most global cells are budget-limited and do not prove finite-graph infeasibility. Absent sampled
connections do not prove physical infeasibility. Collision claims cover only the pinned MuJoCo
model and exclude unmodeled workpiece/tool-body geometry, environment, and cables. No RSS-level,
continuous-time, hardware-safety, or planner-novelty claim follows from this repair.

Machine-readable evidence and reproduction commands are in
`results/e09_continuous_routing_repair_v1/` in the Code repository.
