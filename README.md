# Modelling and Predicting Photovoltaic Power Output from Environmental Conditions

## Overview

This project investigates how accurately photovoltaic (PV) power output can be predicted from environmental conditions.

The project compares a simple empirical model, a physics-informed model, and Random Forest machine-learning models. The aim is to determine whether incorporating temperature effects or additional environmental variables provides a meaningful improvement over irradiance-based prediction.

## Research Question

How accurately can photovoltaic power output be predicted from environmental conditions, and how does a physics-based model compare with data-driven approaches?

## Dataset

The dataset used in this project is PVDAQ System 1433 from the NREL Research Support Facility in Golden, Colorado.

The analysis uses 14 days of data from January 2018, with measurements recorded every 15 minutes.

The variables used were:

- POA irradiance
- Ambient temperature
- Module temperature
- AC power

The system has a DC capacity of 449.28 kW.

Only observations with POA irradiance greater than 20 W/m² and valid AC power measurements were used. This resulted in 374 observations.

Dataset source:

https://data.openei.org/submissions/4568

## Methodology

The data was divided chronologically rather than randomly.

| Dataset | Period | Observations |
|---|---|---:|
| Training | January 1–10, 2018 | 263 |
| Testing | January 11–14, 2018 | 111 |

Three modelling approaches were investigated.

### 1. Empirical irradiance model

The first model assumes that PV power output is approximately proportional to POA irradiance:

$$
P = P_0 + kG
$$

where:

- \(P\) is predicted AC power
- \(G\) is POA irradiance
- \(P_0\) is the fitted intercept
- \(k\) is the fitted irradiance coefficient

### 2. Physics-informed model

The second model incorporates the effect of module temperature on PV performance:

$$
P = P_0 + kG[1-\beta(T-T_{ref})]
$$

where:

- \(T\) is module temperature
- \(T_{ref}\) is the reference temperature
- \(beta\) is the fitted temperature coefficient
- \(G\) is POA irradiance

A reference temperature of 25°C was used.

### 3. Random Forest models

Two Random Forest models were trained.

The first used:

- POA irradiance
- Module temperature
- Ambient temperature

The second used POA irradiance only.

The models were evaluated on the chronological test set.

## Model Evaluation

The models were compared using three metrics:

### Mean Absolute Error

$$
MAE = \frac{1}{n}\sum_{i=1}^{n}|y_i-\hat{y}_i|
$$

MAE measures the average absolute difference between measured and predicted power.

### Root Mean Square Error

$$
RMSE =
\sqrt{
\frac{1}{n}
\sum_{i=1}^{n}
(y_i-\hat{y}_i)^2
}
$$

RMSE gives greater weight to larger prediction errors.

### Coefficient of Determination

$$
R^2 =
1-
\frac{
\sum_{i=1}^{n}(y_i-\hat{y}_i)^2
}{
\sum_{i=1}^{n}(y_i-\bar{y})^2
}
$$

R² measures how much of the variation in measured power is explained by the model.

## Results

The performance of the four models on the test dataset was:

| Model | MAE (kW) | RMSE (kW) | R² |
|---|---:|---:|---:|
| Linear irradiance | 6.83 | 13.81 | 0.884 |
| Physics-informed | 6.61 | 13.84 | 0.883 |
| Random Forest: all variables | 6.83 | 13.58 | 0.887 |
| Random Forest: irradiance only | 6.89 | 13.56 | 0.888 |

The physics-informed model produced the lowest MAE.

The Random Forest using irradiance only produced the lowest RMSE and highest R².

Overall, the models performed similarly, with no large improvement from adding the additional environmental variables to the Random Forest model.

## Feature Importance

For the Random Forest model using all environmental variables, POA irradiance accounted for approximately 99.6% of the feature importance.

Module temperature and ambient temperature each contributed approximately 0.2%.

This indicates that irradiance was overwhelmingly the most important predictor in this dataset.

## Residual Analysis

Residuals were calculated as:

$$
e_i = y_i-\hat{y}_i
$$

where \(y_i\) is the measured power and \(\hat{y}_i\) is the predicted power.

The standard deviation of the Random Forest residuals was approximately 13.22 kW.

Eleven test observations exceeded the sensitivity threshold of approximately 19.82 kW.

Some of the largest residuals included:

- +59.0 kW on January 11
- −56.3 kW on January 13
- +40.6 kW on January 14

The residual analysis did not show a strong systematic relationship between prediction error and either irradiance or module temperature.

## Energy Analysis

Predicted power was integrated over time to estimate the total electrical energy produced during the test period.

The measured and predicted energy values were:

| Model | Energy (kWh) | Relative Error |
|---|---:|---:|
| Measured | 1500.48 | — |
| Linear irradiance | 1392.21 | 7.22% |
| Physics-informed | 1392.39 | 7.20% |
| Random Forest | 1390.29 | 7.34% |

The physics-informed model produced the smallest energy error.

A sensitivity analysis using 15-, 30-, and 45-minute integration gaps produced errors of approximately 6–7%, indicating that the overall energy estimates were reasonably stable to the integration interval.

## Key Findings

The main findings from the analysis were:

1. All four models achieved similar predictive performance.
2. The physics-informed model produced the lowest MAE.
3. The irradiance-only Random Forest produced the lowest RMSE and highest R².
4. POA irradiance was by far the most important predictor in the Random Forest model.
5. Adding module and ambient temperature to the Random Forest produced little improvement.
6. The physics-informed model produced the closest estimate of total energy generation.
7. The remaining prediction errors suggest that additional variables or longer-duration data may be required to capture all sources of PV system variability.

## Limitations

The analysis has several limitations.

The dataset covers only 14 days, with the final four days used for testing. This limits the ability to assess how well the models generalise across different seasons and weather conditions.

The dataset also contains a limited number of environmental variables. Additional information such as wind speed, humidity, cloud cover, inverter conditions, and other system-level measurements could potentially improve prediction accuracy.

Some AC power measurements were missing and therefore excluded during preprocessing.

The results are also specific to the PV system and time period investigated. Longer-term data would be required to determine whether the findings generalise to other PV systems and operating conditions.

## Repository Structure

```text
pv-physics-modelling/
│
├── README.md
│
├── report/
│   └── PV_Performance_Modelling_Report.pdf
│
├── notebook/
│   └── PV_Performance_Modelling.ipynb
│
├── data/
│   ├── raw/
│   │   └── dataset.csv
│   └── README.md
│
├── figures/
│   └── ...
│
└── requirements.txt
```

If the original dataset cannot be redistributed, the raw dataset should not be uploaded to the repository. Instead, the `data/README.md` file can provide the original source, access information, and details of the variables used.

## Reproducibility

The analysis was implemented in Python using a Jupyter/Google Colab notebook.

The notebook contains the data preprocessing, exploratory analysis, model development, evaluation, residual analysis, and energy calculations used in this project.

The original dataset can be accessed through the Open Energy Data Initiative:

https://data.openei.org/submissions/4568

## Author

Reva Singh

BSc (Hons) Physics with Astrophysics  
University of Manchester
