# E08 global completion bound v1 — frozen plan

Registered: 2026-09-16 (Australia/Sydney), before E08 audit or planner results.

This experiment separates a one-time IK implementation diagnosis from the planning test. The
diagnostic compares the unchanged six-row DLS backend with a five-row backend that removes tool
roll from the residual and Jacobian, while keeping damping, gains, update clipping, stopping
rules, seeds, and call budgets fixed. The planning test compares one history-aware label search
with its completion-bound switch disabled (S0) or enabled (S1) on byte-identical frozen graphs.

The physical task is the full original saddle or hemisphere at each of the six registered
placements T17/T21/T10 and T30/T27/T33. The tool axis follows the exact nominal analytical
normal and tool roll is free. The footprint radius is 0.008 m. The normalized 5D task uses
`Lc=0.1 m`, `W=I`, and fixed `sigma_safe=0.07237417172157597`. Sampled admission requires
maximum position error no larger than 0.0001 m, maximum axis error no larger than 0.1 degrees,
`E_miss<=0.02`, and `E_rep<=0.10`. IK stopping tolerances are 0.00005 m and 0.05 degrees.
The main dense joint step is at most 0.025 rad; independent verification uses 0.0125 rad.

The ON-segment budgets are exactly 1 and 2. The initial state is ON. Leaving and returning adds
one ON segment, while lifting after the finished task does not. All ON and OFF joint motion is
charged to `J_q`. Feasible plans are ordered lexicographically by `(ON segments - 1, J_q)`.

The four candidate families per surface are fixed in
`configs/global_completion_bound_v1.json`. They are generated directly on the analytical
surface using the existing pattern implementation with overlap 1.0. No family, placement,
seed, or tolerance will be added after qualification or planner results are observed. Failed
scenes remain in the qualification table. Main comparisons run only for scenes with a sampled,
strict, complete reference using at most two ON segments.

Each graph stores actual float64 joint witnesses, analytical surface targets and normals,
ordered ON/OFF membership, endpoint activity, sparse episode counts, footprint union, joint
length, and within-edge OFF-to-ON count. Nodes are never merged by phi or branch label alone.
The graph is frozen and hashed before either arm. Bounds only certify the frozen finite graph;
failed numerical connection attempts do not prove physical non-existence.

S0 uses coverage history, repeat-budget pruning, ON-segment pruning, Pareto dominance, and an
ordinary reachable-area relaxation. S1 changes only one predicate: it additionally prunes when
`R + LB_future > 0.10`, where `LB_future` is the area-weighted completion quantile of target
distances under prefix-frozen optimistic repeat edge costs. Target distances are never summed.
Both arms share graph hash, outgoing order, queue order, validator, empty incumbent, limits,
and checkpoints. A bound-validation failure or any oracle-observed false prune stops the main
comparison.

Coverage-summary correctness is exact only for the frozen membership arrays. Final witnesses
are rechecked from actual FK at <=1 mm trajectory spacing and again at <=0.5 mm with one level
denser surface quadrature. No result is described as a continuous interval certificate.

The MuJoCo model is the actual XML at the path recorded in the config. Audit records the TCP,
tool axis, q6 axis, limits, collision-pair scope, site offset, XML includes and assets, and their
SHA256 hashes. A frozen manifest created after implementation and before the main comparison
records all input hashes, the complete uncommitted code diff hash, environment versions, graph
hashes, and thresholds. It is immutable: later stages create separate checkpoint/result files.

Historical E06 evidence keeps its original contract. E06-R is evidence of local execution-cost
anisotropy only. E06-R2 and E06-J do not support a material benefit for their tested local
parameterizations and do not decide this global question. The hemisphere canonical changes by
about 6.179 mm under its historical representation conversion, and its 72 numerical failures
are not physical infeasibility certificates. Coverage equivalence does not qualify either old
canonical as a high-quality E08 teacher. Task roll, 5D kinematics, surface kinematics, and NUC
counting are treated as implementation ingredients rather than novelty claims.

Possible conclusions are limited to: useful; informative but too expensive; correct with no
additional information; or failed premise/implementation. The run will not tune toward a
positive outcome, start flow matching, rerun E06 local deformation, or claim an RSS decision.
