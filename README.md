

📄 Paper             🎥 Video              💥 Failure Gallery

# VERAGMIL: Virtual Environment for Robot‑Assisted Feeding of Granular Foods with Imitation Learning Models

> **Repository accompanying the IROS 2025 paper**  
> Amanuel Ergogo, Diego Dall’Alba, Przemyslaw Korzeniowski  
> *VERAGMIL: Virtual Environment for Robot‑Assisted Feeding of Granular Foods with Imitation Learning Models*

This repository contains the simulation assets, data collection interface, training pipelines, and evaluation tools used in the study. VERAGMIL combines a high‑fidelity **Isaac Sim** environment with an **Oculus Quest 2** virtual‑reality interface to enable Learning‑from‑Demonstration (LfD) of challenging granular‑food manipulation tasks such as scooping and transporting rice, buckwheat, or pasta.

---

## Table of Contents
1. [Features](#features)
2. [Quick Start](#quick-start)
3. [Installation](#installation)
4. [Repository Layout](#repository-layout)
5. [Collecting Demonstrations](#collecting-demonstrations)
6. [Training Imitation‑Learning Policies](#training-imitation-learning-policies)
7. [Evaluation and Reproduction of Paper Results](#evaluation-and-reproduction-of-paper-results)
8. [Dataset Details](#dataset-details)
9. [Troubleshooting](#troubleshooting)
10. [Citation](#citation)
11. [License](#license)

---

## Features
- **Photo‑realistic granular simulation** powered by NVIDIA PhysX 5 (GPU) inside Isaac Sim 2024.1.  
- **Stand‑alone VR interface** streaming 6‑DoF poses and RGB‑D images at 100 Hz with zero external dependencies beyond SteamVR.  
- **Multiple robot embodiments**, including **UFactory xArm 7** and **Unitree G1**, with spoon or fork end‑effectors.  
- **Plug‑and‑play Learning‑from‑Demonstration back‑ends** (Behavioral Cloning, BC‑RNN, BCQ) via the [IsaacLab](https://github.com/NVIDIA-Omniverse/IsaacLab) API.  
- **Comprehensive metrics** for success rate, spillage, task time, and generalisation to unseen food items.

---

## Quick Start
```bash
# Clone and enter the repository
$ git clone https://github.com/AmanuelErgogo/VERAGMIL.git
$ cd VERAGMIL

# Create the Conda environment (Python 3.10)
$ conda env create -f env/veragmil.yml
$ conda activate veragmil

# (Optional) Install Isaac Sim 2024.1 hotfix1
$ ./scripts/install_isaac.sh   # ~6 GB download

# Launch a ten‑episode benchmark with pre‑trained BCQ model
$ python scripts/eval.py --cfg configs/bcq_xarm7.yaml \
                        --checkpoint weights/bcq_rice.ckpt
```
The above command opens an Isaac Sim window, executes the scooping task, and prints summary statistics.

---

## Installation
### Prerequisites
| Software | Version | Notes |
|----------|---------|-------|
| Ubuntu | 20.04 LTS or 22.04 LTS | Isaac Sim officially supports these distributions |
| NVIDIA Driver | ≥ 535.xx | Required for GPU PhysX |
| CUDA Toolkit | 12.2 | Installed automatically with Isaac Sim |
| Conda | ≥ 4.12 | Miniconda or Anaconda |
| SteamVR | Latest stable | Only necessary for VR demonstration collection |
| Oculus (Meta) Integration | Quest 2 / Quest 3 | On the same network |

### Step‑by‑step
1. **Install Isaac Sim** using `scripts/install_isaac.sh` or the official launcher.  
2. **Export** the environment variable `ISAAC_PATH=/opt/isaac-sim` (adapt if different).  
3. **Build the Omniverse extension**:
   ```bash
   $ ./scripts/build_extension.sh  # compiles the VERAGMIL .so plugin
   ```
4. **Verify** the installation by running
   ```bash
   $ python scripts/diagnostics/smoke_test.py
   ```
   You should observe a single rice particle falling under gravity.

---

## Repository Layout
```
VERAGMIL/
├── env/                 # Conda and Docker environment specs
├── isaac_ext/           # Custom Omniverse extension for granular foods
├── veragmil/            # Python API: vr_interface, datasets, training, eval
│   ├── assets/          # USD robot models, meshes, textures
│   ├── foods/           # USDA‑parameterised granular food definitions
│   ├── policies/        # BC, BC‑RNN, BCQ model definitions
│   ├── metrics/         # Success, spillage, timing, generalisation
│   └── utils/
├── configs/             # YAML experiment configurations
├── scripts/             # CLI entry points (collect, train, eval, vis)
└── weights/             # Pre‑trained checkpoints (downloaded on demand)
```

---

## Collecting Demonstrations
### Virtual‑Reality Mode (recommended)
```bash
$ python scripts/collect.py --cfg configs/vr_rice.yaml \
                           --episodes 30 \
                           --output data/vr_rice
```
Move the controller like a spoon; the trigger presses the “record” button. RGB‑D video and robot states are stored in `data/vr_rice/`.

### 3‑D Space‑Mouse Mode
```bash
$ python scripts/collect.py --cfg configs/3dm_buckwheat.yaml \
                           --device spacemouse
```

---

## Training Imitation‑Learning Policies
```bash
$ python scripts/train.py --cfg configs/bc_rnn.yaml \
                          dataset.path data/vr_rice \
                          trainer.gpus 1
```
All hyper‑parameters correspond to those reported in Table II of the paper. Checkpoints and TensorBoard logs are saved to `outputs/{date_time}`.

---

## Evaluation and Reproduction of Paper Results
```bash
# Evaluate on unseen food items (peas, beans, flakes)
$ python scripts/eval.py --cfg configs/bcq_generalisation.yaml \
                        --checkpoint weights/bcq_all.ckpt

# Re‑create every figure and table in the manuscript
$ bash scripts/reproduce_all.sh   # ≈3 hours on RTX 4090
```
The reproduction script will generate CSV results and visualisations under `outputs/final_results/`.

---

## Dataset Details
| Split | Episodes | Materials | Acquisition | Size |
|-------|----------|-----------|-------------|------|
| `train` | 120 | Rice, Buckwheat, Barley, Pasta | VR & 3DM | 17 GB |
| `test`  |  60 | Rice, Buckwheat, Barley, Pasta | VR & 3DM |  8 GB |
| `unseen` |  45 | Peas, Beans, Flakes | VR |  6 GB |

Run `python scripts/download_dataset.py` to pull the data automatically from our public S3 bucket.

---

## Roadmap & Planned Integrations
The following milestones describe how VERAGMIL will expand to support **LeRobotDataset** and additional policy families—**ACT**, **Diffusion Policies**, **Pi0**, and **Groot**.

| Milestone | Target Release | Key Additions |
|-----------|----------------|---------------|
| **v0.2** | May 2025 | • Streaming loader for **LeRobotDataset**<br>• Conversion scripts to VERAGMIL/IsaacLab format |
| **v0.3** | June 2025 | • Native training/evaluation pipelines for **Action Chunking Transformer (ACT)** and **Diffusion Policy**<br>• Sample configs & checkpoints |
| **v0.4** | July 2025 | • Integration of **Pi0** and **Groot** hierarchical models<br>• Unified benchmark harness comparing BC/BCQ/ACT/Diffusion/Pi0/Groot |
| **v1.0** | September 2025 | • Stable plugin API<br>• Comprehensive docs & tutorials<br>• IROS camera‑ready artefacts |

### Technical Tasks
- **LeRobotDataset**
  1. Implement `veragmil.datasets.lerobot.LerobotDataModule` adhering to IsaacLab’s interface.
  2. Provide one‑click download via `scripts/download_lerobot.py`.
  3. Add unit tests for sequence alignment and modality mapping (RGB‑D, proprioception).
- **Model Zoo Extensions**
  - **ACT**: Port reference PyTorch code, wrap into `veragmil.policies.act.ACTPolicy`.
  - **Diffusion**: Integrate [diffusion‑policy‑torch] backend with spoon trajectory conditioning.
  - **Pi0 & Groot**: Expose via `veragmil.policies.pi0` and `veragmil.policies.groot`, including pretrained weights.
- **Benchmark Automation**
  - Extend `scripts/eval.py` with `--policy {bcq,act,diffusion,pi0,groot}` selector.
  - Add Hydra sweeps for hyper‑parameter search across model families.

> *Progress is tracked on the [project board](https://github.com/AmanuelErgogo/VERAGMIL/projects/1). Community contributions are welcome—feel free to open PRs!*  

---

## Troubleshooting
| Symptom | Possible cause | Fix |
|---------|----------------|-----|
| Isaac Sim window is black | Missing NVIDIA driver or mismatched CUDA | Verify driver ≥ 535.xx and reboot |
| Low VR frame‑rate | SteamVR running in power‑save mode | Disable motion smoothing; ensure GPU is set to “Prefer Maximum Performance” |
| `IsaacSimPython` cannot be found | `ISAAC_PATH` not exported | `export ISAAC_PATH=/absolute/path/to/isaac-sim` |

---

## Citation
```bibtex
@inproceedings{ergogo_2025_veragmil,
  author    = {Amanuel Ergogo and Diego Dall’Alba and Przemyslaw Korzeniowski},
  title     = {{VERAGMIL}: Virtual Environment for Robot‑Assisted Feeding of Granular Foods with Imitation Learning Models},
  booktitle = {Proceedings of the IEEE/RSJ International Conference on Intelligent Robots and Systems},
  year      = {2025},
  doi       = {10.XXXX/iros.2025.4500}
}
```

---

## License
Code is released under the **MIT License**. All simulation assets and food meshes are provided under **CC‑BY‑NC‑4.0** licences. Commercial use of the assets requires written permission from the authors.

---

For questions, please open an issue or contact **amanuel.ergogo@gmail.com**.

