# AQI Predictor Karachi

An end-to-end Machine Learning system for forecasting **Karachi's Air Quality Index (AQI)** at **24-hour, 48-hour, and 72-hour horizons** using air-quality and weather data.

---

## 1. Overview

AQI Predictor Karachi is an automated forecasting pipeline that collects environmental data, processes it into machine learning features, trains forecasting models, tracks experiments, and serves predictions through a Streamlit application.

The project was designed as an end-to-end machine learning system rather than a standalone model or notebook.

The pipeline covers:

* Data ingestion
* Data cleaning
* Feature engineering
* Historical backfilling
* Feature storage
* Model training
* Model evaluation
* Experiment tracking
* Model explainability
* Automated workflows
* Interactive deployment

---

## 2. Objective

The objective is to forecast Karachi's future AQI using historical air-quality and meteorological information.

The system produces forecasts for:

```text
24 Hours
48 Hours
72 Hours
```

This allows the application to provide both short-term and longer-horizon AQI predictions.

---

## 3. System Architecture

```text
        Open-Meteo APIs
              |
       +------+------+
       |             |
       v             v
 Air Quality      Weather
    Data            Data
       |             |
       +------+------+
              |
              v
        Data Ingestion
              |
              v
       Data Cleaning
              |
              v
      Feature Engineering
              |
              v
       Parquet Feature Store
              |
              v
       Model Training
              |
       +------+------+
       |      |      |
       v      v      v
      24h    48h    72h
       |      |      |
       +------+------+
              |
              v
       MLflow Tracking
              |
              v
       Model Registration
              |
              v
      Streamlit Application
```

---

## 4. Data Sources

The system uses **Open-Meteo** APIs to collect air-quality and weather information.

### Air Quality Data

The pipeline works with environmental variables including:

* PM2.5
* PM10
* Carbon Monoxide (CO)
* Nitrogen Dioxide (NO₂)
* Sulfur Dioxide (SO₂)
* Ozone (O₃)
* Dust
* AQI
* UV Index

### Weather Data

Weather-related variables include:

* Temperature
* Relative humidity
* Wind
* Atmospheric pressure
* Solar radiation
* Boundary-layer height
* Vapour-pressure deficit

These variables are combined with historical AQI information during feature engineering.

---

## 5. Data Pipeline

The project maintains an automated feature pipeline.

```text
Raw API Data
     |
     v
Data Validation
     |
     v
Cleaning
     |
     v
Feature Engineering
     |
     v
Historical Features
     |
     v
Parquet Feature Store
```

The feature pipeline can continuously update the stored dataset as new environmental observations become available.

---

## 6. Feature Engineering

The project transforms raw environmental observations into features suitable for forecasting.

The feature pipeline incorporates:

* Historical AQI information
* Air-pollutant measurements
* Weather variables
* Temporal information
* Lagged observations
* Rolling / historical features

The resulting features are stored in Parquet format for efficient reuse during model training and inference.

---

## 7. Forecasting Models

Multiple machine learning models are evaluated for the forecasting task.

### Ridge Regression

A linear baseline with L2 regularization.

### Random Forest

An ensemble tree-based model capable of capturing nonlinear relationships between environmental variables and AQI.

### XGBoost

A gradient-boosted tree model used to model more complex nonlinear relationships in the data.

The models are evaluated across the different forecasting horizons.

---

## 8. Forecast Horizons

Separate forecasting targets are maintained for:

| Horizon  | Prediction |
| -------- | ---------- |
| 24 hours | Future AQI |
| 48 hours | Future AQI |
| 72 hours | Future AQI |

This allows model performance and predictions to be considered separately for each forecasting horizon.

---

## 9. Experiment Tracking

**MLflow** is used to track machine learning experiments.

The training pipeline records model-related information and experiment results, allowing different training runs and forecasting models to be compared.

**DagsHub** is also used as part of the experiment and model management workflow.

---

## 10. Model Explainability

**SHAP** is used to analyze model predictions and investigate feature contributions.

This helps identify which environmental and weather variables contribute to individual predictions and overall model behavior.

The explainability component provides an additional layer of analysis beyond simply reporting forecast values.

---

## 11. Automation

The project uses **GitHub Actions** to automate recurring machine learning workflows.

### Hourly Feature Pipeline

The hourly workflow:

```text
New API Data
     |
     v
Feature Processing
     |
     v
Parquet Feature Store Update
```

### Daily Training Pipeline

The training workflow:

```text
Updated Feature Store
          |
          v
     Model Training
          |
          v
   Model Evaluation
          |
          v
    MLflow Tracking
          |
          v
   Model Registration
```

This reduces the need for manual data collection and model retraining.

---

## 12. Deployment

The project includes an interactive **Streamlit** application for viewing AQI forecasts.

The application provides access to the trained forecasting models and their predictions through a web interface.

### Live Application

https://aqi-predictor-karachi-vdtnfg6rzatxqniuho9vj2.streamlit.app/

The project also includes support for serving predictions through **FastAPI / Uvicorn**.

---

## 13. Machine Learning Workflow

The complete workflow can be summarized as:

```text
             Data Collection
                   |
                   v
            Data Cleaning
                   |
                   v
          Feature Engineering
                   |
                   v
           Feature Storage
                   |
                   v
          Model Development
                   |
          +--------+--------+
          |        |        |
          v        v        v
        Ridge   Random    XGBoost
                 Forest
          |        |        |
          +--------+--------+
                   |
                   v
             Evaluation
                   |
                   v
            SHAP Analysis
                   |
                   v
          MLflow Experiment
              Tracking
                   |
                   v
              Deployment
```

---

## 14. Technologies

### Programming and Data Processing

* Python
* Pandas
* NumPy
* PyArrow
* Parquet

### Machine Learning

* Scikit-learn
* Ridge Regression
* Random Forest
* XGBoost

### MLOps

* MLflow
* DagsHub
* GitHub Actions

### Explainability

* SHAP

### Deployment

* Streamlit
* FastAPI
* Uvicorn

### APIs

* Open-Meteo Air Quality API
* Open-Meteo Weather API

---

## 15. Repository Structure

```text
aqi-predictor-karachi/
│
├── .github/
│   └── workflows/
│       ├── hourly_feature_pipeline.yml
│       └── daily_training_pipeline.yml
│
├── data/
│   └── features.parquet
│
├── plots/
│
├── app.py
├── backfill_pipeline.py
├── data_cleaning.py
├── eda.py
├── feature_pipeline.py
├── model_utils.py
├── training_pipeline.py
├── list_models.py
├── requirements.txt
└── README.md
```

---

## 16. Local Setup

### Clone the Repository

```bash
git clone https://github.com/muhammadrayan-codes/aqi-predictor-karachi.git
cd aqi-predictor-karachi
```

### Create a Virtual Environment

```bash
python -m venv venv
```

### Activate the Environment

Windows:

```bash
venv\Scripts\activate
```

Linux / macOS:

```bash
source venv/bin/activate
```

### Install Dependencies

```bash
pip install -r requirements.txt
```

### Run the Application

```bash
streamlit run app.py
```

---

## 17. What This Project Demonstrates

* End-to-end machine learning development
* Environmental data ingestion
* API-based data collection
* Data cleaning and preprocessing
* Exploratory data analysis
* Feature engineering
* Time-series forecasting
* Multiple model comparison
* Automated feature pipelines
* Automated model training
* Experiment tracking
* Model registration
* Model explainability
* GitHub Actions
* Streamlit deployment
* FastAPI model serving

---

## 18. Possible Extensions

Potential future improvements include:

* More advanced time-series models
* Additional meteorological features
* Longer historical training periods
* Probabilistic AQI forecasting
* Prediction intervals
* Automated model selection
* More extensive drift monitoring
* Additional cities
* Real-time alerting based on forecasted AQI
