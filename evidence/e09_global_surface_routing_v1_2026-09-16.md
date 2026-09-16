# E09: full-surface robot-aware coverage routing

Date: 2026-09-16  
Status: COMPLETED / NUMERICALLY UNRESOLVED CAPABILITY; RECOMBINATION LIMITED; BOUND INFORMATIVE BUT SLOWER  
Code result: `a401dbc7e3bdadea7af2d9420bc090b64b3dfb09` on `exp/e09-global-surface-routing-v1`

## Contract and implementation

E09 used the complete analytical hemisphere at frozen placements T30/P_low, T27/P_mid, and
T33/P_high, with ON-segment budgets one and two. Its 238-port immutable geometry bank contains
470 directed source macro-arcs and 1,120 deterministic nearby connector proposals from the three
E08-R1 accepted hemisphere paths. The geometry hash is
`01050368f57eca9a904a04106dc705aec89cab65248c508462ad232328976047`.

Every nested E09 solve now explicitly uses the task5 backend. The actual-execution validator
densifies stored joint witnesses, evaluates FK and activity, applies exact spherical footprint
episodes to the achieved ordered centerline, and keeps task, transition, coverage, and overall
status separate. The cyclic unconstrained reachability diagnostic now visits nodes rather than an
unbounded sequence of segment-count states. G0 and G1 share the finite-budget layered reachable
area test; only G1 adds the prospective repeat bound. Focused regressions passed 27/27.

The literal repository suite produced 178 passes, one skip, and ten failures. All ten failures
load one of two historical gitignored E06 fixtures absent from the isolated worktree:
`saddle_T17.npz` or `nuc_robot_skeleton_coupling_v1/config.json`. No E09, history-search,
completion-bound, or ordered-trace regression failed.

## Frozen graphs

| Placement | states | edges | cross-port ON | OFF reconfigurations | IK calls | build s | status |
|---|---:|---:|---:|---:|---:|---:|---|
| T30/P_low | 241 | 1,429 | 0 | 964 | 103,607 | 119.13 | recombination_limited |
| T27/P_mid | 238 | 1,423 | 0 | 952 | 102,646 | 117.11 | recombination_limited |
| T33/P_high | 242 | 1,431 | 0 | 968 | 103,732 | 118.07 | recombination_limited |

All original source fragments and explicit retreat/move/return OFF edges were retained. None of
the frozen cross-port proposals yielded an accepted task-preserving ON robot edge. Endpoint
membership mismatches were rejected rather than overwritten. This is a limitation of the sampled
graph construction, not evidence of physical non-connectivity.

## Six-task result

All 18 F/G0/G1 cells ended by finite queue exhaustion and G0/G1 agreed on graph feasibility and
objective. The metrics below are independently recomputed on Q2; every returned plan is marked
`numerically_unresolved` because the Q1/Q2 absolute change exceeded 0.002.

| Task | F | G0 | G1 |
|---|---|---|---|
| T30, k=1 | no graph plan | no graph plan | no graph plan |
| T30, k=2 | no graph plan | unresolved: miss .011565, repeat .081400, Jq 99.981 | same plan |
| T27, k=1 | unresolved: miss .016922, repeat .026743, Jq 90.068 | same plan | same plan |
| T27, k=2 | unresolved: same one-segment plan | same plan | same plan |
| T33, k=1 | no graph plan | no graph plan | no graph plan |
| T33, k=2 | no graph plan | unresolved: miss .011565, repeat .081420, Jq 100.815 | same plan |

T27 uses a 77-arc prefix of raster-u phase 0 with one ON segment. T30 and T33 use 74 forward
raster-u arcs, four reverse arcs, one verified OFF relocation from port 70 to port 79, then four
more reverse arcs. Their Jq ON/OFF decompositions are 99.604/0.377 and 100.450/0.365. These are
within-family direction-reversal and reconfiguration routes, not cross-family ON recombination.

All ten graph-plan rows passed denser sampled robot checks: maximum position error was at most
`6.21e-5 m`, maximum axis error at most `0.00631 deg`, minimum sigma5 at least `0.08325`, minimum
normalized joint margin at least `0.08088`, and no modeled collision was observed. The final
Q1/Q2 differences were 0.002628 for T27 and about 0.00797--0.00799 for T30/T33, so zero executions
were accepted under the frozen numerical-stability rule. No extra refinement was run.

## Questions

**Q1:** No independently accepted full-surface execution was established. The finite graphs
contain ten graph-feasible, robot-check-passing plan rows, but all remain numerically unresolved.

**Q2:** T30/T33 k=2 show a bounded routing signal: exhausted G0 found a reconfiguration plus
direction-reversal route where exhausted F found none. It is not a validated execution advantage,
and the absence of cross-port ON edges makes the intended global recombination capability limited.
T27 gave the same fixed-template prefix to all methods.

**Q3:** The bound was informative but too expensive. Across informative cells G1 made 1,627 bound
calls, pruned 255 prefixes by a finite prospective repeat bound, and expanded fewer labels, while
spending 98.68 seconds in the bound and increasing total search time. A natural T30/k2 prefix had
past repeat 0.093340 and future lower bound 0.015870 while ordinary segment-budget reachability
still passed, proving total repeat at least 0.109209 on that frozen graph.

## Boundaries

The result concerns a sampled finite graph and sampled trajectory validation, not continuous-time
certification or physical infeasibility. Collision checks cover only contacts represented by the
pinned MuJoCo model; unmodeled workpiece, tool body, environment, and cable collisions remain
outside the claim. Jq is not energy or execution time. F is an internal fixed-library baseline.
No saddle experiment, hardware run, FM training, planner-superiority claim, or RSS novelty claim
was made.

The complete report, result tables, selected float64 witnesses, plots, manifests, and reproduction
commands are in Code
`results/e09_global_surface_routing_v1/`. The three reconstructible graph NPZ intermediates remain
local; the published manifest records both their semantic hashes and file SHA256 values.
