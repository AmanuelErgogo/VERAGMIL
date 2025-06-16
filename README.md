# VERAGMIL: Virtual Environment for Robot‑Assisted Feeding of Granular Foods with Imitation Learning Models

> **Repository accompanying the IROS 2025 paper**  
> Amanuel Ergogo, Diego Dall’Alba, Przemyslaw Korzeniowski  
> *VERAGMIL: Virtual Environment for Robot‑Assisted Feeding of Granular Foods with Imitation Learning Models*

---
<img src="https://github.com/AmanuelErgogo/VERAGMIL/blob/main/IROS25VERAGMIL.gif" width="1500" />
![Alt Text](https://github.com/AmanuelErgogo/VERAGMIL/blob/main/IROS25VERAGMIL.gif)


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
                        --checkpoint weights/bcq.ckpt
```
---

## Installation
### Prerequisites
| Software | Version | Notes |
|----------|---------|-------|
| Ubuntu | 20.04 LTS or 22.04 LTS | Isaac Sim officially supports these distributions |
| NVIDIA Driver | ≥ 535.xx | Required for GPU PhysX |
| CUDA Toolkit | 12.2 | Installed automatically with Isaac Sim |
| Conda | ≥ 4.12 | Miniconda or Anaconda |
| Quest 2 / Quest 3 | On the same network |

### Step‑by‑step
1. **Install Isaac Sim** using `scripts/install_isaac.sh` or the official launcher.  
2. **Export** the environment variable `ISAAC_PATH=/opt/isaac-sim` (adapt if different).  
3. **Build the Omniverse extension**:
   ```bash
   $ ./scripts/build_extension.sh  # compiles the VERAGMIL .so plugin
   ```
4. **Verify** the installation by running
   ```bash
   $ python scripts/scoop_x.py
   ```
---

## Collecting Demonstrations
### Virtual‑Reality Mode (recommended)
```bash
$ python scripts/demonstrate.py --cfg configs/scoop.yaml \
                           --episodes 30 \
                           --output data/vr_scoop
```

### 3‑D Space‑Mouse Mode
```bash
$ python scripts/demonstrate.py --cfg configs/scoop.yaml \
                           --device spacemouse
```

---

## Training Imitation‑Learning Policies
```bash
$ python scripts/train.py --cfg configs/bc_rnn.yaml \
                          dataset.path data/vr_scoop \
```
---

## Roadmap & Planned Integrations
The following milestones describe how VERAGMIL will expand to support **LeRobotDataset** and additional policy families—**ACT**, **Diffusion Policies**, **Pi0**, and **Groot**.

- [ ] Integrate **LeRobotDataset**
- [ ] Integrate generative policies:
  - **Action Chunking Transformer (ACT)**
  - **Diffusion Policy**
- [ ] Integrate foundational models:
  - **Pi0**
  - **smolVLA**

---



