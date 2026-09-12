# E06-G2 symmetry-preserving global layout gate (2026-09-12)

## Provenance

- Code branch: `exp/symmetry-preserving-global-layout-v1`
- Code parent: `c0b29be63d55ad007d2e77381fb111dee6e6790d`
- Frozen geometry/input commit: `b1cf1458ec85b9ce5c393d2faab24d5a9cd43711`
- Final code/result commit: `ad1696bd450bffb282baae61b5223ba8bc7ab3db`
- ARA branch: `exp/symmetry-preserving-global-layout-v1`
- Parent ARA commit: `94bc41221c87f35309707fede2a0200aaae584df`
- Preregistration commit: `8e69f9f`
- Frozen-contract commit: `32dca4a`
- Result directory: `results/symmetry_preserving_global_layout_v1/`
- Orbit hash: `479f4e0497e4ddd771ab7f445c3505aaaa5da4543895cc82296110abbfc4fdd1`

The canonical paths, exact symmetry group, invariance ceiling and realized tolerance, six scenes,
default/strong search budgets, and capacity gate were committed before robot outcomes. E06-G and
all earlier evidence remain unchanged.

## Canonical path provenance and representation audit

Both paths derive from the E06 `S00`, `upstream_first`, P_easy strict witness generated at code
commit `78876d52d5313c0e99978700ff3cb7de02e2d0a5`:

| Surface | Source artifact | Source samples | Canonical samples | Max analytical projection | Canonical hash |
|---|---|---:|---:|---:|---|
| saddle | `witnesses/saddle_P_easy_S00.npz` | 3,171 | 3,171 | 0.203906 mm | `dfd59b447091...` |
| hemisphere | `witnesses/hemisphere_P_easy_S00.npz` | 5,597 | 6,340 | 6.178775 mm | `989323dd6ebd...` |

E06-R used a coarse triangular task surface, not exact analytical points. E06-G2 therefore mapped
the source trace back to object coordinates, projected it once to the analytical surface and
deterministically densified at at most `0.002 m`. `theta=0` reproduces this newly frozen canonical
path exactly. It does not byte-replay the old q witness, and its coverage values must not replace
historical E06 values.

This audit invalidated an assumption in the prompt: the qualified old hemisphere witness did not
guarantee that the analytically normalized path remained continuously liftable in the same scenes.
The 6.179 mm maximum normalization is larger than the `0.003 m` final position tolerance and is a
material task change, even though it is the technically necessary correction for exact analytical
rotational symmetry.

## Exact symmetry and coverage contract

Hemisphere used 24 object-frame rotations at `15 deg` increments. Saddle used the exact bounded
square-domain group: identity, x reflection, y reflection and 180-degree z rotation. Every member
passed analytical surface/boundary, transported normal, mesh adjacency, sample/weight permutation,
order/activity, topology and segment-sequence checks.

Stage A found that independently recomputing shortest mesh polylines introduced vertex-ID-dependent
tie breaking at symmetry-related edges. Before robot execution, this was corrected by densifying the
canonical analytical path once, evaluating ordered footprint membership once with the same
mesh-edge geodesic disk backend, verifying each transform as a mesh/sample automorphism, and
transporting membership by the verified permutation. This preserves temporal revisit semantics
without widening the scientific criterion.

Frozen invariance tolerance was `1e-12`. Observed maximum errors were:

- `E_miss`: `2.78e-17`
- `E_rep`: `2.78e-17`
- `E_NUC`: `5.55e-17`
- total `L_S`: `1.78e-15 m`
- ordered segment length: `2.49e-17 m`
- reference automorphism: `1.74e-16 m`
- transported normal: `4.49e-16`

Canonical symmetry-compatible metrics were saddle `E_NUC=0.7565340822`, `L_S=6.0139313520 m` and
hemisphere `E_NUC=0.4033100677`, `L_S=10.4904846652 m`. These values use a different, explicitly
symmetry-compatible physical evaluator representation and are not historical metric revisions.

## Robot contract and tests

The six placements were saddle `T17/T21/T10` and hemisphere `T30/T27/T33`. The unchanged task used
UR5e, `attachment_site`, the axis-symmetric normalized 5D task, `L_c=0.1 m`, `W=I`,
`sigma_safe=0.07237417172157597`, maximum dense q step `0.05 rad`, position tolerance `0.003 m`,
axis tolerance `10 deg`, and the existing collision/joint-limit contract.

Every orbit member independently received the same scene-level seed, eight random restarts, six
retained start candidates and five orientation-cone samples. No angle used a neighbouring witness.
The complete suite passed: `162 passed, 14 warnings`; warnings are existing Matplotlib/PyParsing
deprecations.

## Execution result

| Scene | Verified | `S_sym` | `Delta_sym` | Best member |
|---|---:|---:|---:|---|
| saddle T17 / low | 4/4 | 1.977% | 1.938% | x reflection |
| saddle T21 / mid | 4/4 | 22.428% | 0.145% | 180-degree rotation |
| saddle T10 / high | 4/4 | 3.646% | 0.482% | 180-degree rotation |
| hemisphere T30 / low | 0/24 | n/a | n/a | n/a |
| hemisphere T27 / mid | 0/24 | n/a | n/a | n/a |
| hemisphere T33 / high | 0/24 | n/a | n/a | n/a |

The exact saddle response in `(identity, reflect-x, reflect-y, rotate-180)` order was:

- T17: `(25.5101, 25.0156, 25.0523, 25.3554)`
- T21: `(22.7983, 27.8710, 26.4099, 22.7652)`
- T10: `(20.7595, 21.3747, 21.4125, 20.6593)`

Thus pure orientation can change cost materially in the supportive saddle T21 scene, but the
canonical member was already within 0.145% of the best orbit member. Across all 12 saddle witnesses,
the median first/middle/last contribution was `5.07%/89.74%/5.19%`; the T21 spread is not an endpoint
artifact.

All 72 hemisphere jobs enumerated six start candidates and completed zero full dense continuations.
No scene mixed successes and failures, so the preregistered strong-search rule selected no cases.
This is not evidence of C-space disconnection and not a symmetry-dependent liftability split.

## Gate and interpretation

**NO-GO. Riemannian Stage D was not authorized and was not run.**

| Frozen gate | Required | Observed | Pass |
|---|---:|---:|---:|
| hemisphere scenes with `S_sym>=10%` | at least 2/3 | 0 evaluable | no |
| median canonical `Delta_sym` | at least 8% | not defined for hemisphere | no |
| robust mixed-orbit liftability scenes | at least 2 | 0 | no |

Administrative classification: **Outcome A-qualified**. No coverage-equivalent pure-orientation
capacity was demonstrated under the frozen contract. However, the literal Outcome A claim that
orientation variation is small is not supported: one saddle scene had 22.43% spread, while the
primary hemisphere response was unobserved because analytical normalization removed full-lift
qualification. Consequently this result also cannot conclude that E06-G raw spread was mainly
caused by path-length/coverage variation.

The next experiment should not build a planner or run Riemannian scoring. It should first resolve
the representation contract: obtain an exactly symmetric analytical canonical path that is
independently qualified as fully liftable before freezing its orbit, or use a physical surface
representation whose exact symmetry group preserves the already-qualified trace. That is a new
registration, not post-hoc repair of E06-G2.

## Reproduction

```bash
/data/chocheng/.venvs/coverage-fm/bin/python scripts/freeze_symmetry_layout_library.py
/data/chocheng/.venvs/coverage-fm/bin/python scripts/run_symmetry_layout_capacity_gate.py --stage default --jobs 12
/data/chocheng/.venvs/coverage-fm/bin/python scripts/summarize_symmetry_layout_capacity_gate.py
/data/chocheng/.venvs/coverage-fm/bin/python scripts/run_symmetry_layout_capacity_gate.py --stage strong --jobs 12
/data/chocheng/.venvs/coverage-fm/bin/python -m pytest -q
```

## Non-claims

E06-G2 does not establish global path/q optimality, global C-space connectivity or disconnection,
general global-orientation utility, benefit from Riemannian global ranking, planner superiority,
hardware performance, cross-robot generality, or Flow Matching/diffusion benefit. It is a finite
symmetry-orbit and finite numerical-search result with an explicit canonical-representation
limitation.
