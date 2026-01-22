# System Architecture

## Overview

This project implements an end-to-end **Predictive Analytics Architecture** to support proactive operational planning for Aadhaar enrolment and update services. The architecture is designed to transform raw UIDAI datasets into actionable forecasts that directly inform staffing, infrastructure deployment, and budget prioritization.

The system follows a three-phase pipeline:
1. Data Ingestion & Standardization  
2. Temporal Feature Engineering  
3. Predictive Modeling & Validation  

Each phase is modular, auditable, and extensible for future integration into UIDAI’s operational workflows.
### End-to-End Predictive Analytics Architecture

![End-to-End Predictive Analytics Architecture](end_to_end_architecture_uidaijpeg)

*Figure 1: High-level architecture illustrating data ingestion, temporal feature engineering, and predictive modeling stages used to transform UIDAI datasets into actionable forecasts.*

---

## Phase 1: Data Ingestion & Schema Harmonization
![Data Ingestion and Schema Harmonization Pipeline](data_ingestion_schema_harmonization.png)

*Figure 2: Raw UIDAI enrolment, demographic, and biometric datasets are standardized through schema harmonization and stored in optimized Parquet format.*
### Inputs
- Aadhaar Enrolment Dataset (raw schema)
- Aadhaar Demographic Update Dataset (raw schema)
- Aadhaar Biometric Update Dataset (raw schema)

### Challenges Addressed
- Inconsistent column naming and schemas across datasets  
- Mixed date formats and numeric encodings  
- Storage inefficiencies for large-scale time-series data  

### Processing Steps
- Schema standardization through header mapping  
- Normalization to a unified structure:  
  `(state, district, date, count)`  
- Conversion to optimized Parquet format for efficient I/O  

### Output
- Unified Parquet files at daily granularity

This phase ensures that downstream analytics operate on consistent, high-quality data while preserving the fidelity of the original UIDAI datasets.

---

## Phase 2: Temporal Aggregation & Feature Engineering
### Temporal Feature Engineering Architecture

![Temporal Feature Engineering Architecture](temporal_feature_engineering_pipeline.png)

*Figure 3: Weekly aggregation followed by lag features, rolling statistics, and velocity vectors to construct the final feature vector \(X_t\).*

### Temporal Aggregation
- Daily records are aggregated to **weekly granularity** (`W_t`)
- Weekly resolution balances signal stability with responsiveness to demand changes

### Feature Engineering
To capture demand dynamics, three complementary feature families are constructed:

#### Lag Features
- `L_t-1`, `L_t-2`, `L_t-4`
- Capture short-term persistence and historical dependency

#### Rolling Statistics
- 4-week rolling averages
- Smooth noise and model local trends

#### Velocity Vectors
- Week-over-week rate of change (`dY/dt`)
- Capture momentum and early surge signals

### Output
- Final feature vector `X_t`, representing the operational state of each region at time `t`

This phase enables the model to learn temporal structure, seasonality, and non-linear demand behavior.

---

## Phase 3: Predictive Modeling & Validation
### Predictive Modeling & Validation Pipeline

![Predictive Modeling Pipeline](predictive_modeling_validation_pipeline.png)

*Figure 4: Time-aware training protocol with log transformation, rolling validation, and LightGBM-based forecasting.*

### Training Protocol
- Log transformation (`log(1 + Y)`) applied to stabilize variance  
- Time-aware train–validation split to prevent data leakage  
- Training on historical windows, validation on future periods  

### Model Choice
- **LightGBM Regressor (Gradient Boosting Trees)**  
- Selected for:
  - Strong performance on structured tabular data  
  - Ability to model non-linear relationships  
  - Interpretability and fast training at scale  

### Evaluation
- Performance measured using **Normalized RMSE**
- Achieves ~4–5% error on high-volume regions
- Accuracy deemed sufficient for **resource planning**, not exact point prediction

---

## End-to-End Flow Summary

1. Raw UIDAI datasets are standardized and unified  
2. Data is aggregated temporally and enriched with dynamic features  
3. A validated forecasting model predicts near-term demand  
4. Outputs are used for operational decision-making, not just reporting  

This architecture enables a shift from retrospective analysis to forward-looking intelligence.

---

## Deployment & Extensibility

The architecture is designed to be deployment-ready with minimal modification:
- Supports extension to 6–12 month forecast horizons  
- Can integrate centre-level capacity and staffing data  
- Compatible with dashboard-based monitoring and alerting systems  

By design, the system is modular and scalable, making it suitable for phased adoption within UIDAI’s existing operational ecosystem.

---

## Design Principles

- **Auditability:** Every transformation step is traceable  
- **Temporal Integrity:** Strict separation of past and future data  
- **Operational Relevance:** Outputs map directly to staffing and infrastructure decisions  
- **Scalability:** Efficient storage and modeling choices for national-scale data  

---

This architecture demonstrates how UIDAI datasets can be transformed into a predictive decision-support system that improves efficiency, reduces backlogs, and enhances service accessibility.
