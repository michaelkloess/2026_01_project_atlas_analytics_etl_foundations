# 📕 Bias Injection Log

**📌 Overview**
This document records potential biases introduced during the data preparation and analytical processing that may influence NYC education analysis outcomes. These biases stem from structural properties of source data, conservative data quality decisions, and necessary technical assumptions for joins and validation.

## 📕 Identified Bias Sources

### 📌 Aggregated Incident Reporting (School Safety – VADIR)
**Bias Type:** Structural / Reporting Bias
**Source:** Upstream data aggregation at school level

**Applied Assumptions:**
```
├── Event-Level Data: Not Available
│   └── Impact: Individual incident context lost
├── Temporal Granularity: Not Available
│   └── Impact: Time-series analysis not possible
├── School-Level Aggregation: Default Format
│   └── Impact: Within-school variation masked
└── Enrollment Normalization: Not Available at Incident Level
    └── Impact: Large schools appear disproportionately
```

**Potential Impact:**
- **Overrepresentation Risk:** Large schools with higher enrollment dominate incident counts
- **Variability Masking:** Within-school incident patterns not observable
- **Temporal Limitations:** Cannot analyze incident frequency changes over time
- **Context Loss:** Individual-level and situational factors not captured

**Mitigation Considerations:**
- Normalize incidents by enrollment size where possible
- Restrict interpretations to descriptive, not causal, analysis
- Explicitly avoid time-series or behavioral inference

### 📌 Geographic Normalization Choices
**Bias Type:** Analytical Framing Bias
**Source:** Borough-level normalization decisions

**Applied Logic:**
- Incident counts compared in absolute terms
- Normalization by number of schools applied
- Enrollment-based normalization not available at incident level

**Potential Impact:**
- **School Size Bias:** Boroughs with fewer but larger schools may appear underrepresented
- **Normalization Dependency:** Comparisons strongly depend on chosen normalization method
- **Ranking Sensitivity:** Borough rankings change based on normalization approach

**Mitigation Considerations:**
- Present both absolute and normalized views
- Avoid ranking boroughs without contextual qualifiers
- Document normalization assumptions clearly

### 📌 Data Quality Corrections

#### 📌 DBN Join Normalization
**Correction Applied:** Identifier standardization logic

**Applied Logic:**
```sql
TRIM(UPPER(dbn))
```

**Rationale:**
- DBN identifiers required trimming and casing normalization to ensure joins across datasets
- Some records contained whitespace or inconsistent casing
- Ambiguous or multiple DBNs present in source data

**Bias Impact:**
- **Join Loss:** Records with ambiguous identifiers excluded from certain joins
- **Undercounting Risk:** Potential undercounting of affected schools in integrated analyses
- **Conservative Approach:** Ambiguous DBNs documented, not corrected

**Validation:** Ambiguous cases explicitly preserved or excluded; no inferred identifiers introduced

#### 📌 SAT Score Validation (Range Enforcement)
**Correction Applied:** Invalid score handling

**Applied Logic:**
```python
# Valid SAT score range: 200-800
invalid = df[col][~(df[col].between(200, 800) | df[col].isna())]
# Invalid scores set to NULL, records retained
```

**Rationale:**
- Invalid SAT scores (outside 200–800) set to NULL
- Non-numeric placeholders coerced to NULL
- Records not dropped unless exact duplicates

**Bias Impact:**
- **Data Reduction:** Reduction in usable outcome data for some schools
- **Reporting Bias:** Possible bias toward schools with cleaner reporting practices
- **Missingness Preference:** Favor missingness over speculative correction

**Validation:** Retain records to preserve institutional coverage; explicitly flag missingness during analysis

#### 📌 Duplicate Removal Strategy
**Correction Applied:** Exact-match deduplication

**Applied Logic:**
```python
df = df.drop_duplicates(keep="first")
```

**Rationale:**
- Only exact duplicates removed
- Near-duplicates or logically overlapping records retained
- Conservative data retention approach

**Bias Impact:**
- **Redundancy Tolerance:** Some redundancy may persist
- **Conservative Retention:** Bias toward keeping potentially duplicate records
- **Data Loss Prevention:** Avoid accidental removal of legitimate records

**Validation:** Avoid heuristic deduplication without ground truth; accept minimal redundancy over data loss

### 📌 Partial Outcome Coverage (SAT Participation)
**Bias Type:** Coverage Bias
**Source:** Incomplete reporting across schools

**Observed Patterns:**
- Not all schools report SAT participation or scores
- Participation rates vary significantly by borough and school type
- Selective reporting may correlate with academic performance

**Potential Impact:**
- **Academic Engagement Bias:** Outcome analyses may overrepresent academically engaged schools
- **Reporting vs. Performance:** Borough-level comparisons may reflect reporting differences rather than true performance
- **Selection Effects:** Schools with low participation may systematically differ from high-participation schools

**Mitigation Considerations:**
- Include participation rate as contextual variable
- Avoid imputing missing outcome data
- Clearly state coverage limitations in all analyses

## 📕 Bias Mitigation Strategies

### 📌 Documentation & Transparency
- **Complete Logging:** All normalization, validation, and exclusion logic documented
- **No Silent Corrections:** No silent corrections or inferred values applied
- **Explicit Handling:** Ambiguous cases explicitly preserved or excluded

### 📌 Analytical Guardrails
- **Descriptive Focus:** Restrict conclusions to descriptive and comparative insights
- **Causal Avoidance:** Avoid causal inference or ranking-based judgments
- **Separated Discussion:** Separate structural bias discussion from analytical results

### 📌 Data-Driven Preference
- **Observable Metrics:** Favor observable metrics over inferred characteristics
- **Transparent Missingness:** Allow missingness to surface structural limitations
- **Upstream Deference:** Defer unresolved issues to upstream data governance

## 📕 Impact Assessment

### 📌 High Risk Biases
1. **Aggregated Incident Reporting:** Limits granularity and causal interpretation
2. **Partial SAT Coverage:** May create systematic bias toward certain school types

### 📌 Medium Risk Biases
1. **Geographic Normalization Choices:** Interpretation depends heavily on chosen normalization
2. **Reporting Variability:** Different data quality standards across schools and boroughs

### 📌 Low Risk Biases
1. **DBN Normalization:** Technical correction aligned with data integrity
2. **Numeric Validation:** Conservative handling prevents invalid data propagation
3. **Deduplication:** Minimal impact with conservative approach

## 📕 Recommendations

### 📌 Immediate Actions
- Always accompany borough-level comparisons with normalization context
- Explicitly report missingness and coverage rates in all analyses
- Avoid interpretive language implying causality
- Document which schools are excluded from each analysis due to missing data

### 📌 Long-term Monitoring
- Seek event-level incident data if available from upstream sources
- Establish authoritative DBN reference mappings with data governance team
- Improve upstream reporting consistency for academic outcomes
- Track bias impact on analytical conclusions over time

**Next Steps:** The documented biases primarily stem from structural properties of source data and conservative data engineering decisions. All identified bias sources are explicitly documented, minimally invasive, and aligned with analytical integrity over completeness. This log serves as a safeguard to ensure transparency, reproducibility, and responsible interpretation in subsequent analytical and modeling stages.