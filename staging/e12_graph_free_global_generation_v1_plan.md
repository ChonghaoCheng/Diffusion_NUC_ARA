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
