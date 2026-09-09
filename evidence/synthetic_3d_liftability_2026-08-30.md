# Synthetic 3D colour liftability benchmark

## Scope and method

Synthetic IK-sheet availability is defined over the analytic chart of each actual 3D
surface. Colour regions repeat, overlap, disappear, and may include a globally available
sheet in the easy regime. A continuous segment must retain one colour over its complete
dense path sample. Dynamic programming computes the minimum number of fixed-colour path
segments exactly for the sampled path.

The benchmark uses all 160 admitted Stage 2 instances: 40 cylinders, 40 hemispheres,
40 saddles, and 40 free-form patches. Four independently phased fields are evaluated in
each easy, medium, and hard regime. The resulting corpus contains 1,920 instance-field
tasks, 24,660 candidate-field evaluations, and 15,360 budget-frontier records.

Sources:

- `src/diffusion_coverage/liftability/synthetic_colours.py`
- `scripts/benchmark_synthetic_liftability.py`
- `results/synthetic_liftability_160_v1/summary.json`
- `results/synthetic_liftability_160_v1/candidate_results.jsonl`
- `results/synthetic_liftability_160_v1/frontier_results.jsonl`

## Main paired result

Verbatim cluster-bootstrap table from
`results/synthetic_liftability_160_v1/cluster_bootstrap.md`:

> | easy | 4 | 0.5891 [0.5484, 0.6297] | 0.7844 [0.7438, 0.8234] | 0.1953 [0.1531, 0.2406] | 1.0218 [1.0149, 1.0294] |
> | medium | 16 | 0.3391 [0.3031, 0.3766] | 0.5219 [0.4750, 0.5703] | 0.1828 [0.1422, 0.2250] | 1.0265 [1.0186, 1.0356] |
> | hard | 32 | 0.2578 [0.2141, 0.3047] | 0.4328 [0.3719, 0.4969] | 0.1750 [0.1344, 0.2188] | 1.0477 [1.0335, 1.0635] |

Columns are workspace-first lift success, colour-aware finite-library success, paired
rescue rate, and colour-aware/workspace length ratio. Intervals are percentile 95% cluster
bootstrap intervals that resample complete 3D instances and retain repeated fields.

The finite-library segment-budget frontier is monotone by construction and checked at
runtime: candidate selection always includes the workspace optimum, so colour-aware
success cannot be lower, and best feasible length cannot increase with larger `k`.

## Mechanism and surface dependence

At the selected operating points, most rescues change raster orientation. Easy `k=4`
contains 59 `raster_v -> raster_u` and 44 `raster_u -> raster_v` rescues. Medium `k=16`
contains 55 and 35 respectively. This supports the mechanism that aligning coverage
orientation with colour regions can trade a small amount of path length for far fewer
required lift segments.

Surface-stratified rescue is not uniform. At easy `k=4`, rescue is 40.0% on free-form
patches and 38.125% on saddles, but zero on cylinders and hemispheres. At hard `k=32`,
rescue is 24.375% on free-form patches, 33.125% on saddles, 3.125% on cylinders, and
9.375% on hemispheres. The present cylinder/hemisphere proposal vocabulary lacks a
`raster_v` family, limiting the finite-library oracle.

Sources:

- `results/synthetic_liftability_160_v1/cluster_bootstrap.json`
- `results/synthetic_liftability_160_v1/summary.json`
- `results/synthetic_liftability_160_v1/synthetic_liftability.png`

## Concrete 3D rescue

For `freeform_patch_0001` under a medium field and `k=16`, the workspace-shortest
`raster_v` plan has length 23.5662 and requires 52 colour segments. The selected
`raster_u` plan has length 23.7676 and requires six segments, so it is liftable within
the budget for less than one percent additional length.

Sources:

- `results/synthetic_liftability_160_v1/rescued_example.json`
- `results/synthetic_liftability_160_v1/rescued_example.png`

## Exactness and discretization audits

- The dynamic program matches full colour-assignment enumeration on 100 random small
  masks.
- Across the same 3,192 pilot candidate-field pairs, maximum UV sampling steps 0.02,
  0.01, and 0.005 produce exactly identical minimum segment counts.
- The complete repository test suite passes: 64 tests.

Sources:

- `tests/test_synthetic_liftability.py`
- `results/synthetic_liftability_160_v1/resolution_audit.json`

## Interpretation boundary

The segment count is exact for each sampled path, and selection is exact over the finite
multi-start candidate library. The resulting length frontier is therefore a
library-restricted reference, not a certificate of global optimality over all continuous
surface paths. A colour-aware multi-start teacher is the next experiment needed to test
whether direct colour-conditioned search improves this reference.
