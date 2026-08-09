Smart EV Charging: Demand Prediction, Dynamic Pricing & Charging Schedule Optimization
AAI-501 Final Team Project — University of San Diego
This project develops an AI-assisted EV charging management system that combines:

Time-series analysis
Regression-based machine learning
K-Means clustering
Dynamic pricing simulation
Mathematical optimization
Empirical, held-out verification
The objective is to determine whether historical EV charging behavior can be used to predict future demand, and whether those predictions can be turned into better charging and pricing decisions. This README is written as a tour guide: read it top to bottom and you should be able to reproduce the exact process and land on the same numbers reported here and in the project report.

1. Project Objective
EV charging stations can experience highly uneven demand throughout the day. During peak periods, many vehicles may attempt to charge simultaneously, increasing electricity costs and creating charger congestion. During off-peak periods, available charging capacity may remain unused.

This project builds a smart charging pipeline that:

Learns historical EV charging behavior.
Forecasts future hourly charging demand.
Identifies different types of EV users based on charging behavior.
Simulates electricity prices and customer-facing dynamic prices.
Optimizes charging schedules for flexible EV sessions.
Compares the optimized strategy against a simple immediate-charging baseline.
The central idea:

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
2. Quick Facts (Final Results)
Raw sessions	72,856
Cleaned sessions used for analysis	72,296 (99.2% retained)
Unique users / chargers / locations	2,335 / 2,116 / 14
Best demand forecaster	Random Forest — RMSE 78.96 kWh, MAE 57.81 kWh, R² 0.673
Naive 24-hour baseline (for comparison)	RMSE 104.26 kWh, R² 0.430
Peak-hour classification agreement	85.58%
Best user segmentation	K-Means, K=2, silhouette 0.440 (after excluding UserID = 0)
Optimized vs. immediate charging	+3.2% estimated profit (+56.12 currency units), same 723.26 kWh delivered
Full detail on every number above is in Section 6, Results and in ev_project_output/project_summary.csv.

3. Repository Layout and Setup
EV_Charging_Patterns_and_Revenue_Optimisation.ipynb   The full analysis — the actual deliverable, run top to bottom
ChargingRecords.csv                                    Raw dataset (see Section 4)
requirements.txt                                       Python packages needed to run the notebook
ev_project_output/                                      Everything the notebook produces when it runs (see Section 7)
AAI501_Smart_EV_Charging_Final_Project_Report.docx      Written report (gitignored — not tracked in this repo)
There is no standalone .py script in this repo — the notebook is the project code, organized into the numbered sections described below, and it is what should be run to reproduce every figure, table, and metric cited in the report.

Installation
Python 3.10+ is recommended.

git clone https://github.com/codemiitkumar/AAI-501-Group-3-EV-Charging-And-Revenue-Optimization.git
cd AAI-501-Group-3-EV-Charging-And-Revenue-Optimization
pip install -r requirements.txt
Running the project
ChargingRecords.csv is already included at the repo root, and the notebook looks for it there automatically (Section 1 of the notebook), so no manual data setup is required. Either:

Open EV_Charging_Patterns_and_Revenue_Optimisation.ipynb in Jupyter, JupyterLab, or VS Code and run all cells top to bottom, or

Run it non-interactively from the command line:

jupyter nbconvert --to notebook --execute --inplace EV_Charging_Patterns_and_Revenue_Optimisation.ipynb
A full run takes a few minutes — training Random Forest / Gradient Boosting and solving the MILP are the slowest steps. RANDOM_STATE = 42 is fixed everywhere a model or sampling step needs one, so a full re-run should reproduce the same numbers reported here (small differences in the 3rd decimal place can happen across different scikit-learn/numpy versions). When the run finishes, ev_project_output/ will contain the charts and CSVs referenced throughout this README and the report.

4. Dataset
The project uses a public, real-world EV charging transaction dataset:

Baek, K., Lee, E., & Kim, J. (2024). A dataset for multi-faceted analysis of electric vehicle charging transactions. Scientific Data, 11, 262. https://doi.org/10.1038/s41597-024-02942-9

The file expected (and included) at the repo root is ChargingRecords.csv, containing 72,856 charging sessions with:

Column	Description
UserID	Customer identifier. 0 is a special value meaning "not a subscribed customer" — see Section 8.
ChargerID	Which charger the session used.
ChargerCompany, ChargerType	Categorical charger attributes.
Location	One of 14 site categories (apartment, hotel, company, etc.).
StartDay/StartTime/EndDay/EndTime/StartDatetime/EndDatetime	Session start/end.
Duration	Recorded session length in minutes (recalculated from timestamps during cleaning).
Demand	Energy delivered during the session, in kWh.
Important limitation
The original dataset does not directly contain:

Electricity prices
Customer charging prices
Battery state of charge
Explicit customer departure deadlines or charging flexibility
Actual station profit
These variables are therefore introduced as transparent simulation assumptions, each documented at the point in the notebook where it's introduced — see Section 8 (Assumptions) below.

5. How the Notebook Is Organized
Each numbered section below is a real section in the notebook — this is the map for reproducing the same process and the same patterns.

#	Section	What it does
1	Import Data File	Loads ChargingRecords.csv and does a first look at row/column counts.
2	Data Cleaning	Parses timestamps, coerces numeric columns, drops 13 exact duplicates and 547 sessions with inconsistent start/end times, and recalculates Duration from the timestamps (the source of truth) rather than trusting the raw field.
3	Feature Engineering	Derives calendar features (hour, day of week, weekend flag, etc.) and average charging power (kW) per session.
4	Data Visualization	Five charts covering sessions/demand by hour, the demand distribution, and demand by location and weekday.
5	Building the Hourly Time Series	Resamples sessions into one row per hour and adds lag features (1, 2, 3, 24, 168 hours back) plus a 24-hour rolling mean, then does a chronological 80/20 train/test split — the model never sees the future during training.
6	Regression Model Comparison	Trains Linear Regression, Decision Tree, Random Forest, and Gradient Boosting on the hourly time series and compares MAE/RMSE/R² on the held-out test period.
7	Forecast Verification	Benchmarks the best model against a naive "same hour as 24 hours ago" baseline, to check the models are learning something beyond a simple heuristic.
8	K-Means User Behavior Clustering	Groups subscribed users (UserID = 0 is the dataset's catch-all for non-subscribed customers and is excluded) by session count, demand, duration, and timing, then picks the number of clusters by silhouette score.
9	Dynamic Pricing Simulation	Defines transparent, clearly-labeled pricing assumptions (a time-of-use electricity cost and a utilization-based customer price) and computes hourly charger utilization from overlapping sessions.
10	Charging Schedule Optimization	Formulates a mixed-integer linear program (MILP, via scipy.optimize.milp) that decides when — within each driver's historical availability window — to charge a sample of sessions, maximizing profit under a grid power-limit constraint.
11	Baseline vs. Optimized Verification	Compares the MILP schedule against an "immediate charging" baseline on cost, profit, peak load, and utilization.
12	Prediction → Decision Verification	Checks whether the demand forecast is useful for real decisions, not just accurate on paper, by seeing how often it flags the same "peak" hours as what actually happened.
13	Project Summary	Prints and saves the one-row summary of headline numbers (project_summary.csv).
6. Results
Regression model comparison (Section 6–7)
Model	MAE (kWh)	RMSE (kWh)	R²
Random Forest	57.81	78.96	0.673
Gradient Boosting	57.38	79.44	0.669
Linear Regression	61.65	83.70	0.633
Decision Tree	65.05	89.85	0.577
Naive 24-hour baseline	75.24	104.26	0.430
Random Forest and Gradient Boosting are effectively tied for best performance and both are comfortably ahead of the naive baseline (about a 24% RMSE reduction) — evidence that the lag/rolling features and calendar variables capture real temporal structure. The forecast also correctly labels a held-out hour as a "peak" (top 25% by demand) or not 85.58% of the time, which is the more operationally relevant number for staffing/scheduling decisions.

K-Means user clustering (Section 8)
UserID = 0 represents every non-subscribed customer lumped together (43% of all sessions), not one person — left in, it forms its own artificial mega-cluster. Excluded, K-Means on the remaining 2,334 users finds a clean two-group split at K=2 (silhouette 0.440):

Cluster	Users	Avg. sessions	Avg. total demand	Avg. duration	Avg. start hour	Weekend share
Casual users	2,125	8.53	142.51 kWh	134.6 min	15.01	30%
Frequent users	209	109.41	2,117.00 kWh	175.1 min	14.42	24%
The smaller "frequent user" group drives roughly 13× the sessions and 15× the total energy of the "casual user" group, and skews toward slightly earlier, more weekday-driven charging.

Dynamic pricing and optimization (Sections 9–11)
Metric	Immediate-charging baseline	Optimized schedule
Total energy delivered	723.26 kWh	723.26 kWh
Estimated electricity cost	5,459.51 currency units	5,403.40 currency units
Estimated revenue	7,232.62 currency units	7,232.62 currency units
Estimated profit	1,773.10 currency units	1,829.22 currency units
Peak charging load	22.00 kW	22.00 kW
Average charger utilization	4.68%	4.68%
Both strategies deliver identical total energy (each session's required charging hours are a hard constraint regardless of policy), so this isolates the value of when to charge. Shifting sessions into cheaper electricity-cost hours cuts cost by ~1.0% and improves estimated profit by 3.2%, with revenue unchanged — the entire gain comes from timing, not price or volume. Peak load and utilization are identical between strategies at this 30-session sample size, showing the assumed 150 kW grid limit was never close to binding; at a larger simultaneous session count the optimizer would likely also reduce peak load.

7. Generated Outputs
Running the notebook creates:

ev_project_output/
│
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
Every figure and table cited in the written report is generated directly by the notebook and saved here, so the report and the code should never drift out of sync — if you re-run the notebook, these files (and the numbers in Section 6 above) should reproduce.

8. Assumptions and Limitations
The following assumptions should be — and are — explicitly discussed in the final report. Each is introduced at the point in the notebook where it becomes necessary:

Electricity price and customer price (Section 9)
The dataset does not contain historical electricity prices or actual customer payments. The project uses a simulated time-of-use electricity cost schedule and a utilization-based customer price tier:

Electricity cost                       Customer price
00:00–06:00   5 currency units/kWh     Utilization < 30%    8 currency units/kWh
06:00–16:00   7 currency units/kWh     30–60%               10 currency units/kWh
16:00–21:00  11 currency units/kWh     60–80%               13 currency units/kWh
21:00–00:00   7 currency units/kWh     >80%                 16 currency units/kWh
These are simulation assumptions, not observations from the dataset.

Charging flexibility and availability windows (Section 10)
The dataset does not state whether a customer is willing to delay charging. The optimization therefore reuses each historical session's real start/end interval as a stand-in for a customer's availability window.

Battery information (Section 10)
Battery capacity and state of charge are not available. Historical energy demand (Demand) is used directly as the required energy for the simulated charging session.

Charging power (Section 10)
Charging power is estimated as Demand / (Duration / 60) and capped at a realistic 22 kW charger limit. Where that cap would make a session's original window too short to fit its required charging hours, the window is stretched rather than left infeasible — this is an approximation, not a direct measurement of any vehicle's physical maximum charging rate.

UserID = 0 exclusion (Section 8)
This ID represents every non-subscribed customer lumped together, not one person, so treating it as a single "user" would create one artificial cluster containing 43% of all sessions. It's excluded from clustering and analyzed only as part of system-wide demand in the earlier sections.

Grid capacity (Sections 10–11)
A single aggregate 150 kW limit stands in for a real electrical network; there is no per-charger or per-station constraint.

Profit
Profit is simulated as estimated revenue − estimated electricity cost. It does not include labor, infrastructure depreciation, maintenance, taxes, demand charges, or other operating expenses.

Because of these assumptions, the profit and peak-load numbers in Sections 9–11 should be read as a demonstration of the decision-making pipeline (forecast → segment → price → schedule), not a real-world financial forecast.

9. Research Questions and Hypotheses
RQ1 / H1 — Supported. Can historical EV charging data predict future hourly demand better than a naive baseline? Random Forest cuts RMSE by ~24% versus a 24-hour naive baseline (78.96 vs. 104.26 kWh).

RQ2 — Answered. Which regression algorithm performs best? Random Forest, narrowly ahead of Gradient Boosting; both clearly ahead of Linear Regression and a single Decision Tree.

RQ3 / H2 — Supported. Can EV users be separated into meaningful behavior groups? Yes, once the aggregate UserID = 0 group is excluded: a clean two-segment split (casual vs. frequent users, silhouette 0.440) rather than a degenerate single-outlier cluster.

RQ4 / H3 — Partially supported. Can predicted utilization drive a dynamic pricing strategy? A transparent utilization-based pricing simulation is implemented and functions correctly, but since the dataset has no real price/elasticity data, whether it would actually shift real customer behavior remains untested — this is the main item in Future Work.

RQ5 / H4 — Supported. Can optimization improve charging schedules over immediate charging? Yes — +3.2% estimated profit for the same energy delivered, purely from timing.

RQ6 / H5 — Supported. Do better predictions produce measurably better decisions? The demand forecast correctly classifies peak vs. non-peak hours 85.58% of the time, which is the signal the scheduling/pricing decisions actually depend on.

10. Academic Scope
This project is intentionally designed as an integrated introductory machine-learning and optimization project — it does not attempt to build a production EV charging management system. The main academic contribution is connecting:

Time-Series Analysis + Machine Learning + Behavioral Clustering
    + Dynamic Pricing + Mathematical Optimization + Empirical Verification
The ML models answer "what is likely to happen?" The optimization model answers "given that prediction, what should we do?" — the project's central question is whether machine-learning predictions can be converted into better charging and pricing decisions, not just accurate ones.

11. Responsible Use of AI-Assisted Development
AI-assisted tools were used during the development of this project (data pipeline construction, debugging, and documentation), consistent with the course's academic-integrity requirements — this should be disclosed per the course's disclosure policy. Every result in this README, the notebook, and the report was independently executed and verified against the notebook's own output cells rather than assumed correct; two real bugs found during that verification (a Series/DataFrame handling issue in the pricing section, and a power-cap edge case that made the optimizer infeasible) were fixed and are reflected in the current code.

12. Future Improvements
A production-level version could incorporate:

Real-time or historical electricity prices
Real-time charger availability
Battery state of charge and vehicle battery capacity
Individual maximum charging rates per vehicle/charger
Customer price elasticity measured from real pricing and behavior
Explicit departure deadlines and flexibility preferences
Renewable/solar generation forecasts and stationary battery storage
Demand charges and a multi-station (rather than single aggregate) grid model
Reinforcement learning for adaptive pricing
Additional forecasting approaches (XGBoost, ARIMA, LSTM/Transformer-based models)
Rolling-origin (rather than single chronological) forecast validation
13. References
Baek, K., Lee, E., & Kim, J. (2024). A dataset for multi-faceted analysis of electric vehicle charging transactions. Scientific Data, 11, 262. https://doi.org/10.1038/s41597-024-02942-9
Breiman, L. (2001). Random forests. Machine Learning, 45(1), 5–32. https://doi.org/10.1023/A:1010933404324
Friedman, J. H. (2001). Greedy function approximation: A gradient boosting machine. Annals of Statistics, 29(5), 1189–1232. https://doi.org/10.1214/aos/1013203451
Hyndman, R. J., & Athanasopoulos, G. (2021). Forecasting: Principles and practice (3rd ed.). OTexts. https://otexts.com/fpp3/
MacQueen, J. B. (1967). Some methods for classification and analysis of multivariate observations. In Proceedings of the Fifth Berkeley Symposium on Mathematical Statistics and Probability (Vol. 1, pp. 281–297). University of California Press.
Virtanen, P., Gommers, R., Oliphant, T. E., et al. (2020). SciPy 1.0: Fundamental algorithms for scientific computing in Python. Nature Methods, 17, 261–272. https://doi.org/10.1038/s41592-019-0686-2
See the full project report (AAI501_Smart_EV_Charging_Final_Project_Report.docx) for the complete discussion, additional references, and the team's detailed contributions.

About
EV Charging And Revenue Optimization using machine learning

Resources
Readme
Activity
Stars
1 star
Watchers
0 watching
Forks
3 forks
Report repository
Releases
No releases published
Create a new release
Packages
No packages published
Publish your first package
Contributors
3
 (3)
@codemiitkumar
codemiitkumar
@meaha711
meaha711Meaha
@codemitkumar
codemitkumarSanjay Kumar
Languages
Jupyter Notebook
100%
Footer
© 2026 Git