# Environment Probe: 2026-08-23

## Hardware

Command: `nvidia-smi`

```text
GPU 0: Tesla V100-PCIE-32GB
GPU 1: Tesla V100-PCIE-32GB
Memory: 187 GiB total, 174 GiB available
Workspace storage: 4.8 PB available
```

## Current PyTorch CUDA Smoke Test

Command: create a CUDA tensor with `/data/chocheng/.venvs/lerobot/bin/python3`.

```text
torch 2.11.0+cu128
GPU compute capability: 7.0
Wheel architecture list: sm_75, sm_80, sm_86, sm_90, sm_100, sm_120
torch.AcceleratorError: CUDA error: no kernel image is available for execution on the device
```

## Missing Dependencies in Existing Environment

```text
scipy: missing
trimesh: missing
matplotlib: missing
open3d: missing
potpourri3d: missing
igl: missing
meshio: missing
torch_geometric: missing
```
