# E11 evidence: mechanism attribution and frozen-policy placement transfer

Date: 2026-09-17 (Australia/Sydney)

Code result commit: `14dcaaab6e0fd8d2514af1d2d5eb4bb7eb7c4fb9`

Branch: `exp/e11-mechanism-placement-transfer-v1`

## Scope and frozen protocol

E11 retained the complete analytical hemisphere, E09 geometry bank, E10 atomic method A,
physical thresholds, exact history state, screening policy, and refined sampled validator. DEV
reused the exact T30/T27/T33 graphs at k=1. TRANSFER used the six prospectively frozen transforms
H00--H05 and built one checked multi-state graph per placement before comparing F, P, and A at
k=1/2. No source-run B, prospective-repeat G1, FM, hardware, non-spherical geometry, external
planner reimplementation, parameter sweep, target crop, or threshold relaxation ran.

F is the internal fixed-order library baseline. A is the frozen bounded E10 atomic-edge search,
initialized by exact replays of F's deterministic prefix archive. A_root is A without nonempty
prefixes and is DEV-only. P gives each root/prefix rollout one greedy continuation, considers every
atomic outgoing edge, and uses A's hard resources, local rank, candidate policy, and validator.

## Correctness and dependency status

The focused E11 suite passed all 65 tests. It covered event-log noninterference, exact prefix
replay, the A/A_root isolation, unrestricted atomic choices in P, one-continuation semantics,
resource-aware loop termination, common fallback/candidate validation, transform construction,
blocked-row retention, and screen-event reconciliation.

The literal repository suite returned 219 passed, one skipped, and ten failed. Every failure was a
`FileNotFoundError` caused by one of two absent historical E06 fixtures:
`results/riemannian_anisotropy_utility_v1/r0_scene_calibration/witnesses/saddle_T17.npz` or
`results/nuc_robot_skeleton_coupling_v1/config.json`. No fixture was fabricated, and the suite is
recorded as dependency-limited rather than fully passing.

An initial result selector omitted a separately validated F fallback when choosing the final A/P
output. Commit `01aa429` corrected only final selection; the measured searches and candidate
witnesses were unchanged. Both pre-correction tables remain in the Code result directory.

## DEV attribution

| placement | F | A | A_root | P |
|---|---|---|---|---|
| T30 | exhausted, no graph goal | accepted new global, Jq 96.949149 | no accepted plan, record limit | accepted same witness, Jq 96.949149 |
| T27 | accepted fixed, Jq 90.011360 | retained F | retained F | retained F |
| T33 | exhausted, no graph goal | accepted new global, Jq 96.153376 | no accepted plan, record limit | accepted same witness, Jq 96.153376 |

On T30 and T33, A succeeded while A_root did not, supporting the usefulness of the deterministic
F-prefix archive on these two development cases. P reproduced A's exact accepted witnesses with
677/735 expansions versus A's 36,910/34,705. Retaining multiple competing continuations was
therefore not necessary for the observed DEV successes; the smallest supported mechanism is
prefix-guided greedy recombination. T27 only demonstrates fallback retention.

## Frozen-placement transfer

All six registered roots used `anchor_root`, the first admissible seed in the frozen order. The six
graphs were ready, represented all 238 ports, retained 741--951 actual robot states, and contained
5,329--6,109 checked edges. Construction used 963,075--984,957 centrally counted IK calls and
701.36--795.44 seconds per scene. The large reconstructible graph archives remain local under
published paths and hashes.

| scene | k | F | P | A |
|---|---:|---|---|---|
| H00 | 1 | no accepted plan | new global, Jq 93.65434 | new global, Jq 93.65400 |
| H00 | 2 | no accepted plan | new global, Jq 94.10597 | new global, Jq 93.65400 |
| H01 | 1 | fixed, Jq 90.26857 | retained F | improved F, Jq 90.22446 |
| H01 | 2 | fixed, Jq 90.26857 | retained F | improved F, Jq 90.22446 |
| H02 | 1 | no accepted plan | new global, Jq 88.14767 | new global, Jq 88.14580 |
| H02 | 2 | fixed, Jq 96.03983 | improved F, Jq 89.18912 | improved F, Jq 88.14580 |
| H03 | 1 | fixed, Jq 86.32892 | retained F | retained F |
| H03 | 2 | fixed, Jq 86.32892 | retained F | retained F |
| H04 | 1 | no accepted plan | new global, Jq 89.74949 | new global, Jq 89.74949 |
| H04 | 2 | no accepted plan | new global, Jq 89.74949 | new global, Jq 89.74949 |
| H05 | 1 | fixed, Jq 92.51940 | retained F | retained F |
| H05 | 2 | fixed, Jq 92.51940 | retained F | retained F |

Every result shown as fixed, retained, improved, or new passed the inherited achieved-FK T0/Q3,
T0/Q4, T1/Q4, and Q4a contract, robot checks, activity budget, joint-cost replay, and exact
same-sample composition. A supplied independently accepted global benefit in four of six placement
neighborhood tests: new routes at H00, H02-k1, and H04, and lower-Jq routes at H01 and H02-k2. P
supplied global benefit at H00, H02, and H04 and otherwise retained F. The paired k rows frequently
refer to the same physical witness and are not independent successes.

## Mechanism and accounting

For the six saved cross-port choices in E10's T30/T33 accepted routes, five lacked the exact next
fixed-family sampled transition at the actual prefix q state. The sixth fixed-family suffix
exhausted after six labels with maximum covered fraction 0.9773576546, below the required 0.98.
These outcomes attribute the selected switches to finite-graph connection and remaining-coverage
structure. They do not prove that the missing numerical transitions are physically infeasible or
that each chosen switch is necessary.

Append-only logging reconciled 20 DEV and 56 TRANSFER screen callbacks exactly with method
counters. The validation logs include novel finalists, fallbacks, failures, and cache hits. Fifteen
unique accepted float64 witnesses, their achieved-FK plot data, and whole-surface figures are
published. The six task families produced 31 accepted method rows out of 36 TRANSFER rows; five F
rows had no accepted result. These method rows are shared, paired conditions rather than 31
independent trajectories.

## Interpretation boundary

The DEV result supports prefix information but not the necessity of A's multi-label complexity.
The TRANSFER result strengthens global recombination capability across deterministic pose
perturbations around the three original anchors, while P's performance favors a simpler supported
mechanism. It does not establish cross-surface generalization, population-level placement success,
global optimality, continuous-time coverage certification, physical infeasibility, or superiority
to a faithful published planner. Collision claims cover only the pinned MuJoCo model; unmodeled
workpiece/tool/environment/cable contacts, dynamics, force, and hardware control remain outside
scope.

Primary machine-readable evidence is under Code
`results/e11_mechanism_placement_transfer_v1/`, especially `dev_results.csv`,
`transfer_results.csv`, `final_validation.csv`, `screen_events.csv`, `validation_events.csv`,
`mechanism_diagnostics.csv`, `accepted_witness_manifest.csv`, and `report.md`.
