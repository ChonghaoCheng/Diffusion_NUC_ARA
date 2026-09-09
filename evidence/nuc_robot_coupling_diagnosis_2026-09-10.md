# E06-D NUC skeleton coupling mechanism diagnosis (2026-09-10)

## Provenance

- Code branch: `exp/nuc-robot-coupling-diagnosis-v1`
- Code commit: `1bfa2d3bfaa11df962d6764d14263fa54218402b`
- Parent E06 code commit: `78876d52d5313c0e99978700ff3cb7de02e2d0a5`
- ARA branch: `exp/nuc-robot-coupling-diagnosis-v1`
- Parent E06 ARA commit: `e21b2abdde70bc639340593e9829132d038bcace`
- Local result directory: `results/nuc_robot_coupling_diagnosis_v1/`
- ARA result snapshot: `evidence/runs/nuc_robot_coupling_diagnosis_v1/`
- Tests: `122 passed`, with 14 Matplotlib/pyparsing dependency deprecation warnings.

The frozen E06 contract was reused without recalibration: `L_c=0.1 m`,
`sigma_safe=0.0723741717`, `delta_NUC=0.0297927413`, maximum dense q step `0.05 rad`, `W=I`,
the 5D position/tool-axis task, the same site and tool-axis convention, the same temporal
geodesic-footprint evaluator, and float64 continuation witnesses.

## Replay agreement

The 80 archived successful E06 witnesses were post-processed without relifting. Reconstructed
mesh-geodesic transition targets matched their archived ordered desired positions and axes at
`1e-12` tolerance. Transition-attributed joint length reproduced archived `L_q` with maximum
absolute errors `3.55e-15` on saddle and `1.42e-14` on hemisphere. Default-budget replay reproduced
the archived `lift_found=False` result for all 40 hemisphere P_mid/P_hard candidates.

## D1: actual skeleton variability

All twenty paths per surface had unique canonical subfacet sequences; this is not merely a label
difference. Nevertheless, most pairwise transition/tree structure was shared.

| Surface | Median directed Jaccard | Median tree-edge Jaccard | Median sequence distance | Median ordered path distance | Median tangent disagreement |
|---|---:|---:|---:|---:|---:|
| saddle | 0.7409 | 0.7975 | 0.3380 | 0.03879 m | 0.7517 rad |
| hemisphere | 0.8617 | 0.8938 | 0.1543 | 0.02211 m | 0.5965 rad |

Both libraries had a median `34.0%` within-parent and `66.0%` cross-parent transition fraction.
The skeletons differ in ordered routing and some spanning-tree edges, but predominantly reuse the
same local facet/subfacet motion vocabulary. Sequence difference alone is not treated as a
topological distinction.

## D2: actual witness-cost decomposition

| Surface | Median eta_variable | Median eta_cross | C(0.50) | C(0.75) | C(0.90) | C(1.00) |
|---|---:|---:|---:|---:|---:|---:|
| saddle | 0.4554 | 0.7851 | 0.9123 | 0.7646 | 0.6726 | 0.5446 |
| hemisphere | 0.7440 | 0.7725 | 0.9795 | 0.9375 | 0.7781 | 0.2560 |

The strict common/variable split does not explain E06 by a small variable fraction: variable edges
carry `39.2-46.3%` of scene-median saddle cost and `74.4%` on hemisphere/P_easy. Instead, edges
appearing in at least half the library carry most witness cost. Candidate paths reorder and exchange
members of this frequent set, while total costs cancel. For example, saddle/P_easy variable-cost
spread is `15.53%`, but total `L_q` spread is only `0.97%`. Thus the prompt's proposed
`small eta_var` mechanism is empirically rejected.

## D3: normalized 5D local metric

| Scene | L_G spread | L_q spread | Candidate Pearson | Candidate Spearman | Local Pearson | Local Spearman | Median L_q/L_G |
|---|---:|---:|---:|---:|---:|---:|---:|
| saddle/P_easy | 1.565% | 0.969% | 0.8984 | 0.9835 | 0.9851 | 0.9762 | 1.0864 |
| saddle/P_mid | 0.826% | 0.754% | 0.9998 | 1.0000 | 0.5688 | 0.9824 | 1.0451 |
| saddle/P_hard | 0.769% | 0.691% | 0.9733 | 0.9338 | 0.9946 | 0.9916 | 1.0420 |
| hemisphere/P_easy | 0.940% | 1.031% | 0.9963 | 0.9877 | 0.9866 | 0.9836 | 1.0852 |

This is Outcome 2: the metric strongly predicts candidate ranking and usually local increments,
and correctly underestimates witness length by roughly `4.2-8.6%`, as expected for a redundant
minimum-task-motion metric. The saddle/P_mid local Pearson reduction with Spearman `0.9824`
indicates nonlinear/outlier sensitivity rather than loss of rank information. Both `L_G` and
`L_q` spreads remain small, so the tested skeleton family exposes little exploitable
robot-geometric freedom. This evidence narrows the Riemannian metric to a diagnostic; it does not
authorize deformation.

## D4: hemisphere continuation localization

| Placement | Default failures | Dominant category | Median failure progress | Strong cases | Recovered |
|---|---:|---|---:|---:|---:|
| P_mid | 20 | transition_graph_empty | 0.3467 | 4 | 2 |
| P_hard | 20 | transition_graph_empty | 0.0913 | 4 | 0 |

At default budget, 18/20 P_mid failures occurred on canonical transition `(285,288)`; 19/20
P_hard failures occurred on `(258,259)`. Safe endpoint candidates were present in every P_mid
failure and 19/20 P_hard failures, but no propagated transition survived. These repeated surface
regions are numerical geometric/kinematic bottlenecks, not evidence of topological disconnection.

The subset was frozen before strong replay. P_mid used S15 (geometry), S19 (earliest), S08
(lower-median), and S17 (latest); P_hard used S15, S17, S13, and S16. Strong search used 32 random
restarts, 24 candidates/active branches, 15 orientation-cone samples, and 15 task-edge samples.
It increased safe catalog candidates by `3.97x` on P_mid and `3.74x` on P_hard. P_mid S17/S19 were
recovered and passed the unchanged strict kinematics checker; S08/S15 were delayed from progress
`0.3467` to `0.5077`, where both failed on `(297,300)`. All four P_hard cases failed at their
original progress, including the one pose-candidate-empty case. Candidate continuation runtime
grew by approximately `7.9-16.0x` for P_mid and `2.8-3.1x` for P_hard.

The hemisphere diagnosis is mixed Outcome 4/5: default P_mid failures contain a material finite
search component, while tested P_hard failures persist as localized continuation/pose bottlenecks
under the stronger numerical representation. The result neither proves disconnection nor supports
treating all forty E06 failures as equivalent.

## Final interpretation

Close legal NUC expansion-order selection as a useful optimization degree of freedom. Continue the
normalized 5D Riemannian line only as a diagnostic, not as an optimizer or deformation method.
Keep E07 blocked. The next experiment to register should qualify hemisphere continuation search
and representation across frozen placements, with particular attention to the repeated P_hard
transition bottleneck; only after that should the project decide whether a new path-design degree
of freedom is scientifically warranted.

## Commands

Exact commands are archived in
`evidence/runs/nuc_robot_coupling_diagnosis_v1/reproduction_commands.txt`. The sequence runs D1,
D2, D3, default D4, freezes the selected IDs, runs strong D4, summarizes, and executes `pytest -q`.

## Limitations and non-claims

This experiment does not establish global C-space connectivity or disconnection, global path
optimality, benefit of surface-joint deformation, benefit of Flow Matching/diffusion, physical
execution performance, or generality outside the tested surfaces and placements. E06 remains the
registered NO-GO, E07 was not run, and collision evidence remains limited to the loaded standalone
UR5e scene.
