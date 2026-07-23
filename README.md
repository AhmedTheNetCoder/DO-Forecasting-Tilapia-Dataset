# Dissolved Oxygen Forecasting Dataset for Nile Tilapia Aquaculture

[![DOI](https://img.shields.io/badge/DOI-10.3390%2Fs26134242-blue)](https://doi.org/10.3390/s26134242)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

## Overview

This repository contains the complete datasets and supporting materials from the research study:

> **Preliminary Field Evaluation of a Low-Cost IoT Workflow for Dissolved Oxygen Monitoring and Short-Horizon Forecasting in Nile Tilapia Pond Aquaculture**
>
> Al-Khaldi, A.M.; Dhandapani, R.; Al-Badri, M.A.
>
> *Sensors* 2026, 26, 4242
>
> National University of Science and Technology, Muscat, Oman

The dataset supports research and education in aquaculture monitoring, dissolved oxygen forecasting, and IoT-based water quality systems.

## Dataset Description

### Study Context

- **Species:** Nile tilapia (*Oreochromis niloticus*)
- **Location:** North Al Sharqiyah, Oman
- **Initial Testing Period:** 26 March - 1 April 2026 (open pond)
- **Live Validation Period:** 1-10 April 2026 (controlled 180L setup)
- **Raw Sampling Interval:** ~5-7 seconds
- **Aggregation Window:** 5 minutes
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
│   ├── raw/
│   │   ├── offline/
│   │   │   └── raw_readings.csv         # Raw sensor readings (initial testing)
│   │   └── live/
│   │       └── raw_readings_new.csv     # Raw sensor readings (live validation)
│   ├── processed/
│   │   └── aggregated_data.csv          # 5-minute aggregated sensor readings
│   ├── validation/
│   │   └── validation_log_new.csv       # Live forecasting validation results
│   ├── features/
│   │   └── feature_importance.csv       # Model feature importance scores
│   ├── events/
│   │   └── event_logs_new.csv           # Operational event records
│   └── calibration/
│       ├── calibration_do.json          # DO sensor calibration parameters
│       ├── calibration_ph.json          # pH sensor calibration parameters
│       └── calibration_temp.json        # Temperature sensor calibration
└── docs/
    └── data_dictionary.md               # Variable descriptions
```

## Data Files

### 1. Raw Sensor Readings

High-frequency sensor readings captured at approximately 5-7 second intervals.

#### Offline Testing (`data/raw/offline/raw_readings.csv`)

Initial data collection from open pond testing in North Al Sharqiyah.

| Column | Description | Unit |
|--------|-------------|------|
| `timestamp` | Reading timestamp | ISO 8601 |
| `node_id` | Sensor node identifier | - |
| `temp_c` | Water temperature | °C |
| `ph` | pH level | pH units |
| `do_mgL` | Dissolved oxygen concentration | mg/L |
| `events` | Event flags (if any) | - |

- **Records:** 41,663
- **Period:** 26 March - 1 April 2026

#### Live Validation (`data/raw/live/raw_readings_new.csv`)

Raw readings from the controlled 180L validation setup with live Nile tilapia.

- **Records:** 102,670
- **Period:** 1-10 April 2026

### 2. Aggregated Sensor Data (`data/processed/aggregated_data.csv`)

5-minute aggregated water quality measurements derived from raw readings.

| Column | Description | Unit |
|--------|-------------|------|
| `timestamp` | Start of aggregation window | ISO 8601 |
| `do_mean` | Mean dissolved oxygen | mg/L |
| `do_min` | Minimum dissolved oxygen | mg/L |
| `do_max` | Maximum dissolved oxygen | mg/L |
| `do_std` | Standard deviation of DO | mg/L |
| `temp_mean` | Mean water temperature | °C |
| `temp_min` | Minimum temperature | °C |
| `temp_max` | Maximum temperature | °C |
| `ph_mean` | Mean pH level | pH units |
| `ph_min` | Minimum pH | pH units |
| `ph_max` | Maximum pH | pH units |
| `reading_count` | Number of raw readings in window | count |

### 3. Validation Log (`data/validation/validation_log_new.csv`)

Live deployment forecasting validation results comparing predictions to observations.

| Column | Description |
|--------|-------------|
| `prediction_timestamp` | When forecast was generated |
| `target_timestamp` | Target time for prediction |
| `horizon` | Forecast horizon (30min or 6hr) |
| `predicted_do` | Predicted dissolved oxygen (mg/L) |
| `actual_do` | Observed dissolved oxygen (mg/L) |
| `error` | Prediction error (mg/L) |
| `abs_error` | Absolute prediction error (mg/L) |
| `direction_correct` | Whether predicted direction was correct |

### 4. Feature Importance (`data/features/feature_importance.csv`)

Random Forest and LightGBM feature importance scores.

| Column | Description |
|--------|-------------|
| `Feature` | Feature name |
| `Importance_30min` | Importance for 30-minute model |
| `Importance_6hr` | Importance for 6-hour model |

### 5. Event Logs (`data/events/event_logs_new.csv`)

Operational events recorded during the study.

| Column | Description |
|--------|-------------|
| `timestamp_logged` | Event timestamp |
| `event` | Event type (Feeding, Water Change, etc.) |
| `notes` | Additional observations |

### 6. Sensor Calibration (`data/calibration/`)

Calibration parameters used for sensor value conversion.

#### DO Sensor (`calibration_do.json`)
- Air saturation calibration method
- Voltage-to-concentration conversion model

#### pH Sensor (`calibration_ph.json`)
- Two-point calibration (pH 4.0 and pH 7.0)
- Linear voltage-to-pH conversion

#### Temperature Sensor (`calibration_temp.json`)
- Linear correction model for DS18B20 sensor

## Key Statistics

| Metric | Value |
|--------|-------|
| Total raw readings (offline) | 41,663 |
| Total raw readings (live) | 102,670 |
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
import json

# Load raw sensor readings
raw_offline = pd.read_csv('data/raw/offline/raw_readings.csv', parse_dates=['timestamp'])
raw_live = pd.read_csv('data/raw/live/raw_readings_new.csv', parse_dates=['timestamp'])

# Load aggregated sensor data
aggregated = pd.read_csv('data/processed/aggregated_data.csv', parse_dates=['timestamp'])

# Load validation results
validation = pd.read_csv('data/validation/validation_log_new.csv',
                         parse_dates=['prediction_timestamp', 'target_timestamp'])

# Load feature importance
features = pd.read_csv('data/features/feature_importance.csv')

# Load event logs
events = pd.read_csv('data/events/event_logs_new.csv', parse_dates=['timestamp_logged'])

# Load calibration parameters
with open('data/calibration/calibration_do.json', 'r') as f:
    do_calibration = json.load(f)
```

### Example Analysis

```python
# Calculate error statistics
mae_30min = validation[validation['horizon'] == '30min']['abs_error'].mean()
mae_6hr = validation[validation['horizon'] == '6hr']['abs_error'].mean()

print(f"30-minute MAE: {mae_30min:.3f} mg/L")
print(f"6-hour MAE: {mae_6hr:.3f} mg/L")

# Explore raw data sampling frequency
raw_live['time_diff'] = raw_live['timestamp'].diff().dt.total_seconds()
print(f"Mean sampling interval: {raw_live['time_diff'].mean():.2f} seconds")
```

## Citation

If you use this dataset in your research, please cite:

```bibtex
@article{alkhaldi2026preliminary,
  title={Preliminary Field Evaluation of a Low-Cost IoT Workflow for Dissolved Oxygen Monitoring and Short-Horizon Forecasting in Nile Tilapia Pond Aquaculture},
  author={Al-Khaldi, Ahmed M. and Dhandapani, Ragavesh and Al-Badri, Mohammed A.},
  journal={Sensors},
  volume={26},
  number={13},
  pages={4242},
  year={2026},
  publisher={MDPI},
  doi={10.3390/s26134242}
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
Dr. Ragavesh Dhandapani
National University of Science and Technology
Muscat, Oman
Email: ragavesh@nu.edu.om

**First Author:**
Ahmed M. Al-Khaldi
Email: ahmed200251@nu.edu.om

## Acknowledgements

This research was supported by National University of Science and Technology, Muscat, Oman, as part of the undergraduate capstone programme.
