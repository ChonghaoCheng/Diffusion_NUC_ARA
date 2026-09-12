# E06-R Riemannian anisotropy planning-utility gate (2026-09-12)

## Provenance

- Code branch: `exp/riemannian-anisotropy-utility-v1`
- Completed code commit: `50aca4cf7011e5bf9ae5ff5576166f374081af61`
- Experiment implementation commit: `3be4386`
- Frozen-scene commit: `92dc3ec0a470c1db0f0460a9747b0249f608d9ed`
- Parent E06-D code commit: `1bfa2d3bfaa11df962d6764d14263fa54218402b`
- ARA branch: `exp/riemannian-anisotropy-utility-v1`
- Parent E06-D ARA commit: `d700f7afeda96b775f4851ee1133e65506d8c15a`
- Local results: `results/riemannian_anisotropy_utility_v1/`
- ARA snapshot: `evidence/runs/riemannian_anisotropy_utility_v1/`
- Tests: `131 passed`, with 14 Matplotlib/pyparsing dependency deprecation warnings.

The run retained the E06-D 5D position/tool-axis task, `L_c=0.1 m`,
`sigma_safe=0.07237417172157597`, identity joint weighting, maximum dense q step `0.05 rad`, the
same MuJoCo model/site/axis convention and task tolerances, and float64 witnesses.

## Metric and validation

For physical surface tangent basis `T_x`, the implementation finite-differences the smooth mesh
normal field and builds `B_h` from both `T_x/L_c` and the minimal tool-axis rotation differential.
It computes `G_exec=B_h^T (Jbar_5 Jbar_5^T)^-1 B_h` by stable solves and rejects non-positive
metrics. Tests cover tangent-basis invariance, finite-difference `B_h`, axis variation, positivity,
synthetic `diag(1,4)` ratio, sign symmetry, curve length, shared `q_i`, and frozen-input refusal.
An archived saddle E06 interval gives relative disagreement approximately `5.2e-6` between the
new surface metric and the previous D3 task-space calculation.

The initial `0.002/0.004 m` length choice was interrupted before any probe-result file was written:
`0.002 m` was only twice the unchanged internal `0.001 m` IK stopping tolerance. Under the
preregistered numerical-resolution clause, final lengths were frozen at `0.004 m` and `0.008 m`,
with `0.0085 m` boundary clearance. The final anchor hash is
`4fdc4cbdc76d2efa767627b271a5754d89453de0c73ddd3fa4556978849db79b`.

## R0 scene calibration

R0 evaluated 72 deterministic rigid placements: 36 each for saddle and hemisphere, always using
the same `upstream_first` baseline path within a surface. Forty-eight passed full continuation,
the unchanged strict checker, `sigma_safe`, and the interior joint-margin gate. Saddle admitted
31 and rejected 5 (four joint-margin, one continuation); hemisphere admitted 17 and rejected 19
(five joint-margin, fourteen continuation).

| Surface | Level | Candidate | median log kappa_R | median R_G | min sigma_min_5 |
|---|---|---|---:|---:|---:|
| saddle | low | T17 | 0.5698 | 1.3297 | 0.5876 |
| saddle | mid | T21 | 1.2189 | 1.8394 | 0.3083 |
| saddle | high | T10 | 1.5731 | 2.1958 | 0.4019 |
| hemisphere | low | T30 | 0.5744 | 1.3327 | 0.1754 |
| hemisphere | mid | T27 | 1.0476 | 1.6884 | 0.2026 |
| hemisphere | high | T33 | 1.2123 | 1.8333 | 0.1313 |

Every selected scene had a complete baseline lift and passed the frozen strict contract. The low
scenes are lower-anisotropy controls relative to the candidate pool, but their median `R_G` is
still about `1.33`; they are not a near-isotropic (`kappa_R approximately 1`) control.

## R1 directional probes

Ninety anchors were frozen: 15 in each surface/level scene. Every anchor generated both signs of
the minimum- and maximum-cost eigenvectors at both fixed lengths. All 720 probes found a lift and
passed strict kinematics, yielding 180 valid anchor-length pairs and 90 independent anchor-level
records. Primary statistics aggregate the two lengths within each anchor to avoid pseudoreplication.

| Level | Anchors | Median R_q | Q25 | Q75 | P(R_q>1) | P(R_q>1.2) |
|---|---:|---:|---:|---:|---:|---:|
| low | 30 | 1.1921 | 0.9989 | 1.3300 | 0.7333 | 0.5000 |
| mid | 30 | 1.7034 | 1.4066 | 2.0670 | 0.9333 | 0.9333 |
| high | 30 | 1.7157 | 1.6106 | 1.9975 | 1.0000 | 0.9667 |

Pooled `Spearman(log R_G0,log R_q)=0.9471`. The fit
`log R_q=a+b log R_G0` gives `a=-0.0628` (95% CI `[-0.1005,-0.0251]`), `b=0.9002`
(95% CI `[0.8478,0.9526]`), and `R^2=0.9280`. Medium/high expensive-direction win rate is
`0.9667`. The maximum paired intrinsic-length mismatch is `0.0002607` (0.0261%), far below the
frozen 2% limit.

The `0.004 m` and `0.008 m` median ratios are `1.5383` and `1.5259`; the directional relationship
does not materially collapse at the longer probe. Median sign asymmetry is `0.0494`. Integrated
`L_G` and actual probe `L_q` have Pearson/Spearman correlations `0.9872/0.9870`, with median
`L_q/L_G=1.0136`.

Removing the lowest-sigma anchor quartile leaves Spearman `0.9488`. Log-ratio residual has Pearson
correlation `-0.2101` with minimum sigma and `-0.1656` with absolute joint margin; corresponding
Spearman values are `-0.2361` and `-0.0827`. These controls do not indicate that the primary
ordering is mainly a hard-threshold or joint-limit artifact, while they do not eliminate every
possible local nonlinear confound.

## Decision

**GO.** All five preregistered mechanism conditions pass: pooled Spearman exceeds 0.70,
medium/high win rate exceeds 0.80, high-level median `R_q` exceeds 1.20, intrinsic lengths match,
and the result survives lowest-sigma-quartile removal.

The supported claim is narrow: under the tested numerical UR5e task and safely liftable local
states, the eigenstructure of the normalized robot-induced surface metric predicts a practically
large direction-dependent continuous joint-motion cost. This supplies a causal local planning
signal that was absent from expansion-order-only E06 variation.

E06-R2, a separate fixed-topology local-deformation validation, is registered but not run. The
historical E07 remains blocked/not run.

## Reproduction

Exact commands are stored in
`evidence/runs/riemannian_anisotropy_utility_v1/reproduction_commands.txt`. Raw placement, probe,
pair, and independent-anchor CSV files and all ten required figures are in the same snapshot.

## Limitations and non-claims

This experiment does not establish improvement of full NUC coverage planning, superiority of
Riemannian deformation, global trajectory optimality, C-space connectivity or disconnection,
Flow Matching/diffusion benefit, physical hardware performance, or generality outside the tested
surfaces, placements, and numerical continuation backend. No full path was optimized.
