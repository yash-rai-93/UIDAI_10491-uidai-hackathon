# Strategic Aadhaar Planning: Trends, Risks & Operations

**UIDAI Data Hackathon | Team ID: UIDAI_10491**
**Team Members:** Yash Rai, Tanishk Thakur, Aryan Nahata
    
---

## 📌 Project Overview

The UIDAI administrative ecosystem operates Aadhaar enrolment and update services at a massive national scale. However, operational planning is still largely driven by retrospective analysis of historical data. This approach struggles to capture non‑linear demographic shifts, seasonal surges, compliance-driven spikes, and localized demand anomalies—leading to idle infrastructure in low-demand regions and severe bottlenecks in high-velocity districts.

This project proposes a **Predictive Analytics & Decision-Support Framework** that enables UIDAI to shift from *reactive* capacity management to *proactive, data-driven operational planning*. Using UIDAI-provided enrolment, demographic update, and biometric update datasets, the system forecasts near-term district-level demand, detects anomalies, segments demand by citizen lifecycle, and translates insights into concrete operational actions.

---

## 🎯 Objectives

The framework is designed around four core objectives:

1. **Future Demand Projection**
   Forecast Aadhaar enrolment and update volumes at the **district level** for upcoming periods (quarter-level planning), enabling advance deployment of biometric kits, operators, and mobile enrolment units.

2. **Anomaly Detection**
   Identify abnormal spikes or drop-offs in activity to flag:

   * Camp-mode activations
   * Reporting delays
   * Fraud or misuse risks
   * Migration- or event-driven surges

3. **Demographic Lifecycle Segmentation**
   Decompose demand into three behavioral cohorts:

   * **Cradle (0–5 years):** Infant enrolments (demographic-only)
   * **Classroom (5–17 years):** Mandatory biometric updates (ages 5 & 15)
   * **Workforce (18+ years):** Migration-driven demographic updates

4. **Operational Optimization**
   Convert forecasts into actionable recommendations for:

   * Staffing augmentation
   * Mobile van placement
   * Hardware vs digital infrastructure prioritization

---

## 🗂️ Datasets Used

All datasets are **official UIDAI-provided hackathon datasets**:

* **Aadhaar Enrolment Dataset** (new registrations)
* **Demographic Update Dataset** (name, address, mobile changes)
* **Biometric Update Dataset** (iris & fingerprint updates)

### Unified Training Dataset

After cleaning and harmonization, all sources were merged into a single analytical dataset:

* **Granularity:** Weekly, State–District level
* **Size:** ~26,600 region-week records
* **Key Variables:**

  * `enrol_count`
  * `demo_count`
  * `bio_count`
  * Temporal identifiers (week, year, week_index)

---

## ⚙️ Methodology

### Phase 1: Data Ingestion & Preprocessing

* Schema standardization across raw CSV files
* Normalization of state/district naming inconsistencies
* Weekly temporal aggregation to reduce reporting noise
* Storage in Parquet format for efficiency and consistency

### Phase 2: Feature Engineering

To capture temporal dynamics and momentum, the following features were engineered:

* **Lag Features:** `lag_1`, `lag_2`, `lag_4`
* **Rolling Averages:** 4-week rolling mean
* **Velocity Features:** Rate and direction of change

These features allow the model to learn persistence, seasonality, and trend acceleration in Aadhaar activity.

### Phase 3: Dual-Model Architecture

Two independent LightGBM pipelines were trained:

* **Model A:** Biometric Updates (`Ybio`)

  * Driven by compliance cycles (ages 5 & 15)
  * Hardware- and operator-intensive

* **Model B:** Demographic Updates (`Ydemo`)

  * Driven by migration and life events
  * Digitally intensive (SSUP, servers)

This separation prevents feature dilution and enables resource-specific planning.

### Phase 4: Model Selection

**LightGBM (Gradient Boosting)** was chosen over traditional models (ARIMA/SARIMA) because:

* Captures non-linear regional and seasonal interactions
* Handles high-cardinality categorical features (districts) natively
* Scales efficiently across nationwide data with a single global model

---

## 📈 Model Performance

* **Normalized RMSE:** ~4.2% (≈95.8% accuracy on high-load districts)
* **MAE:** ~849 updates per district (operationally negligible in high-volume regions)
* **Validation Strategy:**

  * Chronological time-series split
  * Early stopping for generalization
  * Focused evaluation on top 20% high-demand districts

Performance is sufficient for **planning and resource allocation**, not exact auditing.

---

## 🔍 Key Insights

### Strategic Resource Concentration

* Top **5 states account for ~60%** of projected national biometric workload
* Maharashtra and Uttar Pradesh alone justify ~60% of peak-period budget allocation

### Anomaly Detection

* Tribal belt spikes (Jharkhand) → Camp-mode activations
* Border & migrant anomalies (Punjab, Nagaland) → Migration & compliance checks
* Religious/transient hubs (Ayodhya) → Floating population effects

### Lifecycle-Based Optimization

* **Cradle (0–5):** Demographic-only → avoid expensive biometric kits
* **Classroom (5–17):** Biometric-heavy → deploy school camps & mobile units
* **Workforce (18+):** Digital-heavy → scale SSUP & server infrastructure

---

## 🧭 Operational Impact

The system enables UIDAI to:

* Reduce peak-period backlogs by **15–25%** through early detection
* Prevent idle infrastructure in low-demand regions
* Optimize staffing, logistics, and budget allocation
* Improve equitable access to Aadhaar services, especially for vulnerable and high-mobility populations

---

## 🚧 Limitations

* Dataset spans only ~10 months (no full annual seasonality)
* District-level aggregation (no tehsil/PIN-level routing)
* No external regressors (holidays, exams, events)
* Requires a 4-week warm-up period for new districts

---

## 🔮 Way Forward

* Extend forecasts to 6–12 months using multi-year UIDAI data
* Integrate center capacity and staffing data for automated alerts
* Deploy real-time dashboards for live monitoring and intervention

---

## 📁 Repository Contents

* Jupyter notebooks for preprocessing, feature engineering, modeling
* Cleaned datasets (`.csv`, `.parquet`)
* Model artifacts and evaluation metrics
* Forecast outputs and visualization assets

---

## 📜 Disclaimer

All forecasts and budget estimates are **planning-level illustrations**, not audited projections. The system is intended to support strategic decision-making, not replace statutory processes.

---

**UIDAI Data Hackathon – Strategic Aadhaar Planning**
