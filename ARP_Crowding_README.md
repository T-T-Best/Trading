# Crowding in Alternative Risk Premia Strategies: Empirical Analysis

> MSc dissertation code. Investigates crowding effects on time-series momentum (TSMOM) strategies using multiple crowding proxies.

## Notebook Walkthrough

### Section 0: Setup
- Imports: pandas, numpy, statsmodels, matplotlib, seaborn, scipy.
- Configuration: file paths, plot style, HAC bandwidth defaults.
- Data source: Bloomberg price data for two volatility-controlled TSMOM fund indices.

### Section 1: TSMOM Strategy Construction (Thesis §5.2)
- Constructs a time-series momentum signal: sign of trailing 12-month return (skipping the most recent month to avoid reversal).
- Scales positions to target 10% annualised volatility using a 63-day rolling standard deviation.
- Outputs: daily TSMOM return series (equal-weighted across assets).
- **Figure 1**: Cumulative NAV of the TSMOM strategy.

### Section 2: Panel Construction & Crowding Proxies (Thesis §4, §5.1)
- **NAV Crowding (roll_corr)**: 67-day rolling correlation between two independent TSMOM fund NAVs. High correlation = crowded positioning.
- **COT Crowding (cot_index)**: CFTC Commitments of Traders net speculative positions, normalised to a 0–100 index.
- **Controls**: VIX (volatility regime), TNX (10-year Treasury yield, funding conditions).
- Panel spans 2002–2026, monthly frequency, all variables z-scored for comparability.

### Section 3: Baseline Predictive Regressions (Thesis §5.3, Table 4)
- OLS with Newey-West HAC standard errors (21 lags).
- Dependent variable: |R_{t+1}| (next-period absolute return, proxy for crowding-induced fragility).
- Univariate and multivariate specifications progressively adding controls.
- Reports coefficients, t-stats, adjusted R², and BIC.

### Section 4: Non-Linear Effects (Thesis §5.4)

**4.1 Decile Sort (Figure 1)**
- Sorts crowding proxy into expanding-window deciles (3-year minimum).
- Plots average |R_{t+1}| per decile — tests whether the crowding-return relationship is monotonic or convex.

**4.2 Extreme Crowding Dummies (Table 5, Eq.6)**
- Creates binary indicators for top/bottom quintile of crowding.
- Tests whether extreme crowding regimes have asymmetric effects.

**4.3 Quantile Regression (Table 6, Eq.7, Figure 2)**
- Estimates crowding coefficients at τ = {0.1, 0.25, 0.5, 0.75, 0.9} quantiles.
- Tests whether crowding disproportionately affects the tails of the return distribution.

### Section 5: Polynomial Interaction & Causal Inference (Thesis §5.4)

**5.1 Polynomial / Interaction Terms**
- Adds quadratic terms (rc², cot²) and cross-product (rc × vix) to the baseline regression.
- Tests for nonlinear and conditional crowding effects.

**5.2 Double Machine Learning (DML)**
- Uses EconML's LinearDML with GradientBoosting nuisance models.
- TimeSeriesSplit cross-validation to estimate a causal average treatment effect of crowding on returns.

### Section 6: Cross-Sectional Double Sort (Thesis §5.5, Table 7)
- Independently sorts assets into momentum terciles and crowding terciles.
- Computes average forward returns for each 3×3 cell.
- Tests the momentum-crowding interaction: does crowding erode momentum profits?

### Section 7: Crowding Timing Overlay (Thesis §5.6)
- Scales TSMOM exposure by inverse crowding percentile.
- Two variants: linear scaling and binary (cut exposure when top-quartile crowded).
- Backtest: compares Sharpe ratio, max drawdown, and cumulative PnL vs. unscaled TSMOM.

### Section 8: Trend Age × Crowding Interaction (Thesis §5.7)
- Defines "trend age" as the number of consecutive days the momentum signal persists in the same direction.
- Tests whether older (more crowded) trends are more susceptible to crowding-induced reversals.

### Section 9: NAV–COT Divergence Signal (Thesis §5.8)
- Divergence = z(NAV crowding) − z(COT crowding).
- Hypothesis: when institutional positioning (NAV) exceeds speculative positioning (COT), crowding risk is elevated.
- Regression tests significance of the divergence term.

### Section 10: Long/Short Crowding Asymmetry (Thesis §5.9, Table 8)
- Decomposes holdings into long-only and short-only crowding measures.
- Tests whether short crowding has a larger crash-risk effect than long crowding.

### Section 11: Robustness Checks (Appendix)

**11.1 Stationarity**: ADF and KPSS tests on all panel variables.
**11.2 Granger Causality**: bidirectional tests between crowding and returns.
**11.3 Coefficient Stability (Oster-style)**: ratio of controlled vs. uncontrolled coefficients.
**11.4 HAC Bandwidth**: re-runs Table 4 with maxlags=63 (quarterly).
**11.5 Reverse Causality**: controls for lagged absolute returns and volatility.
**11.6 First Differences**: re-estimates baseline with differenced variables (addresses non-stationarity).
**11.7 Subsample Stability**: 2003–2014 vs. 2015–2025 split.
**11.8 VIX Regime Split**: tests whether crowding effects are stronger in high-volatility regimes.

### Section 12: Data Representativeness
- Checks that the two fund indices target comparable volatility levels.
- Rolling vs. full-sample annualised volatility comparison.

### Section 13–14: Additional Robustness
- Fund's own NAV return as alternative dependent variable.
- COT-residual decomposition (orthogonalising COT from NAV crowding).
- Realised-vol and lagged |R| controls.
- Left-tail analysis: VaR/CVaR by crowding decile.
- Asset-class labels and liquidity split.
