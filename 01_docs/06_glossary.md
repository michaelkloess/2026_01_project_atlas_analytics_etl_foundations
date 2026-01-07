# 📕 Comprehensive Glossary  

---

## 📌 Overview

This glossary defines **all technical terms, datasets, columns, metrics, and analytical concepts** used throughout the NYC education analytics project.  
It serves as the **single source of truth** for terminology referenced in documentation, notebooks, SQL queries, and analytical outputs.

All definitions are descriptive and aligned with the actual data preparation and observation steps applied in the project.

---

## 📕 Core Datasets

### 📌 School Safety Report (VADIR)

Aggregated dataset reporting school-level safety and incident information.

| Term | Description |
|-----|-------------|
| **Incident** | A reported safety-related occurrence aggregated at school level |
| **Incident Type** | Categorical classification of reported incidents (e.g. non-criminal crimes) |
| **Aggregated Reporting** | Reporting structure where incidents are summarized per school rather than logged individually |
| **Borough** | NYC administrative region (Bronx, Brooklyn, Manhattan, Queens, Staten Island) |
| **NA Borough** | Records without a borough assignment |

---

### 📌 High School Directory

Reference dataset containing institutional and structural information for NYC high schools.

| Term | Description |
|------|------------|
| **DBN** | District Borough Number – unique school identifier |
| **Borough (Directory)** | Administrative borough associated with a school |
| **Grade Span Min / Max** | Lowest and highest grade levels offered by a school |
| **Total Students** | Reported enrollment count per school |
| **Reference Dataset** | Dataset used primarily for joins and contextual enrichment |

---

### 📌 School Demographics

Dataset describing student population composition at school level.

| Term | Description |
|------|------------|
| **ELL Percent** | Percentage of English Language Learner students |
| **SPED Percent** | Percentage of students receiving special education services |
| **Demographic Coverage** | Degree to which demographic attributes are reported across schools |

---

### 📌 NYC SAT Results

Dataset reporting SAT participation and score outcomes.

| Term | Description |
|------|------------|
| **SAT Critical Reading Avg Score** | Average critical reading score (200–800) |
| **SAT Math Avg Score** | Average math score (200–800) |
| **SAT Writing Avg Score** | Average writing score (200–800) |
| **Pct Students Tested** | Percentage of eligible students who took the SAT |
| **Outcome Coverage** | Proportion of schools with valid SAT reporting |

---

## 📕 Database & Schema Terms

### 📌 Table-Level Concepts

| Term | Description |
|------|------------|
| **Fact-like Table** | Dataset containing measurable outcomes (e.g. SAT scores) |
| **Reference Table** | Dataset used primarily for joins and metadata (e.g. directory) |
| **Aggregated Table** | Dataset summarizing data across multiple events or entities |

---

### 📌 Column Normalization

| Term | Description |
|------|------------|
| **Snake Case** | Naming convention using lowercase letters and underscores |
| **Schema Normalization** | Process of standardizing column names and formats |
| **Join-Safe Columns** | Columns formatted consistently to support reliable joins |

---

## 📕 Join & Integration Terms

| Term | Description |
|------|------------|
| **Primary Join Key** | Field used to join datasets (`dbn`) |
| **Join Normalization** | Trimming whitespace and normalizing casing before joins |
| **Join Loss** | Records excluded due to unmatched identifiers |
| **Query-Time Integration** | Joining datasets dynamically rather than persisting a master table |

---

## 📕 Data Quality & Validation Terms

### 📌 Validation Concepts

| Term | Description |
|------|------------|
| **Range Validation** | Checking numeric values fall within known bounds |
| **Type Coercion** | Converting values to numeric types, invalid entries set to `NULL` |
| **Duplicate Record** | Exact row-level repetition of data |
| **Missingness** | Absence of data values (`NULL`, `NA`) |

---

### 📌 Known Data Quality Issues

| Issue | Description | Handling |
|------|-------------|----------|
| **Out-of-Range SAT Scores** | Scores outside 200–800 | Coerced to `NULL` |
| **Mixed Percentage Formats** | Percent strings, numbers, placeholders | Normalized or set to `NULL` |
| **Ambiguous DBNs** | Multiple identifiers in one field | Documented, not corrected |
| **Aggregated Incidents** | No event-level detail | Interpreted descriptively |

---

## 📕 Analytical Concepts

### 📌 Descriptive Analysis

| Term | Description |
|------|------------|
| **Exploratory Data Analysis (EDA)** | Initial inspection of data structure and quality |
| **Descriptive Statistics** | Counts, averages, distributions without inference |
| **Normalization** | Adjusting values for fair comparison (e.g. per school) |

---

### 📌 Bias & Limitations

| Term | Description |
|------|------------|
| **Structural Bias** | Bias introduced by how data is collected or reported |
| **Coverage Bias** | Bias due to incomplete reporting across entities |
| **Analytical Framing Bias** | Bias introduced by choice of aggregation or normalization |
| **Conservative Cleaning** | Preferring missing values over speculative correction |

---

## 📕 SQL & Technical Terms

### 📌 SQL Operations

| Term | Description |
|------|------------|
| **CTE (Common Table Expression)** | Temporary query result used for structuring logic |
| **LEFT JOIN** | Join preserving all records from the left table |
| **GROUP BY** | Aggregation operation |
| **WINDOW FUNCTION** | Calculation over partitions without collapsing rows |

---

### 📌 Python & ETL Concepts

| Term | Description |
|------|------------|
| **ETL** | Extract, Transform, Load workflow |
| **DataFrame** | Tabular data structure in Pandas |
| **Transactional Insert** | Database write operation ensuring atomicity |
| **Schema Freeze** | Explicit selection of final output columns |

---

## 📕 Interpretation Guardrails

| Term | Description |
|------|------------|
| **Descriptive Interpretation** | Reporting observed patterns without causal claims |
| **Causal Inference** | Analysis aiming to explain cause-effect relationships (not applied) |
| **Analytical Scope** | Explicit boundaries of what conclusions are supported |

---

## 📕 Summary

This glossary ensures:
- Consistent interpretation of terms across notebooks and documentation  
- Clear separation between **data properties**, **technical decisions**, and **analytical conclusions**  
- Transparency around limitations, assumptions, and definitions  

It is intended to be referenced alongside:
- Data preparation documentation  
- Data observations  
- Bias injection logs  
- Analytical notebooks  

and should be updated if new datasets, metrics, or concepts are introduced.
