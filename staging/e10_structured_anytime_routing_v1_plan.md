# E10 structured anytime global coverage search — frozen plan

Registered 2026-09-17 03:31:35 Australia/Sydney, before E10 solver measurements.

E10 reuses the three immutable E09-R1 multi-state graphs and complete analytical hemisphere tasks
T30/T27/T33 at k=1/2. It preserves geometry hash
`01050368f57eca9a904a04106dc705aec89cab65248c508462ad232328976047`, roots, edge witnesses,
activity, robot model, coverage thresholds, and collision scope. No IK, connector, geometry, FM,
hardware, saddle, Q5, threshold, or prospective-bound optimization is part of E10.

F is the corrected fixed-route search and supplies a bounded exact prefix archive plus an
independently validated fallback. A and B receive the same archive and fallback. Both use the same
history state, bucketed bounded frontier, exact-history Pareto rules, ordinary segment-aware
reachability, candidate reservoir, online Q3 screen, and final E09-R1 validator. B alone also
offers exact compositions of consecutive source edges of lengths 2, 4, 8 and 16; all atomic edges
remain available.

Each A/B pipeline has 300 seconds including F search and online screens, 200,000 expansions,
6 GiB process memory, 30,000 retained records, 32 live labels per `(used_on, progress_bin)`, and
at most four online screens. Beam eviction makes A/B approximate; exhaustion is not an
infeasibility or optimality certificate. Only independently validated plans are accepted, and a
validated F fallback cannot be erased by timeout, eviction, or a rejected novel candidate.

The experiment consists of six tasks and 18 reported F/A/B cells. F versus B evaluates the
practical pipeline and route freedom; A versus B isolates source-run proposals. Historical G0/G1
timings are context only. Negative and budget-limited results will be published without tuning.
