# BV-Driven-History-RTSGA

This repository contains the datasets, code/implementation, and result arteifacts (selection histories and visualizations)

## Repository Structure

.
├── Data/
│   ├── mapped-dataset-124-40-defaultJS.xlsx   # Mapped dataset for the 124-40 instance
│   └── mapped-dataset-248-80-defaultJS.xlsx   # Mapped dataset for the 248-80 instance
├── Hist-RTSGA/
│   └── Hist-RTSGA.ipynb                       # Notebook to generate / inspect RTSGA selection histories
├── RTSGA/
│   └── RTSGA Code.ipynb                       # Core RTSGA implementation and experiment runner
├── Results/
│   ├── 124-40_SelectionHist.xlsx              # Selection history for 124-40
│   ├── 248-80_SelectionHist.xlsx              # Selection history for 248-80
│   ├── BV,Req,Test Viz-124-40.pdf             # Combined BV/requirements/test visualizations for 124-40
│   └── BV,Req,Test Viz-248-80.pdf             # Combined BV/requirements/test visualizations for 248-80
└── README.md                                  # Project documentation (this file)

## Directory Descriptions

- `Data/`  
  Contains the two mapped datasets used in all experiments:
  - `mapped-dataset-124-40-defaultJS.xlsx`
  - `mapped-dataset-248-80-defaultJS.xlsx`  

- `Hist-RTSGA/`  
  Contains the notebook implementing the **history-aware, business-value-driven Regression Test Selection (RTS)** approach:
  - `Hist-RTSGA.ipynb` – uses past selection history to bias the search and balance business value with starvation control over cycles.

- `RTSGA/`  
  Contains the notebook implementing the **non-history-aware, business-value-driven RTS** based on a **Set Union Knapsack Problem (SUKP) formulation**:
  - `RTSGA Code.ipynb` – formulates RTS as SUKP and applies the RTSGA algorithm without using historical cycles.

- `Results/`  
  Stores the outputs of the RTSGA runs for both datasets:
  - `124-40_SelectionHist.xlsx` – RTSGA selections over *K* cycles for the 124-40 dataset.  
  - `248-80_SelectionHist.xlsx` – RTSGA selections over *K* cycles for the 248-80 dataset.  
  - `BV,Req,Test Viz-124-40.pdf` – Plots of business value, requirement coverage, and test coverage for multiple combinations of **BV-tolerance** and **starvation-weight** (124-40 dataset).  
  - `BV,Req,Test Viz-248-80.pdf` – Same set of plots for the 248-80 dataset.

---

## Workflow: How to Run the Experiments

The notebooks are designed to be run top-to-bottom with minimal setup.  
At a high level, you only need to choose which dataset you want and then execute all cells in the corresponding notebook.

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
   - Generates plots of business value, requirement coverage, and test coverage (used for the PDFs in `Results/`).

### 3. Run the history-aware RTS (Hist-RTSGA)

1. Open `Hist-RTSGA/Hist-RTSGA.ipynb`.
2. Point it to the same dataset file you want to analyse.
3. Run all cells sequentially from top to bottom:
   - The notebook loads the dataset and (optionally) previous selection history.
   - Applies the history-aware, business-value-driven RTS logic.
   - Produces updated selection histories and the same family of BV / requirement / test coverage plots under varying **BV-tolerance** and **starvation-weight** settings.

After these steps, all relevant selection histories and visualization artefacts will be available for both RTSGA and Hist-RTSGA.


