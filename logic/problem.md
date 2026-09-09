# Problem

## Planning Instance

A planning instance is

\[
\mathcal I=(S,r,\tau,k,\varepsilon_0,T_{\mathrm{base}},\mathcal R),
\]

where `S` is a triangular 3D surface, `r` is the finite tool-footprint radius,
`tau` is the tool-axis normal tolerance, `k` is the segment budget,
`epsilon_0` is the permitted missed-area fraction, `T_base` places the robot
relative to the workpiece, and `R` specifies the robot model and hard limits.

The robot-feasible contact configuration set is

\[
\mathcal C=\{q:\operatorname{tip}(q)\in S,\;
\angle(\operatorname{axis}(q),n_S)\le\tau,\;
q_{\min}\le q\le q_{\max},\;q\text{ is collision-free}\}.
\]

A plan is a set of continuous configuration-space segments

\[
\Pi=\{\tilde\gamma_1,\ldots,\tilde\gamma_N\},\qquad
\tilde\gamma_j:[0,1]\to\mathcal C,\qquad N\le k.
\]

Its workspace trace is `gamma_j = pi o tilde_gamma_j`, where `pi` is forward
kinematics followed by tool-tip projection to the surface.

## Finite-Footprint Coverage

For workspace trace `K(Pi)` and intrinsic geodesic distance `d_S`, the swept
surface is

\[
M(\Pi)=\mathcal N_r(K(\Pi))
=\{x\in S_{\mathrm{reach}}:d_S(x,K(\Pi))\le r\}.
\]

Missed coverage is measured only over the physically reachable surface:

\[
\varepsilon(\Pi)=
\frac{|S_{\mathrm{reach}}\setminus M(\Pi)|}{|S_{\mathrm{reach}}|}.
\]

The primary optimization object is

\[
\ell^\star(k,\varepsilon_0)=
\inf\{\ell(\Pi):N(\Pi)\le k,\;\varepsilon(\Pi)\le\varepsilon_0,\;
\tilde\gamma_j\subset\mathcal C\}.
\]

Segment budget can reduce minimum length even on a connected plane. A tube
bound of the form

\[
|M(\Pi)|\lesssim 2r\ell(\Pi)+N\pi r^2
\]

shows the free end-cap contribution, while multiple segments can also avoid
long connectors between coverage strokes. Configuration-space components make
`k` additionally encode a robotics continuity budget; they are not required for
`ell-star(k)` to be nontrivial.

## Objectives and Metrics

The optimization objective is path length subject to hard finite-footprint,
segment-budget, and robot-liftability constraints. Swept-area efficiency is an
independent metric:

\[
\rho(\Pi)=\frac{|M(\Pi)|}{2r\ell(\Pi)+N\pi r^2}.
\]

Minimizing length and maximizing `rho` are **not equivalent** when `N <= k` and
coverage is constrained only by `epsilon <= epsilon_0`, because both `N` and
covered area may vary across feasible plans.

"Non-revisiting" means avoiding unnecessary repeated processing of already
covered surface area. It is quantified through finite-footprint swept-area
overlap efficiency, not through curve self-intersection. `rho` is initially an
evaluation metric, not a hard constraint.

## Temporal NUC and Robot Execution Cost

The current experiment refines non-revisiting into a temporal action-episode count. For each
area-weighted surface sample `x_i`, `nu_i` is the number of connected time/index intervals in
which the active tool footprint contains `x_i`; separate active plan segments are separate
episodes. The measured errors are

\[
E_{\mathrm{miss}}=\frac{\sum_i a_i\mathbf 1[\nu_i=0]}{\sum_i a_i},\qquad
E_{\mathrm{rep}}=\frac{\sum_i a_i\max(\nu_i-1,0)}{\sum_i a_i},\qquad
E_{\mathrm{NUC}}=E_{\mathrm{miss}}+E_{\mathrm{rep}}.
\]

Robot execution quality is computed on the actual continuous numerical witness, without
additional angle wrapping:

\[
L_q=\sum_j\|q_{j+1}-q_j\|_W,\qquad W=I\text{ initially}.
\]

Task-singularity admission uses the weakest singular value of a dimensionless five-dimensional
position-plus-tool-axis Jacobian, not the legacy full-6D manipulability determinant.

## Current Research Question

Workspace-path IK failures and the limitations of the old coarse q-space labels are historical
E01-E05 evidence, not the current question. Before introducing another learned generator, E06
asks:

> Given surface paths with comparable temporal NUC quality, does robot-aware NUC skeleton
> selection expose a material reduction in actual continuous-witness joint travel while
> retaining a positive normalized 5D task-singularity margin?

Only if this mechanism exists does E07 ask whether fixed-topology local surface-joint
deformation provides incremental benefit. Flow Matching, diffusion, and other learned proposal
models are deferred until multiple useful paths exist, explicit search is measurably expensive,
the representation is stable, and an equal-budget baseline is established.

## Current Hypotheses

1. Legal NUC expansion choices produce coverage-equivalent skeletons with materially different
   robot witness cost under the same physical scene.
2. The ranking of one surface's skeletons changes with rigid robot/workpiece placement, showing
   genuine robot-skeleton coupling rather than a geometry-only ordering.
3. If E06 passes, local tangential deformation can further reduce `L_q` while preserving the
   frozen `E_NUC` equivalence and `sigma_min_5` safety constraints.
