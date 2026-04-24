# Language Disparities in Patients Undergoing IMV

**CLIF Version:** 2.1

Measure disparities in sedation depth and paralytic usage for patients with non-English EHR documented language preferences.

**1st Author:** Alex Ortiz \| **Senior Author:** Gary Weissman

## Objective

Disparities in sedation depth have previously been found in racial and ethnic minorities; however, no studies to date have looked into language-based disparities in sedation depth.

## Important: ED + Floor Data Required

> **This study is limited to individuals undergoing invasive mechanical ventilation in the ICU.** However, time from intubation to ICU arrival is part of the inclusion criteria and thus data from the **Emergency Department (ED)** and **wards** locations are necessary. Sites with ICU-only data cannot participate.

ADT records must include `location_category` values for `ed`, `icu`, and floor locations.

## Required CLIF Tables and Fields

### 1. `patient`

| Column               | Description                        |
|----------------------|------------------------------------|
| `patient_id`         | Unique patient identifier          |
| `race_category`      | Race category                      |
| `sex_category`       | Sex category                       |
| `ethnicity_category` | Ethnicity category                 |
| `language_name`      | Language name                      |
| `language_category`  | Language category                  |
| `death_dttm`         | Date/time of death (if applicable) |

### 2. `hospitalization`

| Column                    | Description                       |
|---------------------------|-----------------------------------|
| `patient_id`              | Unique patient identifier         |
| `hospitalization_id`      | Unique hospitalization identifier |
| `admission_dttm`          | Admission date/time               |
| `discharge_dttm`          | Discharge date/time               |
| `age_at_admission`        | Age at admission (years)          |
| `discharge_category`      | Discharge disposition             |
| `admission_type_category` | Admission type (e.g., ED, OSH)    |
| `zipcode_five_digit`      | 5-digit zip code                  |

### 3. `adt`

| Column               | Description                                    |
|----------------------|------------------------------------------------|
| `hospitalization_id` | Unique hospitalization identifier              |
| `hospital_id`        | Hospital identifier                            |
| `location_category`  | Location category (`ed`, `icu`, floor)         |
| `location_type`      | Location subtype (e.g., MICU, SICU)            |
| `in_dttm`            | Location entry date/time                       |
| `out_dttm`           | Location exit date/time                        |

### 4. `hospital_diagnosis`

| Column               | Description                                                            |
|----------------------|------------------------------------------------------------------------|
| `hospitalization_id` | Unique hospitalization identifier                                      |
| `diagnosis_code`     | ICD diagnosis code (used for Elixhauser Comorbidity Index calculation) |

### 5. `labs`

| Column               | Description                       |
|----------------------|-----------------------------------|
| `hospitalization_id` | Unique hospitalization identifier |
| `lab_result_dttm`    | Lab result date/time              |
| `lab_order_dttm`     | Lab order date/time               |
| `lab_collect_dttm`   | Lab collection date/time          |
| `lab_category`       | Lab category                      |
| `lab_value_numeric`  | Numeric lab value                 |

Used for LAPS2 and respiratory failure calculations.

**Required `lab_category` values:** `creatinine`, `bilirubin_total`, `platelet_count`, `po2_arterial`, `pco2_arterial`, `ph_arterial`, `ph_venous`, `pco2_venous`, `anion_gap`, `troponin_t`, `troponin_i`, `sodium`, `bicarbonate`, `chloride`, `bun`, `albumin`, `glucose_serum`, `hemoglobin`, `hematocrit`, `lactate`, `so2_arterial`, `wbc`

### 6. `vitals`

| Column               | Description                       |
|----------------------|-----------------------------------|
| `hospitalization_id` | Unique hospitalization identifier |
| `recorded_dttm`      | Vital sign recorded date/time     |
| `vital_category`     | Vital sign category               |
| `vital_value`        | Vital sign value                  |

Used for LAPS2, BMI, and respiratory failure calculations.

**Required `vital_category` values:** `height_cm`, `weight_kg`, `sbp`, `dbp`, `map`, `spo2`, `temp_c`, `heart_rate`, `respiratory_rate`

### 7. `medication_admin_continuous`

| Column               | Description                       |
|----------------------|-----------------------------------|
| `hospitalization_id` | Unique hospitalization identifier |
| `admin_dttm`         | Administration date/time          |
| `med_category`       | Medication category               |
| `med_group`          | Medication group                  |
| `med_dose`           | Dose amount                       |
| `med_dose_unit`      | Dose unit                         |
| `med_route_category` | Route of medication               |

**Required `med_group` values:** `vasoactives`, `sedation`, `paralytics`, `analgesia`, `paralytic`

### 8. `respiratory_support`

| Column                          | Description                        |
|---------------------------------|------------------------------------|
| `hospitalization_id`            | Unique hospitalization identifier  |
| `recorded_dttm`                 | Recorded date/time                 |
| `device_category`               | Respiratory device category        |
| `mode_category`                 | Ventilation mode                   |
| `tracheostomy`                  | Flag for tracheostomy presence     |
| `fio2_set`                      | FiO2 setting                       |
| `lpm_set`                       | Liters per minute setting          |
| `peep_set`                      | PEEP setting                       |
| `tidal_volume_set`              | Tidal volume setting               |
| `resp_rate_set`                 | Respiratory rate setting           |
| `pressure_control_set`          | Pressure control setting           |
| `pressure_support_set`          | Pressure support setting           |
| `flow_rate_set`                 | Flow rate setting                  |
| `inspiratory_time_set`          | Inspiratory time setting           |
| `peak_inspiratory_pressure_set` | Peak inspiratory pressure setting  |

### 9. `medication_admin_intermittent`

| Column               | Description                       |
|----------------------|-----------------------------------|
| `hospitalization_id` | Unique hospitalization identifier |
| `admin_dttm`         | Administration date/time          |
| `med_category`       | Medication category               |
| `med_group`          | Medication group                  |
| `med_dose`           | Dose amount                       |
| `med_dose_unit`      | Dose unit                         |
| `med_route_category` | Route of medication               |

**Required `med_group` values:** `paralytic`, `paralytics`, `sedation`, `analgesia`

### 10. `patient_assessments`

| Column                | Description                       |
|-----------------------|-----------------------------------|
| `hospitalization_id`  | Unique hospitalization identifier |
| `recorded_dttm`       | Recorded date/time                |
| `assessment_category` | Assessment category               |
| `numerical_value`     | Numerical assessment value        |
| `categorical_value`   | Categorical assessment value      |

**Required `assessment_category` values:** `rass`, `gcs_total`

## Cohort Identification

The base cohort is defined by the following inclusion criteria:

1. **Age**: 18 years or older at admission
2. **IMV duration**: Intubated for at least 4 hours
3. **Tracheostomy status**: No tracheostomy present prior to IMV
4. **Admission type**: Intubation occurring after hospital admission (no pre-admission intubations)
5. **Time to ICU admission**: ICU admission within 4 hours of IMV start
6. **First IMV episode only**: Primary cohort includes only the first episode of IMV per hospitalization
7. **RASS documentation**: Timely and complete RASS documentation throughout IMV episode
8. **Complete demographic and anthropometric data**: Available height, weight, age, sex, race, ethnicity, language, zip code, and hospital and ICU location identifiers
9. **Complete hospital diagnosis data**: Sufficient diagnosis codes available for Elixhauser comorbidity index calculation

From the base cohort, runs of invasive mechanical ventilation are determined to begin by identifying rows where `device_category` is "imv" and there are a minimum of 2 IMV-related settings. They are stopped for either 2 or more consecutive explicit non-IMV rows (i.e. `device_category` is "room air" or "nippv") OR due to a gap of greater than or equal to 24 hours between IMV signal rows.

Key subgroups include acute respiratory failure patients, patients with Hispanic ethnicity, and patients admitted to MICUs.

There are 4 total studies in this project: 1) English vs Spanish sedation depth in all IMV patients, 2) broad regional language groups sedation depth in ARF patients only, 3) paralytic use across broad regional language groups, and 4) evaluation of measures of early sedation depth.

## Expected Results

Output files are written to the directory specified by `local_dir` in `deep_sedation_language_config.yaml`.

All final files will be in the `output` folder, which contains a subfolder named `FINAL_DATA_UPLOAD_ME`. This folder contains 5 subfolders: `arf_project`, `cohort_characteristics`, `measurements_project`, `paralytics_project`, and `sedation_project`. Each contains `final_data` and `final_tables` subdirectories with regression model outputs and Table 1s respectively.

Protected intermediate files (`analytic_file.parquet`, `cleaned_cohort_w_laps.parquet`) are written to the `intermittent_data_DO_NOT_UPLOAD` folder.

## Prerequisites

- **R**: version 4.0 or higher recommended
- **R packages**: All required packages are automatically installed at the start of `00_cohort_id.Rmd`. The following packages are used:

```r
packages <- c("tidyverse", "zoo", "data.table", "dplyr", "lubridate", "tidyr", "fst",
              "arrow", "table1", "lme4", "metafor", "yaml", "pscl", "MASS", "htmltools",
              "car", "WeightIt", "MatchIt", "purrr", "survival", "mediation", "cmprsk",
              "gfoRmula", "nnet", "medflex", "bruceR", "survey", "ipw", "geepack",
              "readr", "collapse", "stringr", "rprojroot", "tableone", "lmtest")
```

## Instructions

### 1. Configure `deep_sedation_language_config.yaml`

Rename the template file and fill in site-specific paths:

```bash
cp deep_sedation_language_config_template.yaml deep_sedation_language_config.yaml
```

```yaml
data_dir: ""       # Path to CLIF tables — must end with "/"
local_dir: ""      # Path to project directory — must end with "/"
file_type: ".parquet"
institution: "YourInstitution"
time_zone: "EST"   # Your site's local timezone
```

- `data_dir`: Path to your CLIF 2.1 parquet (or CSV) tables
- `local_dir`: Where intermediate and final results will be saved
- `file_type`: File extension of your CLIF tables (e.g. `.parquet` or `.csv`)
- `institution`: Your site name, used for labeling output files
- `time_zone`: Your site's local timezone

### 2. Run `code/00_cohort_id.Rmd` in RStudio

> **Note:** 256GB of RAM is recommended for sites with larger databases; 128GB may be sufficient for smaller databases.

### 3. Run `code/01_analysis.Rmd` in RStudio

### 4. Upload results to Box

Upload the contents of `FINAL_DATA_UPLOAD_ME` to the shared Box folder:
https://app.box.com/folder/291920680241

## Questions

Please contact **Alex Ortiz on Slack** with any questions or issues.