# Data Dictionary

This document describes all variables in the dataset files.

## 1. Raw Sensor Readings

High-frequency sensor readings captured directly from the IoT monitoring system at approximately 5-7 second intervals.

### 1.1 Offline Raw Readings (`data/raw/offline/raw_readings.csv`)

Initial data collection from open pond testing in North Al Sharqiyah, Oman.

| Variable | Type | Unit | Description |
|----------|------|------|-------------|
| `timestamp` | datetime | ISO 8601 | Reading timestamp (local Oman time, GMT+4) |
| `node_id` | string | - | Sensor node identifier (e.g., "node_001") |
| `temp_c` | float | °C | Water temperature from DS18B20 sensor |
| `ph` | float | pH | pH level from analog pH sensor |
| `do_mgL` | float | mg/L | Dissolved oxygen concentration |
| `events` | string | - | Event flags (empty if no event) |

- **Records:** 41,663
- **Period:** 26 March - 1 April 2026
- **Sampling interval:** ~5-7 seconds

### 1.2 Live Raw Readings (`data/raw/live/raw_readings_new.csv`)

Raw readings from the controlled 180L validation setup with live Nile tilapia.

| Variable | Type | Unit | Description |
|----------|------|------|-------------|
| `timestamp` | datetime | ISO 8601 | Reading timestamp (local Oman time, GMT+4) |
| `node_id` | string | - | Sensor node identifier (e.g., "node_001") |
| `temp_c` | float | °C | Water temperature from DS18B20 sensor |
| `ph` | float | pH | pH level from analog pH sensor |
| `do_mgL` | float | mg/L | Dissolved oxygen concentration |
| `events` | string | - | Event flags (empty if no event) |

- **Records:** 102,670
- **Period:** 1-10 April 2026
- **Sampling interval:** ~5-7 seconds

## 2. Aggregated Sensor Data (`data/processed/aggregated_data.csv`)

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
- Raw sampling interval: approximately 5-7 seconds
- Typical readings per window: 40-60

## 3. Validation Log (`data/validation/validation_log_new.csv`)

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

## 4. Feature Importance (`data/features/feature_importance.csv`)

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

## 5. Event Logs (`data/events/event_logs_new.csv`)

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

## 6. Sensor Calibration Files (`data/calibration/`)

Calibration parameters used for converting raw sensor voltages to physical units.

### 6.1 DO Sensor Calibration (`calibration_do.json`)

Air saturation calibration method for the Gravity analog dissolved oxygen sensor.

| Parameter | Type | Description |
|-----------|------|-------------|
| `zero_voltage` | float | Voltage reading at zero DO (V) |
| `air_sat_voltage` | float | Voltage reading at air saturation (V) |
| `do_saturation_mgL` | float | DO saturation concentration at calibration temperature (mg/L) |
| `temperature_c` | float | Water temperature during calibration (°C) |
| `model` | string | Conversion equation description |

**Model:** `DO = ((V - V0)/(Vair - V0)) * DO_sat(T)`

### 6.2 pH Sensor Calibration (`calibration_ph.json`)

Two-point calibration for the analog pH sensor.

| Parameter | Type | Description |
|-----------|------|-------------|
| `calibration_type` | string | Calibration method ("2-point") |
| `points` | object | Calibration point data (pH 4.0 and pH 7.0) |
| `slope_m` | float | Linear slope coefficient |
| `offset_b` | float | Linear offset coefficient |
| `equation` | string | Conversion equation |

**Model:** `pH = m * V + b`

Each calibration point includes:
- `ph_target`: Target pH value
- `voltage_v_avg`: Average voltage reading (V)
- `temp_c_avg`: Average temperature during calibration (°C)
- `n`: Number of samples averaged

### 6.3 Temperature Sensor Calibration (`calibration_temp.json`)

Linear correction model for the DS18B20 temperature sensor.

| Parameter | Type | Description |
|-----------|------|-------------|
| `a` | float | Linear slope coefficient |
| `b` | float | Linear offset coefficient |
| `model` | string | Correction equation description |
| `points` | int | Number of calibration points used |

**Model:** `T_corrected = a * T_raw + b`

## Data Quality Notes

1. **Missing values**: Minimal; gaps represent sensor downtime or communication interruptions
2. **Anomalies**: Zero DO values and voltage spikes were filtered during preprocessing
3. **Calibration**: DO sensor calibrated using air-saturation method; temperature sensor verified against reference thermometer; pH sensor calibrated using standard buffer solutions (pH 4.0 and pH 7.0)
4. **Timezone**: All timestamps in local Oman time (GMT+4)
5. **Sensor drift**: DO sensor recalibrated periodically during the study period

## Recommended Preprocessing

```python
import pandas as pd
import json

# Load raw data with proper datetime parsing
raw = pd.read_csv('data/raw/live/raw_readings_new.csv', parse_dates=['timestamp'])

# Load aggregated data
df = pd.read_csv('data/processed/aggregated_data.csv', parse_dates=['timestamp'])

# Set timestamp as index for time series analysis
df.set_index('timestamp', inplace=True)

# Check for missing values
print(df.isnull().sum())

# Basic statistics
print(df.describe())

# Load calibration parameters
with open('data/calibration/calibration_do.json', 'r') as f:
    do_cal = json.load(f)
print(f"DO calibration temperature: {do_cal['temperature_c']:.1f}°C")
print(f"DO saturation at calibration: {do_cal['do_saturation_mgL']:.2f} mg/L")
```
