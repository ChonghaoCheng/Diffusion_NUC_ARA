# M0 Validation: 2026-08-23

## Regression tests

```text
14 direct tests passed
```

## Cylinder convergence

```text
n_azimuth   area rel.err   half-geodesic rel.err
        12   1.138407e-02            1.138407e-02
        24   2.853343e-03            2.853343e-03
        48   7.137942e-04            7.137942e-04
        96   1.784772e-04            1.784772e-04
```

## Plane finite-footprint convergence

The analytic covered fraction for the tested unit-square strip is `0.4`.

```text
grid       covered fraction   abs.error
   8x8             0.382812    0.017188
  16x16            0.382812    0.017188
  32x32            0.392578    0.007422
```

## Five-surface smoke benchmark

```text
Surface             V      F   Samples   Missed    Length    Eval time
cylinder             216    384      1536    0.910     3.534     0.2176s
hemisphere           193    360      1440    0.940     0.786     0.0908s
saddle               289    512      2048    0.892     1.414     0.1460s
torus                240    480      1920    0.962     0.940     0.1255s
freeform_patch       289    512      2048    0.887     1.481     0.1486s
```

Commands:

```text
python scripts/benchmark_surface_m0.py
python scripts/benchmark_surface_convergence.py
```
