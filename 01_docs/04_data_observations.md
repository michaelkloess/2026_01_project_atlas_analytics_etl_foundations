# 📕 Data Observations Documentation

## 📌 Overview
This document presents key observations from exploratory data analysis and ETL preparation of multiple NYC education datasets. The focus is on identifying data quality characteristics, structural limitations, and descriptive patterns across behavioral (school safety), demographic, and academic outcome data. All observations are strictly derived from documented exploration and cleaning steps, without speculative interpretation.

---

## 📕 Data Quality Assessment

### 📌 School Safety Report (VADIR)

- **6,314 total rows**, of which **6,312 records** are usable after excluding empty columns  
- **1,890 unique schools** represented  
- Data is **aggregated at the school level**, not at individual incident level  
- **No duplicate rows** detected at the aggregation level  

**Key Characteristics:**
- Most frequent incident category: **non-criminal crimes**
- Incident counts reflect reported totals, not unique events
- Missing values likely stem from incomplete reporting or campus-level aggregation

**Quality Status:**  
No imputation or corrective transformations applied; data interpreted descriptively.

---

### 📌 High School Directory

- Core reference dataset used for joins and contextualization
- Column names required normalization due to inconsistent casing and spacing
- Borough values required case normalization for reliable filtering

**Quality Status:**  
Schema normalization applied; no row-level data removal required.

---

### 📌 NYC SAT Results

- Dataset contained **mixed data types** in numeric columns
- **Exact duplicate rows** detected and removed
- SAT score columns included **invalid values outside the 200–800 range**
- Percentage fields exhibited **inconsistent formatting** (numeric, percent strings, placeholders)

**Quality Status:**  
Conservative cleaning applied:
- Invalid numeric values coerced to `NULL`
- Duplicates removed only when exact matches
- Ambiguous identifiers documented but not corrected

---

## 📕 Descriptive Observations

### 📌 Geographic Distribution of Incidents

- Incidents are **geographically concentrated**:
  - **Brooklyn and the Bronx** together account for **58.37 %** of all reported incidents
  - **Staten Island and N/A** contribute marginal shares (**7.34 % combined**)

**Normalized by number of schools per borough:**
- Bronx: **31 %**
- Brooklyn: **30.17 %**
- Manhattan: **22.21 %**
- Staten Island: **2.69 %**

These patterns indicate uneven incident distribution relative to institutional density.

---

### 📌 Incident Reporting Structure

- Reporting is **not event-granular**, limiting temporal or causal inference
- Aggregation masks within-school variability
- Results are suitable for **borough- or school-level comparison only**

---

### 📌 Academic Outcome Data Characteristics

**SAT Results:**
- Presence of placeholder values (`"N/A"`, `"s"`) in numeric fields
- Some schools report SAT participation percentages with missing or non-numeric formats
- Not all schools report complete SAT information

**Implication:**  
Outcome analyses must account for partial coverage and missingness.

---

## 📕 Cross-Dataset Integration Observations

- **DBN** is the primary join key across all datasets
- Join reliability required:
  - Trimming whitespace
  - Uppercasing identifiers
- Some DBN fields contained **multiple identifiers**, which were left unresolved to avoid unsafe assumptions
- No denormalized master table persisted; all integration performed at query time

---

## 📕 Analytical Limitations

- Incident data aggregation limits causal analysis
- Missing demographic and outcome data reduces comparability across schools
- Ambiguous identifiers restrict full automation of joins
- No statistical modeling or hypothesis testing performed at this stage

---

## 📕 Data Quality Recommendations

### 📌 Immediate Actions

1. **Incident Data Granularity Review**
   - Assess feasibility of acquiring event-level safety data

2. **Identifier Governance**
   - Establish authoritative DBN reference mapping
   - Flag multi-identifier records upstream

3. **Outcome Coverage Validation**
   - Quantify schools missing SAT participation or score data
   - Assess borough-level completeness bias

---

### 📌 Monitoring Metrics

- Incident counts per school normalized by enrollment
- SAT participation rate distribution
- Missing-value rates by borough and dataset
- Join success rate across domains

---

## 📕 Summary

The exploratory and preparatory analysis confirms that:
- Data quality issues are present but **explicitly documented and controlled**
- Cleaning decisions were conservative, reversible, and reproducible
- The resulting datasets are suitable for **descriptive, comparative, and modeling-oriented analysis**, within clearly defined limitations

These observations provide the empirical foundation for subsequent analytical and modeling phases of the project.
