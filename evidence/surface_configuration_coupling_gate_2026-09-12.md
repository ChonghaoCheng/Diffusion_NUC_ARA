# E06-J surface--configuration coupling capacity gate (2026-09-12)

## Provenance

- Code branch: `exp/surface-configuration-coupling-gate-v1`
- Code parent: `72c40e5466487b0eaf0c6daad78b5a72d1a17880`
- Frozen implementation/input commit: `27c3f1c`
- Final code/result commit: `380f9cd`
- ARA branch: `exp/surface-configuration-coupling-gate-v1`
- Parent ARA commit: `beffd282baeeaad11321a4baafc15b7ee8435cf0`
- Preregistration commits: ARA `56b5f95`, frozen-input record `6300d39`
- Result directory: `results/surface_configuration_coupling_gate_v1/`
- Source E06-R2 window hash: `9bd7673f4de735ea5e0aedd985e753145741f402f4f9912497815110e11065c5`
- Shared surface-candidate hash: `4833ab735eff5786c801704c9f8e0700714bf6227d7f484f01236fd738e8709c`

The preregistration and shared candidate bank were committed before F1--F3 results were generated.
E06, E06-D, E06-R, E06-R2, and the blocked historical E07 were not modified or reinterpreted.

## Frozen design and solvers

The experiment reused all 60 E06-R2 windows: ten windows in each low/mid/high placement for saddle
and hemisphere. Every formulation minimized the same float64 strict-witness objective
`J_q=sum_i ||q_(i+1)-q_i||_2`.

- F0 replayed the archived surface path and q witness.
- F1 fixed the surface task and used an eight-state layered-q beam. Each parent generated IK
  seeds at null offsets `{-0.12,0,+0.12} rad`; start and end q were exact.
- F2 evaluated one shared bank of 24 nonzero Sobol surface deformations plus F0. Every candidate
  received one deterministic warm continuation, and admitted candidates were ranked by verified
  `J_q`. It had no independent q decision variable.
- F3 evaluated the identical surface bank, ran the F1 layered-q search for each surface candidate,
  and ranked joint `(x,q)` candidates by verified `J_q`.

This is a deterministic numerical capacity comparison, not a continuous global optimum. The
unchanged contract used `L_c=0.1 m`, `W=I`, `sigma_safe=0.07237417172157597`, maximum dense q step
`0.05 rad`, position tolerance `0.003 m`, axis tolerance `10 deg`, maximum local surface-control
displacement `0.002 m`, maximum relative surface-length change `2%`,
`delta_NUC=0.0297927413`, exact common start q, and terminal mismatch at most `0.05 rad`.

## Tests and replay

`pytest -q` completed with `147 passed, 14 warnings`. The warnings are existing Matplotlib/PyParsing
deprecations. All 60 F0 windows passed strict replay, and the maximum archived reconstruction error
was exactly `0.0` at stored precision. The eight new tests cover formulation variable access,
bounded deterministic candidate generation, normalized task-null direction, deterministic beam
retention, rejection-safe incumbent updates, common `J_q`, shared endpoint replay, and frozen
checker/evaluator contracts.

## Primary result

| Stratum | Windows | Median Delta_conf | Median Delta_surface | Median Delta_joint | Median B_joint | F3 beats both |
|---|---:|---:|---:|---:|---:|---:|
| all | 60 | 0.0000% | 1.2997% | 1.3239% | 0.0000% | 48.33% |
| low | 20 | 0.0000% | 1.1595% | 1.1113% | 0.0036% | 55.00% |
| medium | 20 | 0.0000% | 1.1287% | 1.1459% | 0.0000% | 45.00% |
| high | 20 | 0.0000% | 2.1904% | 2.2038% | 0.0000% | 45.00% |
| medium + high | 40 | 0.0000% | 1.7162% | 1.6605% | 0.0000% | 45.00% |

For medium/high windows the `B_joint` IQR was `[-0.0188%, 0.0120%]`, and its paired-bootstrap
95% median interval was `[-0.00756%, 0.00458%]`. F3 beat F1 in `87.5%`, but beat F2 and both in
only `45%`. Thus the first comparison mostly reflects surface-path improvement over unchanged F1,
not a coupling advantage.

F1 solved and strictly admitted all 60 beam searches but never improved F0. F2 and F3 each found a
non-baseline final solution in 54/60 windows. Of 1,440 nonzero candidate attempts per method, 529
passed geometry and strict execution, 403 were excluded by the frozen 2% length bound, and 508 by
the frozen topology/turn contract. F2 and F3 selected the same surface candidate in all 60 windows.
Conditional on that common surface path, the F3-versus-F2 effect ranged from `-0.0919%` to
`+0.0711%` of F0 cost and had median zero. This directly localizes the missing capacity: independent
q realization did not add material value at this local fixed-topology scale.

## Admission and sensitivity controls

- Maximum relative final surface-length change: `1.9944%`.
- Maximum terminal-q mismatch: `0.001094 rad`, far below `0.05 rad`.
- Maximum absolute changes: `E_NUC=0.002310`, `E_miss=0.000849`, `E_rep=0.002310`.
- Minimum final `sigma_min_5=0.317239`, far above `sigma_safe=0.0723742`.
- Minimum absolute joint-limit margin: `0.566389 rad`.

Removing the lowest-sigma medium/high quartile gave median `B_joint=0.00215%` and F3-both win rate
`56.67%`. The terminal `0.025 rad` filter retained all 40 windows and left the primary result
unchanged. Restricting to at most 1% surface-length change retained 20 windows, with median
`B_joint=0`, F3-both win rate `40%`, and median `Delta_joint=0.6874%`. The NO-GO therefore does not
come from singularity proximity, joint limits, NUC admission, or endpoint mismatch.

## Riemannian diagnostics

`G_exec` was diagnostic only. Median integrated `L_G` reduction was `0` for F1 and `1.1646%` for
both F2 and F3, because both selected the same surface candidate. Its reduction correlated with
actual F3 `J_q` reduction at Spearman `0.7600`. Mean cheap-eigendirection alignment changed only
from median `0.7467` in F0 to `0.7518` in F3. These diagnostics remain consistent with E06-R: the
induced geometry predicts execution tendencies, but the present local formulation offers little
additional coupled q freedom beyond selecting the surface path.

## Gate and interpretation

**NO-GO. Outcome D: low local headroom.**

| Frozen medium/high gate | Required | Observed | Pass |
|---|---:|---:|---:|
| F3 beats both | >=70% | 45.0% | no |
| median B_joint | >=5% | 0.0% | no |
| median Delta_joint | >=8% | 1.6605% | no |
| qualitative sensitivity persistence | required | absent | no |

Under the tested 2 mm, fixed-endpoint, fixed-topology local windows, explicit x--q coupling does
not justify its added method complexity and should not become the project's central planning
formulation. The next registered experiment should move to the scale identified by Outcome D:
measure capacity from global path/layout variables such as root or entry/exit choice, sweep
orientation, remeshing realization, global skeleton layout, or path family/topology. That future
experiment should remain a non-learned mechanism gate before any planner is built.

## Reproduction

```bash
/data/chocheng/.venvs/coverage-fm/bin/python scripts/run_surface_configuration_coupling_gate.py --stage freeze-candidates
/data/chocheng/.venvs/coverage-fm/bin/python scripts/run_surface_configuration_coupling_gate.py --stage run --jobs 16
/data/chocheng/.venvs/coverage-fm/bin/python scripts/summarize_surface_configuration_coupling_gate.py
/data/chocheng/.venvs/coverage-fm/bin/python -m pytest -q
```

## Interpretation boundaries

E06-J does **not** establish global surface--configuration optimality, full-path NUC planning
benefit, global C-space connectivity/disconnection, hardware performance, cross-robot generality,
Riemannian optimizer benefit, or Flow Matching/diffusion benefit. Solver failure and finite bank
coverage remain numerical limitations. The result closes only the tested local fixed-topology
coupling formulation under the frozen search budget.
