# E12 evidence: graph-free global generation and candidate-only execution

Date: 2026-09-18 (Australia/Sydney)

Code result commit: `2cff766da6a58468afbdb7471e656930a2a8850d`

Branch: `exp/e12-graph-free-global-generation-v1`

## Scope and execution

E12 encoded complete-hemisphere routes as source-frame `SCAN`/`VIA`/`END` programs and realized
each candidate continuously from its checked `q0` with task5. Runtime generation and lifting did
not read test robot graphs, per-port q, graph adjacency, future teacher q, or teacher branch
labels. The physical contract and T0/Q3, T0/Q4, T1/Q4, and Q4a refined sampled checks were
inherited unchanged.

The completed protocol used the original frozen 20 TRAIN, 4 VALIDATION, and 8 SEALED_TEST poses
and 5,000 updates for the shared categorical decoder, REG, and FM. A proposed expansion was
stopped before any added graph or label completed; it did not enter training or results. All
registered downstream stages ran, including P_lazy and post-freeze P_graph references.

## Correctness and interface gate

The focused E12 suite passed 26 tests with one warning. The literal repository suite reported
245 passed, one skipped, and ten failed. All ten failures were `FileNotFoundError` from the absent
historical `saddle_T17.npz` or `nuc_robot_skeleton_coupling_v1/config.json` E06 fixtures. No
fixture was fabricated, so the suite is dependency-limited rather than fully passing.

Fifteen accepted E11 witness hashes were indexed. Twelve were eligible single-ON programs and all
12/12 passed geometry encoding plus independent q0-only re-lift under the refined sampled
contract. Three two-ON witnesses remained indexed outside the primary k=1 scope. This is an
oracle interface diagnostic; it is not autonomous generated-plan success.

## Corpus and training

Teacher collection attempted all 24 TRAIN/VALIDATION poses. It qualified 27 labels from 14 tasks,
including 13 TRAIN tasks and one VALIDATION task, and retained failures in the denominator.
Offline teacher graph construction consumed 18,282.9 aggregate CPU-seconds. The shared categorical
decoder and matched REG/FM heads used one training seed and 5,000 updates each. Training ran on
two recorded RTX A5500 devices when concurrent; the critical path was 34.5 seconds and summed GPU
time was 96.8 seconds. Final parameter counts were 807,050 for the categorical model and 823,170
for each continuous head.

On the four validation poses, RETRIEVE passed two, while REG and FM passed none. These outcomes
did not alter the frozen sealed-test candidate policy.

## Sealed test outcomes

| task | RETRIEVE | REG | FM | P_lazy | P_graph |
|---|---|---|---|---|---|
| TE00 | no accepted candidate | no accepted candidate | no accepted candidate | no accepted candidate | no accepted candidate |
| TE01 | accepted, Jq 84.208 | accepted, Jq 83.893 | accepted, Jq 83.538 | accepted, Jq 83.547 | accepted, Jq 83.548 |
| TE02 | accepted, Jq 90.148 | no accepted candidate | no accepted candidate | accepted, Jq 93.503 | accepted, Jq 93.503 |
| TE03 | accepted, Jq 91.473 | no accepted candidate | no accepted candidate | accepted, Jq 91.473 | accepted, Jq 91.473 |
| TE04 | accepted, Jq 90.487 | no accepted candidate | no accepted candidate | no accepted candidate | no accepted candidate |
| TE05 | accepted, Jq 88.182 | no accepted candidate | no accepted candidate | no accepted candidate | no accepted candidate |
| TE06 | no accepted candidate | no accepted candidate | no accepted candidate | no accepted candidate | no accepted candidate |
| TE07 | accepted, Jq 88.748 | no accepted candidate | no accepted candidate | accepted, Jq 88.124 | accepted, Jq 88.124 |

Accepted task counts were RETRIEVE 6/8, REG 1/8, FM 1/8, P_lazy 4/8, and P_graph 4/8. These are
paired method rows over eight tasks, not independent trajectory samples. Every accepted route
passed achieved-FK task residual, sigma5, joint limits, modeled collisions, activity, exact
same-sample episode composition, and all required refined coverage checks. The four selected
P_graph witnesses were replayed independently after result locking and all four again passed.
The largest accepted resolution change was below 0.00140.

One P_lazy TE04 candidate remained numerically unresolved with a 0.00298 resolution change. REG
and FM each produced a TE07 candidate with complete sampled coverage but repeat error above 1.10
and unstable resolution. These candidates were not accepted. Fixed candidate-slot accounting also
recorded 17 RETRIEVE and 55 each REG/FM IK-call-limit failures; missing or unattempted slots remain
separate from failures.

## Cost and interpretation

P_graph built eight ready graphs with 7,693,304 IK calls. Graph construction cost 5,827.3 seconds
and full cold cells cost 6,589.9 seconds, or 823.7 seconds per task. P_lazy query work cost 597.0
seconds and candidate evaluation cost 349.4 seconds; including root checks, its eight cold cells
cost 947.3 seconds, or 118.4 seconds per task. P_lazy matched P_graph's four-task accepted set at
about 6.96 times less measured cold time in this implementation.

RETRIEVE was the strongest sealed method by accepted-task count. FM demonstrated one valid
end-to-end generated route but matched deterministic REG at 1/8 and did not show incremental value
over retrieval or P_lazy. The graph-free representation and q0-only realization interface are
supported within this finite route vocabulary, while stochastic flow-based prediction is not
supported as an incremental advantage by this one-seed pilot. Avoiding complete graph
preprocessing explains a concrete cold-start saving without crediting FM.

This is same-hemisphere pose transfer around three anchors, not shape generalization, exogenous
coverage-history conditioning, RFM evidence, hardware validation, continuous-time certification,
or a faithful external published-planner comparison. Collision claims cover only the pinned
MuJoCo model, and numerical/budget failures are not physical-infeasibility proofs.

Primary machine-readable evidence is in Code
`results/e12_graph_free_global_generation_v1/`, including `report.md`,
`sealed_test_main_results.csv`, `accepted_route_metrics.csv`, `cold_cost_breakdown.csv`,
`candidate_failure_taxonomy.csv`, `p_graph_validation_details.json`, `tests.txt`, compact selected
witnesses, and whole-surface figures.
