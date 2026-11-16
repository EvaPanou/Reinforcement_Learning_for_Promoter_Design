# Yeast promoter design using Reinforcement Learning 
#### *Using TACO and running on Google Colab*

This project runs the published **TACO method**<sup>1</sup> on yeast promoters. [TACO](https://github.com/yangzhao1230/TACO) teaches a DNA-writing network to design new regulatory sequences by reinforcement learning, using a scoring model that predicts how active a sequence is. This current project of mine is a small personal attempt towards learning PyTorch<sup>2</sup> and is built around existing code. A separate *checker* network, trained here, is planned as an extra control.

**Reinforcement learning** is a way of training in which a network tries something, receives a score (the reward), and shifts toward what scored well. In this project the *writer* writes a DNA sequence letter by letter, a frozen *judge* model scores it, and the writer is updated<sup>1</sup>.

Two notebooks make up the project:

`01_Yeast_promoter_analysis.ipyn` runs TACO on yeast promoters (task complex) with my own settings: writer = regLM yeast_reglm.ckpt, reward model = regLM paired, checker model = regLM separate, motif weight 0, 100 rounds with 256 sequences per round, seeds 0, 1 and 42. It needs a graphics card and runs on Google Colab.
`02_Checker_Neural_Networks.ipynb` trains two checker networks of my own (a convolutional network and a transformer) on native yeast promoters, and uses them to score the sequences that TACO designed. It needs no graphics card and also runs on Google Colab.

## Repository structure 

| Path | What it is | Source |
|---|---|---|
| `README.md` | Description, setup, notes and references | General Repo file |
| `.gitignore` | Files that Git should not track | General Repo file |
| `LICENSE` | MIT licence for the code of this repository | General Repo file |
| `00_requirements.txt` | Package versions | Dependencies for running the notebooks |
| `00_requirements_taco.txt` | Package versions | Dependencies for running the TACO env |
| `01_Yeast_promoter_analysis.ipynb` | The notebook analysis (Part 1: setup on Colab) | - |
| `02_Checker_Neural_Networks.ipynb` | The notebook analysis (Part 2: setup locally) | - |
| `figures/` | Result plots and figures | Product of the Notebook |

## Data sources and results location
No data and no result files are stored in this repository. The native promoter table is downloaded from Zenodo by the notebooks. The model files are downloaded from Zenodo into Google Drive. 

- The Native Yeast Promoters were downloaded from the `Native_complex.csv file` [Zenodo record 4436477](https://zenodo.org/records/4436477).
- The TACO Source code was downloaded from the [TACO GitHub Repository](https://github.com/yangzhao1230/TACO). 
- The regLM Source Code downloaded from the [regLM archive on Zenodo](zenodo.org/records/12668907).
- The HyenaDNA base model code was found in the [HyenaDNA Repository](https://github.com/HazyResearch/hyena-dna).

The TACO result tables (about 3 megabytes per seed) and the trained checker networks stay in Google Drive, to save space, and either the link or the tables themselves will be added <here> shortly. Only a few small pictures, saved by the notebooks into the Drive folder results/figures, were copied by hand into `figures/` of this repository. The tables of numbers in this README are summaries copied from the notebook outputs.

#### Google Drive directory structure

```text
Reinforcement_Learning_for_Promoter_Design/
  taco_code/TACO-main.zip   TACO source code
  model_files/              regLM model files
  data/                     starting sequences, native promoter table
  results/                  TACO result tables, checker network files
```

## Setup

The heavy part runs on Google Colab, because the laptop has no graphics card from NVIDIA. TACO's own code reads files from the authors' computer, so the notebook finds those paths and they are changed in a working copy.

1. Create a folder called `Reinforcement_Learning_for_Promoter_Design_colab` in Google Drive, with the subfolders `taco_code`, `model_files`, `data` and `results`. Upload `TACO-main.zip` into `taco_code`.
2. In Colab, open the notebook from GitHub (File, then Open notebook, then the GitHub tab).
3. Choose a graphics card: Runtime, then Change runtime type, then pick a card (a Tesla T4 was given on 2026-11-13).
4. Run the notebook from top to bottom. Colab asks permission to reach Drive.

[to add later: local setup with a virtual environment in Visual Studio Code, if any step is run on the laptop]

## RESULTS & CONCLUSIONS

#### **Notebook 1**: TACO on yeast promoters (three seeds)

TACO starts from 200 weak native promoters (expression below 5.391858; 554 weak promoters exist in the table of 3929). In each round the writer writes 256 sequences, the reward model scores them, and the writer is updated. The checker model (a second regLM model that is not used for the updates) scores the same sequences. top is the mean score of the 16 best sequences of a round, fitness the median of the best 128; both scores are scaled by TACO between a minimum and a maximum, so values above 1 are possible.

TACO scores over the rounds for three seeds

Last round (100) over the three seeds, mean and standard deviation:

Measure	Mean	Standard deviation
top (reward model)	1.078	0.012
fitness (reward model)	1.004	0.006
top (checker model)	1.064	0.009
fitness (checker model)	1.022	0.009
diversity	53.667	0.577
novelty	54.167	0.289
All three seeds rise in the same way, and the checker model follows the reward model. The checker model's rise flattens after about round 80.
Diversity (median number of positions at which two of the best 128 sequences differ) falls from about 59 at round 10 to about 54 at round 100. Novelty (median distance to the closest starting sequence) stays between 52 and 54.5 positions out of 80.
Looking into the result file of seed 0: the best sequence of round 1 already differs from its closest starting sequence at 48 of 80 positions, that of round 50 at 53, and that of round 100 at 54. The designs do not start from the starting sequences and change them step by step. They are written by the writer, and the starting sequences only fill its memory at the start.


#### **Notebook 2**: my own checker networks

The 3929 native promoters were split at random (seed 0) into 3143 training, 392 validation and 394 test promoters. Always predicting the training mean gives a mean absolute error of 2.61 (validation) and 2.75 (test).

Distribution of native expression values

Test split, three training seeds (0, 1, 42):

Network (learnable numbers)	Mean absolute error	Pearson correlation	Spearman correlation
Convolutional network (76353)	0.699 (standard deviation 0.029)	0.955	0.959
Transformer (72449)	0.806 (standard deviation 0.012)	0.940	0.940
Baseline: training mean	2.753	-	-

Training curves of both networks

Limit of these numbers. For every test promoter, a training promoter differs at only 1 or 2 of the 80 positions (1 position for 324 of 394 test promoters, 82.2 percent). The test numbers therefore describe close variants of sequences the networks have seen, not new sequences. A split by groups of related sequences has not been made yet.

Checking TACO's designs (three TACO seeds, 76800 sequences). The weak starting sequences score about 4.6 with both networks. The table shows the mean predicted expression per round, with the smallest and largest value over the three TACO seeds in brackets:

Round	Convolutional network	Transformer
1	9.78 (9.69 to 9.90)	10.80 (10.76 to 10.84)
10	10.15 (10.11 to 10.23)	10.57 (10.49 to 10.66)
30	11.04 (10.86 to 11.24)	10.92 (10.50 to 11.18)
50	12.16 (12.04 to 12.34)	11.13 (10.89 to 11.32)
70	13.12 (12.93 to 13.23)	11.10 (10.98 to 11.26)
100	14.53 (14.43 to 14.65)	11.02 (10.95 to 11.09)

Mean predicted expression per TACO round, three seeds

Agreement with the score of TACO's own checker model (Spearman correlation with oracle_score), for each TACO seed (0, 1, 42):

Network	Over all rounds together	Inside single rounds
Convolutional network	0.646, 0.632, 0.625	0.383, 0.386, 0.371
Transformer	0.060, 0.054, 0.041	0.030, 0.041, 0.023
Both of my networks agree with TACO that the designs lie far above the starting sequences (about 4.6 against 10 to 15).
The convolutional network also sees a steady improvement over the rounds. The transformer sees a small rise in the first 50 rounds and then no change.
The convolutional network's agreement over all rounds together (about 0.63) is larger than inside single rounds (about 0.38), so a part of it comes from both rising over the rounds. The transformer shows almost no agreement with TACO's checker model in either way. Why is not tested here.
A mean prediction of 14.5 lies near the upper end of the native values, so these predictions may be extrapolations. They are not expression levels measured in a cell.
In the printed examples, the starting sequences contain about 40 percent G and C, and the designed sequences about 66 to 79 percent. This was only counted by eye on eight printed sequences. A count over all designs is not done yet.

## NEXT STEPS

- Notebook 2: split the native promoters into training, validation and test sets by groups of related sequences, so that no test promoter has a near-copy in the training set, and compare with the random split.
- Notebook 2: measure how far the designed sequences lie from the training sequences, and count the letters of the designs (for example the share of G and C per round) to see what the writer changes.
- Find out why the transformer does not follow the rise that the convolutional network and TACO's two models see.

## Documentation and versions

### Environment used *[to be updated as I go]*

| Item | Version or detail |
|---|---|
| Platform | 	Google Colab |
| Graphics card (notebook 1) | Tesla T4 (2026-11-15) |
| Python on Colab - Notebook 1 | 3.13.16 |
| Python on Colab - Notebook 2 | 3.13.18 |
| Python in taco_env | 3.9.25 |
| Date of setup | 2026-11-10 |

### Package versions *[to be updated as I go]*

The exact versions are pinned in `requirements.txt`. They were taken from the working environment after the first successful run:

| Package | Colab Version | taco_env |
|---|---|
| torch | 2.14.1 (processor-only build) | 2.1.0+cu121 |
| numpy | 2.4.6 | 1.26.4 |
| pandas |	2.2.3 | - | 
| scipy | 1.16.3 | - | 
| matplotlib | 3.10.0 | - | 
| pytorch-lightning | - | 1.8.6 | 
| transformers | - | 4.55.4 | 
| FlashAttention | - | 	2.5.9.post1 | 

## References

1. Yang Z, Su B, Cao C, Wen JR. Regulatory DNA sequence design with reinforcement learning. In: International Conference on Learning Representations. 2025. arXiv:2503.07981. Code (MIT licence): https://github.com/yangzhao1230/TACO
2. Paszke A, Gross S, Massa F, et al. PyTorch: an imperative style, high-performance deep learning library. In: Wallach H, Larochelle H, Beygelzimer A, d’Alché-Buc F, Fox E, Garnett R, eds. Advances in Neural Information Processing Systems 32. Curran Associates, Inc.; 2019:8024-8035.
3. Lal A, Garfield D, Biancalani T, Eraslan G. Designing realistic regulatory DNA with autoregressive language models. *Genome Res*. 2024;34(9):1411-1420. doi:10.1101/gr.279142.124
4. Nguyen E, Poli M, Faizi M, et al. HyenaDNA: long-range genomic sequence modeling at single nucleotide resolution. *Advances in Neural Information Processing Systems*. 2023;36. arXiv:2306.15794. Code: https://github.com/HazyResearch/hyena-dna
