# Stage 0 Representation Audit

Source artifacts:

- `results/stage0_representation_audit_200/summary.json`
- `results/stage0_representation_audit_200/raw_results.csv`
- `results/stage0_representation_audit_200/representation_audit.png`

The audit used 200 expert paths split evenly across cylinder, hemisphere, saddle, and
free-form surfaces, with footprint radius varied from 0.10 to 0.30. The preregistered
p95 limits were `E_geom/r <= 0.25`, absolute missed-fraction change `<= 0.01`, and
absolute relative-length change `<= 0.05`.

At 128 B-spline control points, the p95 values were respectively `0.3290`, `0.04962`,
and `0.04053`. Geometry and coverage therefore still failed the acceptance limits.
The default representation was changed to canonical variable-token arclength paths
with padding and masks.
