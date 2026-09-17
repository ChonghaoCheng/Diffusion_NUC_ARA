# E11 mechanism attribution and frozen-policy placement transfer — preregistration

Registered 2026-09-17 05:03 Australia/Sydney, before DEV planning measurements and before any
TRANSFER root or graph computation.

E11 preserves the complete analytical hemisphere, E09 geometry hash, E10 atomic search A,
coverage/robot thresholds, history semantics, bucket quotas, candidate policy, and refined
validator. It does not run source-run B, G1, FM, hardware, non-spherical geometry, or parameter
tuning.

DEV reuses the exact T30/T27/T33 graphs at k=1 and compares F, A, A_root (A without nonempty F
prefixes), and P (one greedy continuation per exact replayed prefix). All receive the same F cost
and independently validated fallback. Six saved E10 cross-port decisions receive bounded
whole-suffix graph diagnostics.

TRANSFER freezes six poses before IK: H00/H01 perturb T30, H02/H03 perturb T27, and H04/H05
perturb T33. PLUS uses `Rz(+10 deg) @ Ry(+12 deg)` and translation `[0.025,-0.015,0.020]` m;
MINUS uses `Rz(-10 deg) @ Rx(-12 deg)` and translation `[-0.025,0.015,-0.020]` m. Rotation acts
on anchor orientation only; translation is added to the anchor origin. Exact float64 matrices and
hashes are in Code `configs/e11_transfer_scenes_v1.json`.

Each new scene selects the first admissible common root from anchor root, home, archived hemisphere
start, and five limit-uniform seeds using `2026091700 + scene_index`, then builds one graph with the
unchanged E09-R1 limits. F/P/A run at k=1/2 on every successfully frozen graph. Root, construction,
search, screening, and refined-validation failures remain separate outcomes. No scene, seed,
threshold, denominator, or policy changes are allowed after TRANSFER results are observed.

Global search retains the 300-second pipeline budget minus actual F search, 200,000 expansions,
6 GiB memory, 30,000 retained records, four online Q3 screens, and two novel finalists. Append-only
screen and full-validation events are required. Beam/record termination is not infeasibility or
optimality. Acceptance remains refined finite sampling rather than continuous or hardware
certification.
