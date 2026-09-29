# Explainability & Transparency Report: Habitable Planet Hunter Agent

This report details the algorithmic decision boundaries, data handling protocols, failure modes, and governance controls implemented within Habitable Planet Hunter Agent, in compliance with OpenGAP 0.1.0 specifications and FAIR scientific data standards.

---

## 1. System Overview & Scientific Architecture

Habitable Planet Hunter Agent operates an autonomous scientific machine learning pipeline designed to evaluate the habitability potential of confirmed exoplanets from the Planetary Habitability Laboratory (PHL) catalog.

```
+-----------------------------------------------------------------------+
|                PHL Exoplanet Catalog (Full Dataset)                   |
+-----------------------------------------------------------------------+
                                   |
                                   v
+-----------------------------------------------------------------------+
|         1. Feature Sanitization & Leakage Prevention Gate             |
|   - Strips forbidden proxies: P_TEMP_EQUIL, P_ESI, P_HABZONE, P_FLUX  |
|   - Isolates approved pool of 30 physical/orbital/stellar parameters  |
+-----------------------------------------------------------------------+
                                   |
                                   v
+-----------------------------------------------------------------------+
|         2. Physics-Aware Astrophysical Imputation Engine              |
|   - Host star spectral grouping (M, K, G, F, A)                       |
|   - Planetary mass-radius empirical relationship preservation         |
+-----------------------------------------------------------------------+
                                   |
                                   v
+-----------------------------------------------------------------------+
|         3. Calibrated Habitability Classification Engine              |
|   - Multi-class (0: Non-Hab, 1: Conservative, 2: Optimistic)          |
|   - Luminosity-to-Distance ratio dynamics ($L / d^2$)                 |
+-----------------------------------------------------------------------+
                                   |
                                   v
+-----------------------------------------------------------------------+
|         4. Scientific Provenance & Uncertainty Audit Dossier          |
|   - Confidence intervals, feature attribution, and validation metrics |
+-----------------------------------------------------------------------+
```

---

## 2. Algorithmic Decision Framework

### 2.1 Feature Sanitization and Proxy Leakage Prevention
- **Decision:** The agent screens all incoming training matrices and inference vectors, rejecting direct mathematical proxies of temperature or habitability to ensure genuine physical learning.
- **Rules:**
  - Forbidden columns (`P_TEMP_EQUIL`, `P_ESI`, `P_HABZONE_OPT`, `P_HABZONE_CON`, `P_FLUX`) trigger immediate pipeline halting or automatic column dropping.
  - Column inclusion is restricted to the 30 approved physical parameters: 6 planetary physical properties, 9 orbital parameters, 11 stellar host properties, and 4 system metadata dimensions.
  - Feature selection reports must demonstrate that model predictions do not correlate perfectly with any single column ($r < 0.85$).

### 2.2 Astrophysical Stellar Flux & Habitable Zone Dynamic Inference
- **Decision:** The agent computes relative stellar insolation dynamically from primary physical observables rather than reading pre-calculated flux fields.
- **Rules:**
  - Insolation scaling is derived via $S_{\text{eff}} \propto \frac{S_{\text{LUMINOSITY}}}{P_{\text{SEMI\_MAJOR\_AXIS}}^2}$.
  - Planets with orbital eccentricity $e > 0.2$ are subjected to periastron and apastron flux boundary checks: $r_{\text{peri}} = a(1 - e)$, $r_{\text{ap}} = a(1 + e)$.
  - Planets orbiting tidal lock boundary stars ($P_{\text{SEMI\_MAJOR\_AXIS}} \le S_{\text{TIDAL\_LOCK}}$) receive specialized atmospheric retention flags.

### 2.3 Multi-Class & Binary Habitability Classification Boundaries
- **Decision:** The agent maps exoplanetary candidates into habitability regimes: Class 0 (Non-Habitable), Class 1 (Conservatively Habitable), and Class 2 (Optimistically Habitable).
- **Rules:**
  - Class 1 (Conservative): Terrestrial radius ($0.5 R_\oplus \le P_{\text{RADIUS}} \le 1.6 R_\oplus$), terrestrial mass ($0.1 M_\oplus \le P_{\text{MASS}} \le 5.0 M_\oplus$), and host star stellar flux within the conservative runaway greenhouse / maximum greenhouse limits.
  - Class 2 (Optimistic): Super-Earth regime ($1.6 R_\oplus < P_{\text{RADIUS}} \le 2.5 R_\oplus$) or candidates situated in early Mars / recent Venus empirical boundary zones.
  - Class 0 (Non-Habitable): Gas giants, sub-Neptunes ($P_{\text{RADIUS}} > 2.5 R_\oplus$), extreme surface gravities ($P_{\text{GRAVITY}} > 3.0 g_\oplus$), or tidally disrupted systems.

### 2.4 Domain-Aware Astrophysical Imputation
- **Decision:** Missing astronomical measurements are imputed using domain-specific astronomical heuristics rather than naive global means.
- **Rules:**
  - Missing stellar parameters ($S_{\text{LUMINOSITY}}, S_{\text{TEMPERATURE}}$) are imputed grouped by stellar spectral classification ($S_{\text{TYPE}}$).
  - Missing planetary masses or radii are estimated using empirical mass-radius power-law relations: $M \propto R^{3.45}$ for rocky regimes, $M \propto R^{2.06}$ for volatile-rich regimes.
  - Imputation uncertainty flags are injected into the metadata dossier for downstream sensitivity analysis.

---

## 3. Data Handling, Provenance & Privacy

The agent processes astrophysical survey data and observational parameters under FAIR data principles.

| Data Type | Sensitivity / Classification | Storage & Handling | Retention & Lineage Policy |
| :--- | :--- | :--- | :--- |
| **Exoplanet Planetary Measurements** | Public Scientific Data | In-memory DataFrames, cached Parquet/CSV | Permanent catalog provenance; mapped to PHL catalog release versions |
| **Stellar Host Observations** | Public Astronomical Data | In-memory NumPy matrices, normalized arrays | Retained with SIMBAD / Gaia DR3 cross-reference identifiers |
| **Model Checkpoints & Weights** | Scientific Artifacts | Serialized Joblib/ONNX models | Versioned with training feature hash and cross-validation score tags |
| **Imputation Transformation State** | Experimental Provenance | Serialized pipeline metadata | Immutable audit logs stored alongside benchmark evaluation metrics |

### Open Science & Privacy Commitments
- **Zero Sensitive Personal Data**: The repository processes celestial observations exclusively; no human subject or PII data is ingested or stored.
- **Reproducible Data Lineage**: All dataset transforms adhere to strict random seed anchoring and version-controlled preprocessing pipelines.
- **Open Benchmark Distribution**: Model outputs, confusion matrices, and ROC curves are formatted for open distribution and peer reproducibility.

---

## 4. Operational Limitations & Failure Modes

1. *Limitation:* Transit detection bias favors short-period, large-radius planets orbiting small stars, creating significant class imbalance.
   *Mitigation:* The agent employs stratified k-fold cross-validation and balanced class weighting (e.g., SMOTE or cost-sensitive gradient boosting) to prevent majority-class collapse.

2. *Limitation:* Host star activity (stellar flares, coronal mass ejections) in M-dwarf systems can strip planetary atmospheres, invalidating simple insolation-based classifications.
   *Mitigation:* The agent flags planets around active M-dwarfs with a `StellarActivityUncertainty` warning, recommending follow-up spectroscopic characterization.

3. *Limitation:* Incomplete observational records where both planetary mass and radius are missing simultaneously prevent accurate density calculations.
   *Mitigation:* When both mass and radius are absent, the agent refuses to impute both simultaneously and designates the planet as `InsufficientData` rather than predicting blindly.

4. *Limitation:* Data leakage caused by hidden correlations in engineered features mimicking forbidden proxy columns (`P_ESI`, `P_TEMP_EQUIL`).
   *Mitigation:* An automated feature leakage detector computes Pearson and Spearman correlation matrices against forbidden proxies before training starts, aborting if correlation coefficients exceed $0.90$.

---

## 5. Human Oversight, Reproducibility & Governance

- **Astronomical Peer Review Gate**: Planetary candidates classified as potentially habitable (Class 1 or Class 2) generate an automated astrophysical summary sheet for verification by planetary scientists.
- **Feature Correlation Auditing**: Model feature importances (e.g., SHAP values) are logged and audited to confirm the model relies on foundational physics ($S_{\text{LUMINOSITY}}$, $P_{\text{SEMI\_MAJOR\_AXIS}}$) rather than catalog artifacts.
- **Reproducibility Guarantee**: The execution pipeline adheres to Python 3.10 runtime standards compatible with Kaggle and Google Colab environments, ensuring 100% deterministic reproducibility across platforms.
