# Fixed-placement UR5e workspace oracle and real IK components

## Workspace proposal-vocabulary control

The fixed-placement all-candidate run evaluated 1,339 hard-feasible workspace
teacher candidates over 80 difficult cylinder/hemisphere instances. The archived
geometry baseline succeeded on 2.5%, while best-of-all workspace candidates
succeeded on 3.75% and produced a 3.75-point paired rescue. A stronger 2.5 mm,
32-restart, beam-24 rerun confirmed two of the three apparent rescues.

Sources:

- `results/ur5e_workspace_modes_periodic_80_all_v2/summary.json`
- `results/ur5e_workspace_modes_periodic_80_all_v2/bootstrap.json`
- `results/ur5e_workspace_modes_rescue_verify_v1/summary.json`

## Coarse real IK-component graphs

Forty fixed-placement cylinder/hemisphere instances used an 8x8 surface grid.
The 10-degree enumeration explicitly retained the complete 3-degree candidate
budget before adding outer-cone candidates. Mean reachable fraction changed from
96.84% to 98.12%, while mean largest-component fraction changed from 60.94% to
85.16%. Cylinder changed from 36.33% to 74.22%; hemisphere changed from 85.55%
to 96.09%.

Sources:

- `results/ur5e_surface_ik_graph_40_tau3/summary.json`
- `results/ur5e_surface_ik_graph_40_nested_tau10/summary.json`

## Exact segment-budget frontier

An exact binary MILP selected numerical IK components under segment budgets.
Mean minimum components needed to cover every reachable grid node fell from 7.45
at 3 degrees to 2.175 at nested 10 degrees. Mean exact reachable-node coverage
at k=1 changed from 0.628149 to 0.866749; at k=2 from 0.732474 to 0.974556; and
at k=4 from 0.858563 to 0.999173. All 40 paired frontiers were monotone.

Sources:

- `results/ur5e_component_frontier_40_v1/summary.json`
- `results/ur5e_component_frontier_40_v1/frontier.csv`
- `results/ur5e_component_frontier_40_v1/component_frontier.png`

## GPU deterministic-prior falsification

After exact-target caching and batch enlargement, the candidate MPNN reached about
82% observed GPU utilization. On held-out instances its top-k components achieved
0.898209 mean coverage relative to exact. Component-size ordering achieved 0.945804.
Greedy marginal coverage achieved 1.0 and an exact hit on every held-out budgeted
task. Learned component ordering is therefore not justified for this coarse proxy.

Sources:

- `results/ur5e_component_gnn_40_seed0_fast/summary.json`
- `results/ur5e_component_gnn_40_seed0_fast/evaluation/summary.json`
- `results/ur5e_component_frontier_40_v1/diagnosis.md`

## Verification

The complete test suite passed 76 tests. Numerical components remain a sampled
connectivity approximation, not a topological certificate. Workpiece collision is
still absent from this gate.
