# Yeast promoter design using Reinforcement Learning 
#### *Using TACO and running on Google Colab*

This project runs the published **TACO method**<sup>1</sup> on yeast promoters. [TACO](https://github.com/yangzhao1230/TACO) teaches a DNA-writing network to design new regulatory sequences by reinforcement learning, using a scoring model that predicts how active a sequence is. This current project of mine is a small personal attempt towards learning PyTorch<sup>2</sup> and is built around existing code. A separate *checker* network, trained here, is planned as an extra control.

**Reinforcement learning** is a way of training in which a network tries something, receives a score (the reward), and shifts toward what scored well. In this project the *writer* writes a DNA sequence letter by letter, a frozen *judge* model scores it, and the writer is updated<sup>1</sup>.

## Repository structure and data sources

| Path | What it is | Source |
|---|---|---|
| `README.md` | Description, setup, notes and references | General Repo file |
| `.gitignore` | Files that Git should not track | General Repo file |
| `LICENSE` | MIT licence for the code of this repository | General Repo file |
| `00_requirements.txt` | Package versions | Dependencies for running the notebooks |
| `00_requirements_taco.txt` | Package versions | Dependencies for running the TACO env |
| `01_Yeast_promoter_analysis.ipynb` | The notebook analysis (Part 1: setup on Colab) | - |
| `02_Checker_Neural_Networks.ipynb` | The notebook analysis (Part 2: setup locally) | - |
| `results/` | Result tables written by the notebook | Product of the Notebook |

#### Google Drive directory structure

```text
Reinforcement_Learning_for_Promoter_Design_colab/
  taco_code/TACO-main.zip   TACO Source Code
  model_files/              regLM Source Code
  data/                     starting sequences, reward files, yeast table
  results/                  outputs of each run
```
*TACO Source code downloaded from the [TACO GitHub Repository](https://github.com/yangzhao1230/TACO)*, *regLM Source Code downloaded from the [regLM record on Zenodo](zenodo.org/records/12668907)*

## Setup

The heavy part runs on Google Colab, because the laptop has no graphics card from NVIDIA. TACO's own code reads files from the authors' computer, so the notebook finds those paths and they are changed in a working copy.

1. Create a folder called `Reinforcement_Learning_for_Promoter_Design_colab` in Google Drive, with the subfolders `taco_code`, `model_files`, `data` and `results`. Upload `TACO-main.zip` into `taco_code`.
2. In Colab, open the notebook from GitHub (File, then Open notebook, then the GitHub tab).
3. Choose a graphics card: Runtime, then Change runtime type, then pick a card (a Tesla T4 was given on 2026-11-13).
4. Run the notebook from top to bottom. Colab asks permission to reach Drive.

[to add later: local setup with a virtual environment in Visual Studio Code, if any step is run on the laptop]

## RESULTS & CONCLUSIONS

[to add later]

## Notes

- The TACO README asks for Python 3.9, torch 2.1 and pytorch_lightning 1.8.6<sup>1</sup>. The Colab session used here has Python 3.13. Whether the old packages install on it is not tested here.
- TACO's script `reinforce_mbo.sh` calls a file `debug.py` that is not in the zip. The real script is `reinforce_mbo.py`.
- [to add later]

## NEXT STEPS

## Documentation and versions

### Environment used *[to be updated as I go]*

| Item | Version or detail |
|---|---|
| Operating system | Windows 11 Pro, version 10.0.26300 (build 26300) |
| Google Colab | x |
| Python | 3.12 |
| Visual Studio Code | 1.146 |
| Environment creation | Made with the Command Palette command "Python: Create Environment" (Venv, named `.venv`), which gives the same result as step 1 above |
| Date of setup | 2026-11-10 |

### Package versions *[to be updated as I go]*

The exact versions are pinned in `requirements.txt`. They were taken from the working environment after the first successful run:

| Package | Version |
|---|---|
| torch | 2.15.0 (CPU build) |
| medmnist | 3.0.2 |
| numpy | 2.5.4 |
| pandas | 3.1.1 |
| scikit-learn | 1.9.1 |
| matplotlib | 3.11.2 |
| ipykernel | 7.4.0 |

## References

1. Yang Z, Su B, Cao C, Wen JR. Regulatory DNA sequence design with reinforcement learning. In: *International Conference on Learning Representations*. 2025. arXiv:2503.07981. Code (MIT licence): https://github.com/yangzhao1230/TACO
2. Paszke A, Gross S, Massa F, et al. PyTorch: an imperative style, high-performance deep learning library. In: Wallach H, Larochelle H, Beygelzimer A, d’Alché-Buc F, Fox E, Garnett R, eds. Advances in Neural Information Processing Systems 32. Curran Associates, Inc.; 2019:8024-8035.
3. Lal A, Garfield D, Biancalani T, Eraslan G. Designing realistic regulatory DNA with autoregressive language models. *Genome Res*. 2024;34(9):1411-1420. doi:10.1101/gr.279142.124
4. Nguyen E, Poli M, Faizi M, et al. HyenaDNA: long-range genomic sequence modeling at single nucleotide resolution. *Advances in Neural Information Processing Systems*. 2023;36. arXiv:2306.15794. Code: https://github.com/HazyResearch/hyena-dna
