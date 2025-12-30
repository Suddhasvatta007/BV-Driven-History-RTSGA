# BV-Driven-History-RTSGA

This repository contains the datasets, code/implementation, and result artefacts (selection histories and visualizations)

## Repository Structure

```text
.
├── Data/
│   ├── mapped-dataset-124-40-defaultJS.xlsx   # Mapped dataset for the 124-40 instance
│   └── mapped-dataset-248-80-defaultJS.xlsx   # Mapped dataset for the 248-80 instance
├── Hist-RTSGA/
│   └── Hist-RTSGA.ipynb                       # Notebook to generate / inspect RTSGA selection histories (history-aware)
├── RTSGA/
│   ├── RTSGA Code.ipynb                       # Core RTSGA implementation and experiment runner (non-history-aware)
│   └── Summary_Statistics_RTSGA.ipynb         # Summary statistics pipeline (generates summary_statistics_RTW files)
├── Results/
│   ├── 124-40_SelectionHist.xlsx              # Selection history for 124-40
│   ├── 248-80_SelectionHist.xlsx              # Selection history for 248-80
│   ├── BV,Req,Test Viz-124-40.pdf             # Combined BV/requirements/test visualizations for 124-40
│   ├── BV,Req,Test Viz-248-80.pdf             # Combined BV/requirements/test visualizations for 248-80
│   └── summary_statistics_RTW_*               # (Generated) aggregated summary files used by analysis notebooks
└── README.md                                  # Project documentation (this file)
```

## Directory Descriptions

- `Data/`  
  Contains the two mapped datasets used in all experiments:
  - `mapped-dataset-124-40-defaultJS.xlsx`
  - `mapped-dataset-248-80-defaultJS.xlsx`  

- `Hist-RTSGA/`  
  Contains the notebook implementing the **history-aware, business-value-driven Regression Test Selection (RTS)** approach:
  - `Hist-RTSGA.ipynb` – uses past selection history to bias the search and balance business value with starvation control over cycles.

- `RTSGA/`  
  Contains notebooks for running the RTS algorithms and producing summary statistics:
  - `RTSGA Code.ipynb` – formulates RTS as a Set Union Knapsack Problem (SUKP) and applies the RTSGA algorithm without using historical cycles.
  - `Summary_Statistics_RTSGA.ipynb` – summary statistics pipeline that reads selection histories (from RTSGA / Hist-RTSGA / BCPSO runs), computes cycle-level aggregated metrics, and writes out "summary_statistics_RTW" files that downstream notebooks and visualization scripts consume. This notebook is intended for post-processing selection history outputs and producing consolidated inputs for plotting and comparative analysis (works with RTSGA, Hist-RTSGA, and BCPSO outputs).

- `Results/`  
  Stores the outputs of the RTSGA runs for both datasets:
  - `124-40_SelectionHist.xlsx` – RTSGA selections over *K* cycles for the 124-40 dataset.  
  - `248-80_SelectionHist.xlsx` – RTSGA selections over *K* cycles for the 248-80 dataset.  
  - `BV,Req,Test Viz-124-40.pdf` – Plots of business value, requirement coverage, and test coverage for multiple combinations of **BV-tolerance** and **starvation-weight** (124-40 dataset).  
  - `BV,Req,Test Viz-248-80.pdf` – Same set of plots for the 248-80 dataset.
  - `summary_statistics_RTW_*` – Aggregated summary files produced by `Summary_Statistics_RTSGA.ipynb`. These are used by analysis/plotting code and combine per-cycle metrics across experiments (format may be CSV/XLSX depending on notebook settings).

---

## Workflow: How to Run the Experiments

The notebooks are designed to be run top-to-bottom with minimal setup. At a high level, you choose which dataset to use, run the experiment notebooks to generate selection histories, and then run the summary pipeline to aggregate results and produce the final visualizations.

### 1. Choose and upload a dataset

1. Decide which instance you want to run:
   - `mapped-dataset-124-40-defaultJS.xlsx`, or  
   - `mapped-dataset-248-80-defaultJS.xlsx`.
2. If you are using a hosted environment (e.g., Google Colab), upload the chosen file from `Data/` into the environment and ensure the notebook points to that filename/path.

### 2. Run the non-history-aware RTSGA (SUKP formulation)

1. Open `RTSGA/RTSGA Code.ipynb`.
2. Verify that the dataset path in the first few cells matches the file you want to use (124-40 or 248-80).
3. Run all cells sequentially from top to bottom:
   - The notebook reads the dataset.
   - Executes the RTSGA algorithm over *K* cycles.
   - Writes the selection history to the corresponding Excel file in `Results/`.
   - Generates plots of business value, requirement coverage, and test coverage.

### 3. Run the history-aware RTS (Hist-RTSGA)

1. Open `Hist-RTSGA/Hist-RTSGA.ipynb`.
2. Point it to the same dataset file you want to analyse and (optionally) to a prior selection history if you want to bootstrap.
3. Run all cells sequentially from top to bottom:
   - The notebook loads the dataset and (optionally) previous selection history.
   - Applies the history-aware, business-value-driven RTS logic.
   - Produces updated selection histories and BV / requirement / test coverage plots under varying **BV-tolerance** and **starvation-weight** settings.

### 4. Run the Summary Statistics Pipeline (new)

1. Open `RTSGA/Summary_Statistics_RTSGA.ipynb`.
2. Ensure the notebook is pointed at the selection history files you want to summarize (e.g., files in `Results/` such as `124-40_SelectionHist.xlsx`, `248-80_SelectionHist.xlsx`, or selection history outputs from BCPSO if available).
3. Run all cells sequentially:
   - The notebook parses selection histories from one or more experiment runs.
   - Computes aggregated per-cycle metrics (business value aggregated over cycles, requirement/test coverage trends, tolerance/starvation parameter effects, etc.).
   - Writes out `summary_statistics_RTW` files (CSV/XLSX as configured) into `Results/` or another specified output directory.
   - Optionally generates combined visualizations or prepares the datasets for downstream plotting tools.

After these steps, the selection histories, summary_statistics_RTW files, and visualization artifacts will be available for comparative analysis across RTSGA, Hist-RTSGA, and BCPSO runs.
