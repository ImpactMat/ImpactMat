# ImpactMat

Official implementation of [ImpactMat](https://arxiv.org/abs/2610.07061):
continuous material estimation for inverse impact sound rendering.

[Project page](https://impactmat.github.io/ImpactMat/) ·
[Paper](https://arxiv.org/abs/2610.07061) ·
[Checkpoint release (947 MB)](https://github.com/ImpactMat/ImpactMat/releases/tag/v1.0.0)

This repository combines the ImpactMat material-parameter estimator with the
modal impact-sound renderer and dataset-generation pipeline used by the project.

## Repository Layout

- `apcl/`: audio-to-material parameter estimator
- `renderer/`: mesh voxelization, FEM modal analysis, dataset generation, and
  modal impact-sound synthesis
- `configs/`: paper-model training configuration
- `checkpoints/`: paper checkpoint download instructions
- `docs/`: estimator inference and evaluation documentation

Renderer setup and examples are documented in
[`renderer/README.md`](renderer/README.md).

## Estimator

The estimator predicts continuous material parameters from one or more impact
sounds. Training code is included for reproducibility, while the easiest reuse
path is checkpoint-only inference:

1. Clone this repository.
2. Download the released checkpoint into `checkpoints/`.
3. Set up a `uv` environment.
4. Run `impactmat-infer-wavs` on your wav files.

## Quick Inference

Install `uv`, then from the repo root:

```bash
uv venv --python 3.10
source .venv/bin/activate
uv pip install torch --index-url https://download.pytorch.org/whl/cu121
uv pip install -e .
```

For CPU-only inference, use the CPU PyTorch wheel instead:

```bash
uv pip install torch --index-url https://download.pytorch.org/whl/cpu
uv pip install -e .
```

Download checkpoint files into [`checkpoints/`](checkpoints/README.md):

```text
checkpoints/
  Cnn14_mAP=0.431.pth
  impactmat.pt
```

Then run:

```bash
impactmat-infer-wavs \
  --checkpoint checkpoints/impactmat.pt \
  --wav /path/to/impact.wav \
  --object-id sample_object \
  --output-dir inference_output \
  --device cuda \
  --pann-repo-root /path/to/audioset_tagging_cnn \
  --pann-checkpoint-path checkpoints/Cnn14_mAP=0.431.pth
```

Outputs are written as:

- `predictions.csv`
- `predictions.json`

See [`docs/inference.md`](docs/inference.md) for manifest input,
multi-hit inference, and CPU/GPU notes.

## Released Model

| Checkpoint | Config | Description |
| --- | --- | --- |
| [`Download impactmat.pt (947 MB)`](https://github.com/ImpactMat/ImpactMat/releases/download/v1.0.0/impactmat.pt) | `configs/impactmat/default.json` | Paper model trained with single- and blended-material supervision |

> **Download notice:** the checkpoint link starts the 947 MB download
> immediately. Visit the [v1.0.0 release page](https://github.com/ImpactMat/ImpactMat/releases/tag/v1.0.0)
> first if you want to review the file details and SHA-256 checksum.

`Single` and `Blend` in the paper are evaluation subsets of this same model,
not separate checkpoints. The public checkpoint corresponds to the paper model
used for the main results in Tables 2--4.

The prediction target is:

```text
[log_E, log_rho, nu, log_alpha, log_beta]
```

## Renderer And Dataset Generation

The included renderer converts a 3D mesh and continuous material parameters
`[rho, E, nu, alpha, beta]` into an impact waveform. It is the forward process
paired with the estimator: the estimator predicts material parameters from
audio, and the renderer applies those parameters to a target object.

```text
OBJ mesh -> voxel grid -> FEM modal analysis -> modal features -> impact WAV
               geometry      rho, E, nu          alpha, beta + strike
```

The pipeline supports the eight base materials as well as arbitrary continuous
parameters. To generate a dataset, run it across object meshes and material
settings, then vary the strike index, timing, and gain to create multiple impact
recordings for each object-material condition. Each run can retain the voxel
grid, FEM modes, modal features, and rendered WAVs as reproducible artifacts.

Quick start on a built-in box:

```bash
cd renderer
uv sync
uv run python examples/quickstart.py
```

For an OBJ stored at `renderer/data/meshes/6/model.obj`:

```bash
uv run python -m impactmat.cli all --obj-id 6
uv run python -m impactmat.cli render \
  --obj-id 6 \
  --params 7850 2e11 0.29 5 3e-8 \
  --out custom.wav
```

The `all` command writes the voxel grid, modal data, features, and synthesized
audio under `renderer/outputs/<obj_id>/`. See the
[`renderer` guide](renderer/README.md) for CPU/CUDA voxelization, configuration,
material presets, and Python API examples.

## Training And Evaluation

Training and evaluation are documented in
[`docs/training.md`](docs/training.md). To train the paper model:

```bash
bash scripts/train_impactmat.sh
```

Evaluate ObjectFolder or RealImpact outputs:

```bash
python scripts/evaluate_real_world_objects.py \
  --dataset objectfolder \
  --root /path/to/ObjectFolder_Real \
  --material-csv /path/to/object_materials.csv \
  --run-dir /path/to/apcl_run_dir \
  --checkpoint-name best.pt \
  --output-dir objectfolder_predictions \
  --device cuda \
  --k 8
```

For ad-hoc wav inference, prefer `impactmat-infer-wavs`.
