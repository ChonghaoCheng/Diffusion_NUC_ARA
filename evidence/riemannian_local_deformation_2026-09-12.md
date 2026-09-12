# E06-R2 fixed-topology local Riemannian deformation validation (2026-09-12)

## Provenance

- Code branch: `exp/riemannian-local-deformation-v1`
- Code parent: `50aca4cf7011e5bf9ae5ff5576166f374081af61`
- Frozen-input commit: `56af57a`
- Final code/result commit: `72c40e5`
- ARA branch: `exp/riemannian-local-deformation-v1`
- Parent ARA commit: `2518051d8a5afe7f174d47946a2232a7370f4d94`
- Preregistration commits: ARA `0fd95c2`, frozen-window record `b05a735`
- Result directory: `results/riemannian_local_deformation_v1/`
- Frozen-window hash: `9bd7673f4de735ea5e0aedd985e753145741f402f4f9912497815110e11065c5`
- Source E06-R anchor hash: `4fdc4cbdc76d2efa767627b271a5754d89453de0c73ddd3fa4556978849db79b`

The preregistration in `staging/e06r2_local_deformation_plan.md` was committed before any M1/M2
deformation result. Historical E06, E06-D, E06-R, and E07 records were not rewritten.

## Frozen design

The experiment reused saddle `T17/T21/T10` and hemisphere `T30/T27/T33` as low/mid/high scenes.
Ten deterministic windows per scene produced 60 paired statistical units. Each path used seven
controls; `x0,x1,x5,x6` were fixed and `x2,x3,x4` could move at most `0.002 m`. M1 and M2 each
received 60 coordinate proposals and at most 12 accepted updates, with common deterministic
proposal schedules. M1 ranked by intrinsic surface length; M2 ranked by integrated, iteratively
recomputed `G_exec`. Both were evaluated using actual float64 continuation witnesses.

The unchanged contract used `L_c=0.1 m`, `sigma_safe=0.07237417172157597`, identity joint metric,
maximum dense q step `0.05 rad`, 10-degree axis tolerance, `0.003 m` position tolerance,
`delta_NUC=0.0297927413`, maximum relative surface-length change `2%`, identical archived start q,
and terminal mismatch at most `0.05 rad`.

## Test and replay status

`pytest -q` completed with `139 passed, 14 warnings`. The warnings are Matplotlib/PyParsing
deprecations. All 60 M0 slices passed strict replay. Maximum difference between direct archived
transition-sum `L_q` and reconstructed strict-witness `L_q` was exactly `0.0` at stored precision.
M1 and M2 each performed exactly 3,600 candidate evaluations.

## Primary result

| Stratum | Windows | Median Delta_E | Median Delta_R | Median A_R | M2 < M1 |
|---|---:|---:|---:|---:|---:|
| all | 60 | 1.5503% | 1.9601% | 0.0039% | 53.33% |
| low | 20 | 1.4591% | 1.5915% | 0.0030% | 55.00% |
| medium | 20 | 1.4368% | 1.8552% | 0.0016% | 50.00% |
| high | 20 | 2.2220% | 2.7031% | 0.0057% | 55.00% |
| medium + high | 40 | 1.6146% | 2.3349% | 0.0040% | 52.50% |

The medium/high median `A_R` bootstrap 95% interval was `[0, 0.0362%]`. The pooled relationship
was `Spearman(A_window,A_R)=0.1772`. Linear regression gave `alpha=-0.001718`, `beta=0.004121`,
`R^2=0.0958`, with bootstrap slope interval `[0.000871,0.008220]`. The slope is positive but the
incremental effect is two orders of magnitude below the frozen 5% practical gate.

## Admission and controls

- Candidate admission: M1 `2379/3600=66.08%`; M2 `2325/3600=64.58%`.
- Dominant rejection causes were the frozen local topology corridor and 2% surface-length bound.
  No final result required relaxed robot, NUC, singularity, or endpoint constraints.
- Maximum relative surface-length change: `1.9996%`.
- Maximum terminal q mismatch: `0.001715 rad`, far below `0.05 rad`.
- Maximum absolute `E_NUC` change: `0.002371`, far below `delta_NUC`.
- Maximum absolute `E_miss` change: `0.001147`; maximum `E_rep` change: `0.002371`.
- Minimum post-deformation `sigma_min_5`: `0.317239`, versus safety threshold `0.0723742`.
- `Spearman(A_R,sigma0)=0.00394`; `Spearman(A_R,joint_margin0)=-0.05976`.

Removing the lowest-sigma medium/high quartile left M2 win rate `53.33%` and median `A_R=0.0040%`.
At at most 1% surface mismatch, only ten medium/high paired windows remained, M2 win rate fell to
`20%`, and median `A_R=0`. All 40 medium/high windows passed the stricter `0.025 rad` terminal-q
filter, leaving the primary result unchanged.

The fixed target window length was `0.048 m`, but selecting exact archived witness indices yielded
actual baseline lengths from `0.046395 m` to `0.049888 m`. This is a pre-result discretization
deviation from exact equal physical window length. It is not a method-dependent confound because
M0/M1/M2 are paired within each frozen window, but it limits cross-window dose-response precision
and is recorded rather than corrected after observing results.

## Metric and optimizer diagnosis

The result does not invalidate E06-R's local directional finding. Across 2,325 admitted M2
candidates, integrated `L_G` and actual `L_q` retained Pearson `0.9930` and Spearman `0.9858`.
Nevertheless, the candidate selected by minimizing M2's `L_G` did not materially outperform M1's
surface-length selection. The median best actual `L_q` reduction available anywhere in each
method's evaluated candidate set was `4.5733%` for M1 and `4.5799%` for M2; these are diagnostic
candidate-set oracles, not valid method outputs. Thus both candidate sets contained somewhat
better actual witnesses, but the intended geometry objectives did not distinguish them reliably
at the fine scale needed for an incremental advantage.

The bounded deformation freedom is small and heavily shared: fixed endpoints/near-end controls,
three free controls, `2 mm` radius, fixed topology, and near-equal path length. Within that
contract, anisotropy remains explanatory at candidate scale but is not sufficiently useful as the
sole optimization geometry. The evidence does not identify robot safety, coverage, or terminal
splice as the limiting constraint.

## Gate decision

**NO-GO.** Frozen gates versus observations:

| Gate | Required | Observed | Pass |
|---|---:|---:|---:|
| medium/high M2 win rate | >=75% | 52.5% | no |
| medium/high median A_R | >=5% | 0.0040% | no |
| medium/high median Delta_R | >=8% | 2.3349% | no |
| anisotropy interaction | rho>=0.40 or clearly positive slope | rho=0.177; slope CI positive | yes by slope |
| sensitivity robustness | qualitative persistence | fails 1% length subset | no |

A larger NUC-constrained surface-joint planner is therefore **not justified by E06-R2**. Do not
run historical E07 or register a learned planner from this result. The Riemannian line should be
narrowed to a diagnostic/local cost model rather than continued as the sole deformation
objective. A future experiment, if registered, should first isolate why strict-witness `L_q`
fine ranking differs from `L_G` despite high aggregate correlation; it must not enlarge bounds or
budgets post hoc to rescue E06-R2.

## Reproduction

```bash
/data/chocheng/.venvs/coverage-fm/bin/python scripts/run_riemannian_local_deformation.py --stage freeze-windows
/data/chocheng/.venvs/coverage-fm/bin/python scripts/run_riemannian_local_deformation.py --stage run --jobs 16
/data/chocheng/.venvs/coverage-fm/bin/python scripts/summarize_riemannian_local_deformation.py
/data/chocheng/.venvs/coverage-fm/bin/python -m pytest -q
```

## Interpretation boundaries

E06-R2 does **not** establish improvement of full NUC coverage planning, superiority of a global
Riemannian deformation method, global trajectory optimality, C-space connectivity/disconnection,
Flow Matching or diffusion benefit, physical execution performance, or cross-robot generality.
