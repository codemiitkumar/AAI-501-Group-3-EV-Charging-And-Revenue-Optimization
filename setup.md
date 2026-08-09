# Smart EV Charging — Setup Guide

This guide provides the installation, configuration, and execution instructions for the **Smart EV Charging: Demand Prediction, Dynamic Pricing & Charging Schedule Optimization** project.

> **Course:** AAI-501 — Applied Artificial Intelligence
> **Program:** Master of Science in Applied Artificial Intelligence
> **University:** University of San Diego (USD)
> **Project Status:** Completed

---

## 1. Prerequisites

Before running the project, make sure the following are installed:

* **Python 3.10 or later**
* **Jupyter Notebook** or **JupyterLab**
* **Git**
* **pip**
* Internet access for repository cloning and package installation

The project does not require a database or external API.

---

## 2. Clone the Repository

Open a terminal, PowerShell, or Command Prompt and run:

```bash
git clone https://github.com/codemiitkumar/AAI-501-Group-3-EV-Charging-And-Revenue-Optimization.git
```

Navigate to the project directory:

```bash
cd AAI-501-Group-3-EV-Charging-And-Revenue-Optimization
```

---

## 3. Create a Virtual Environment

A virtual environment is recommended to keep the project's Python dependencies isolated from other projects.

### Windows

Create the virtual environment:

```powershell
python -m venv .venv
```

Activate it:

```powershell
.venv\Scripts\activate
```

### macOS / Linux

Create the virtual environment:

```bash
python3 -m venv .venv
```

Activate it:

```bash
source .venv/bin/activate
```

After activation, the terminal should indicate that the `.venv` environment is active.

---

## 4. Install Dependencies

Upgrade `pip`:

```bash
python -m pip install --upgrade pip
```

Install all project dependencies:

```bash
pip install -r requirements.txt
```

The project primarily uses:

* Pandas
* NumPy
* Scikit-learn
* SciPy
* Matplotlib
* Seaborn
* Jupyter

---

## 5. Dataset Setup

The required EV charging dataset is already included in the repository.

### Dataset File

```text
ChargingRecords.csv
```

The file should be located in the **project root directory**:

```text
AAI-501-Group-3-EV-Charging-And-Revenue-Optimization/
│
├── ChargingRecords.csv
├── EV_Charging_Patterns_and_Revenue_Optimisation.ipynb
└── requirements.txt
```

No additional dataset download or manual data configuration is required.

The dataset contains:

* **72,856** raw charging sessions
* **2,335** unique users
* **2,116** unique chargers
* **14** locations

### Important Dataset Note

The original dataset does not directly contain:

* Historical electricity prices
* Customer charging prices
* Battery state of charge
* Explicit charging flexibility
* Actual station profit

These variables are represented using transparent simulation assumptions documented in the project README and notebook.

---

## 6. Run the Project

### Option 1 — Jupyter Notebook

Start Jupyter Notebook:

```bash
jupyter notebook
```

Or start JupyterLab:

```bash
jupyter lab
```

Open the following notebook:

```text
EV_Charging_Patterns_and_Revenue_Optimisation.ipynb
```

Run the notebook **from top to bottom**.

This ensures that all data-processing, feature-engineering, modeling, clustering, pricing, optimization, and verification steps are executed in the correct order.

### Option 2 — Visual Studio Code

The notebook can also be opened in **Visual Studio Code** using the Python and Jupyter extensions.

Open:

```text
EV_Charging_Patterns_and_Revenue_Optimisation.ipynb
```

Select the project's Python environment and run the notebook cells from top to bottom.

---

## 7. Run the Notebook from the Command Line

The notebook can also be executed non-interactively:

```bash
jupyter nbconvert --to notebook --execute --inplace EV_Charging_Patterns_and_Revenue_Optimisation.ipynb
```

This executes the complete notebook and updates its output cells.

A full execution may take several minutes. Random Forest / Gradient Boosting training and the MILP optimization are among the more computationally intensive steps.

---

## 8. Reproducibility

A fixed random seed is used wherever model training or sampling requires randomness:

```text
RANDOM_STATE = 42
```

This helps reproduce the reported results.

Small numerical differences may occur across different versions of:

* Python
* NumPy
* Scikit-learn
* SciPy

The results should remain approximately consistent with those reported in the README and project report.

---

## 9. Notebook Workflow

The notebook follows the project workflow below:

```text
ChargingRecords.csv
        │
        ▼
  Data Cleaning
        │
        ▼
Feature Engineering
        │
        ├───────────────┐
        ▼               ▼
Demand Forecasting   User Clustering
        │               │
        └───────┬───────┘
                ▼
        Dynamic Pricing
                │
                ▼
   Charging Optimization
                │
                ▼
 Baseline vs. Optimized
       Verification
```

The main notebook stages are:

| Section | Stage                  | Description                                                                     |
| ------: | ---------------------- | ------------------------------------------------------------------------------- |
|       1 | Import Data            | Loads and examines the charging dataset                                         |
|       2 | Data Cleaning          | Cleans timestamps, duplicates, and inconsistent sessions                        |
|       3 | Feature Engineering    | Creates temporal and charging-power features                                    |
|       4 | Data Visualization     | Explores charging behavior and demand patterns                                  |
|       5 | Hourly Time Series     | Builds hourly demand and lag/rolling features                                   |
|       6 | Regression Models      | Compares Linear Regression, Decision Tree, Random Forest, and Gradient Boosting |
|       7 | Forecast Verification  | Compares the best model with a naive baseline                                   |
|       8 | K-Means Clustering     | Identifies user charging behavior groups                                        |
|       9 | Dynamic Pricing        | Simulates electricity costs and customer pricing                                |
|      10 | Charging Optimization  | Uses MILP to optimize charging schedules                                        |
|      11 | Baseline vs. Optimized | Compares immediate and optimized charging                                       |
|      12 | Prediction → Decision  | Evaluates the operational value of demand forecasts                             |
|      13 | Project Summary        | Generates final summary metrics                                                 |

---

## 10. Generated Outputs

After successfully running the notebook, outputs are stored in:

```text
ev_project_output/
```

### CSV Outputs

```text
cleaned_charging_records.csv
regression_model_comparison.csv
forecast_verification.csv
user_cluster_profiles.csv
user_clusters.csv
dynamic_pricing_simulation.csv
optimized_charging_schedule.csv
baseline_vs_optimized.csv
prediction_decision_verification.csv
project_summary.csv
```

### Visualization Outputs

```text
01_sessions_by_hour.png
02_demand_by_hour.png
03_demand_distribution.png
04_location_demand.png
05_weekday_demand.png
06_actual_vs_predicted.png
07_cluster_selection.png
08_baseline_vs_optimized.png
09_model_rmse_comparison.png
```

The generated outputs correspond to the figures, tables, and metrics discussed in the project README and final report.

---

## 11. Expected Results

A successful execution should produce results approximately consistent with the final project results.

### Demand Forecasting

**Random Forest:**

```text
MAE  = 57.81 kWh
RMSE = 78.96 kWh
R²   = 0.673
```

**Naive 24-hour baseline:**

```text
RMSE = 104.26 kWh
R²   = 0.430
```

### Peak-Hour Verification

```text
Peak-hour classification agreement = 85.58%
```

### User Clustering

```text
Number of clusters = 2
Silhouette score   = 0.440
```

### Charging Optimization

```text
Estimated profit improvement = 3.2%
Profit improvement            = 56.12 currency units
Total energy delivered        = 723.26 kWh
```

Exact numerical values may vary slightly depending on software and package versions.

---

## 12. Troubleshooting

### Python Is Not Recognized

If the following command does not work:

```bash
python
```

verify that Python is installed and added to the system PATH.

On some systems, use:

```bash
python3
```

instead.

### Jupyter Is Not Recognized

If:

```bash
jupyter notebook
```

does not work, install Jupyter with:

```bash
python -m pip install jupyter
```

Then run:

```bash
jupyter notebook
```

### Missing Python Package

If the notebook displays:

```text
ModuleNotFoundError
```

make sure the virtual environment is activated and run:

```bash
pip install -r requirements.txt
```

### Dataset Not Found

If the notebook cannot find:

```text
ChargingRecords.csv
```

verify that the file is located in the repository root:

```text
AAI-501-Group-3-EV-Charging-And-Revenue-Optimization/
│
├── ChargingRecords.csv
├── EV_Charging_Patterns_and_Revenue_Optimisation.ipynb
└── requirements.txt
```

Also make sure that Jupyter is running from the project directory.

### Optimization Becomes Infeasible

The charging optimization model uses assumptions for:

* Charging power
* Customer availability windows
* Grid capacity

Changing these assumptions may cause the optimization problem to become infeasible.

For reproduction of the reported results, use the original project configuration.

---

## 13. Recommended Execution Order

For the most reliable reproduction, follow these steps:

```text
1. Clone the repository
          ↓
2. Create the virtual environment
          ↓
3. Activate the virtual environment
          ↓
4. Install requirements.txt
          ↓
5. Open Jupyter / JupyterLab / VS Code
          ↓
6. Open the project notebook
          ↓
7. Run all notebook sections from top to bottom
          ↓
8. Review generated visualizations
          ↓
9. Review generated CSV outputs
          ↓
10. Review project_summary.csv
```

---

## 14. Repository Structure

```text
AAI-501-Group-3-EV-Charging-And-Revenue-Optimization/
│
├── EV_Charging_Patterns_and_Revenue_Optimisation.ipynb
├── ChargingRecords.csv
├── requirements.txt
├── README.md
├── setup.md
├── LICENSE
│
└── ev_project_output/
    ├── cleaned_charging_records.csv
    ├── regression_model_comparison.csv
    ├── forecast_verification.csv
    ├── user_cluster_profiles.csv
    ├── user_clusters.csv
    ├── dynamic_pricing_simulation.csv
    ├── optimized_charging_schedule.csv
    ├── baseline_vs_optimized.csv
    ├── prediction_decision_verification.csv
    ├── project_summary.csv
    │
    ├── 01_sessions_by_hour.png
    ├── 02_demand_by_hour.png
    ├── 03_demand_distribution.png
    ├── 04_location_demand.png
    ├── 05_weekday_demand.png
    ├── 06_actual_vs_predicted.png
    ├── 07_cluster_selection.png
    ├── 08_baseline_vs_optimized.png
    └── 09_model_rmse_comparison.png
```

---

## 15. Additional Documentation

For the complete project overview, methodology, research questions, hypotheses, results, assumptions, limitations, references, and conclusions, see:

**`README.md`**

For the detailed academic discussion and team contributions, see:

**`AAI501_Smart_EV_Charging_Final_Project_Report.docx`**

---

## 16. Academic and Responsible Use

This setup guide is intended to support reproducibility of the AAI-501 academic project.

AI-assisted tools were used during development for activities including data-pipeline construction, debugging, and documentation. Project results were independently executed and verified against the notebook outputs.

The pricing, electricity-cost, battery, flexibility, and profit assumptions used in the project are simulations rather than direct observations from the source dataset.

For a complete discussion of assumptions and limitations, refer to the **README.md** and final project report.


