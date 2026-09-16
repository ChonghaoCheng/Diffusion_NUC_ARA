# E10 evidence: structured anytime global coverage routing

Date: 2026-09-17 (Australia/Sydney)

Code result commit: `defd63315996bceb113a06a57a295b8eaec9a635`

Branch: `exp/e10-structured-anytime-routing-v1`

## Scope and frozen inputs

E10 reused the three E09-R1 multi-state hemisphere graphs, including their roots, robot states,
edge witnesses, activity, endpoint membership, and geometry hash
`01050368f57eca9a904a04106dc705aec89cab65248c508462ad232328976047`. The graph archives were
loaded from the retained E09-R1 worktree and matched their published SHA256 values. No IK,
connector, port, path-family, placement, graph-construction, G1, single-state, FM, saddle, or
hardware campaign ran.

The measured methods were F, A, and B. F is the corrected fixed-order search. A is a bounded
atomic-edge global search initialized with exact replays of a deterministic F-prefix archive. B
uses the same search and initialization while also offering exact compositions of consecutive
source edges. A/B retain an independently accepted F result and separate Q2 graph goals, online
Q3 screens, frozen finalists, and refined accepted incumbents.

## Correctness and dependency status

The focused command passed all 55 tests. These include exact atomic/run episode-summary
composition, prefix replay, fallback retention, search after failed initialization, two-segment
bucket service, live-frontier accounting, repeat/Jq Pareto behavior, continued search after a
failed screen, and atomic versus grouped agreement on untruncated tiny graphs.

The literal repository suite returned 209 passed, one skipped, and ten failed. Each failure was a
`FileNotFoundError` attributable to either the absent E06 saddle witness under
`results/riemannian_anisotropy_utility_v1/` or the absent
`results/nuc_robot_skeleton_coupling_v1/config.json`. It is recorded as dependency-limited, not
fully passing. All 17 selected-plan same-sample checks produced exact pointwise visit-count
agreement between composed edge summaries and whole-trace evaluation.

## Six-task result

| placement | k | F | A | B |
|---|---:|---|---|---|
| T30 | 1 | no graph goal; exhausted | accepted new global plan | accepted same global plan |
| T30 | 2 | no graph goal; exhausted | accepted new global plan | accepted same global plan |
| T27 | 1 | accepted F fallback | retained F | retained F |
| T27 | 2 | accepted F fallback | retained F | retained F |
| T33 | 1 | no graph goal; exhausted | accepted new global plan | accepted same global plan |
| T33 | 2 | no graph goal; exhausted | accepted new global plan, Jq 96.394518 | accepted new global plan, Jq 96.153376 |

All accepted plans use one ON segment. Thus the six task rows contain three best unique physical
trajectories: a T30 cross-family ON recombination, the inherited T27 spiral prefix, and a T33
cross-family ON recombination. T30 and T33 are genuine global routes in the frozen graph: their
selected witnesses use three accepted cross-port ON connections and no OFF relocation. F had no
graph goal for either placement.

| placement | role | Jq (ON/OFF/entry) | T1/Q4a miss | T1/Q4a repeat | min sigma5 | max position error | max axis error |
|---|---|---|---:|---:|---:|---:|---:|
| T30 | global recombination | 96.949149 (96.876117/0/0.073032) | 0.015433 | 0.046278 | 0.082973 | 5.12e-05 m | 0.0188 deg |
| T27 | F fallback | 90.011360 (89.946565/0/0.064795) | 0.018332 | 0.019571 | 0.109752 | 3.91e-05 m | 0.0214 deg |
| T33 | global recombination | 96.153376 (96.086162/0/0.067213) | 0.018369 | 0.037363 | 0.083249 | 6.15e-05 m | 0.0307 deg |

Each selected witness passed the inherited T0/Q3, T0/Q4, T1/Q4, and T1/Q4a miss/repeat limits and
the 0.002 stability rule, together with sampled task residual, singularity, joint-limit,
modeled-collision, activity, and joint-cost checks. These are refined sampled acceptances, not
continuous-time or hardware certificates.

## Search and mechanism result

Every A/B cell stopped at the explicit 30,000 retained ancestry-plus-Pareto-record safeguard.
This differs from E09-R1's cumulative-admission shutdown; live OPEN storage remained controlled by
the fixed bucket quotas. A expanded 219,154 labels over six tasks; B expanded 231,225 and evaluated
859,891 source-run actions. B was slower in every paired cell. Its only objective difference was
T33/k=2, where it retained the lower-Jq route that A had already retained in T33/k=1. Therefore
the run supports the global bounded-search mechanism, while a general source-run timing benefit is
not established.

Seventeen online Q3 screens ran and all passed. The implementation correctly prevented an
unvalidated Q2 candidate from becoming an incumbent, but this run did not naturally exercise
resumption after an online Q3 rejection. The prospective-repeat bound received zero calls.

Core A/B times, including F search and online screens, ranged from 72.08 to 240.12 seconds. Frozen
graph loading measured 3.12--3.53 seconds per scene and selected-witness refined validation took
approximately 63.9--67.4 seconds uncached. The inherited graph-construction costs remain
703--723 seconds per placement and are reported separately rather than treated as zero.

## Interpretation boundary

E10 establishes an integrated, finite-graph, refined-sampled capability on three development
placements. It does not establish global optimality, continuous coverage certification,
generalization to new surfaces or placements, an advantage for source-run proposals, or an
RSS-level contribution. The collision statement is limited to contacts modeled by the pinned
MuJoCo XML; unmodeled workpiece, tool-body extent, environment, cables, dynamics, force, and
controller behavior remain outside scope.

Primary machine-readable evidence is under Code
`results/e10_structured_anytime_routing_v1/`, especially `global_results.csv`,
`final_validation.csv`, `validation_resolution.csv`, `screening_results.csv`,
`route_classification.csv`, the compact float64 witnesses, and whole-surface figures.
