# Pre-FM UR5e Liftability Gate

Source artifacts:

- `results/ur5e_liftability_200/combined_raw_results.csv`
- `results/ur5e_liftability_200/summary.json`
- `results/ur5e_liftability_200/report.md`
- `results/ur5e_liftability_200/liftability_summary.png`
- `results/ur5e_liftability_sensitivity_10/report.md`

The formal audit evaluated 200 workspace-first coverage plans at 3 and 10 degree
tool-axis tolerances. Overall continuous-lift success was 52.0%. Among the 195 plans
whose sparse calibration poses all admitted IK, success was 53.3%.

Surface-stratified success was cylinder 2.0%, hemisphere 6.0%, saddle 100.0%, and
free-form patch 100.0%. In the posewise-calibrated subset, cylinder success was 2.2%
and hemisphere success was 6.1%.

A fixed-placement sensitivity rerun selected five cylinder and five hemisphere cases,
halved pose spacing from 5 mm to 2.5 mm, doubled the active beam from 12 to 24, and
doubled random restarts from 16 to 32. All ten remained non-liftable.

These are numerical continuation results, not topology certificates. The model checks
UR5e self-collision but not workpiece collision, and detailed failure categories changed
under checker settings even when the binary liftability result remained stable.
