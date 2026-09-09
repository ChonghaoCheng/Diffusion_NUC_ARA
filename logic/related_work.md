# Related Work

## Flow Matching Ergodic Coverage
Sun, Pinosky, and Murphey formulate ergodic coverage as distribution matching and derive flow-matching controllers under alternative ergodic metrics. Their RSS 2025 implementation includes 2D and 3D control tutorials and Franka drawing/erasing demonstrations. It is a mandatory coverage baseline, adapted to uniform surface-area measure and footprint-scaled kernels, but it does not directly impose finite footprints, segment budgets, or configuration-space component continuity.

- Paper: https://www.roboticsproceedings.org/rss21/p051.pdf
- Official code: https://github.com/MurpheyLab/lqr-flow-matching

## FlowMP
FlowMP uses conditional Flow Matching with B-spline trajectory representations and second-order trajectory dynamics for robot motion planning. It is an architecture and implementation reference for smooth configuration-space generation, while the present problem additionally requires finite-footprint surface coverage and segment/component budgets.

- Paper: https://arxiv.org/abs/2503.06135
