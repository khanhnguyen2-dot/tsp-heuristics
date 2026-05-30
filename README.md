# Traveling Salesman Problem — A Heuristic Approach

**Group 4.4** · Class **DS67B** · National Economics University (NEU – College of Technology)
Members: **Nguyễn Thị Ngọc Khánh** · **Chu Khánh Ly** · **Đặng Nguyễn Thảo My**
Supervisor: Assoc. Prof. Dr. **Hà Minh Hoàng** · Hanoi, May 2026

---

## 1. Project Overview

This project implements and empirically compares four heuristic pipelines for the Traveling Salesman Problem on a benchmark suite of **8 TSPLIB instances** spanning 14 to 7,397 cities and four edge-weight conventions (`EUC_2D`, `CEIL_2D`, `GEO`, `EXPLICIT`):

| | **Swap** (local search) | **2-opt** (local search) |
|---|---|---|
| **Farthest Insertion** (constructive) | **FI + Swap** — assigned main pipeline | FI + 2-opt |
| **Nearest Neighbor** (constructive) | NN + Swap | NN + 2-opt |

**Headline result.** FI + 2-opt is the strongest pipeline on six of the eight instances. On `d2103.tsp` the ranking inverts and NN-based pipelines win by more than fourteen percentage points — an instance-specific anomaly that motivates the diverse benchmark suite.

---

## 2. Repository Layout

```
tsp-heuristics/
│
├── README.md                              this file
├── requirements.txt                       Python dependencies
├── LICENSE                                MIT
│
├── code/
│   └── project_discrete_maths.ipynb       main self-contained notebook
│
├── datasets/                              8 TSPLIB instances
│   ├── burma14.tsp     (14,    GEO)
│   ├── gr17.tsp        (17,    EXPLICIT)
│   ├── lin105.tsp      (105,   EUC_2D)
│   ├── fl417.tsp       (417,   EUC_2D)
│   ├── u574.tsp        (574,   EUC_2D)
│   ├── pr1002.tsp      (1002,  EUC_2D)
│   ├── d2103.tsp       (2103,  EUC_2D)
│   └── pla7397.tsp     (7397,  CEIL_2D)
│
└── report/
    └── report.pdf                         full 36-page report
```

---

## 3. Requirements

Python 3.8 or newer, plus the libraries listed in `requirements.txt`:

```
numpy
scipy
matplotlib
```

Install with:

```bash
pip install -r requirements.txt
```

---

## 4. How to Reproduce

### 4.1. Local Jupyter

```bash
git clone https://github.com/khanhnguyen2-dot/tsp-heuristics.git
cd tsp-heuristics
pip install -r requirements.txt
jupyter notebook code/project_discrete_maths.ipynb
```

Before running, set the first parameter cell:

```python
DATASET_DIR = '../datasets/'
```

Then run all cells in order (Cell → Run All). Total runtime is approximately **20–25 minutes** on a modern laptop, dominated by `pla7397.tsp`.

### 4.2. Google Colab

Upload the whole `tsp-heuristics/` folder to your Google Drive, open the notebook in Colab, mount Drive when prompted, and set:

```python
DATASET_DIR = '/content/drive/MyDrive/tsp-heuristics/datasets/'
```

Run all cells in order.

---

## 5. Notebook Outline

The notebook is a single file organised in the order it should be executed:

1. **Setup** — imports, `DATASET_DIR`, seeds, the 8 known optimal lengths.
2. **TSPLIB parser** — `read_tsp()`.
3. **Distance matrix** — `build_dist_matrix()` covers `EUC_2D`, `EUC_3D`, `CEIL_2D`, `MAN_2D`, `MAX_2D`, `GEO`, `ATT`, and `EXPLICIT` (six storage formats).
4. **Helpers** — `total_distance`, `is_valid_tour`, `calc_gap`.
5. **Farthest Insertion** — with convex-hull multi-start.
6. **Swap local search** — with double-bridge perturbation and restarts.
7. **Nearest Neighbor** — with multi-start.
8. **2-opt local search** — with double-bridge restarts, adaptive neighbour list.
9. **Cross pairs** — FI + 2-opt and NN + Swap.
10. **Validation + final benchmark table.**
11. **Visualization** — gap vs. instance size.

---

## 6. Expected Output

The consolidated benchmark table at the end of the notebook prints:

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

A separate validation table confirms that every produced tour passes all three feasibility checks (length = N, no duplicates, valid city ids).

**Reproducibility.** All stochastic components are seeded with `[11, 22, 33, 44, 55]` (5 seeds for `N ≤ 1000`, 3 seeds for `1000 < N ≤ 5000`, 1 seed for `N > 5000`). Running the notebook end-to-end on a fresh runtime reproduces the table above to the last decimal.

---

## 7. Data Source

The eight benchmark instances are from the **TSPLIB** library:

> Reinelt, G. (1991). *TSPLIB — A traveling salesman problem library.* ORSA Journal on Computing, 3(4), 376–384.

The specific `.tsp` files bundled in `datasets/` were downloaded from the GitHub mirror, which is the most reliable source at the time of writing (the original Heidelberg site is frequently unreachable):

- **Primary source used in this project:** <https://github.com/mastqe/tsplib>

The best-known optimal tour lengths used to compute gaps (the `OPTIMAL_VALUES` dictionary in the notebook) are taken from the same TSPLIB mirror.

---

## 8. Report

The full report is in `report/report.pdf` (36 pages). It contains the complete methodology, code listings, experimental results, a critical discussion of the `d2103.tsp` anomaly, lessons learned in algorithmic complexity and heuristic design, applications to logistics and supply chain, and future-work suggestions. This README only describes *how* to run the code.

---

## License

MIT. See `LICENSE`.
