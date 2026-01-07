# 📕 Project Structure Documentation

**📌 Overview**
This document provides a comprehensive overview, including folder structure, file purposes, and development workflow.

## 📕 Directory Structure

### 📌 Project Root
```
Atlas/
├── README.md                             ← Project overview and goals
```

### 📌 01_docs/ - Business and project documentation
**Purpose:** Comprehensive documentation covering all aspects of the segmentation project
```
├── 🟢 01_docs/
│   ├── 🟢 00_project_structure.md           ← Overview of folders, file purposes, and project organization
│   ├── 🟢 01_business_use_case.md           ← Business goals, KPIs, context
│   ├── 🟢 02_data_requirements.md           ← Required data sources and fields
│   ├── 🟢 03_preparing_data.md              ← Data understanding, cleansing steps
│   ├── 🟢 04_data_observations.md           ← Observations made during EDA
│   ├── 🟢 05_bias_injection_log.md          ← Log of where and how bias was introduced
│   └── 🟢 06_glossary.md                    ← Domain terms and definitions
```

**Status:** 
- 🟢 **Complete:** All documentation files completed with comprehensive coverage
- **Content:** Business context, technical implementation, and strategic recommendations

### 📌 02_data/ - Organized data storage
**Purpose:** Structured data management throughout the analytics pipeline
```
├── 🟢 02_data/
|   ├── 🟢 01_raw/                           ← Just Raw Datasets without any modification
│   ├── 🟢 02_interim/                       ← Intermediate cleaned or transformed data
│   └── 🟢 03_processed/                     ← Final datasets ready for analysis/modeling
```

**Status:** 🟢 **Complete:** Data pipeline established with cleaned and processed datasets

**Data Flow:**
- **Raw → Interim:** Data cleaning and quality corrections
- **Interim → Processed:** Feature engineering and user aggregation
- **Processed:** Production-ready datasets for segmentation

### 📌 03_notebooks/ - Jupyter notebooks in pipeline order
**Purpose:** Sequential analytical workflow from exploration to evaluation
```
├── 🟢 03_notebooks/
│   ├── 🟢 01_python_data_exploration.ipynb   ← Python Data Exploration
│   ├── 🟢 02_sql_via_python.ipynb            ← SQL via Python: NYC School Data Exploration
│   ├── 🟢 03_populating_database.ipynb       ← Populating a AWS PostgreSQL Database
```

**Status:**
- 🟢 **Complete:** Core analytical etl pipeline implemented (notebook 03)

## 📕 Development Workflow

### 📌 File Naming Conventions
- **Sequential Numbering:** 01_, 02_, 03_ for processing order
- **Descriptive Names:** Clear purpose indication
- **Status Indicators:** 🟢 Complete, 🔴 Pending, 🟡 In Progress

### 📌 Documentation Standards
- **Markdown Format:** Consistent .md files for readability
- **Emoji Structure:** 📕 for main sections, 📌 for subsections
- **Cross-References:** Links between related documents
- **Version Control:** Git-friendly plain text formats

## 📕 Quality Assurance

### 📌 Completeness Tracking
**Current Project Status:**
- 🟢 **Documentation:** 7/7 files complete (100%)
- 🟢 **Notebooks:** 3/3 files complete (100%)
- 🟢 **Data Pipeline:** Complete with processed datasets

### 📌 Maintenance Guidelines
- **Regular Updates:** Documentation reflects current implementation
- **Consistency Checks:** Terminology alignment across documents
- **Stakeholder Reviews:** Periodic validation of business alignment
- **Technical Accuracy:** Code and documentation synchronization

**Next Steps:** Complete evaluation notebook and generate final reports following A/B testing validation results.
