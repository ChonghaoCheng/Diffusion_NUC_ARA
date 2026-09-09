# Strict UR5e q-space teacher audit (2026-08-30)

## Contract correction

The first real-component graph checked only discrete neighbour transitions and the first
q-space teacher checker sampled those transitions too sparsely. A dense interpolation
recheck with maximum joint step `0.05` rejected all six formerly feasible 32x10 teacher
variants for surface-tracking error. Those old teacher labels and the associated 20-instance
corpus are diagnostic only and must not be used for training.

## Corrected graph and checker

The graph now stores the exact float64 IK-continuation witness for every compatible candidate
pair and preserves it through serialization. On the 5x5 roundtrip audit, compatibility and
witness counts matched exactly: 272 cylinder edges and 939 hemisphere edges. Teacher routing
reuses these witnesses, while the hard checker densely interpolates in joint space and applies
the final task-space constraints independently. IK construction uses a stricter 1.5 mm target
tolerance than the 3 mm final admission tolerance.

## Strict 24x8 pilot

With maximum graph joint step `0.1` and 31 edge poses, all six combinations of two surfaces and
segment budgets `k={4,8,16}` passed the continuous hard checker. Aggregate missed coverage was
42.81%, 34.77%, and 24.61% for k=4, 8, and 16 respectively.

At k=16, the cylinder covered 61.458% of nodes and missed 40.430% swept-area coverage; its
largest strict component covered only 12.5% of nodes. The hemisphere covered 96.875% of nodes
and missed 8.798% swept-area coverage; its largest component covered 87.5% of nodes. Maximum
position errors were 1.502 mm and 1.316 mm respectively. This is a two-surface pilot, not a
population estimate.

Source: `results/ur5e_qspace_teacher_witness_step01_24x8_pilot_v1/summary.json`.

## Representation and compute audit

On the corrected 16x6 teachers, adaptive simplification at 0.005 rad maximum q error remained
hard-feasible and compressed the cylinder from 1,312 to 362 samples and the hemisphere from
1,540 to 409 samples. This supports a padded variable-token q representation.

Synthetic six-DoF GPU throughput tests on each RTX A5500 showed that a 256-hidden, six-layer
model with 512 q tokens and 256 surface tokens used about 5.116 GiB at batch 128 and processed
about 453k tokens/s. These values are terminal observations; the interrupted benchmark did not
write its planned JSON artifact. They establish capacity only, not trained C-space FM results.

The full test suite passed: 84 tests, with 14 warnings.

