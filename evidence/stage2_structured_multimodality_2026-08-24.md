# Stage 2 Structured Multimodality Evidence

## Data Contract

Source: `results/stage2_structured_multimodal_200/summary.json`

- Input instances: `200`
- Output instances with at least one raw hard-feasible structured mode: `187`
- Admitted candidates: `491`
- Excluded repaired candidates: `52`
- Excluded hard-infeasible structured roundtrip candidates: `1`
- Empty instances after exclusions: `13`
- Admitted modes: raster u phase 0 (`168`), raster u phase 0.25 (`55`),
  raster v phase 0 (`95`), spiral phase 0 (`144`), spiral phase 0.25 (`29`)
- Maximum structured/source length ratio over every admitted mode was at most
  `1.0227851701101183`.

The sanitizer extracts float32 model controls, verifies geometry-derived token count,
decodes through the inference path, and applies the unchanged hard coverage evaluator.

## Representation Audit

Source: `results/flow_matching_stage2_mode_conditioned_run1/target_audit_validation.json`

- Audited validation instances: `38`
- Hard-feasible target roundtrips: `100%`
- Mean decoded/source length ratio: `0.9995966349820687`
- Maximum absolute missed-fraction change: `0.008065383908284707`

## Training

Source: `results/flow_matching_stage2_mode_conditioned_run1/history.json` and `best.pt`

- Training limit: `5,000` optimization steps
- Best checkpoint epoch: `370`
- Best checkpoint step: `4,810`
- Best validation velocity loss: `0.036898`
- Representation: variable-length analytic structured UV controls
- Conditioning: footprint/tolerance plus a five-way explicit mode one-hot
- Sampling: inverse-frequency balanced training over admitted modes

## Held-Out Hard Evaluation

Source: `results/flow_matching_stage2_mode_conditioned_run1/eval_validation_k8/metrics.json`

Protocol: `38` held-out instances, `103` expert-supported instance-mode tasks,
`8` stochastic samples per conditioned mode, `32` Heun steps, unchanged hard checker.
The evaluator explicitly enumerates expert-supported modes; it does not evaluate a
learned categorical mode prior.

- Generated candidates: `824`
- Per-candidate hard-feasible rate: `0.32402912621359226`
- Best-of-8 mode recovery: `0.7475728155339806`
- Best-of-8 mode-recovery Wilson 95% interval:
  `[0.6558210776288516, 0.8215217119860896]`
- Mean instance mode coverage: `0.7609649122807017`
- Instances recovering all expert modes: `0.4473684210526316`
- Mean recovered/expert modes per instance: `2.026315789473684 / 2.710526315789474`
- Mean feasible generated/teacher length ratio: `0.9855183870510913`
- Median feasible generated/teacher length ratio: `0.9679459741907338`

Best-of-K mode recovery:

| K | Recovered instance-modes | Recovery rate |
|---:|---:|---:|
| 1 | 34 / 103 | 0.3300970873786408 |
| 2 | 47 / 103 | 0.4563106796116505 |
| 4 | 64 / 103 | 0.6213592233009708 |
| 8 | 77 / 103 | 0.7475728155339806 |

Per-mode best-of-8 recovery:

| Mode | Tasks | Recovery | Per-sample feasible | Feasible length ratio |
|---|---:|---:|---:|---:|
| raster u phase 0 | 34 | 0.7941176470588235 | 0.375 | 0.8975920916065435 |
| raster u phase 0.25 | 11 | 0.7272727272727273 | 0.29545454545454547 | 0.9337724116236754 |
| raster v phase 0 | 20 | 0.7 | 0.2625 | 1.0159736479215682 |
| spiral phase 0 | 31 | 0.7741935483870968 | 0.3588709677419355 | 1.0793327402464088 |
| spiral phase 0.25 | 7 | 0.5714285714285714 | 0.14285714285714285 | 1.0130332999380434 |

## Verification

- Full test suite: `49 passed in 12.50s`
- Plot: `results/flow_matching_stage2_mode_conditioned_run1/eval_validation_k8/stage2_mode_recovery.png`
- Human-readable report: `results/flow_matching_stage2_mode_conditioned_run1/eval_validation_k8/report.md`

## Interpretation Boundary

The monotonic best-of-K curve supports stochastic proposals within an explicitly
conditioned mode. The run does not demonstrate autonomous discovery or probability
estimation over discrete modes. Low per-sample feasibility and incomplete all-mode
recovery require mode-specific stabilization and seed replication before synthetic
colour or direct C-space experiments.
