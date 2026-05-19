# Data Dictionary

This document describes all variables in the dataset files.

## 1. Aggregated Sensor Data (`aggregated_data.csv`)

5-minute aggregated water quality measurements from the IoT monitoring system.

| Variable | Type | Unit | Description |
|----------|------|------|-------------|
| `timestamp` | datetime | ISO 8601 | Start of the 5-minute aggregation window |
| `do_mean` | float | mg/L | Mean dissolved oxygen concentration |
| `do_min` | float | mg/L | Minimum dissolved oxygen in window |
| `do_max` | float | mg/L | Maximum dissolved oxygen in window |
| `do_std` | float | mg/L | Standard deviation of DO readings |
| `temp_mean` | float | °C | Mean water temperature |
| `temp_min` | float | °C | Minimum temperature in window |
| `temp_max` | float | °C | Maximum temperature in window |
| `ph_mean` | float | pH | Mean pH level |
| `ph_min` | float | pH | Minimum pH in window |
| `ph_max` | float | pH | Maximum pH in window |
| `reading_count` | int | count | Number of raw readings in window |

### Notes
- Aggregation window: 5 minutes
- Raw sampling interval: approximately 5 seconds
- Typical readings per window: 50-60

## 2. Validation Log (`validation_log_new.csv`)

Live deployment validation results comparing forecasted and observed dissolved oxygen.

| Variable | Type | Unit | Description |
|----------|------|------|-------------|
| `prediction_timestamp` | datetime | ISO 8601 | When the forecast was generated |
| `target_timestamp` | datetime | ISO 8601 | Target time being predicted |
| `horizon` | string | - | Forecast horizon ("30min" or "6hr") |
| `predicted_do` | float | mg/L | Model-predicted dissolved oxygen |
| `actual_do` | float | mg/L | Observed dissolved oxygen at target time |
| `error` | float | mg/L | Prediction error (predicted - actual) |
| `abs_error` | float | mg/L | Absolute prediction error |
| `direction_correct` | bool | - | Whether predicted direction was correct |

### Horizons
- **30min**: 30-minute ahead forecast (Random Forest model)
- **6hr**: 6-hour ahead forecast (LightGBM model)

## 3. Feature Importance (`feature_importance.csv`)

Feature importance scores from the trained forecasting models.

| Variable | Type | Description |
|----------|------|-------------|
| `Feature` | string | Feature name |
| `Importance_30min` | float | Importance score for 30-minute Random Forest model (0-1) |
| `Importance_6hr` | float | Importance score for 6-hour LightGBM model (0-1) |

### Feature Categories

**Sensor-derived features:**
- `do_mean`, `do_min`, `do_max`: Current DO statistics
- `temp_mean`, `ph_mean`: Current temperature and pH
- `do_roll_mean_3`, `do_roll_mean_6`: Rolling DO means (15min, 30min windows)
- `do_roll_std_6`: Rolling DO standard deviation

**Lag features:**
- `do_lag_1`, `do_lag_2`, etc.: Previous DO values
- `temp_lag_1`, `ph_lag_1`: Previous temperature and pH

**Temporal features:**
- `hour`: Hour of day (0-23)
- `hour_sin`, `hour_cos`: Cyclical hour encoding

**Event timing features:**
- `hours_since_feeding`: Time elapsed since last feeding event
- `hours_since_water_change`: Time elapsed since last water change

## 4. Event Logs (`event_logs_new.csv`)

Operational events recorded during the monitoring period.

| Variable | Type | Description |
|----------|------|-------------|
| `timestamp_logged` | datetime | When the event was recorded |
| `event` | string | Event type |
| `notes` | string | Additional observations (optional) |

### Event Types

| Event | Description | Typical DO Effect |
|-------|-------------|-------------------|
| `Feeding` | Fish feeding event | Gradual decline over 2-4 hours |
| `Water Change` | Partial water exchange | Rapid recovery within 30-60 min |
| `Aeration Adjustment` | Change in aeration rate | Variable |
| `Observation` | General observation | - |

## Data Quality Notes

1. **Missing values**: Minimal; gaps represent sensor downtime
2. **Anomalies**: Zero DO values and voltage spikes were filtered during preprocessing
3. **Calibration**: DO sensor calibrated using air-saturation method; temperature sensor verified against reference thermometer
4. **Timezone**: All timestamps in local Oman time (GMT+4)

## Recommended Preprocessing

```python
import pandas as pd

# Load with proper datetime parsing
df = pd.read_csv('aggregated_data.csv', parse_dates=['timestamp'])

# Set timestamp as index for time series analysis
df.set_index('timestamp', inplace=True)

# Check for missing values
print(df.isnull().sum())

# Basic statistics
print(df.describe())
```
