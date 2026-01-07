# 📕 Data Requirement Documentation

### 📕 Overview

This document describes the **data foundations** of the atlas project.

The project integrates multiple **public NYC education datasets** into a coherent analytical model using Python and PostgreSQL.  
Rather than documenting data by onboarding steps, the datasets are organized by their **functional role within the analytics and ETL workflow**.

The purpose of this document is to:
- clearly describe all data sources used
- explain how datasets relate to each other
- document structural characteristics and limitations
- support transparency, reproducibility, and professional review

The analytical focus is **descriptive**, not predictive.

---

### 📂 Dataset Inventory (Quick Reference)

| Dataset | Domain | Analytical Role |
|------|-------|----------------|
| High School Directory | Reference Data | School metadata & attributes |
| School Safety Report | Behavioral / Incident Data | Safety & incident metrics |
| School Demographics | Demographic Data | Enrollment & population metrics |
| SAT Results | Outcome Data | Academic performance metrics |

---

### 📂 Core Reference Data

### 📂 Dataset: High School Directory

#### Purpose & Scope
The High School Directory provides **descriptive and administrative metadata** for NYC public high schools.

Each record represents **one school**, uniquely identified by `dbn`.  
This dataset functions as a **reference (dimension) dataset**, enriching all analytical queries with institutional context.

---

#### Data Characteristics

| Attribute | Value |
|---------|------|
| Source | NYC Department of Education |
| Granularity | One row per school |
| Data Type | Snapshot |
| Analytical Role | Dimension dataset |

---

#### Table Schema: `high_school_directory`

**Primary Identifier:** `dbn`

**Column Groups**

| Group | Columns |
|----|--------|
| Identifiers | `dbn`, `building_code` |
| Location | `boro`, `zip`, `latitude`, `longitude` |
| Enrollment | `total_students` |
| Grade Coverage | `grade_span_min`, `grade_span_max` |
| Programs | `number_programs`, `program_highlights` |
| Accessibility | `school_accessibility_description` |
| Descriptive Text | `overview_paragraph`, `language_classes`, `advancedplacement_courses` |

---

#### Dimensions vs Measures

|                       | Dimensions        | Measures          |
|-----------------------|-------------------|-------------------|
| high_school_directory | dbn               | total_students    |
|                       | boro              | number_programs   |
|                       | school_type       | -                 |
|                       | grade_span_min    | -                 |
|                       | grade_span_max    | -                 |
|                       | accessibility     | -                 |

---

#### Data Quality Notes
- Grade span fields may contain non-numeric encodings
- Many attributes are free-text and non-standardized
- Transportation and metadata fields are excluded from analytics

---

### 📂 Behavioral & Incident Data

### 📂 Dataset: School Safety Report (2015–2016)

#### Purpose & Scope
This dataset provides **aggregated counts of violent, non-violent, and non-criminal incidents** reported at NYC public school buildings.

Each record represents a **school building or campus** for a given academic year.  
The data is collected by the NYPD and published via the NYC Department of Education.

---

#### Data Characteristics

| Attribute | Value |
|---------|------|
| Source | NYC DOE / NYPD |
| Update Frequency | Annual |
| Granularity | School building / campus |
| Time Coverage | 2015–2016 |
| Data Type | Aggregated counts |
| Analytical Role | Fact dataset |

---

#### Table Schema: `school_safety_report`

**Primary Identifier:** `dbn` (non-unique across years)

**Column Groups**

| Group | Columns |
|----|--------|
| Identifiers | `dbn`, `building_code`, `location_code` |
| Location | `borough`, `postcode`, `latitude`, `longitude` |
| Enrollment | `register`, `engroupa`, `rangea` |
| Incident Counts | `major_n`, `oth_n`, `nocrim_n`, `prop_n`, `vio_n` |
| Reference Averages | `avgofmajorn`, `avgofothn`, `avgofnocrimn`, `avgofpropn`, `avgofvion` |

---

#### Dimensions vs Measures

|                      | Dimensions                 | Measures     |
|----------------------|----------------------------|--------------|
| school_safety_report | dbn                        | major_n      |
|                      | borough                    | oth_n        |
|                      | geographical_district_code | nocrim_n     |
|                      | building_name              | prop_n       |
|                      | engroupa                   | vio_n        |
|                      | rangea                     | register     |

---

#### Data Quality Notes
- Data is aggregated (not event-level)
- `NA` values may indicate consolidated reporting
- Counts reflect reported incidents only
- Suitable for descriptive, non-causal analysis

---

### 📂 Demographic Data

### 📂 Dataset: School Demographics

#### Purpose & Scope
This dataset provides **enrollment counts and demographic composition** per school and school year.

Each record represents **one school in a specific academic year**, enabling longitudinal and comparative analysis.

---

#### Data Characteristics

| Attribute | Value |
|---------|------|
| Grain | One school per school year |
| Key | (`dbn`, `schoolyear`) |
| Analytical Role | Fact dataset |

---

#### Measures
- total enrollment by grade
- ELL and special education percentages
- race-based and gender-based distributions

---

#### Data Quality Notes
- Percentages are derived from enrollment counts
- Multi-year structure requires explicit year filtering
- Demographic categories follow NYC DOE definitions

---

### 📂 Academic Outcome Data

### 📂 Dataset: NYC SAT Results

#### Purpose & Scope
The SAT Results dataset provides **school-level average SAT performance metrics** for NYC public high schools.

It is primarily used to demonstrate **data cleaning, validation, and ETL integration** into an existing relational schema.

---

#### Data Characteristics

| Attribute | Value |
|---------|------|
| Source | NYC Department of Education |
| Granularity | One row per school |
| Data Type | Aggregated performance metrics |
| Analytical Role | Fact dataset |

---

#### Final Table Schema: `sat_results`

**Primary Identifier:** `dbn`

**Measures**
- sat_critical_reading_avg_score  
- sat_math_avg_score  
- sat_writing_avg_score  
- pct_students_tested  

---

#### Cleaning & Exclusions
The following fields were excluded during ETL:
- duplicate reading score column (typo)
- internal system identifiers
- contact extensions
- malformed percentage strings

Validation rules applied:
- SAT scores restricted to **200–800**

---

### 📂 Data Relationships & Integration Logic

All datasets are linked using the **District Borough Number (`dbn`)**.

| From Dataset | Join Key | To Dataset |
|------------|----------|-----------|
| School Safety Report | dbn | School Demographics |
| School Safety Report | dbn | High School Directory |
| School Demographics | dbn | High School Directory |
| SAT Results | dbn | School Demographics |
| SAT Results | dbn | High School Directory |

These relationships enable school-level analysis across **institutional context, demographics, safety, and academic outcomes**.

---

### 📌 Summary

The data architecture of this project reflects real-world analytics environments:
- multiple heterogeneous data sources
- mixed snapshot and time-series data
- explicit dimensional modeling
- ETL workflows with documented validation rules

This structure supports transparent, reproducible, and extensible analytical work.