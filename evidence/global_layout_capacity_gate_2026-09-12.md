# E06-G global coverage-layout capacity gate (2026-09-12)

## Provenance

- Code branch: `exp/global-layout-capacity-gate-v1`
- Code parent: `380f9cd55a3017a759a89b6b93f12606f0c24f86`
- Stage-A contract commit: `7c98c13`
- Frozen layout-library commit: `4bdbaa1`
- Final code/result commit: `c0b29be63d55ad007d2e77381fb111dee6e6790d`
- ARA branch: `exp/global-layout-capacity-gate-v1`
- Parent ARA commit: `80c9f49adf3994aa425972120d31e27da604fbad`
- Preregistration commits: ARA `82a4dd4`, tolerance correction `7467bc6`, frozen-input record `af8bb87`
- Result directory: `results/global_layout_capacity_gate_v1/`
- Frozen root hash: `8547418f72c3a46e08f70bfaa06378ac1f8ea884e20801ccd29f6f352ad003dd`
- Frozen remesh hash: `3bb2170272191537772602724db94c3d587c0c55f460d43c46d76cf6783b99b7`
- Frozen layout-library hash: `ad897a3249e4f39aaac606812e4501ecc7f28046475763b1075c472d94b6bc09`

The eight physical roots, four deterministic remeshes, geometry-only selection rule, coverage
admission, six placements, robot contract, numerical search budget, and G0 gate were committed
before robot outcomes were generated. E06, E06-D, E06-R, E06-R2, E06-J, and historical E07 were
not modified or reinterpreted.

## Frozen design

Each physical surface used eight remesh-independent roots and four matched-resolution planning
meshes. The NUC policy was always `upstream_first`; only physical root and remesh realization
varied. Every resulting path was mapped to one frozen dense physical reference surface before NUC,
intrinsic-length, diversity, visualization, and robot evaluation. The two surfaces and three
placements were saddle `T17/T21/T10` and hemisphere `T30/T27/T33`.

The unchanged robot contract used the UR5e `attachment_site`, the axis-symmetric normalized 5D
task, `L_c=0.1 m`, `W=I`, `sigma_safe=0.07237417172157597`, maximum dense q step `0.05 rad`,
position tolerance `0.003 m`, axis tolerance `10 deg`, footprint radius `0.008 m`, and
`delta_NUC=0.0297927413`. Execution cost was computed from actual float64 witnesses as
`J_q=sum_i ||q_(i+1)-q_i||_2`.

During Stage A, before any full freeze or robot result, the provisional `0.006 m` common-reference
projection ceiling was corrected to `0.0061 m`: canonical hemisphere M00 itself measured
`0.0060700861 m`, and all four realizations agreed within `1e-14 m`. No coverage or robot
admission threshold changed. The 2-root x 2-remesh saddle smoke test then passed canonical-path,
physical-root, common-reference, root-mapping, fixed-policy, and structural contracts.

## Tests and physical library

`pytest -q` completed with `154 passed, 14 warnings`; the warnings are existing
Matplotlib/PyParsing deprecations. Added tests cover canonical R00/M00 replay, remesh-independent
physical roots, deterministic mapping, same-surface/remesh resolution checks, common-reference
evaluation, fixed expansion policy, robot-blind geometry selection, deterministic coverage
admission, verified-only finite-library oracle selection, and refusal to run G1 after G0 NO-GO.

The four saddle meshes each contain 49 vertices and 72 faces; the four hemisphere meshes each
contain 61 vertices and 108 faces. Maximum common-reference discrepancy was `0.0005224 m` for
saddle and `0.0060701 m` for hemisphere. Maximum root mapping distances were `0.0004448 m` and
`0.0044398 m`, respectively, within the frozen tolerance.

The 32 candidates per surface did not collapse geometrically:

| Surface | Median ordered-path distance | Median tangent disagreement | Diversity failure |
|---|---:|---:|---:|
| saddle | 0.129189 m | 89.959 deg | no |
| hemisphere | 0.146989 m | 89.849 deg | no |

## Geometry admission

The placement-independent geometry baseline was saddle `R01_M03`, with `E_NUC=0.4735459` and
`L_S=4.7725413 m`; it was the only saddle layout within the frozen NUC-equivalence band. The
hemisphere baseline was `R00_M03`, with `E_NUC=0.5513469` and `L_S=8.3899631 m`; eight layouts
were equivalent. This admission bottleneck was retained rather than widening the threshold after
inspection.

Remeshing was the larger geometry factor. Across the 32 saddle layouts, remesh mean-range was
`0.11935` in `E_NUC` and `0.54888 m` in `L_S`, versus root mean-ranges `0.05412` and `0.20247 m`.
For hemisphere the corresponding remesh ranges were `0.09787` and `1.30684 m`, versus root ranges
`0.01100` and `0.04516 m`. Thus the chosen remesh degree of freedom changed coverage quality and
path length enough to remove most robot-diverse candidates from the equivalent set.

## G0 execution result

The full run evaluated 64 object-frame layouts under three placements, yielding 192 executions.
There were 107 verified strict witnesses and 85 cases where no continuous lift was found under the
frozen numerical budget.

| Scene | Equivalent | Eq. verified | All verified | Eq. `S_q` | All-layout `S_q` | `Delta_global` |
|---|---:|---:|---:|---:|---:|---:|
| saddle T17 / low | 1 | 1 | 32 | n/a | 28.285% | 0.000% |
| saddle T21 / mid | 1 | 1 | 31 | n/a | 49.962% | 0.000% |
| saddle T10 / high | 1 | 1 | 31 | n/a | 38.928% | 0.000% |
| hemisphere T30 / low | 8 | 0 | 2 | n/a | 3.010% | n/a |
| hemisphere T27 / mid | 8 | 0 | 11 | n/a | 8.420% | n/a |
| hemisphere T33 / high | 8 | 0 | 0 | n/a | n/a | n/a |

No apparent geometry-baseline-failure/equivalent-alternative-success pair existed, so the frozen
strong-search sensitivity rule selected no cases. This is not evidence of C-space disconnection.
It means only that no qualifying rescue occurred under the default finite numerical library.

The non-equivalent saddle library shows that the robot is sensitive to global layouts, but those
differences are outside the registered admission contract. Among all verified saddle layouts,
surface length and `J_q` had Pearson correlations `0.8423`, `0.6245`, and `0.6227` for low/mid/high;
Spearman correlations were `0.7991`, `0.5286`, and `0.4460`. The corresponding root mean-ranges in
`J_q` were `7.10%`, `8.93%`, and `6.08%`; remesh mean-ranges were `13.38%`, `10.03%`, and `14.91%`.
Because these pools violate the NUC-equivalence restriction, they are diagnostic only.

## Gate and decision

**G0 NO-GO. G1 was not authorized and was not run.**

| Frozen gate | Required | Observed | Pass |
|---|---:|---:|---:|
| scenes with equivalent `S_q>=10%` | at least 4/6 | 0/6 | no |
| median `Delta_global` | at least 8% | 0% | no |
| strong-confirmed feasibility rescues | at least 2 scenes | 0 | no |

Classification: **A, no demonstrated admitted global-layout headroom in the frozen root/remesh NUC
family.** This is specifically an admission-limited negative result. It does not establish that
all global layout freedom is unimportant: the physical path library was diverse and raw
non-equivalent layouts changed both execution cost and numerical lift success. It establishes that
the tested root/remesh mechanism does not expose usable robot-aware capacity while preserving the
frozen finite-footprint NUC criterion.

The registered research consequence is to reconsider the NUC/path family or the way globally
distinct layouts preserve coverage quality before building more robot-aware geometry. A global
planner, Riemannian selector, or learned model is not justified by this gate.

## Reproduction

```bash
/data/chocheng/.venvs/coverage-fm/bin/python scripts/freeze_global_layout_library.py --stage smoke
/data/chocheng/.venvs/coverage-fm/bin/python scripts/freeze_global_layout_library.py --stage freeze --jobs 12
/data/chocheng/.venvs/coverage-fm/bin/python scripts/run_global_layout_capacity_gate.py --jobs 12
/data/chocheng/.venvs/coverage-fm/bin/python scripts/summarize_global_layout_capacity_gate.py
/data/chocheng/.venvs/coverage-fm/bin/python -m pytest -q
```

## Interpretation boundaries

E06-G does **not** establish continuous global optimality, global C-space connectivity or
disconnection, exact physical equivalence to the original T-Mech NUC contact model, hardware
performance, cross-robot or cross-surface generality, planner superiority, benefit from global
Riemannian layout selection, or Flow Matching/diffusion benefit. Every result is bounded by a
finite 32-layout surface library and a finite continuation budget.
