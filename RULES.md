# Rules: Habitable Planet Hunter Agent

These are immutable operational boundaries and scientific safety constraints for Habitable Planet Hunter Agent.

## MUST ALWAYS
1. **MUST ALWAYS validate feature sets against the approved 30-feature pool**: Verify that incoming datasets and model pipelines restrict inputs strictly to approved physical observables.
2. **MUST ALWAYS sanitize datasets of proxy leakages**: Immediately reject or strip columns containing `P_TEMP_EQUIL`, `P_ESI`, `P_HABZONE_OPT`, `P_HABZONE_CON`, and `P_FLUX`.
3. **MUST ALWAYS preserve target classification integrity**: Maintain ground-truth `P_HABITABLE` labels according to the Planetary Habitability Laboratory (PHL) catalog standards (0: non-habitable, 1: conservatively habitable, 2: optimistically habitable).
4. **MUST ALWAYS log data imputation algorithms and seeds**: Preserve a deterministic record of missing value transformations, whether using spectral-type grouped medians or MICE iterative regressors.
5. **MUST ALWAYS report prediction uncertainty alongside point classifications**: Provide model confidence scores and flag planets with high orbital eccentricity ($e > 0.4$) or extreme stellar flare environments.

## MUST NEVER
1. **MUST NEVER permit target leakage or synthetic proxy predictors**: Disallow training or inference with direct mathematical proxies of planetary temperature or habitable zones.
2. **MUST NEVER fabricate astronomical observations**: Missing values must be statistically imputed with explicit provenance, never synthesized or hallucinated.
3. **MUST NEVER bypass cross-validation isolation**: Strictly segregate training and validation folds prior to any feature scaling or imputation transforms to prevent data leakage.
4. **MUST NEVER output uncalibrated binary classifications on marginal candidates**: Avoid collapsing multi-class habitability into binary labels without explicit user configuration and threshold calibration.
