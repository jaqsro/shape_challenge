# FPSO Equipment Failure Analysis

Technical case study: predicting equipment failure on an FPSO (Floating Production,
Storage and Offloading) vessel from anonymized time-series sensor data.

## Problem

Given ~800 cycles of sensor readings (temperature, pressure, vibration on 3 axes,
frequency) plus two operating-point controls (Preset_1, Preset_2) and a binary
failure flag, the goal is to:

1. Count how many times the equipment failed
2. Characterize failures by operating configuration (presets)
3. Categorize failures by their sensor signature / root cause
4. Build and evaluate a model to predict failure *before* it occurs
5. Determine which variables most influence the prediction

## Approach & Key Findings

- **Failures are events, not rows.** 66 failure rows correspond to only **10 distinct
  failure events** (consecutive cycles in a failure state).
- **Presets do not drive failure.** They vary cycle-to-cycle (sawtooth) and neither
  individual presets nor their combinations show a reliable association with failure.
- **Failures are heterogeneous.** Pre-failure signatures differ across episodes
  (thermal vs. vibrational), suggesting distinct candidate failure types.
- **Prediction is framed as classification** ("will the machine fail within the next
  τ cycles?", τ=5), with strictly temporal validation (forward-chaining CV) to avoid
  leakage, and PR-AUC as the primary metric given ~7% class imbalance.
- **Three model paradigms converge on a weak ceiling** (PR-AUC ≈ 0.1–0.25): logistic
  regression, gradient boosting, and Isolation Forest. The dominant limitation is the scarcity of failure
  events (10). This is a proof of concept, not a deployable model.
- **Most consistent predictor:** short-term mean frequency ('Frequency_mean_5'),
  confirmed by both permutation importance and SHAP.

## Methodology Reference

The modeling and validation approach was informed by Al-Ali & Alharbi (2026),
*Temporally Rigorous and Traceable Predictive Maintenance via Joint Labeler-Model
Optimization* (Scientific Reports, in press). The paper frames PdM as lookahead-window
prediction with strict temporal validation. Note: it is an unedited "in press"
manuscript; it was used as methodological inspiration, not a settled reference.
