# QUOPS with CUDA-Q

This directory implements the mirror-circuit fidelity estimation (MCFE) workflow in [*Benchmarking the computational power of quantum computers*](https://arxiv.org/html/2609.12146v1). The main notebook estimates QUOPS circuit sizes and simulator throughput.

| File or directory | Purpose |
| --- | --- |
| [quops.ipynb](quops.ipynb) | Main workflow: BR/RR/REF measurements, polarization, shape tests and plots. |
| [src.py](src.py) | Circuit generation, randomized compilation, analysis, plotting and result storage. |
| [requirements.txt](requirements.txt) | Pinned direct dependencies and Jupyter tools. |
| [HARDWARE_GUIDE.md](HARDWARE_GUIDE.md) | Best practices for adapting and running QUOPS on quantum hardware. |
| [logical_quops.ipynb](logical_quops.ipynb) | BR synthesis, encoded-resource estimates and ideal Steane simulator launches. |
| [plots/](plots/) | Saved example figures. |

## Run the notebook

Use **Linux x86-64 with Python 3.12**. The default target is `density-matrix-cpu`; no GPU is required. From this directory:

```bash
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python -m pip check
python -m ipykernel install --sys-prefix --name quops --display-name "Python (QUOPS)"
python -m jupyterlab quops.ipynb
```

Select **Python (QUOPS)** and run the cells in order. Keep `src.py` and `plots/` beside the notebook. Let acquisition finish before running analysis. Raw counts, targets, observations and settings are saved under `results/`.


## Logical example

`logical_quops.ipynb` imports one BR kernel and lowers it through P0 synthesis, P1 placement, P2 encoded operations and P3 physical compilation. A separate P2 resource model adds syndrome extraction after each T/T-dagger. The executed realization has no syndrome extraction or recovery.

Use Linux x86-64/Python 3.12 with this CUDA-Q Logical preview image; the physical notebook's `cudaq==0.16.0` environment does not provide this workflow:

```text
nvcr.io/nvidia/nightly/cuda-quantum@sha256:b5a168d9b90af764a8bf13e1fec9d3ca7633276476f23b62e3e228e83fcdd096
```

The tested preview reports CUDA-Q commit `9867ab91f0cc5500b7b39054b9604e1806c317f7` and `cudaq-logical==0.1`. It was checked with NumPy 2.2.6, pandas 3.0.5 and Matplotlib 3.11.2. Add pandas and Jupyter tools if needed; keep the preview's CUDA-Q installation. No GPU is required. For a container limited to two CPU cores, set `OMP_NUM_THREADS=2` and `OPENBLAS_NUM_THREADS=2` before starting Jupyter.

All ten code cells completed from a fresh kernel on 6 November 2026. The final cell uses the private `_kernel_from_quake` adapter to launch the 15-carrier circuit four times on `qpp-cpu`. The entry point returns no logical bits. These launches do not verify decoded logical outcomes or produce a logical QUOPS score. The bare magic-state supply is ideal and excludes distillation and delivery costs. The private adapter makes this example dependent on the pinned preview.
