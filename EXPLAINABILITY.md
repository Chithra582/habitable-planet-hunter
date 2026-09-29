# EXPLAINABILITY — Habitable Planet Hunter Agent

> **Admissibility & Transparency Report for OpenGAP / Agent Passport**  
> *Agent Name:* Habitable Planet Hunter Agent (`habitable-planet-hunter-agent`)  
> *Specification:* OpenGAP v0.1.0  
> *Domain:* Data & Analytics / Astrophysical Machine Learning & Exoplanet Habitability  

---

## 1. Overview & Scientific Purpose

Habitable Planet Hunter Agent is an autonomous scientific machine learning intelligence, astrophysical feature validator, and catalog imputation pipeline built for exoplanetary exploration. The underlying system operates across astronomical observation records from the **Planetary Habitability Laboratory (PHL) Exoplanet Catalog**, analyzing confirmed exoplanets discovered by Kepler, K2, TESS, and ground-based radial velocity observatories.

The agent's primary scientific purpose is to accurately classify the potential habitability of exoplanets (`P_HABITABLE`: non-habitable, conservatively habitable, or optimistically habitable) strictly from fundamental planetary and host star physical properties. By systematically preventing data leakage from synthetic habitability proxies (such as equilibrium temperature `P_TEMP_EQUIL`, Earth Similarity Index `P_ESI`, or pre-computed habitable zone flags `P_HABZONE`), the agent ensures machine learning models learn authentic astrophysical relationships between stellar luminosity, orbital dynamics, and planetary bulk density.

---

## 2. How the Agent Decides (Decision-Making Logic)

Habitable Planet Hunter Agent operates across a deterministic, multi-stage scientific decision pipeline that grounds every prediction in verified physical observables:

```
[PHL Astronomical Catalog Data] ──> [Anti-Leakage & Feature Boundary Gate] ──> [Physics-Aware Imputation]
                                                                                       │
                                                                                       ▼
[Calibrated Prediction & Uncertainty Dossier] <── [Scientific Provenance Audit] <── [Ensemble ML Classifier]
```

### 2.1 Feature Sanitization & Leakage Prevention
- **Decision:** Determines whether incoming datasets comply with the approved 30-feature pool and purges all synthetic habitability proxies.
- **Rules:**
  - Automatically identifies and strips forbidden proxy predictors: `P_TEMP_EQUIL` (Equilibrium Temperature), `P_ESI` (Earth Similarity Index), `P_HABZONE_OPT` / `P_HABZONE_CON` (Habitable Zone flags), and `P_FLUX` (Insolation Flux).
  - Restricts feature input space strictly to the approved 30 physical observables: 6 planetary physical properties, 9 orbital parameters, 11 stellar host properties, and 4 system metadata dimensions.
  - Rejects incoming training batches if any engineered feature exhibits an artificial cross-correlation ($r \ge 0.85$) with forbidden proxy metrics.

### 2.2 Astrophysical Stellar Flux & Habitable Zone Dynamic Inference
- **Decision:** Evaluates relative stellar insolation and planetary thermal boundaries directly from primary physical parameters.
- **Rules:**
  - Calculates relative stellar flux dynamically using the inverse-square law: $S_{\text{eff}} \propto \frac{S_{\text{LUMINOSITY}}}{P_{\text{SEMI\_MAJOR\_AXIS}}^2}$.
  - Computes periastron and apastron insolation limits for eccentric orbits ($e > 0.2$): $r_{\text{peri}} = a(1 - e)$, $r_{\text{ap}} = a(1 + e)$.
  - Flags planets orbiting inside the host star's tidal locking radius ($P_{\text{SEMI\_MAJOR\_AXIS}} \le S_{\text{TIDAL\_LOCK}}$) for specialized atmospheric circulation checks.

### 2.3 Multi-Class & Binary Habitability Classification Boundaries
- **Decision:** Maps exoplanetary candidates into distinct habitability regimes based on physical planetary characteristics and stellar environments.
- **Rules:**
  - **Class 1 (Conservatively Habitable):** Terrestrial radius ($0.5 R_\oplus \le P_{\text{RADIUS}} \le 1.6 R_\oplus$), terrestrial mass ($0.1 M_\oplus \le P_{\text{MASS}} \le 5.0 M_\oplus$), and dynamic stellar flux within empirical runaway greenhouse / maximum greenhouse limits.
  - **Class 2 (Optimistically Habitable):** Super-Earth radius regime ($1.6 R_\oplus < P_{\text{RADIUS}} \le 2.5 R_\oplus$) or candidates situated in early Mars / recent Venus boundary zones.
  - **Class 0 (Non-Habitable):** Gas giants, sub-Neptunes ($P_{\text{RADIUS}} > 2.5 R_\oplus$), extreme surface gravity ($P_{\text{GRAVITY}} > 3.0 g_\oplus$), or tidally disrupted systems.

### 2.4 Domain-Aware Astrophysical Imputation
- **Decision:** Imputes missing astronomical catalog measurements using astrophysics-grounded heuristics rather than global statistical means.
- **Rules:**
  - Imputes missing stellar parameters ($S_{\text{LUMINOSITY}}, S_{\text{TEMPERATURE}}$) grouped by host star spectral classification ($S_{\text{TYPE}}$: M, K, G, F, A).
  - Estimates missing planetary masses or radii using empirical power-law relations: $M \propto R^{3.45}$ for rocky terrestrial planets, $M \propto R^{2.06}$ for volatile-rich envelopes.
  - Records transformation provenance and imputation uncertainty flags into the catalog audit metadata.

---

## 3. Data Sources & Inputs Used

| Data Input | Source | Purpose | Data Handling & Privacy |
|---|---|---|---|
| **PHL Exoplanet Catalog** | Planetary Habitability Laboratory / Kaggle | Providing physical and orbital parameters for confirmed exoplanets | Public scientific dataset; processed in-memory; immutable catalog versioning |
| **Approved 30 Physical Features** | NASA Exoplanet Archive / Gaia DR3 | Training and evaluating machine learning habitability classifiers | Restricted feature pool; zero synthetic proxy contamination; normalized arrays |
| **Ground-Truth Habitability Labels** | PHL Scientific Benchmarks (`P_HABITABLE`) | Providing target classes (0: Non-Hab, 1: Conservative, 2: Optimistic) | Ground-truth labels preserved without synthetic modification or label leakage |
| **Model Weights & Ensembles** | Scikit-Learn / XGBoost / LightGBM | Executing calibrated habitability classification and inference | Serialized model artifacts versioned with training hash and validation scores |

Habitable Planet Hunter Agent complies with scientific integrity and open data standards:
- **Zero Sensitive Personal Data:** Operates entirely on celestial astronomical observations; no human subject or personally identifiable information (PII) is collected or processed.
- **FAIR Data Governance:** Preprocessing scripts, feature scalers, and data splits adhere to Findable, Accessible, Interoperable, and Reusable (FAIR) principles.
- **Strict Anti-Leakage Protocol:** Direct proxies of habitability are permanently expunged before any feature transformation or model training occurs.
- **Reproducible Lineage:** All dataset splits, imputation routines, and model training iterations are anchored to deterministic random seeds compatible with Python 3.10 runtime standards.

---

## 4. Known Limitations & Failure Modes

Reviewers, auditors, and users should note the following operational constraints:

1. **Transit Detection Bias & Imbalanced Classes:**
   - *Limitation:* Transit detection favors short-period, large-radius planets orbiting small stars, creating an extreme majority-class imbalance against rare habitable terrestrial candidates.
   - *Mitigation:* The agent applies stratified k-fold cross-validation with synthetic minority oversampling (SMOTE) and cost-sensitive ensemble boosting to prevent majority-class collapse.

2. **Active Stellar Environments & Flare Activity:**
   - *Limitation:* Insolation flux calculations alone do not account for coronal mass ejections and high-energy flare radiation in active M-dwarf systems that can strip planetary atmospheres.
   - *Mitigation:* The agent flags planets around M-dwarf host stars with an automated `StellarActivityUncertainty` warning, recommending follow-up transmission spectroscopy verification.

3. **Dual Mass-Radius Observational Gaps:**
   - *Limitation:* Incomplete catalog records where both planetary mass and radius are missing simultaneously prevent accurate bulk density and surface gravity computation.
   - *Mitigation:* When both mass and radius are absent, the agent refuses to synthesize both parameters simultaneously and flags the record as `InsufficientData` rather than predicting blindly.

4. **Latent Proxy Correlation & Feature Leakage:**
   - *Limitation:* Unsupervised feature transformations or polynomial combinations could inadvertently reconstruct forbidden proxy indices (`P_ESI`, `P_TEMP_EQUIL`).
   - *Mitigation:* An automated feature leakage detector computes Pearson and Spearman cross-correlations against forbidden proxies, halting the pipeline if correlation exceeds $0.85$.

---

## 5. Verification, Safety & Human Oversight

- **Astronomical Peer Review Gate:** Planetary candidates classified as potentially habitable (Class 1 or Class 2) generate an automated astrophysical summary sheet for verification by planetary scientists.
- **Human-in-the-Loop Governance:** The agent functions as an analytical decision-support copilot; astronomical discoveries and telescope observation time allocations remain subject to human verification.
- **Deterministic Quality Gates:** Model feature importances (e.g., SHAP values) and mathematical flux limits are evaluated through deterministic physics routines rather than uncalibrated LLM generation.
- **Kill Switch & Immutable Audit Logging:** The pipeline can be interrupted instantly via configuration flags; all data transformations, imputation seeds, and classification thresholds are recorded in structured audit logs.
