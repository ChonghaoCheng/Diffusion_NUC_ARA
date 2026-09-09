# Stage 2 GPU and coupling ablation

Sources:

- `results/gpu_throughput_audit_2026-08-30/metrics.json`
- `results/stage2_gpu_coupling_ablation/summary.json`
- `results/stage2_gpu_coupling_ablation/report.md`

## GPU throughput

The host has two NVIDIA RTX A5500 GPUs with 24,564 MiB each. On the corrected
393-candidate training split, measured sample throughput increased from 352.3
samples/s at batch 8 to 2,456.1 at batch 64 and 3,679.4 at batch 256. Dynamic
`torch.compile` was rejected for this variable-length short-run workload because
compilation dominated while the GPU remained idle.

## Controlled hard-checker results

| Run | Batch | Updates | Coupling | Train s | Candidate feasible | K=8 recovery | All modes | Length ratio |
|---|---:|---:|---|---:|---:|---:|---:|---:|
| Original seed-0 | 8 | 5,000 | independent | 150.25 | 26.70% | 54.55% | 37.50% | 1.0617 |
| Large batch, equal epochs | 64 | 700 | independent | 15.82 | 1.14% | 9.09% | 0.00% | 1.1274 |
| Large batch, scaled LR | 64 | 700 | independent | 16.84 | 3.41% | 22.73% | 0.00% | 1.1526 |
| Large batch, OT | 64 | 700 | minibatch OT | 17.97 | 0.57% | 4.55% | 0.00% | 1.0195 |
| Large batch, full updates | 64 | 5,000 | independent | 103.74 | 23.30% | 45.45% | 25.00% | 1.1253 |
| Large batch, full updates, OT | 64 | 5,000 | minibatch OT | 110.03 | 9.09% | 36.36% | 12.50% | 1.1155 |

At 5,000 updates, the batch-64 independent run recovered no raster-v modes,
16.7% of spiral-phase-0 modes, and 20% of free-form modes. Minibatch OT did not
improve the aggregate result under either update budget.

## Interpretation

The model currently needs optimizer updates, not only additional samples per
update. Larger batches are useful for throughput and larger datasets, but cannot
be substituted for updates without a validated optimization rule. The correct
use of both GPUs is parallel seeds and ablations with fixed protocols. The next
method work targets raster-v, spiral, and free-form conditioning rather than
additional undirected compute or the tested OT coupling.

The regression suite passed: `53 passed`.
