---
name: "habitability-classifier"
description: "Classify exoplanet habitability regimes using machine learning ensembles trained on approved physical features."
---

# Habitability Classifier Skill

## Overview
Executes machine learning classification workflows (Gradient Boosted Trees, Random Forests, Neural Classifiers) to predict planetary habitability regimes (`P_HABITABLE`).

## Operations
1. Prepares balanced training and evaluation splits with stratified sampling.
2. Computes multi-class probabilities (Non-Habitable, Conservative, Optimistic).
3. Evaluates ROC-AUC, precision-recall curves, and F1 scores per habitability class.
4. Generates SHAP and feature importance attribution rankings for model explainability.
