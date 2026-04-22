# BV-Driven-History-RTSGA

This repository contains the datasets, code, and result artefacts for a **Business-Value-Driven, History-Aware Regression Test Selection** study using a Genetic Algorithm (RTSGA).

The core idea: frame regression test selection as a Set Union Knapsack Problem (SUKP) and solve it with a GA that is optionally guided by past selection history and starvation penalties to improve requirement diversity across cycles.

---

## Repository Structure

```text
.
├── Data/
│   ├── mapped-dataset-36-20.xlsx    # Small dataset (D2 => 36 requirements, 20 tests)
│   └── mapped-dataset-248-80.xlsx   # Large dataset (D1 => 248 requirements, 80 tests)
│
├── RTSGA/
│   ├── RTSGA Code.ipynb             # GA experiment runner — single-budget, multi-run
│   ├── Summary_Statistics_RTSGA.ipynb  # Generates the RTSGA baseline summary file
│   └── Algo.png                     # Algorithm diagram
│
├── Hist-RTSGA/
│   ├── Hist_RTSGA.ipynb             # History-aware GA with starvation sensitivity sweep
│   └── New_Reqs_Per_Cycle.ipynb     # Post-processing: new requirements per cycle table
│
├── Results/
│   ├── 36-20_SelectionHistory.xlsx  # Per-cycle selections for the 36-20 dataset
│   ├── 248-80_SelectionHistory.xlsx # Per-cycle selections for the 248-80 dataset
│   ├── BV,Req,Test Cov-36-20.pdf    # BV / coverage visualisations for the 36-20 dataset
│   └── BV,Req,Test Cov-248-80.pdf   # BV / coverage visualisations for the 248-80 dataset
│
├── .gitignore
└── README.md
```

> **Generated directories** (created when notebooks are run, excluded from version control):
> `Checkpoints/`, `History_Tables/`, `Statistics/`, `Comparison_Plots/`

---

## Input data

- **Data** lives in `Data/` (e.g. `D1.xlsx`, `D2.xlsx`).
- Each Excel file must contain columns that can be mapped to: `tc_id`, `us_id`, `tc_executiontime`, `us_businessvalue`.
- For D2, test execution data can be produced as described in `Data/generating_test_execution_data_for_D2.md`. The user story mappings to the test cases can be found [here](https://gitlab.com/SEMERU-Code-Public/Data/icse20-comet-data-replication-package/-/blob/main/LibEST/req_to_test_ground.txt?ref_type=heads).

---

## Notebooks

| Notebook | Folder | Purpose |
|---|---|---|
| `Summary_Statistics_RTSGA.ipynb` | `RTSGA/` | Runs standard RTSGA over 20 cycles and produces the baseline summary file consumed by the other notebooks |
| `RTSGA Code.ipynb` | `RTSGA/` | Runs the full GA experiment (V5_AG configuration) and compares results against the RTSGA baseline |
| `Hist_RTSGA.ipynb` | `Hist-RTSGA/` | Runs the history-aware GA across a grid of BV-tolerance × starvation-weight settings with checkpointing |
| `New_Reqs_Per_Cycle.ipynb` | `Hist-RTSGA/` | Reads the History_Tables output and builds a table of new requirements introduced each cycle |

---

## Running the Notebooks

All notebooks are self-contained and **portable** — they run on local Jupyter or Google Colab without any manual path changes.

### Environment detection

Every notebook's first cell auto-detects the runtime and sets three path variables:

| Variable | Description |
|---|---|
| `DATA_FILE` | Absolute path to the datasets (`For example, mapped-dataset-36-20.xlsx`) |
| `OUTPUT_DIR` | Directory where results, checkpoints, and reports are written |
| `BASELINE_FILE` | Path to `summary_statistics_RTSGA_only_RTW_10%.xlsx` (RTSGA and Hist-RTSGA only) |

**Local Jupyter** — no action needed. The setup cell walks up from the notebook's directory until it finds `Data/mapped-dataset-36-20.xlsx`, so the notebooks work regardless of where Jupyter was launched from.

**Google Colab** — upload the project to Google Drive, then set `DRIVE_PROJECT_PATH` in Cell 0 / Cell 1 to the folder that contains `Data/`. A file-upload fallback is available if Drive mount fails.

### Recommended run order

#### Step 1 — Generate the RTSGA baseline

Open `RTSGA/Summary_Statistics_RTSGA.ipynb` and run all cells. This executes 20 cycles of standard RTSGA (30 runs each) and writes `summary_statistics_RTSGA_only_RTW_10%.xlsx` to `RTSGA/`. This file is required by steps 2a and 2b.

#### Step 2a — Run the GA experiment (RTSGA Code)

Open `RTSGA/RTSGA Code.ipynb` and run all cells. This runs the V5_AG GA configuration and produces a `Comprehensive_Report_<version>.xlsx` report with plots comparing the run against the RTSGA baseline.

#### Step 2b — Run the history-aware experiment (Hist-RTSGA)

Open `Hist-RTSGA/Hist_RTSGA.ipynb` and run all cells. This sweeps over 15 combinations of BV-tolerance (`0.7, 0.8, 0.9`) and starvation weight (`0.05 – 0.25`) across 20 cycles × 30 runs each. Results are checkpointed after every configuration so the notebook can be safely interrupted and resumed.

Outputs written to `Hist-RTSGA/`:

| Directory | Contents |
|---|---|
| `Checkpoints/` | `.pkl` files — one per configuration, enable resume-on-interrupt |
| `History_Tables/` | Per-configuration and combined selection history Excel files |
| `Statistics/` | Per-configuration and combined summary statistics Excel files |
| `Comparison_Plots/` | PNG plots and a combined PDF comparing Hist-RTSGA vs RTSGA vs random |

#### Step 3 — Analyze new requirements per cycle

Open `Hist-RTSGA/New_Reqs_Per_Cycle.ipynb` and run all cells. It reads `History_Tables/selection_history_ALL_COMBINED.xlsx` (produced in step 2b) and outputs a table showing how many new requirement IDs appear in each cycle relative to the previous one, saved to `new_requirements_per_cycle.xlsx`.

---

## Key Parameters

These are defined at the top of each notebook and can be adjusted before running:

| Parameter | Default | Description |
|---|---|---|
| `NUM_CYCLES` | 20 | Number of regression test cycles to simulate |
| `RUNS_PER_CYCLE` | 30 | Independent GA runs per cycle (for statistical robustness) |
| `RTW_RATIOS` | `[0.1]` | Regression Testing Window as a fraction of total execution time |
| `max_generations` | 100 | GA generations per run |
| `crossover_prob` | 0.08 (RTSGA Code) / 0.8 (others) | Crossover probability |
| `mutation_prob` | 0.05 | Mutation probability |
| `bv_tolerances` | `[0.9, 0.8, 0.7]` | Hist-RTSGA: minimum BV fraction to activate starvation bonus |
| `starvation_weights` | `[0.05, 0.10, 0.15, 0.20, 0.25]` | Hist-RTSGA: strength of the starvation bonus |
