# bike-demand-forecasting

Bike-Sharing Demand Forecasting & Capacity Planning
# Business problem
Bike-sharing operators need to anticipate hourly rental demand to inform bicycle positioning, staffing, and maintenance planning. Underestimation can contribute to stockouts; overestimation can lead to inefficient resource allocation.
This project predicts hourly rental count (cnt) using time, calendar, and weather features from the UCI Bike Sharing Dataset.
# Dataset
- Source: UCI Machine Learning Repository — Bike Sharing Dataset
- File used: hour.csv
- Observations reported: 17,379 rows and 17 columns
- Target: cnt
- Data-quality checks reported: 0 missing values and 0 exact duplicate rows
The raw dataset is not included in this repository. Download it from the official source and place hour.csv in data/.
# Method
1. Inspected schema, missing values, duplicates, and date ordering.
2. Explored average demand by hour, weekday, weather, and temperature.
3. Sorted records chronologically and used the earliest 80% for training and latest 20% for testing.
4. Compared a DummyRegressor(strategy="mean") baseline with a RandomForestRegressor.
5. Evaluated MAE, RMSE, and WAPE, then examined errors by peak hour, working-day status, and weather category.
# Features
season, yr, mnth, hr, holiday, weekday, workingday, weathersit, temp, atemp, hum, windspeed
casual and registered are excluded because they sum to cnt and would leak the target.
# Results
Model	MAE	RMSE	WAPE
DummyRegressor baseline	174.98	232.61	70.34%
Random Forest	45.13	69.87	18.14%


The reported Random Forest reduced MAE by approximately 74.2% versus the baseline. These metrics are from the reported experiment and should be reproduced from the notebook before publication.
# Segment findings
- The EDA identified 17:00 (5 PM) as the highest-average-demand hour and 04:00 (4 AM) as the lowest.
- Reported MAE at 5 PM was 146, substantially higher than overall MAE (45.13), so peak-hour performance needs further investigation.
- Working-day MAE: 43.53; non-working-day MAE: 48.49.
- Weather category 3 had MAE 72.38 and WAPE 42.79% across 238 test observations, a smaller sample than categories 1 and 2.
- The largest errors included both strong overpredictions and underpredictions, suggesting that aggregate metrics alone do not tell the full story.
# Business recommendations
1. Plan for recurring demand peaks: Investigate pre-peak bicycle positioning and staffing around the observed 5 PM peak. Validate station-level demand and peak-hour errors before making deployment decisions.
2. Evaluate maintenance windows: The 4 AM low-demand pattern may be a candidate for maintenance, subject to station demand, staffing, repair duration, and service constraints.
3. Monitor weather-sensitive errors: Track errors by weather condition and investigate performance under precipitation. Category-level findings should be interpreted with sample sizes in mind.
4. Add operational data before claiming impact: Fleet availability, station-level rentals, trip duration, unmet demand, and labor/repositioning costs are needed to estimate savings or optimize fleet allocation.
# Limitations and next steps
- The dataset is historical and the model's performance may change over time.
- A single chronological holdout is useful but does not replace rolling-origin validation.
- temp and atemp are related; feature importance is not causal evidence.
- A high 5 PM MAE warrants error analysis and possibly a peak-period model or time-aware validation.
- Predictions should use only features available at forecast time. Weather features should come from forecasts when making real operational predictions.
- The dataset is not station-level, so it cannot directly determine how many bicycles to deploy at each location.
- Do not claim financial savings without real cost and operational data.
Reproduce
# Install dependencies:
pip install pandas numpy matplotlib scikit-learn joblib
Then:
1. Download hour.csv from the official UCI page into data/hour.csv.
2. Run notebooks/demand_forecasting.ipynb in order.
3. Confirm the chronological split, metrics, and prediction row alignment.
4. Generate results/actual_vs_predicted.png from the complete held-out test set.
5. Save the measured metrics to results/metrics.json.
