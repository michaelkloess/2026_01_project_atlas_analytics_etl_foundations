# 📕 Data Preparation Documentation

## 📌 Overview
This document outlines the data preparation process for 2026_01_project_atlas_analytics_etl_foundations, focusing on building an analysis-ready foundation from multiple NYC education datasets. The workflow spans Google Sheets, Python (Pandas), and PostgreSQL, and is organized by data domains (not onboarding days).

## 📕 Data Preprocessing Pipeline

### 📌 1. Column Name Standardization (Cross-Domain Convention)

- **Objective**: Ensure consistent, join-safe, and analysis-friendly column names across datasets  
- **Rationale**: Reduces schema friction, improves reproducibility, and avoids errors in grouping/filtering  
- **Output**: Normalized column names (snake_case, alphanumeric + underscores)

## 📕 Behavioral & Incident Data

### 📌 Dataset: School Safety Report (VADIR)

### 📌 2. Google Sheets – Column Name Cleaning

```excel
=REGEXREPLACE(REGEXREPLACE(LOWER('school-safety-report'!A1),"[^a-z0-9]+", "_"), "^_|_$", "")
```

- **Objective**: Normalize raw column headers into a consistent schema
- **Rationale**: Enables stable referencing in formulas and downstream processing
- **Output**: Standardized headers (lowercase, underscore-separated, special chars removed)

### 📌 3. Google Sheets – Descriptive Transformations (Exploration Support)

Count Rows

=ROWS('[Copy] school-safety-report'!A2:A6311)


Count Unique

=COUNTUNIQUE('[Copy] school-safety-report'!C2:C)


% Incidents occurred (Bronx)

=IFERROR(
  SUMIF('[Copy] school-safety-report'!Z2:Z,"Bronx",'[Copy] school-safety-report'!R2:R)
  / SUM('[Copy] school-safety-report'!R2:R),
  0
)


total_values

=SUM('[Copy] school-safety-report'!M2:M)


Unique Boroughs

=UNIQUE('[Copy] school-safety-report'!Z2:Z)


Sorts by values descending

=SORT('Q&A'!A16:B20, 'Q&A'!B16:B20, FALSE)


Objective: Produce quick sanity checks and descriptive statistics (rows, uniques, borough share)

Rationale: Validate data structure and support early insight generation without code

Output: Exploration metrics used for reporting and interpretation

📌 Data Quality Notes (School Safety Report)

Data is aggregated (not event-level)

NA values may indicate missing reporting or consolidated campus-level reporting

No imputation applied; results interpreted descriptively

📕 Core Reference Data
📌 Dataset: High School Directory
📌 4. Python – Ingestion & Column Standardization
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("high-school-directory.csv")

df.columns = (
    df.columns
      .str.lower()
      .str.replace(" ", "_")
      .str.replace("[^a-z0-9_]", "", regex=True)
)


Objective: Load the directory dataset and standardize the schema

Rationale: Required for grouping, filtering, and joins across datasets

Output: Cleaned DataFrame with normalized column names

📌 5. Python – Domain Transformations (Filtering, Aggregation, Visualization)

Brooklyn filter (case-normalized):

filtered_brooklyn_df = df[df["borough"].str.lower() == "brooklyn"]


Grade 9 availability using range logic (min/max span):

nine_grade_schools_brooklyn_df = filtered_brooklyn_df[
    (filtered_brooklyn_df["grade_span_min"] <= 9) &
    (filtered_brooklyn_df["grade_span_max"] >= 9)
]


Borough-level summaries:

schools_per_borough = df.groupby("borough")["dbn"].nunique()

avg_students_per_borough = (
    df.groupby(["borough", "dbn"])["total_students"]
      .mean()
      .groupby("borough")
      .mean()
)

df.groupby("borough")["grade_span_max"].describe()


Visualization:

schools_per_borough.sort_values(ascending=False).plot.bar()
avg_students_per_borough.sort_values(ascending=False).plot.bar()


Objective: Prepare reference context and compute borough-level descriptors

Rationale: Directory data is used to contextualize safety, demographics, and outcomes

Output: Filtered subsets, grouped summaries, and bar charts used in reporting

📕 Demographic Data
📌 Dataset: School Demographics (PostgreSQL)
📌 6. SQL via Python – Join Key Normalization
TRIM(UPPER(hsd.dbn)) = TRIM(UPPER(sd.dbn))


Objective: Ensure consistent dbn matching across relational tables

Rationale: Prevents join loss from casing/whitespace differences

Output: Stable joins for borough-level aggregation and segmentation

📌 7. SQL via Python – Analytical Aggregations (Preparation for Insights)

Schools per borough (unique schools):

SELECT 
  hsd.borough AS borough,
  COUNT(DISTINCT(hsd.dbn)) AS cnt_schools
FROM nyc_schools.high_school_directory AS hsd
JOIN nyc_schools.school_safety_report AS ssr
  ON hsd.dbn = ssr.dbn
GROUP BY hsd.borough
ORDER BY cnt_schools DESC;


Average % ELL per borough:

SELECT
  hsd.borough,
  AVG(sd.ell_percent) AS avg_perc_ell
FROM nyc_schools.high_school_directory hsd
LEFT JOIN nyc_schools.school_demographics sd
  ON TRIM(UPPER(hsd.dbn)) = TRIM(UPPER(sd.dbn))
GROUP BY hsd.borough
ORDER BY avg_perc_ell ASC;


Top 3 schools per borough by SPED % (window function ranking):

WITH per_school AS (
  SELECT
    TRIM(UPPER(dbn)) AS dbn,
    sped_percent
  FROM nyc_schools.school_demographics
  WHERE sped_percent IS NOT NULL
),
ranked AS (
  SELECT
    hsd.borough,
    ps.sped_percent,
    ROW_NUMBER() OVER (PARTITION BY hsd.borough ORDER BY ps.sped_percent DESC) AS rank
  FROM nyc_schools.high_school_directory AS hsd
  JOIN per_school AS ps
    ON TRIM(UPPER(hsd.dbn)) = ps.dbn
  WHERE hsd.borough IS NOT NULL
)
SELECT
  borough,
  sped_percent,
  rank
FROM ranked
WHERE rank <= 3
ORDER BY borough, rank;


Objective: Prepare aggregated outputs for downstream interpretation

Rationale: Borough-level comparisons require clean joins and robust aggregation logic

Output: Query result sets used for analysis summaries

📕 Academic Outcome Data
📌 Dataset: NYC SAT Results
📌 8. Python – Column Standardization (Schema Normalization)
df.columns = (
    df.columns
      .str.strip()
      .str.lower()
      .str.replace(r"[^a-z0-9]+", "_", regex=True)
      .str.replace(r"_+", "_", regex=True)
      .str.strip("_")
)


Objective: Normalize headers into a stable schema for validation + database insertion

Rationale: Prevents column ambiguity and supports consistent downstream logic

Output: Clean column names suitable for ETL

📌 9. Python – Column Exclusions (Reducing Noise & Ambiguity)
df = df.drop(
    columns=[
        "sat_critical_readng_avg_score",
        "internal_school_id",
        "contact_extension",
        "academic_tier_rating"
    ],
    errors="ignore"
)


Objective: Remove redundant, internal, or analytically unclear fields

Rationale: Keep the table outcome-focused and defensible

Output: Reduced schema for reliable modeling and insertion

📌 10. Python – Duplicate Handling
df = df.drop_duplicates(keep="first")


Objective: Ensure uniqueness of records before insertion

Rationale: Prevents duplicated school rows in a fact-like outcomes dataset

Output: De-duplicated DataFrame

📌 11. Python – SAT Score Validation (Range Enforcement)
score_cols = [
    "sat_critical_reading_avg_score",
    "sat_math_avg_score",
    "sat_writing_avg_score",
]

for col in score_cols:
    invalid = df[col][~(df[col].between(200, 800) | df[col].isna())]
    assert invalid.empty, f"{col} has invalid values: {invalid.unique()}"


Objective: Enforce known valid SAT score bounds (200–800)

Rationale: Prevents invalid values from entering the database

Output: Validated score columns, pipeline stops if invalid values remain

📌 12. Python – Percentage Normalization (pct_students_tested)

Invalid pattern detection:

df["pct_students_tested_is_invalid"] = (
    df["pct_students_tested"].isna()
    | df["pct_students_tested"].eq("N/A")
    | ~df["pct_students_tested"].astype(str).str.match(r"^\d+%$", na=False)
)


Normalization and numeric conversion:

df["pct_students_tested"] = df["pct_students_tested"].str.rstrip("%")
df["pct_students_tested"] = pd.to_numeric(df["pct_students_tested"], errors="coerce")


Objective: Convert % strings into numeric values suitable for analysis

Rationale: Percentage fields often contain mixed formatting and missing markers

Output: Numeric pct_students_tested (coerced to NaN when non-convertible)

📌 13. Python – Final Output Columns for Database Insert
final_cols = [
    "dbn",
    "school_name",
    "num_of_sat_test_takers",
    "sat_critical_reading_avg_score",
    "sat_math_avg_score",
    "sat_writing_avg_score",
    "pct_students_tested",
]

df_load = df[final_cols].copy()


Objective: Select and freeze the insertion schema

Rationale: Explicit schemas improve ETL safety and reviewability

Output: Clean, insertion-ready DataFrame

📌 14. PostgreSQL Append (Transactional Insert)
with engine.begin() as conn:
    df_load.to_sql(
        name="michael_kloess_sat_scores",
        schema="nyc_schools",
        con=conn,
        if_exists="append",
        index=False,
        method="multi"
    )


Objective: Append cleaned SAT results into PostgreSQL

Rationale: Transactional insert ensures consistency and reduces partial writes

Output: Populated table nyc_schools.michael_kloess_sat_scores

📕 Integration & Join Strategy

Primary join key across domains: dbn

Join preparation includes trimming and casing normalization where needed

Join approach: executed at query time (no denormalized master table persisted)

📕 Summary

The pipeline combines Sheets-based exploration, Python-based cleaning and validation, and SQL-based aggregation.

Preparation emphasizes schema consistency, explicit validations, and reproducible transformations.

The resulting datasets support school-level analysis across:

institutional context (directory)

safety metrics (incident report)

demographics (enrollment composition)

outcomes (SAT performance)