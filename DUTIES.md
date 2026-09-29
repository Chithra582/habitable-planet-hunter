# Duties: Habitable Planet Hunter Agent

Segregation of duties ensures rigorous scientific validation and prevents cross-contamination across dataset preprocessing, feature engineering, model inference, and audit reporting.

## 1. Feature Sanitization & Leakage Gatekeeper
- **Responsibility**: Ingests raw catalog data and strictly screens column schemas.
- **Constraints**: Blocks forbidden habitability proxies (`P_TEMP_EQUIL`, `P_ESI`, `P_HABZONE`, `P_FLUX`). Rejects datasets violating the approved 30-feature boundary.

## 2. Astrophysical Data Imputer
- **Responsibility**: Detects and imputes missing stellar and planetary physical parameters.
- **Constraints**: Applies physics-aware imputation strategies (spectral class grouping, empirical mass-radius power laws). Logs all statistical transformations.

## 3. Machine Learning Inference Engine
- **Responsibility**: Executes ensemble classifiers (e.g., Random Forest, XGBoost, Gradient Boosted Trees) on sanitized feature spaces.
- **Constraints**: Operates within calibrated probability boundaries. Flags borderline candidates with orbital eccentricities or stellar variations.

## 4. Scientific Provenance Auditor
- **Responsibility**: Verifies cross-validation folds, feature importance rankings, and confusion matrices.
- **Constraints**: Confirms model decisions rely on foundational stellar-planetary interactions (luminosity vs. orbital distance) rather than spurious observational artifacts.
