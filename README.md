# Diffusion NUC Research Artifact

Agent-Native Research Artifact (ARA) for the Diffusion-Guided Manipulator Coverage Planning
project. This repository records the research process, current interpretation, experimental
evidence, failed branches, and open questions separately from the executable code repository.

## Start Here

- [`PAPER.md`](PAPER.md): artifact manifest and layer index
- [`logic/problem.md`](logic/problem.md): current problem formulation
- [`logic/experiments.md`](logic/experiments.md): experiment registry and current results
- [`logic/solution/architecture.md`](logic/solution/architecture.md): current system architecture
- [`trace/exploration_tree.yaml`](trace/exploration_tree.yaml): append-only research DAG
- [`trace/sessions/session_index.yaml`](trace/sessions/session_index.yaml): session history
- [`evidence/`](evidence/): compact evidence summaries and provenance
- [`staging/observations.yaml`](staging/observations.yaml): observations awaiting crystallization

## Code

Executable source code, scripts, and tests are maintained in
[`ChonghaoCheng/Diffusion_NUC`](https://github.com/ChonghaoCheng/Diffusion_NUC).

Large raw datasets, checkpoints, and generated result directories are intentionally not stored
in this repository. Evidence summaries retain their originating result paths and interpretation
boundaries.
