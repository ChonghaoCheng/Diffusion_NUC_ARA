# E12 graph-free global coverage generation — preregistration

Registered 2026-09-17 19:30 Australia/Sydney, before any E12 root solve, graph construction,
training, graph-free candidate generation, or sealed-test outcome.

E12 tests whether complete hemisphere routes can be represented as source-frame programs and
continuously lifted from the current q0 without a robot graph or future teacher q. The physical
contract is inherited unchanged from E11: complete radius-0.14 m hemisphere, one continuous ON
segment, 0.008 m intrinsic footprint, E_miss <= 0.02, E_rep <= 0.10, 0.0001 m/0.1 degree task
limits, sigma5 >= 0.07237417172157597, sampled collision/joint checks, and the T0/Q3, T0/Q4,
T1/Q4, Q4a stability contract.

The runtime program contains only SCAN, VIA, and END tokens over the three frozen source families.
It contains no q states, graph edges, feasibility labels, or teacher joint witnesses. Candidate
lifting uses current-state task5 continuation with fixed q6 and at most three spacing halvings.
Oracle encoding/re-lifting of the 15 E11 accepted witnesses is an interface diagnostic and is not
counted as generated success. Failure of this interface blocks corpus expansion and is reported as
an encoding/lifting limitation rather than an FM result.

The prospective pose table contains exactly 20 TRAIN, 4 VALIDATION, and 8 SEALED_TEST transforms,
drawn with NumPy PCG64 streams 1201/1202/1203 from SeedSequence([20260917, stream_id]). Anchors
cycle T30/T27/T33; translations and xyz angles follow the registered uniform ranges. The matrices
and hashes are frozen in Code `configs/e12_pose_splits_v1.json` before root evaluation.

If the oracle interface passes, TRAIN/VALIDATION teacher graphs are built under inherited E11 caps,
then one shared autoregressive token policy, deterministic REG head, and Euclidean conditional FM
head are trained with the fixed 5,000-update recipe and seed zero. RETRIEVE, REG, and FM use the
same eight predicted symbolic slots. SEALED_TEST graph-free outputs and checkpoints are frozen
before constructing P_graph references. P_lazy is the registered graph-free nonlearning control.

Primary tables retain every attempted task. Root, proposal syntax, lifting, robot contract,
coverage, numerical-resolution, budget, and dependency failures remain separate. No replacement
tasks, test-driven tuning, extra seeds, Q5, RFM, hardware, non-spherical geometry, or graph fallback
inside a graph-free success metric is allowed.

## Resource-scheduling amendment (2026-09-17 22:43 Australia/Sydney)

After measuring a task-owned single teacher worker at a largest observed 6,316,832 KiB RSS,
the host reported 125 GiB total and 102 GiB available memory with 16 logical CPUs. Teacher
collection therefore uses the already permitted maximum of four single-thread workers. Scene
membership and the absolute serial scene-order seed remain unchanged; shards are merged by scene
ID. This changes scheduling only.

The host exposed two idle NVIDIA RTX A5500 GPUs at the audit. The categorical, REG, and FM models
remain the registered architectures, root seed 0, datasets, update counts, and objectives. Their
independent training jobs use deterministic NumPy `SeedSequence([0, model_order_index])` streams
and at most two GPU workers, followed by a hash-checked manifest merge. GPU ownership and running
processes are checked again immediately before launch. This schedule was frozen before any E12
model training or validation output.

## User-authorized training expansion (2026-09-18 00:55 Australia/Sydney)

Before any validation-pilot candidate generation or sealed-test output, the user explicitly asked
to increase both the training sample count and the number of training updates. The registered
TRAIN set is therefore expanded from 20 to 40 attempted poses. The added `TR20`--`TR39` poses use
an independently fixed PCG64 stream `SeedSequence([20260917, 1204])`, the same anchor cycle and
the same translation/rotation ranges. The four VALIDATION and eight SEALED_TEST transforms remain
bitwise unchanged. All added transforms and hashes are committed before their root or graph
outcomes are computed; failed added tasks will not be replaced.

Categorical, REG, and FM training is increased symmetrically from 5,000 to 15,000 updates, and the
batch size is increased from 16 to 128 (1,920,000 sampled examples per model), with all other
optimizer, architecture, seed and loss settings unchanged. Two 5,000-update checkpoints
created before this amendment (categorical and REG) had not been used for validation or sealed
inference. They are retained and marked invalid rather than used or silently overwritten in the
evidence chain. All three reported models will be retrained on the expanded frozen corpus. This is
a prospective protocol amendment authorized by the user, not an outcome-driven hyperparameter
selection; it weakens comparability to the originally specified small-data budget and will be
reported explicitly.

## Expansion cancelled before use (2026-09-18 00:58 Australia/Sydney)

The user subsequently restored the original E12 contract to prioritize completion: 20 TRAIN,
4 VALIDATION, 8 SEALED_TEST, batch 16, and 5,000 updates for each model. The four expansion
workers were stopped after roughly five minutes, before any added scene produced a completed
graph or label. No TR20--TR39 sample, expanded-corpus normalization, or expanded checkpoint is
used in training, validation, sealed testing, or final conclusions. The already completed original
categorical and REG checkpoints are restored unchanged; only the missing original FM checkpoint is
trained. The attempted expansion commits and partial local shard locations remain recorded as an
aborted protocol branch, preserving the chronology without mixing it into the E12 main result.
