# Systematic Trading: Feature Engineering & Meta-Labelling Pipeline

> Group coursework project. Builds a complete systematic trading pipeline from raw OHLCV data to strategy backtesting.

## Notebook Walkthrough

### Section 0–1: Setup & Data Loading
- Imports standard libraries (numpy, pandas, sklearn, pytorch, matplotlib).
- Loads two CSV files: OHLCV price data (multi-asset) and primary trading signals (long/short entries).

### Section 2: Exploratory Data Analysis
- Counts signal frequency per instrument to assess class balance.
- Visualises price dynamics alongside signal firing points.

### Section 3: Feature Engineering
Constructs ~55 features across 7 families, all computed with **rolling windows on past data only** (no look-ahead):
- **Price/Return**: SMA, EMA, log-returns at 5/10/21-day horizons.
- **Volatility**: Realised vol, ATR, Garman-Klass, Parkinson estimators.
- **Momentum**: RSI, MACD, Bollinger %B, rate-of-change.
- **Volume**: OBV, VWAP deviation, volume z-score.
- **Microstructure**: Kyle's lambda proxy, Amihud illiquidity.
- **Latent Regime**: GMM soft-clustering on (return, vol), HMM state probabilities.
- **Signal Interaction**: Cross-terms between raw signal and vol/momentum features (meta-labelling insight).

### Section 4: Build All Instruments
- Applies the feature pipeline to each instrument independently.
- Adds cross-asset features (log-price spreads, return correlations within asset classes).

### Section 5: Feature Visualisations
- **HMM Regime overlay** on gold price: identifies bull/bear/sideways regimes.
- **Gold/Silver ratio** as a macro risk-appetite proxy.
- **Clustered correlation heatmap**: Spearman correlations across all features for metals.
- **Train vs OOS distribution shift**: kernel density comparison for key features.

### Section 6: Feature Dictionary
- Tabulates all features with family labels and descriptions.

### Section 7: Feature Clustering (Spearman Hierarchy)
- Hierarchical clustering on the correlation matrix to identify redundant feature groups.
- Dendrogram visualisation and cluster-level importance aggregation.

### Section 8: Triple-Barrier Labelling
- Implements López de Prado's triple-barrier method for label generation.
- **Grid search** over barrier height (h) and horizon (T) to optimise label quality.
- Diagnostic metrics: class balance, average barrier hit time, realised return by label.
- Selected parameters: h=1.5, T=15 days.

### Section 9: Save Outputs
- Exports labelled feature matrix to CSV for downstream model training.

### Section 10: Summary Table
- Recap of feature counts, labelling parameters, and pipeline design choices.

### Sections 3–5 (Model): Model Development & Comparison
- **Logistic Regression** (L1/L2, cross-validated).
- **Random Forest** with hyperparameter tuning.
- **MLP Neural Network** (PyTorch) with batch normalisation and dropout.
- Time-series train/test split with purging to prevent leakage.
- Cluster-level feature importance analysis (aggregating SHAP or impurity across correlated clusters).

### Section 5 (Eval): Model Evaluation
- OOS confusion matrix, classification report, ROC-AUC.
- Probability calibration curves.

### Optional: Strategy Construction
- Converts meta-model probabilities into position sizing.
- Backtests with transaction costs. Reports Sharpe, max drawdown, cumulative PnL.
