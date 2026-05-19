# Dissolved Oxygen Forecasting Dataset for Nile Tilapia Aquaculture

[![DOI](https://img.shields.io/badge/DOI-Pending-blue)]()
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

## Overview

This repository contains the processed datasets and supporting materials from the research study:

> **Low-Cost IoT-Based Dissolved Oxygen Monitoring and Short-Horizon Forecasting for Nile Tilapia Aquaculture in Oman**
>
> Al-Khaldi, A.M.; Al Dhuli, A.; Al-Badri, M.A.; Al Saidi, A.Z.; Ragavesh, D.
>
> National University of Science and Technology, Muscat, Oman

The dataset supports research and education in aquaculture monitoring, dissolved oxygen forecasting, and IoT-based water quality systems.

## Dataset Description

### Study Context

- **Species:** Nile tilapia (*Oreochromis niloticus*)
- **Location:** North Al Sharqiyah, Oman
- **Validation Period:** 1-10 April 2026
- **Sampling Interval:** 5-minute aggregated windows
- **Forecasting Horizons:** 30-minute and 6-hour

### Data Collection

Data was collected using a low-cost IoT prototype consisting of:
- ESP32 microcontroller
- Gravity analog dissolved oxygen sensor
- DS18B20 waterproof temperature sensor
- Analog pH sensor
- MQTT-based cloud communication

## Repository Structure

```
dataset-repository/
├── README.md
├── LICENSE
├── data/
│   ├── processed/
│   │   └── aggregated_data.csv       # 5-minute aggregated sensor readings
│   ├── validation/
│   │   └── validation_log_new.csv    # Live forecasting validation results
│   ├── features/
│   │   └── feature_importance.csv    # Model feature importance scores
│   └── events/
│       └── event_logs_new.csv        # Operational event records
└── docs/
    └── data_dictionary.md            # Variable descriptions
```

## Data Files

### 1. Aggregated Sensor Data (`data/processed/aggregated_data.csv`)

5-minute aggregated water quality measurements.

| Column | Description | Unit |
|--------|-------------|------|
| `timestamp` | Measurement timestamp | ISO 8601 |
| `do_mean` | Mean dissolved oxygen | mg/L |
| `do_min` | Minimum dissolved oxygen | mg/L |
| `do_max` | Maximum dissolved oxygen | mg/L |
| `temp_mean` | Mean water temperature | °C |
| `ph_mean` | Mean pH level | pH units |

### 2. Validation Log (`data/validation/validation_log_new.csv`)

Live deployment forecasting validation results.

| Column | Description |
|--------|-------------|
| `prediction_timestamp` | When forecast was generated |
| `target_timestamp` | Target time for prediction |
| `horizon` | Forecast horizon (30min or 6hr) |
| `predicted_do` | Predicted dissolved oxygen (mg/L) |
| `actual_do` | Observed dissolved oxygen (mg/L) |
| `error` | Prediction error (mg/L) |

### 3. Feature Importance (`data/features/feature_importance.csv`)

Random Forest and LightGBM feature importance scores.

| Column | Description |
|--------|-------------|
| `Feature` | Feature name |
| `Importance_30min` | Importance for 30-minute model |
| `Importance_6hr` | Importance for 6-hour model |

### 4. Event Logs (`data/events/event_logs_new.csv`)

Operational events recorded during the study.

| Column | Description |
|--------|-------------|
| `timestamp_logged` | Event timestamp |
| `event` | Event type (Feeding, Water Change, etc.) |
| `notes` | Additional observations |

## Key Statistics

| Metric | Value |
|--------|-------|
| Total aggregated samples | 3,041 |
| 30-minute validated predictions | 4,657 |
| 6-hour validated predictions | 3,391 |
| 30-min MAE | 0.783 mg/L |
| 6-hr MAE | 1.109 mg/L |
| 30-min Directional Accuracy | 60.23% |
| 6-hr Directional Accuracy | 53.82% |

## Usage

### Loading the Data (Python)

```python
import pandas as pd

# Load aggregated sensor data
aggregated = pd.read_csv('data/processed/aggregated_data.csv', parse_dates=['timestamp'])

# Load validation results
validation = pd.read_csv('data/validation/validation_log_new.csv', parse_dates=['prediction_timestamp', 'target_timestamp'])

# Load feature importance
features = pd.read_csv('data/features/feature_importance.csv')

# Load event logs
events = pd.read_csv('data/events/event_logs_new.csv', parse_dates=['timestamp_logged'])
```

### Example Analysis

```python
# Calculate error statistics
mae_30min = validation[validation['horizon'] == '30min']['error'].abs().mean()
mae_6hr = validation[validation['horizon'] == '6hr']['error'].abs().mean()

print(f"30-minute MAE: {mae_30min:.3f} mg/L")
print(f"6-hour MAE: {mae_6hr:.3f} mg/L")
```

## Citation

If you use this dataset in your research, please cite:

```bibtex
@article{alkhaldi2026dissolved,
  title={Low-Cost IoT-Based Dissolved Oxygen Monitoring and Short-Horizon Forecasting for Nile Tilapia Aquaculture in Oman},
  author={Al-Khaldi, Ahmed Mohammed and Al Dhuli, Amal and Al-Badri, Mohammed Ahmed and Al Saidi, Awasif Zaid and Ragavesh, D.},
  journal={Fishes},
  year={2026},
  publisher={MDPI}
}
```

## License

This dataset is released under the [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).

You are free to:
- **Share** — copy and redistribute the material in any medium or format
- **Adapt** — remix, transform, and build upon the material for any purpose

Under the following terms:
- **Attribution** — You must give appropriate credit and indicate if changes were made

## Contact

For questions about this dataset or to request additional data:

**Corresponding Author:**
Dr. Ragavesh D.
National University of Science and Technology
Muscat, Oman

## Acknowledgements

This research was supported by National University of Science and Technology, Muscat, Oman, as part of the undergraduate capstone programme.
