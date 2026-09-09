# Concepts

## Continuous liftability
A workspace path is continuously liftable when there exists a continuous joint-space path whose forward kinematics satisfies position, tool-axis tolerance, joint-limit, singularity, and collision constraints at every path parameter.

## Representation gauge
Endpoint choice, traversal direction, sampling density, and parameterization can make the same geometric path appear as different waypoint sequences. Canonicalization removes only these symmetries.

## Planning multimodality
Distinct homotopy classes, winding directions, coverage pattern families, and IK-sheet/component choices are physically meaningful alternatives and must not be removed by canonicalization.

## Non-revisiting
Avoidance of unnecessary repeated processing of already swept surface area. It is evaluated through finite-footprint overlap efficiency rather than curve self-intersection.

## Amortized planning
Training cost is paid offline so that best-of-M proposal generation plus bounded refinement can reach feasible low-cost plans earlier than equal-wall-clock multi-start optimization.
