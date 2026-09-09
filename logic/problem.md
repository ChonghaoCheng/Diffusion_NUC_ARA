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

## Core Research Question

The main question is not whether Flow Matching can draw a surface path. It is
whether direct conditional generation can amortize the expensive search for
finite-footprint, segment-budgeted, continuously liftable coverage plans:

\[
p_\theta(\tilde\Pi\mid S,r,\tau,k,T_{\mathrm{base}},\mathcal R).
\]

The central comparison is workspace-first planning followed by post-hoc
continuous IK versus direct configuration-space generation followed by the same
hard projection, refinement, and checker.

## Primary Hypotheses

1. Surface-efficient coverage paths and robot-realizable coverage plans are
   different objects on sufficiently constrained 3D manipulator tasks.
2. Direct configuration-space generation can improve continuous-lift success
   while retaining competitive finite-footprint coverage quality.
3. Best-of-M generation plus short refinement can provide a better feasible-cost
   versus wall-clock tradeoff than equal-budget multi-start optimization.

The first pre-training motivation gate is whether classical workspace-first
coverage paths exhibit a material continuous-lift failure rate on UR5e. If they
do not, the robotics motivation is weakened regardless of FM sample quality.
