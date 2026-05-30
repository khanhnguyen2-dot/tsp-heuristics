# Traveling Salesman Problem — A Heuristic Approach

> **Group 4.4 · Class DS67B · National Economics University (NEU - College of Technology)**
> **Supervisor:** Assoc. Prof., Dr. Hà Minh Hoàng
> **Members:** Nguyễn Thị Ngọc Khánh · Chu Khánh Ly · Đặng Nguyễn Thảo My

## Overview

This project implements and empirically compares **four heuristic pipelines** for the Traveling Salesman Problem (TSP) on a benchmark suite of **eight TSPLIB instances** spanning four edge-weight conventions and instance sizes from 14 to 7,397 cities.

| Stage | Main method | Reference method |
|---|---|---|
| Constructive | Farthest Insertion (FI) | Nearest Neighbor (NN) |
| Local search | Swap (2-exchange) | 2-opt |

The four resulting pipelines are **FI + Swap** (the assigned main pipeline), **FI + 2-opt**, **NN + Swap**, and **NN + 2-opt**.

**Headline result.** Farthest Insertion + 2-opt is the strongest pipeline on six of the eight instances; on `d2103.tsp` the ranking inverts and NN-based pipelines win by more than fourteen percentage points — a structural anomaly that motivates the diverse benchmark suite.

---

## Project Structure

```
tsp-heuristics/
│
├── README.md                              This file
├── requirements.txt                       Required Python libraries
├── LICENSE                                MIT
│
├── code/
│   └── project_discrete_maths.ipynb       Main notebook (single self-contained file)
│
├── datasets/                              8 TSPLIB benchmark instances
│   ├── burma14.tsp                        14 cities,    GEO
│   ├── gr17.tsp                           17 cities,    EXPLICIT
│   ├── lin105.tsp                         105 cities,   EUC_2D
│   ├── fl417.tsp                          417 cities,   EUC_2D
│   ├── u574.tsp                           574 cities,   EUC_2D
│   ├── pr1002.tsp                         1,002 cities, EUC_2D
│   ├── d2103.tsp                          2,103 cities, EUC_2D
│   └── pla7397.tsp                        7,397 cities, CEIL_2D
│
└── report/
    └── report.pdf                         Full project report (36 pages)
```

---

## Requirements

- Python 3.8 or newer
- Jupyter Notebook or Google Colab

Install Python dependencies:

```bash
pip install -r requirements.txt
```

The `requirements.txt` lists:

```
numpy
scipy
matplotlib
```

---

## How to Run

### Option 1 — Local Jupyter Notebook

1. Clone the repository and `cd` into it:
   ```bash
   git clone https://github.com/khanhnguyen2-dot/tsp-heuristics.git
   cd tsp-heuristics
   ```
2. Install the dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Launch Jupyter and open the notebook:
   ```bash
   jupyter notebook code/project_discrete_maths.ipynb
   ```
4. In the first parameter cell, set the dataset path to point at the bundled `datasets/` folder:
   ```python
   DATASET_DIR = '../datasets/'        # because the notebook lives in code/
   ```
5. Run all cells in order (Cell → Run All).

### Option 2 — Google Colab

1. Upload the entire `tsp-heuristics/` folder to your Google Drive.
2. Open `code/project_discrete_maths.ipynb` in Colab.
3. Mount Google Drive when prompted.
4. Update the `DATASET_DIR` variable to the path of the `datasets/` folder on your Drive, e.g.
   ```python
   DATASET_DIR = '/content/drive/MyDrive/tsp-heuristics/datasets/'
   ```
5. Run all cells in order.

---

## Notebook Outline

The notebook is a single self-contained script organised in the order it should be executed:

1. **Setup** — imports, `DATASET_DIR`, `SEEDS`, and the 8 known optimal tour lengths.
2. **Dataset Information** — summary statistics for every instance.
3. **TSPLIB Parser** — `read_tsp()`.
4. **Distance Matrix Construction** — `build_dist_matrix()`, supporting `EUC_2D`, `EUC_3D`, `CEIL_2D`, `MAN_2D`, `MAX_2D`, `GEO`, `ATT`, and `EXPLICIT` (with six storage formats).
5. **Helpers** — `total_distance`, `is_valid_tour`, `calc_gap`.
6. **Farthest Insertion** — `greedy_farthest_insertion()` with convex-hull multi-start.
7. **Swap Local Search** — `swap_local_search()` with double-bridge restarts.
8. **Validation & FI vs FI+Swap comparison table.**
9. **Nearest Neighbor** — `nearest_neighbor()` multi-start.
10. **2-opt Local Search** — `two_opt_local_search()` with double-bridge restarts.
11. **Cross pairs (FI+2-opt, NN+Swap).**
12. **Final validation & consolidated 4-pair benchmark table.**
13. **Visualisation** — gap vs. instance size for all 4 pipelines.

Total runtime on a Google Colab CPU runtime is approximately **20–25 minutes**, dominated by `pla7397.tsp`.

---

## Expected Output

After running all cells, the final benchmark table prints the gap to optimum for every algorithm pair on every instance:

```
File             N    FI+Swap    FI+2opt    NN+Swap    NN+2opt       Best
burma14.tsp     14       0.0%       0.0%    0.6921%    0.3912%       0.0%
gr17.tsp        17       0.0%       0.0%       0.0%       0.0%       0.0%
lin105.tsp     105    1.9473%     1.537%   13.6101%    3.3521%     1.537%
fl417.tsp      417    2.8244%    2.3944%   15.6901%    6.2474%    2.3944%
u574.tsp       574     7.392%    5.4491%   20.2439%   11.6136%    5.4491%
pr1002.tsp    1002     8.341%    6.8563%   19.8487%   14.9302%    6.8563%
d2103.tsp     2103   21.9229%    21.642%    7.3151%     7.028%     7.028%
pla7397.tsp   7397   12.9524%   11.8125%   20.4109%   20.3119%   11.8125%
```

A separate validation table confirms that every produced tour passes all three feasibility tests (length = N, no duplicates, valid city ids).

---

## Reproducibility

All stochastic components are seeded with values from `SEEDS = [11, 22, 33, 44, 55]`. The protocol is adapted to instance size:

- 5 seeds for instances with `N <= 1000`
- 3 seeds for `1000 < N <= 5000`
- 1 seed (`[11]`) for `N > 5000`

Running the notebook end-to-end on a fresh runtime with the same seeds reproduces the numbers above to the last decimal.

---

## Dataset Source

All eight benchmark instances are taken unchanged from the **TSPLIB** library:

> Reinelt, G. (1991). *TSPLIB — A traveling salesman problem library.* ORSA Journal on Computing, 3(4), 376–384.

Official site: <http://comopt.ifi.uni-heidelberg.de/software/TSPLIB95/>

The best-known optimal tour lengths used to compute gaps (`OPTIMAL_VALUES` in the notebook) are taken from the TSPLIB best-known-solutions file.

---

## Report

The full report (`report/report.pdf`, 36 pages) presents the methodology, full Python code listings, experimental results, a critical discussion section (including the `d2103.tsp` anomaly), and lessons learned in algorithmic complexity and heuristic design. Readers interested in *why* the algorithms behave as they do should consult the report; this README only describes *how* to run the code.

---

## License

Released under the MIT License. See `LICENSE` for details.
