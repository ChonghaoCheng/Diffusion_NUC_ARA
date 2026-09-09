# M1 Teacher Validation: 2026-08-23

## Regression

```text
19 direct tests passed across 3 modules
benchmark_rows 5
all_teacher_feasible True
```

## Five-surface benchmark

Inputs: footprint radius `0.2`, missed-coverage tolerance `0.05`, mesh resolution `8`, refinement iterations `3`, seed `20`.

```text
Surface           Raster miss/len     Spiral miss/len     Teacher miss/len   Feas.   Time
cylinder           0.000/  58.90       0.018/  55.04       0.018/  54.72        7    16.48s
hemisphere         0.037/  48.45       0.057/  47.84       0.040/  48.17        4    17.53s
saddle             0.004/  18.95       0.016/  19.36       0.004/  18.91        6     3.64s
torus              0.004/ 118.03       0.078/  70.24       0.008/ 117.33        4    36.24s
freeform_patch     0.000/  19.89       0.030/  21.33       0.000/  19.82        6     3.92s
```

Raw rows: `results/teacher_m1_benchmark.json`.

## Feasibility-admission failure and correction

The first low-resolution dataset smoke run saved infeasible candidates:

```text
cylinder_0000     feasible=0   missed=0.0625
hemisphere_0000   feasible=0   missed=0.0502
```

The writer was changed to reject instances without feasible candidates. The generator now retries with denser tracks. The corrected dataset reports:

```text
instances=5
feasible_candidates=29
all serialized candidate feasibility flags=True
dataset size=400K
```

Corrected manifest: `results/teacher_m1_smoke_v2/manifest.jsonl`.
