# Smart EV Charging: Demand Prediction, Dynamic Pricing & Charging Schedule Optimization

## AAI-501 Final Team Project — University of San Diego

This project is a part of the **AAI-501** course in the **Master of Science in Applied Artificial Intelligence Program** at the **University of San Diego (USD)**.

**Project Status:** Completed

---

## Contributors

* **Meaha J**
* **Sanjay Kumar**
* **Navneet Kumar**

**Course:** AAI-501 — Applied Artificial Intelligence
**University:** University of San Diego

---

## Project Introduction

This project develops an AI-assisted EV charging management system that combines time-series analysis, regression-based machine learning, K-Means clustering, dynamic pricing simulation, mathematical optimization, and empirical verification.

The objective is to determine whether historical EV charging behavior can be used to predict future charging demand and whether those predictions can be translated into improved charging schedules and pricing decisions. The project focuses on connecting machine-learning predictions with optimization-based decisions rather than evaluating prediction accuracy alone.

---

## Project Objective

EV charging stations can experience highly uneven demand throughout the day. During peak periods, many vehicles may attempt to charge simultaneously, increasing electricity costs and creating charger congestion. During off-peak periods, available charging capacity may remain unused.

This project builds a smart charging pipeline that:

1. Learns historical EV charging behavior.
2. Forecasts future hourly charging demand.
3. Identifies different types of EV users based on charging behavior.
4. Simulates electricity prices and customer-facing dynamic prices.
5. Optimizes charging schedules for flexible EV sessions.
6. Compares the optimized strategy against a simple immediate-charging baseline.

### Project Workflow

```text
Historical Charging Data
          |
          v
     Data Cleaning
          |
          v
   Feature Engineering
          |
     +----+----+
     |         |
     v         v
Time-Series   User
Forecasting  Clustering
     |         |
     +----+----+
          |
          v
    Demand / User
      Behavior
          |
          v
    Dynamic Pricing
          |
          v
 Mathematical Optimization
          |
          v
 Optimal Charging Schedule
          |
          v
Baseline vs Optimized
     Verification
```

---

## Methods Used

* Data Cleaning and Preprocessing
* Exploratory Data Analysis (EDA)
* Feature Engineering
* Time-Series Analysis
* Regression-Based Machine Learning
* Linear Regression
* Decision Tree Regression
* Random Forest Regression
* Gradient Boosting
* K-Means Clustering
* Dynamic Pricing Simulation
* Mathematical Optimization
* Mixed-Integer Linear Programming (MILP)
* Data Visualization
* Model Evaluation
* Empirical Hold-Out Verification

---

## Technologies

* Python 3.10+
* Jupyter Notebook
* Pandas
* NumPy
* Scikit-learn
* SciPy
* Matplotlib
* Seaborn
* Git
* GitHub

---

## Quick Facts

| Metric                             |                  Result |
| ---------------------------------- | ----------------------: |
| Raw charging sessions              |                  72,856 |
| Cleaned sessions used for analysis | 72,296 (99.2% retained) |
| Unique users                       |                   2,335 |
| Unique chargers                    |                   2,116 |
| Locations                          |                      14 |
| Best demand forecaster             |           Random Forest |
| Random Forest RMSE                 |               78.96 kWh |
| Random Forest MAE                  |               57.81 kWh |
| Random Forest R²                   |                   0.673 |
| Naive 24-hour baseline RMSE        |              104.26 kWh |
| Peak-hour classification agreement |                  85.58% |
| Best user segmentation             |            K-Means, K=2 |
| K-Means silhouette score           |                   0.440 |
| Optimized vs. immediate charging   |  +3.2% estimated profit |
| Estimated profit improvement       |   +56.12 currency units |
| Total energy delivered             |              723.26 kWh |

---

## Project Description

The project uses historical EV charging transaction data to develop an integrated demand prediction, user segmentation, dynamic pricing, and charging optimization pipeline.

The machine-learning component focuses on forecasting hourly charging demand using historical temporal patterns, lag variables, rolling averages, and calendar features. User behavior is analyzed through K-Means clustering to identify meaningful charging patterns.

The project then extends the analysis into decision-making by simulating dynamic electricity and customer pricing and using mixed-integer linear programming to determine optimized charging schedules. The optimized strategy is compared with an immediate-charging baseline to evaluate whether predictions and optimization can improve estimated profitability.

---

## Dataset

The project uses a public, real-world EV charging transaction dataset:

**Baek, K., Lee, E., & Kim, J. (2024).** A dataset for multi-faceted analysis of electric vehicle charging transactions. *Scientific Data, 11*, 262. DOI: 10.1038/s41597-024-02942-9

The dataset is provided in the repository as:

```text
ChargingRecords.csv
```

It contains **72,856 charging sessions**.

### Dataset Overview

| Attribute             |      Value |
| --------------------- | ---------: |
| Raw charging sessions |     72,856 |
| Cleaned sessions      |     72,296 |
| Data retained         |      99.2% |
| Unique users          |      2,335 |
| Unique chargers       |      2,116 |
| Locations             |         14 |
| Main demand variable  |   `Demand` |
| Duration variable     | `Duration` |

### Data Dictionary

| Column           | Description                                                                    |
| ---------------- | ------------------------------------------------------------------------------ |
| `UserID`         | Customer identifier. `0` represents non-subscribed customers grouped together. |
| `ChargerID`      | Identifier of the charger used for the session.                                |
| `ChargerCompany` | Charger company/category.                                                      |
| `ChargerType`    | Type of charger.                                                               |
| `Location`       | Charging site category such as apartment, hotel, company, etc.                 |
| `StartDay`       | Session start day.                                                             |
| `StartTime`      | Session start time.                                                            |
| `EndDay`         | Session end day.                                                               |
| `EndTime`        | Session end time.                                                              |
| `StartDatetime`  | Combined session start timestamp.                                              |
| `EndDatetime`    | Combined session end timestamp.                                                |
| `Duration`       | Charging session duration in minutes.                                          |
| `Demand`         | Energy delivered during the charging session in kWh.                           |

### Dataset Limitations

The original dataset does not directly contain:

* Electricity prices
* Customer charging prices
* Battery state of charge
* Explicit customer departure deadlines
* Explicit charging flexibility preferences
* Actual station profit

Therefore, these variables are introduced as transparent simulation assumptions and are documented in the assumptions section.

---

## Research Questions and Hypotheses

### RQ1 / H1 — Demand Prediction

**Research Question:** Can historical EV charging data predict future hourly demand better than a naive baseline?

**Hypothesis:** Machine-learning models using historical temporal patterns will outperform a simple 24-hour historical baseline.

**Result:** Supported. Random Forest achieved an RMSE of 78.96 kWh compared with 104.26 kWh for the naive baseline, representing approximately a 24% reduction in RMSE.

### RQ2 — Model Comparison

**Research Question:** Which regression algorithm performs best for hourly EV demand forecasting?

**Result:** Random Forest produced the best overall R² and RMSE performance, narrowly ahead of Gradient Boosting.

### RQ3 / H2 — User Behavior Segmentation

**Research Question:** Can EV users be separated into meaningful behavioral groups?

**Hypothesis:** EV users will exhibit distinct charging patterns that can be identified through clustering.

**Result:** Supported. K-Means produced a two-group segmentation after excluding `UserID = 0`, which represents aggregated non-subscribed customers.

### RQ4 / H3 — Dynamic Pricing

**Research Question:** Can predicted utilization be used to develop a dynamic pricing strategy?

**Hypothesis:** Utilization-based pricing can provide a framework for adjusting customer prices according to station demand.

**Result:** Partially supported. A transparent utilization-based pricing simulation was successfully implemented. However, the dataset does not contain real customer price or elasticity information, so the actual behavioral impact of dynamic pricing cannot be measured.

### RQ5 / H4 — Charging Optimization

**Research Question:** Can optimization improve charging schedules compared with immediate charging?

**Hypothesis:** Scheduling charging during lower-cost periods can reduce estimated electricity costs and increase estimated profit.

**Result:** Supported. The optimized schedule improved estimated profit by 3.2%, or approximately 56.12 currency units, while delivering the same total energy.

### RQ6 / H5 — Prediction-to-Decision Verification

**Research Question:** Do better demand predictions produce measurably better operational decisions?

**Hypothesis:** Accurate demand predictions should improve identification of peak charging periods that can support scheduling and pricing decisions.

**Result:** Supported. The demand forecast correctly classified peak versus non-peak hours 85.58% of the time.

---

## Methodology

### 1. Data Import

The `ChargingRecords.csv` file is loaded and examined for its number of rows, columns, data types, and initial data quality.

### 2. Data Cleaning

The data-cleaning process:

* Parses timestamp fields.
* Converts numeric columns to appropriate formats.
* Removes 13 exact duplicate records.
* Removes 547 sessions with inconsistent start/end times.
* Recalculates `Duration` from timestamps.
* Produces 72,296 cleaned sessions.

### 3. Feature Engineering

Additional variables are derived from the original data, including:

* Hour of day
* Day of week
* Weekend indicator
* Average charging power
* Historical demand lags
* Rolling demand statistics

### 4. Exploratory Data Analysis and Visualization

The project examines charging behavior using visualizations of:

* Sessions by hour
* Demand by hour
* Demand distribution
* Demand by location
* Demand by weekday
* Actual versus predicted demand
* Cluster selection
* Baseline versus optimized charging
* Model RMSE comparison

### 5. Time-Series Demand Forecasting

Charging sessions are aggregated into an hourly time series.

Lag features include:

* 1-hour lag
* 2-hour lag
* 3-hour lag
* 24-hour lag
* 168-hour lag

A 24-hour rolling mean is also included.

A chronological 80/20 train-test split is used so that future observations are not used during training.

### 6. Regression Model Comparison

The following models are trained and evaluated:

* Linear Regression
* Decision Tree
* Random Forest
* Gradient Boosting

Evaluation metrics include:

* Mean Absolute Error (MAE)
* Root Mean Squared Error (RMSE)
* R²

### 7. Forecast Verification

The best forecasting model is compared against a naive 24-hour baseline to determine whether the machine-learning approach provides meaningful improvement over a simple historical heuristic.

### 8. K-Means User Behavior Clustering

Subscribed users are grouped according to:

* Session frequency
* Total demand
* Session duration
* Charging timing
* Weekend charging behavior

`UserID = 0` is excluded from clustering because it represents an aggregate group of non-subscribed customers rather than an individual customer.

The number of clusters is evaluated using silhouette score.

### 9. Dynamic Pricing Simulation

Because the original dataset does not contain historical electricity prices or customer payment information, transparent pricing assumptions are introduced.

#### Electricity Cost

| Time        |      Electricity Cost |
| ----------- | --------------------: |
| 00:00–06:00 |  5 currency units/kWh |
| 06:00–16:00 |  7 currency units/kWh |
| 16:00–21:00 | 11 currency units/kWh |
| 21:00–00:00 |  7 currency units/kWh |

#### Customer Price

| Utilization |        Customer Price |
| ----------- | --------------------: |
| < 30%       |  8 currency units/kWh |
| 30–60%      | 10 currency units/kWh |
| 60–80%      | 13 currency units/kWh |
| > 80%       | 16 currency units/kWh |

These are simulation assumptions rather than observed historical prices.

### 10. Charging Schedule Optimization

A Mixed-Integer Linear Programming (MILP) model is developed using `scipy.optimize.milp`.

The optimization determines when charging should occur within a simulated availability window while considering:

* Required energy
* Charging duration
* Charging power
* Electricity cost
* Grid capacity

The objective is to maximize estimated profit.

### 11. Baseline vs. Optimized Verification

The optimized charging schedule is compared with an immediate-charging baseline using:

* Total energy delivered
* Estimated electricity cost
* Estimated revenue
* Estimated profit
* Peak charging load
* Average charger utilization

### 12. Prediction-to-Decision Verification

The project evaluates whether the demand forecast can correctly identify peak versus non-peak periods. This connects forecasting performance with the operational decisions that depend on predicted demand.

---

## Results

### Regression Model Comparison

| Model                  | MAE (kWh) | RMSE (kWh) |        R² |
| ---------------------- | --------: | ---------: | --------: |
| **Random Forest**      | **57.81** |  **78.96** | **0.673** |
| Gradient Boosting      |     57.38 |      79.44 |     0.669 |
| Linear Regression      |     61.65 |      83.70 |     0.633 |
| Decision Tree          |     65.05 |      89.85 |     0.577 |
| Naive 24-hour baseline |     75.24 |     104.26 |     0.430 |

Random Forest and Gradient Boosting performed similarly, with Random Forest achieving the best RMSE and R². Both models substantially outperformed the naive 24-hour baseline.

The forecast correctly classified held-out hours as peak or non-peak periods **85.58%** of the time.

### K-Means User Clustering

After excluding `UserID = 0`, K-Means identified two main user groups with a silhouette score of **0.440**.

| Cluster        | Users | Avg. Sessions | Avg. Total Demand | Avg. Duration | Avg. Start Hour | Weekend Share |
| -------------- | ----: | ------------: | ----------------: | ------------: | --------------: | ------------: |
| Casual users   | 2,125 |          8.53 |        142.51 kWh |     134.6 min |           15.01 |           30% |
| Frequent users |   209 |        109.41 |      2,117.00 kWh |     175.1 min |           14.42 |           24% |

The smaller frequent-user group generated substantially more charging sessions and energy demand than the casual-user group.

### Dynamic Pricing and Optimization

| Metric                      | Immediate-Charging Baseline | Optimized Schedule |
| --------------------------- | --------------------------: | -----------------: |
| Total energy delivered      |                  723.26 kWh |         723.26 kWh |
| Estimated electricity cost  |                    5,459.51 |           5,403.40 |
| Estimated revenue           |                    7,232.62 |           7,232.62 |
| Estimated profit            |                    1,773.10 |       **1,829.22** |
| Peak charging load          |                    22.00 kW |           22.00 kW |
| Average charger utilization |                       4.68% |              4.68% |

The optimized schedule reduced estimated electricity costs by approximately 1.0% and increased estimated profit by **3.2%** while delivering the same total amount of energy.

The profit improvement comes from shifting charging to lower-cost periods rather than increasing revenue or charging volume.

---

## Challenges, Assumptions, and Limitations

### Electricity Price and Customer Price

The dataset does not contain historical electricity prices or actual customer payments. Therefore, the project uses simulated time-of-use electricity costs and utilization-based customer pricing.

### Charging Flexibility

The dataset does not explicitly indicate whether a customer is willing to delay charging. Historical session start and end times are therefore used as a proxy for the customer's availability window.

### Battery Information

Battery capacity and state of charge are not available. Historical energy demand is used as the required energy for the simulated charging session.

### Charging Power

Charging power is estimated as:

```text
Demand / (Duration / 60)
```

and capped at a 22 kW charger limit.

Where this cap would make the original session window infeasible, the window is extended as an approximation.

### UserID = 0

`UserID = 0` represents all non-subscribed customers as one aggregated category rather than an individual user. Including it as a single user would create an artificial cluster, so it is excluded from user-level clustering.

### Grid Capacity

A single aggregate 150 kW grid capacity is used to represent the electrical network. The model does not include individual station or charger-level capacity constraints.

### Profit

Estimated profit is calculated as:

```text
Estimated Profit = Estimated Revenue − Estimated Electricity Cost
```

It does not include:

* Labor
* Infrastructure depreciation
* Maintenance
* Taxes
* Demand charges
* Other operating expenses

Therefore, the reported profit should be interpreted as a demonstration of the decision-making pipeline rather than a real-world financial forecast.

---

## Repository Structure

```text
AAI-501-Group-3-EV-Charging-And-Revenue-Optimization/
│
├── EV_Charging_Patterns_and_Revenue_Optimisation.ipynb
├── ChargingRecords.csv
├── requirements.txt
├── README.md
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

The notebook is the primary project code and should be run from beginning to end to reproduce the analysis, figures, tables, and reported metrics.

---

## Installation

### Requirements

* Python 3.10 or later
* Jupyter Notebook, JupyterLab, or VS Code
* Packages listed in `requirements.txt`

### Setup

Clone the repository from GitHub and navigate to the project directory.

Then install the required packages:

```bash
pip install -r requirements.txt
```

---

## Running the Project

The `ChargingRecords.csv` dataset is included in the repository, so no additional manual dataset setup is required.

### Option 1: Jupyter Notebook

Open:

```text
EV_Charging_Patterns_and_Revenue_Optimisation.ipynb
```

in Jupyter Notebook, JupyterLab, or VS Code and run all cells from top to bottom.

### Option 2: Command Line

```bash
jupyter nbconvert --to notebook --execute --inplace EV_Charging_Patterns_and_Revenue_Optimisation.ipynb
```

A complete run takes several minutes. Random Forest / Gradient Boosting training and the MILP optimization are among the more computationally intensive steps.

A fixed `RANDOM_STATE = 42` is used wherever model or sampling randomness is required.

Small numerical differences may occur across different versions of NumPy or Scikit-learn.

---

## Generated Outputs

Running the notebook creates the following files in `ev_project_output/`:

### CSV Outputs

* `cleaned_charging_records.csv`
* `regression_model_comparison.csv`
* `forecast_verification.csv`
* `user_cluster_profiles.csv`
* `user_clusters.csv`
* `dynamic_pricing_simulation.csv`
* `optimized_charging_schedule.csv`
* `baseline_vs_optimized.csv`
* `prediction_decision_verification.csv`
* `project_summary.csv`

### Visualization Outputs

* `01_sessions_by_hour.png`
* `02_demand_by_hour.png`
* `03_demand_distribution.png`
* `04_location_demand.png`
* `05_weekday_demand.png`
* `06_actual_vs_predicted.png`
* `07_cluster_selection.png`
* `08_baseline_vs_optimized.png`
* `09_model_rmse_comparison.png`

---

## Academic Scope

This project is designed as an integrated academic machine-learning and optimization project. It does not attempt to build a production-ready EV charging management system.

The main academic contribution is connecting:

```text
Time-Series Analysis
        +
Machine Learning
        +
Behavioral Clustering
        +
Dynamic Pricing
        +
Mathematical Optimization
        +
Empirical Verification
```

The machine-learning models answer:

> **What is likely to happen?**

The optimization model answers:

> **Given that prediction, what should we do?**

The central focus is therefore on determining whether machine-learning predictions can be translated into better charging and pricing decisions, rather than evaluating prediction accuracy alone.

---

## Responsible Use of AI-Assisted Development

AI-assisted tools were used during the development of this project for activities including data-pipeline construction, debugging, and documentation, consistent with the course's academic-integrity and disclosure requirements.

All reported results were independently executed and verified against the notebook's output cells rather than being accepted solely based on AI-generated suggestions.

During verification, issues including a Series/DataFrame handling issue in the pricing section and a power-cap edge case that could make the optimization infeasible were identified and corrected in the project code.

---

## Future Improvements

A production-level version of the project could incorporate:

* Real-time or historical electricity prices
* Real-time charger availability
* Battery state of charge
* Vehicle battery capacity
* Individual maximum charging rates
* Customer price elasticity measured from real pricing and behavior
* Explicit departure deadlines
* Customer flexibility preferences
* Renewable/solar generation forecasts
* Stationary battery storage
* Demand charges
* Multi-station grid modeling
* Reinforcement learning for adaptive pricing
* XGBoost, ARIMA, LSTM, or Transformer-based forecasting
* Rolling-origin forecast validation

---

## Conclusion

This project demonstrates an end-to-end approach for transforming historical EV charging data into operational charging and pricing decisions.

The results show that machine-learning models can capture useful temporal patterns in EV charging demand, while K-Means clustering can identify distinct user behavior groups. The optimization experiment further demonstrates that shifting charging schedules according to simulated electricity costs can improve estimated profit while maintaining the same delivered energy.

Although the pricing and profit results rely on simulation assumptions because the source dataset does not contain real pricing, battery, or customer-flexibility information, the project provides a reproducible framework connecting demand forecasting, user segmentation, dynamic pricing, and charging schedule optimization.

---

## References

1. Baek, K., Lee, E., & Kim, J. (2024). A dataset for multi-faceted analysis of electric vehicle charging transactions. *Scientific Data, 11*, 262. DOI: 10.1038/s41597-024-02942-9

2. Breiman, L. (2001). Random forests. *Machine Learning, 45*(1), 5–32. DOI: 10.1023/A:1010933404324

3. Friedman, J. H. (2001). Greedy function approximation: A gradient boosting machine. *Annals of Statistics, 29*(5), 1189–1232. DOI: 10.1214/aos/1013203451

4. Hyndman, R. J., & Athanasopoulos, G. (2021). *Forecasting: Principles and Practice* (3rd ed.). OTexts.

5. MacQueen, J. B. (1967). Some methods for classification and analysis of multivariate observations. In *Proceedings of the Fifth Berkeley Symposium on Mathematical Statistics and Probability*, Vol. 1. University of California Press.

6. Virtanen, P., Gommers, R., Oliphant, T. E., et al. (2020). SciPy 1.0: Fundamental algorithms for scientific computing in Python. *Nature Methods, 17*. DOI: 10.1038/s41592-019-0686-2

---

## License

This project is licensed under the **MIT License**. See the `LICENSE` file for details.

> Note: The MIT License applies to the project code and documentation created by the team. The third-party EV charging dataset remains subject to its original licensing and usage terms.

---

## Acknowledgments

We would like to thank **Dr. Anuj Kumar Sirohi** for his guidance and support throughout the development of this project as part of the AAI-501 course in the Master of Science in Applied Artificial Intelligence Program at the University of San Diego.

We also acknowledge the authors of the EV charging transaction dataset and the open-source Python libraries that supported the data analysis, machine learning, visualization, and optimization components of this project.

---

## Project Report

The complete project report contains the detailed methodology, additional analysis, discussion, references, and team contributions.

**Report:** `AAI501_Smart_EV_Charging_Final_Project_Report.docx`
