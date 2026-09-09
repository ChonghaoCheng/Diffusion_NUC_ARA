# NUC robot contract calibration (2026-09-09)

## Provenance

- Code repository commit: `84b3e9892d3a2736b070852b8d1680019c919174`
- Branch: `exp/nuc-robot-coupling-v1`
- Result directory: `results/nuc_robot_contract_calibration_v1/`
- Configuration: `configs/nuc_robot_coupling_v1.json`
- Robot model: MuJoCo Menagerie UR5e, site `attachment_site`, local tool axis `+z`

## Frozen contract

| Quantity | Value |
|---|---:|
| Surface samples per face | 48 |
| Dense reference samples per face | 96 |
| Path sample spacing | 0.002 m |
| Dense reference path spacing | 0.001 m |
| Maximum q interpolation step | 0.05 rad |
| Characteristic length `L_c` | 0.1 m |
| `sigma_safe` | 0.0723741717 |
| Maximum normal/reference `E_NUC` difference | 0.0148963707 |
| Frozen `delta_NUC` | 0.0297927413 |

The first 4/12-sample trial was rejected before method evaluation because its maximum
normal/reference discrepancy was 0.229696. Increasing the quadrature to 48/96 samples
per face reduced this discrepancy to 0.014896. Per the preregistered rule,
`delta_NUC = 2 * delta_eval`.

## Numerical checks

- Position-Jacobian finite-difference maximum error: `1.8117e-10`.
- Tool-axis-row finite-difference maximum error: `1.8378e-10`.
- Axis-basis rotation singular-value maximum error: `1.7764e-15`.
- Meter/millimeter scaling singular-value maximum error: `2.2204e-16`.
- Float64 continuation-witness serialization: exact round trip, 7 samples.

## Interpretation boundary

This evidence supports the numerical evaluation contract used by E06. It does not
compare skeleton-selection methods, establish robot-aware improvement, reproduce the
original T-Mech contact model exactly, or demonstrate physical execution. Collision
checking is limited to contacts represented by the loaded standalone UR5e model.
