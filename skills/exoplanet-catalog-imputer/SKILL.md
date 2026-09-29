---
name: "exoplanet-catalog-imputer"
description: "Impute missing astronomical measurements using spectral-type clustering and empirical mass-radius relationships."
---

# Exoplanet Catalog Imputer Skill

## Overview
Imputes missing observational features in exoplanet datasets using physics-informed astronomical methodologies, preserving physical correlations while preventing synthetic bias.

## Operations
1. Identifies missing value distributions across planetary and stellar parameters.
2. Imputes stellar parameters grouped by host star spectral class ($S_{\text{TYPE}}$).
3. Applies empirical mass-radius power-law relations for terrestrial and gaseous regimes.
4. Emits transformation provenance logs recording all imputation methods and random seeds.
