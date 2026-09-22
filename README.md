# Crime Incidents Data Cleaning & Exploratory Data Analysis (EDA)

A Python-based data cleaning and exploratory data analysis pipeline for the `crime_incidents.csv` dataset, implemented using vectorized operations in **pandas** and **numpy**.

---

## Overview

Real-world incident report datasets often suffer from mixed data types, typographical errors, irregular category codes, out-of-range anomalies, and duplicate entries. This project implements a reproducible, vectorized cleaning workflow and provides summary statistics and baseline distributions across temporal, geographic, and incident-level dimensions.

---

## Dataset Summary

- **Raw Dimensions**: 5,250 records, 33 attributes
- **Deduplicated Records**: 5,050 unique records (200 duplicate entries removed)
- **Time Horizon**: 2018 – 2024
- **Scope**: Municipal incident reports detailing crime classification, involved parties (suspects/victims), location, severity, weapon usage, and case resolution.

---

## Data Cleaning Workflow

The cleaning pipeline utilizes vectorized operations (`.map()`, `np.where()`, `Series.between()`, and `.dt` accessors) to ensure performance and reproducibility:

1. **Deduplication**: Identified and purged 200 duplicate records matching on primary key `incident_id`.
2. **Text Normalization & Mapping**:
   - `crime_type`: Standardized irregular entries and abbreviations (e.g., `'asslt'` $\rightarrow$ `'Assault'`, `'B&E'` / `'Burglry'` $\rightarrow$ `'Burglary'`, `'Homocide'` $\rightarrow$ `'Homicide'`).
   - `district`: Standardized directional abbreviations (e.g., `'Sou'`, `'cen'`, `'eas'`) to title-cased district names.
   - `severity`: Unified mixed integer codes and text strings (`'1'`, `'2'`, `'med'`, `'Crit'`) into four standard tiers: `Low`, `Medium`, `High`, `Critical`.
   - `case_status`: Consolidated duplicate resolution tags (`'Resolved'` / `'closed'` $\rightarrow$ `'Closed'`, `'investgation'` $\rightarrow$ `'Under Investigation'`).
   - `reported_online`: Cast boolean synonyms (`'yes'`, `'1'`, `'True'`, `'0'`, `'no'`) into native boolean types.
3. **Outlier Filtering & Range Constraints**:
   - **Ages (`suspect_age`, `victim_age`)**: Removed negative values and extreme anomalies ($>105$ years), constraining valid suspect age to $[10, 100]$ and victim age to $[0, 105]$.
   - **Arrests (`num_arrests`)**: Removed negative counts ($\ge 0$ valid).
   - **Financial Loss (`property_loss_usd`)**: Coerced string formats and removed negative loss amounts ($\ge 0$ valid).
   - **Coordinates (`latitude`, `longitude`)**: Filtered out erroneous coordinate values outside contiguous US bounding limits ($24.0 \le \text{lat} \le 50.0$, $-125.0 \le \text{lon} \le -65.0$).
4. **Temporal Feature Extraction**: Parsed `incident_datetime` into native datetime types and extracted `year`, `month`, `day_name`, and `hour`.

---

## Key Exploratory Findings

### Summary Statistics (Cleaned Features)

| Feature | Count | Mean | Std Dev | Min | Median (50%) | Max |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Suspect Age** | 3,675 | 45.43 | 17.45 | 15.00 | 46.00 | 75.00 |
| **Victim Age** | 4,119 | 49.88 | 23.31 | 10.00 | 50.00 | 90.00 |
| **Arrests per Incident** | 4,551 | 2.46 | 1.72 | 0.00 | 2.00 | 5.00 |
| **Property Loss (USD)** | 4,305 | $24,923.12 | $14,422.31 | $20.66 | $24,725.40 | $49,998.30 |

### Categorical Distributions

- **Top Offense Categories**: Burglary ($n = 316$), Homicide ($n = 282$), Arson ($n = 272$), Trespassing ($n = 263$), Kidnapping ($n = 259$).
- **Weapon Utilization**: Where specified, **Firearms** represent the primary category ($1,539$ incidents), followed by **Blunt Objects** ($932$), and **Personal Weapons / Hands** ($659$).
- **Case Resolutions**:
  - Closed: 34.6% ($1,472$)
  - Open: 25.8% ($1,096$)
  - Under Investigation: 25.2% ($1,073$)
  - Pending: 16.8% ($713$)
- **Temporal Volume**: Yearly incident volume remained stable between 2018 and 2024, ranging from 598 to 653 recorded incidents per year.

---
