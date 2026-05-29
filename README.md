# Course Project Report: Applied Data Science Capstone

**Course Title:** IBM Data Science Professional Certificate Specialization (Capstone Artifact)

**Project Title:** Winning the Space Race with Data Science: Predicting Falcon 9 First Stage Landing Success

---

## 1. Introduction & Project Background

In the modern era of commercial spaceflight, SpaceX has emerged as a disruptive leader by significantly reducing the cost of orbital access. The corporation advertises Falcon 9 rocket launches at a baseline cost of $62 million, whereas competing aerospace providers incur costs upward of $165 million per launch. A primary driver of this cost disparity is SpaceX’s ability to reliably recover and reuse the vehicle's first-stage booster.

Because first-stage asset recovery translates directly to operational savings, accurately predicting booster landing success yields critical strategic value for evaluating the true economic cost of a launch. This project establishes an end-to-end data science pipeline utilizing historical telemetry data and comparative machine learning classification architectures to predict whether a given Falcon 9 first-stage booster will successfully land.

## 2. Core Research Objectives

The investigative framework of this assignment aims to answer the following operational questions:

1. **Temporal Evolution:** Does the historical recovery rate of Falcon 9 first stages demonstrate a statistically significant increase over time, indicating an operational learning curve?
2. **Feature Influence:** How do independent mission variables—specifically payload mass, target orbit profile, total flight counts, and localized launch sites—influence the statistical probability of a successful landing?
3. **Predictive Modeling:** Which machine learning classification algorithm yields optimal accuracy, precision, and recall when tasked with forecasting the binary landing outcome of a booster?

## 3. Methodology

### 3.1 Multi-Channel Data Collection

The data ingestion pipeline utilized two distinct extraction vectors to build a comprehensive historical record:

* **SpaceX REST API Integration:** Programmatic retrieval was executed using Python’s `requests` library to query the official `/v4/launches/past` endpoint. Using core identification tokens from the primary payload, additional structural data was systematically backfilled by querying the corresponding child endpoints: `/v4/rockets/`, `/v4/launchpads/`, `/v4/payloads/`, and `/v4/cores/`. This process generated the initial structural baseline (`dataset_part_1.csv`).
* **Automated Web Scraping:** Historical launch tables spanning flights up through June 2021 were programmatically parsed from Wikipedia. The extraction workflow utilized the `BeautifulSoup` framework to navigate the HTML Document Object Model (DOM), accompanied by text-cleaning routines to strip superscript footnote elements and markdown artifacts.

### 3.2 Data Wrangling & Feature Engineering

Prior to exploratory analysis, a rigorous preprocessing workflow was applied to format and normalize the raw data:

* **Dataset Filtering:** Early sub-orbital or low-capacity test vehicles (such as the Falcon 1) were filtered out to isolate an analytical baseline consisting exclusively of Falcon 9 orbital flights.
* **Missing Value Imputation:** Null observations within critical tracking metrics—specifically missing `PayloadMass` values—were resolved by calculating and substituting the mean payload mass of corresponding target mission orbits.
* **Target Label Generation:** Raw categorical status strings detailing landing results were mapped into a definitive binary target variable, `Class`. Successful recoveries (including drone ship arrivals `True ASDS`, ground pad returns `True RTLS`, and controlled water landings `True Ocean`) were encoded as `1`. Unsuccessful profiles (crashes `False ASDS`/`False RTLS`, unattempted drops `None None`, or lost components) were encoded as `0` (`dataset_part_2.csv`).
* **Categorical Vectorization:** Nominal categorical columns (including `Orbit`, `LaunchSite`, and core `Serial` tracking numbers) were transformed into a model-ready numeric matrix using One-Hot Encoding via the `pd.get_dummies()` function.

### 3.3 Exploratory Data Analysis (EDA)

* **SQL-Driven Relational Ingestion:** The preprocessed dataset was staged inside an SQLite database container. Structured queries were executed to aggregate metrics, track cumulative payload masses, count unique launchpad distributions, and audit ranked chronological success rates.
* **Statistical Data Visualization:** Relational scatter plots, line graphs, and categorical bar charts were generated using `Seaborn` and `Matplotlib` to isolate behavioral trends across variables.

### 3.4 Interactive Visual Analytics

* **Geospatial Mapping (Folium):** An interactive map was constructed to analyze launch pad geography. The pipeline plotted coordinate markers for physical sites, applying the Haversine formula to measure proximity to critical infrastructure and coastlines.
* **Dynamic Web Dashboard (Plotly Dash):** A standalone web application (`spacex_dash_app.py`) was developed to allow real-time filtering. The dashboard linked a range slider (payload mass) and a dropdown menu (launch site selection) directly to live-updating pie and scatter charts.

### 3.5 Predictive Machine Learning Modeling

* **Pipeline Standardization:** The engineered feature matrix was scaled using Scikit-Learn's `StandardScaler` to bring feature variances to a uniform scale before splitting into an 80/20 train/test partition.
* **Hyperparameter Optimization:** Exhaustive hyperparameter tuning was conducted via `GridSearchCV` utilizing a 10-fold stratified cross-validation workspace. Four core classification frameworks were trained and evaluated:
1. *Logistic Regression*
2. *Support Vector Machine (SVM)*
3. *K-Nearest Neighbors (KNN)*
4. *Decision Tree Classifier*



---

## 4. Analytical Findings & Results

### 4.1 Exploratory Analysis Insights

* **The Geospatial Parameter:** Mapping data confirmed that all major launch facilities are positioned within immediate proximity ($<1\text{ km}$) of coastlines, ensuring safe over-water flight trajectories during early flight phases.
* **The Operational Learning Curve:** Temporal analysis revealed a distinct technological maturation profile. Booster landing success rates climbed systematically from a baseline of 0% between 2010–2013 to a stabilized tier consistently exceeding 80% post-2018.
* **Mass & Orbit Demographics:** Heavier payload configurations demonstrated a high failure rate early in the program but achieved significant improvement over time. High-mass payloads exceeding 8,000 kg eventually scaled to a near-perfect success rate. In terms of trajectories, the low-energy orbits **ES-L1, SSO, HEO, and GEO** consistently produced optimal recovery success rates.

### 4.2 Machine Learning Model Comparison

While multiple optimized classification pipelines converged on high accuracy scores during cross-validation training, the final evaluation highlighted distinct performance profiles when exposed to the hidden test set:

* **Top Performing Model:** The **Decision Tree Classifier** emerged as the optimal algorithm for this dataset, demonstrating high accuracy, precision, and recall metrics.
* **Confusion Matrix Classification Profile:** The optimized Decision Tree pipeline successfully resolved the test partition by correctly predicting **12 true positive observations** (successful landings) and **3 true negative observations** (failed landings), while encountering 3 false positive misclassifications and 0 false negatives.

---

## 5. Summary Conclusion

The assignment successfully proves that SpaceX's booster reusability is highly quantifiable using public operational variables. The historical data validates a stark upward trajectory in technology stabilization over time, heavily correlated with specific low-energy target orbits and a clear payload mass threshold. For deployment in competitive market intelligence frameworks, the **Decision Tree Classifier** provides the most accurate and reliable tool to forecast asset recovery outcomes, enabling stakeholders to accurately assess true launch cost dynamics.