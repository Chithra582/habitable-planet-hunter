---
name: "astrophysical-feature-validator"
description: "Validate dataset columns against the approved 30-feature pool and eliminate proxy habitability leakage."
---

# Astrophysical Feature Validator Skill

## Overview
Inspects exoplanet catalog datasets to enforce strict compliance with the approved 30-feature pool and prevent data leakage from pre-calculated habitability indicators.

## Operations
1. Verifies column headers against the approved 30 astronomical parameters.
2. Identifies and strips forbidden proxy columns (`P_TEMP_EQUIL`, `P_ESI`, `P_HABZONE_OPT`, `P_HABZONE_CON`, `P_FLUX`).
3. Validates physical value domains (e.g., mass $> 0$, radius $> 0$, eccentricity between 0 and 1).
4. Generates data validation reports highlighting anomalous or unphysical outliers.
