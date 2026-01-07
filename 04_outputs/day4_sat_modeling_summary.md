# SAT Results – Data Cleaning & ETL Documentation

## Cleaning Logic Overview

The dataset was processed using a structured ETL workflow consisting of **exploration, transformation, validation, and loading** phases.

During **Data Exploration**, all columns were inspected to identify data quality issues such as invalid values, inconsistent formatting, duplicate records, and ambiguous identifiers. No data was modified during this phase; issues were only documented and, where appropriate, flagged.

During **Transformation**, the following steps were applied:

- Column names were normalized to snake_case to ensure database compatibility and consistency.
- Exact duplicate rows were removed to prevent redundant records in the database.
- Numeric columns containing invalid string values (e.g. `"s"`, `"N/A"`) were coerced to numeric types, with invalid entries converted to `NULL`.
- SAT score columns were validated against the official score range (200–800); out-of-range values were set to `NULL`.
- Percentage values were normalized by removing percent symbols and converting values to numeric form.
- Redundant, administrative, or derived columns were deliberately excluded from the final dataset.
- Exploratory quality-check flags were removed before loading to keep the target schema clean.

All transformations were followed by targeted sanity checks to ensure that data invariants were satisfied before loading.

---

## Challenges Encountered

- **Inconsistent data types in numeric fields**  
  Several columns expected to be numeric contained string placeholders such as `"s"` or `"N/A"`, requiring careful type coercion without dropping entire records.

- **Invalid SAT score values**  
  Some SAT scores fell outside the valid range (including negative values), which needed to be detected and safely neutralized.

- **Duplicate records in the source data**  
  The dataset contained multiple exact duplicates that would have resulted in redundant database entries if not handled explicitly.

- **Ambiguous identifiers (DBN)**  
  Certain rows contained multiple school identifiers within a single field, making automated correction unsafe. These cases were documented rather than modified.

- **Inconsistent formatting in percentage fields**  
  The `pct_students_tested` column mixed numeric percentages, strings with percent symbols, and placeholder values, requiring normalization.

- **Separating exploration from transformation logic**  
  Maintaining a strict separation between inspection and mutation was essential to ensure transparency and reproducibility.

- **Safe database integration**  
  Establishing a secure SSL connection to a cloud-hosted PostgreSQL (Neon) database and ensuring transactional inserts required careful handling.

---

## Database Schema & Integration Notes

### Final Table Schema

The following columns were selected for persistence in the database:

- `dbn` (TEXT)
- `school_name` (TEXT)
- `num_of_sat_test_takers` (INTEGER)
- `sat_critical_reading_avg_score` (INTEGER)
- `sat_math_avg_score` (INTEGER)
- `sat_writing_avg_score` (INTEGER)
- `pct_students_tested` (NUMERIC)

Exploratory flags and administrative or derived fields were intentionally excluded to maintain a clean, analysis-ready schema.

### Integration Strategy

- The table is created automatically using `pandas.to_sql()` if it does not already exist.
- Data is inserted using `if_exists="append"` within a transactional context to ensure atomic writes.
- SSL is enforced via the database connection string to ensure secure communication.
- Post-insert validation is performed using a row count query to confirm successful loading.

---

## Summary

This workflow demonstrates a reproducible and reviewable ETL process that prioritizes data quality, transparency, and safe interaction with external systems. All cleaning decisions were driven by documented observations, and no speculative corrections were applied.
