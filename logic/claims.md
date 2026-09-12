# Claims

## C01: Robot-induced surface anisotropy predicts local joint-motion direction cost
- **Statement**: At a shared safely feasible manipulator contact state, the eigenstructure of the normalized position-plus-tool-axis surface metric predicts which equal-length local surface directions require greater continuous joint travel.
- **Conditions**: Supported for the tested numerical UR5e, axis-symmetric tool task, frozen saddle and hemisphere meshes, strictly admitted local witnesses, and short face-traversing probes. Near-isotropic scenes, other robots, larger deformations, full coverage paths, and hardware remain incompletely tested or untested.
- **Sources**: [`0.9471` ← `evidence/riemannian_anisotropy_utility_2026-09-12.md:70` «Pooled `Spearman(log R_G0,log R_q)=0.9471`.» [result]; `0.9667` ← `evidence/riemannian_anisotropy_utility_2026-09-12.md:72-73` «Medium/high expensive-direction win rate is `0.9667`.» [result]; `720` ← `evidence/riemannian_anisotropy_utility_2026-09-12.md:60` «All 720 probes found a lift and passed strict kinematics» [result]]
- **Status**: supported
- **Provenance**: ai-suggested
- **Falsification**: Under independently frozen safely feasible shared states with matched intrinsic probe lengths, predicted high-cost directions fail to rank above predicted low-cost directions or the relationship disappears after singularity and joint-limit controls.
- **Proof**: [`evidence/riemannian_anisotropy_utility_2026-09-12.md`, `evidence/runs/riemannian_anisotropy_utility_v1/summary.json`]
- **Dependencies**: []
- **Tags**: robot geometry, Riemannian metric, local planning, UR5e
- **Last revised**: 2026-09-12 (`2026-09-12_001#1`)
