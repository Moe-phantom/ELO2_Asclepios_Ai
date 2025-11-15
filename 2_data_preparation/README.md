# Data Cleaning Pipeline For Teds-A Dataset

## Overview

This folder contains the data cleaning and preprocessing pipeline for the TEDS-A
2023 dataset, used in the project: **Optimization of Treatment Planning and Resource**
**Allocation in Substance Use Disorder (SUD) Rehabilitation Facilities using**
**Artificial Intelligence and Predictive Analytics**.

## Dataset Information

- **Source:** SAMHSA (Substance Abuse and Mental Health Services Administration)
- **Dataset:** Treatment Episode Data Set - Admissions (TEDS-A), 2023
- **Records:** ~1.6 million admission records
- **Original Variables:** 60 variables
- **Cleaned Variables:** 50 relevant variables

---

## Files

### 1. `cleaning_teds_a.ipynb`

**Purpose:** Main data cleaning script that processes raw TEDS-A data.

**Pipeline Steps:**

1. Load raw CSV data
2. Handle missing values (convert -9 codes to NaN)
3. Optimize data types (reduce memory by ~70%)
4. Engineer 17 new features for treatment optimization
5. Decode categorical codes to readable labels
6. Select and rename 50 relevant columns
7. Save cleaned dataset

**Input:** `1_datasets/raw/tedsa_puf_2023.csv`  
**Output:** `1_datasets/processed/teds_a_2023_cleaned.csv`

---

### 2. `missing_value_handling_teds_a.ipynb`

**Purpose:** Advanced missing value analysis and handling strategy for different
analysis types.

**Processing Steps:**

1. Analyze missing value patterns and percentages
2. Identify critical variables (cannot be missing)
3. Create analysis-ready dataset (minimal removal, ~95% retention)
4. Create ML-ready dataset (imputation, 100% retention)
5. Generate missing value report

**Input:** `1_datasets/processed/teds_a_2023_cleaned.csv`

**Outputs:**

- `1_datasets/processed/teds_analysis_ready.csv` - For statistical analysis
(pairwise deletion)
- `1_datasets/processed/teds_ml_ready.csv` - For machine learning (imputed values)

---

## Input/Output Summary

### Input

- **File:** `1_datasets/raw/tedsa_puf_2023.csv`
- **Format:** CSV file with TEDS-A 2023 admission records
- **Size:** ~1.6M rows × 60 columns

### Outputs

#### 1. Primary Cleaned Dataset

- **File:** `1_datasets/processed/teds_a_2023_cleaned.csv`
- **Records:** ~1.6M rows × 50 columns
- **Features:** Human-readable column names and decoded categorical values
- **Use:** EDA
- **Missing Data:** Preserved as NaN

#### 2. Analysis-Ready Dataset

- **File:** `1_datasets/processed/teds_analysis_ready.csv`
- **Records:** ~1.54M rows (95% retention)
- **Strategy:** Minimal removal - only rows missing critical variables
- **Use:** Statistical hypothesis testing, correlation analysis
- **Missing Data:** Present in non-critical variables (handled via pairwise deletion)

#### 3. ML-Ready Dataset

- **File:** `1_datasets/processed/teds_ml_ready.csv`
- **Records:** ~1.6M rows (100% retention)
- **Strategy:** Statistical imputation (median/mode)
- **Use:** Machine learning model training
- **Missing Data:** None (imputed)

#### 4. Sample Dataset

- **File:** `1_datasets/sample/tedsa_sample.csv`
- **Records:** 1000 rows (0.0625% retention)
- **Strategy:** Sampling
- **Use:** visualization, initial exploration
- **Missing Data:** almost all

---

## Engineered Features (17 New Variables)

| Feature | Description | Type |
|---------|-------------|------|
| `years_using` | Years between first use and admission | Continuous |
| `number_of_substances` | Count of substances used (0-3) | Discrete |
| `is_polysubstance` | Uses 2+ substances | Binary |
| `is_opioid_primary` | Primary substance is opioid | Binary |
| `is_stimulant_primary` | Primary substance is stimulant | Binary |
| `is_injection_user` | Uses injection route | Binary |
| `is_criminal_justice_referral` | Referred by criminal justice | Binary |
| `has_recent_arrest` | Arrested in past 30 days | Binary |
| `is_chronic_treatment` | 3+ prior treatment episodes | Binary |
| `is_first_treatment` | No prior treatment | Binary |
| `has_long_wait` | Waited 15+ days for treatment | Binary |
| `is_adolescent` | Age 12-17 | Binary |
| `is_older_adult` | Age 55+ | Binary |
| `is_pregnant` | Pregnant at admission | Binary |
| `is_homeless` | Experiencing homelessness | Binary |
| `has_no_income` | No source of income | Binary |
| `has_mental_health_disorder` | Co-occurring mental health disorder | Binary |

---

## Final Variables (50 Columns)

### Demographics (10)

- `patient_id`, `age_group`, `sex`, `race`, `ethnicity`, `marital_status`,
`education_level`, `employment_status`, `living_arrangement`, `income_source`

### Treatment Planning (8)

- `service_type`, `wait_time_days`, `referral_source`, `prior_treatments`,
`medication_assisted_therapy`, `dsm_diagnosis`, `self_help_attendance`,
`payment_source`

### Substance Use Profile (10)

- `primary_substance`, `secondary_substance`, `tertiary_substance`,
`route_primary`, `route_secondary`, `route_tertiary`, `frequency_primary`,
`frequency_secondary`, `frequency_tertiary`, `age_first_use_primary`

### Clinical Indicators (11)

- `injection_drug_use`, `years_using`, `number_of_substances`,
`substance_category`, `is_polysubstance`, `is_opioid_primary`,
`is_stimulant_primary`, `is_injection_user`, `has_cooccurring_mental_health`,
`has_mental_health_disorder`, `pregnant`

### Risk Factors (6)

- `recent_arrests`, `is_criminal_justice_referral`, `has_recent_arrest`,
`is_homeless`, `has_no_income`, `is_pregnant`

### Treatment History (2)

- `is_chronic_treatment`, `is_first_treatment`

### Other (3)

- `state`, `region`, `veteran_status`, `health_insurance`, `has_long_wait`,
`is_adolescent`, `is_older_adult`

---

## Key Transformations

### Missing Values

- **Original:** `-9` codes indicate missing/unknown/not collected
- **Cleaned:** Converted to `NaN` for proper pandas handling

### Categorical Decoding

All categorical variables decoded from numeric codes to text labels:

| Variable | Before | After |
|----------|--------|-------|
| AGE | `7` | `'35-39'` |
| SEX | `1` | `'Male'` |
| SUB1 | `5` | `'Heroin'` |
| SERVICES | `7` | `'Non-intensive Outpatient'` |
| EMPLOY | `3` | `'Unemployed'` |

### Column Renaming

All columns renamed for clarity:

| Original | Cleaned |
|----------|---------|
| `CASEID` | `patient_id` |
| `SERVICES` | `service_type` |
| `DAYWAIT` | `wait_time_days` |
| `NOPRIOR` | `prior_treatments` |
| `LIVARAG` | `living_arrangement` |

---

## Data Quality Notes

### Missing Data Strategy

1. **Minimal Removal (Analysis-Ready)** - Recommended for statistical tests
   - Remove only rows missing: `patient_id`, `service_type`, `primary_substance`
, `age_group`, `sex`
   - Retains ~95% of data
   - Uses pairwise deletion for remaining variables
   - Maximizes statistical power

1. **Imputation (ML-Ready)** - For machine learning
   - Median for continuous variables
   - Mode for categorical variables
   - 0 for binary flags
   - Retains 100% of data

### Missing Data Patterns

Key variables with high missing rates (>20%):

- `wait_time_days` (53.7%)
- `payment_source` (53.4%)
- `income_source` (34.6%)
- `marital_status` (29.2%)
- `education_level` (20.9%)

---

## Workflow

### Step 1: Data Cleaning

```bash
# Run main cleaning notebook
jupyter notebook cleaning_teds_a.ipynb
```

**Output:** `teds_a_2023_cleaned.csv`

### Step 2: Missing Value Handling

```bash
# Run missing value handling notebook
jupyter notebook missing_value_handling_teds_a.ipynb
```

**Outputs:**

- `teds_analysis_ready.csv`
- `teds_ml_ready.csv`

### Step 3: Choose Dataset for Your Analysis

- **For EDA:** Use `teds_a_2023_cleaned.csv`
- **For Statistical Tests:** Use `teds_analysis_ready.csv`
- **For ML Models:** Use `teds_ml_ready.csv`

---

## Usage

### As Jupyter Notebook

#### Main Cleaning

1. Open `cleaning_teds_a.ipynb`
2. Update file paths if needed
3. Run all cells sequentially
4. Cleaned data will be saved automatically

#### Missing Value Handling

1. Open `missing_value_handling_teds_a.ipynb`
2. Ensure cleaned dataset exists
3. Run all cells sequentially
4. Two datasets will be created for different analyses
