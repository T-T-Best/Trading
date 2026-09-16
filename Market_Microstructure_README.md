# Market Microstructure & Trading Strategies

> Group coursework project. Includes market microstructure empirical analysis and two sets of algorithmic trading strategies for a simulated exchange.

## Notebook Walkthrough

### Part A: Market Microstructure Analysis

Empirical analysis of limit order book data. All computations use timestamped trade-level records.

**Cell [0–1]: Call Auction**
- Constructs aggregate demand/supply schedules from individual orders.
- Determines the equilibrium clearing price graphically.

**Cell [2]: Liquidity Definition**
- Conceptual discussion: tightness (spread), depth (volume at BBO), resilience (recovery speed).

**Cell [3–4]: Quoted Spread for Different Trade Sizes**
- Loads LOB data (bid/ask/mid/price/size/direction).
- Computes volume-weighted average prices for hypothetical orders of varying size.
- Shows effective spread widens non-linearly with order size.

**Cell [5–6]: Empirical Quoted Spreads**
- **Absolute spread** S = ask − bid, **relative spread** s = S / mid.
- Half-hourly average spread profile over the trading day.
- Observed U-shape pattern: wide at open/close, narrow mid-day.

**Cell [7–9]: Effective Spread**
- eff_S = 2 × direction × (trade_price − mid).
- Captures the true execution cost including any price improvement.

**Cell [10–12]: Realized Spread & Market Maker Profit**
- real_S = 2 × direction × (trade_price − mid_{t+1}).
- Decomposition: effective spread = realized spread + price impact.
- Realized spread is typically smaller, reflecting adverse selection costs.

**Cell [13–14]: VWAP Computation**
- Volume-weighted average price for all trades, buy-side, sell-side.
- Comparison with simple average and mid-price benchmarks.

**Cell [15–17]: Roll's Spread Estimator**
- Autocovariance-based implicit spread measure.
- Computed in both transaction time and clock time.
- Transaction-time version is more reliable (avoids stale-quote contamination).

**Cell [18–20]: Price Impact Regression**
- 15-minute intervals. Dependent variable: mid-price change.
- Regressor: signed order flow (net buy volume).
- Kyle's lambda coefficient: permanent price impact per unit of order flow.

### Part B: Dual-Listing Arbitrage Strategies

Three strategies for exploiting price dislocations between a primary listing (ORIG) and its dual-listed counterpart (DUAL). Tested in a simulated exchange with 100-lot position limits and self-trade prevention.

**B.1 Active Arbitrage**
- Monitors cross-spread: (DUAL bid − ORIG ask) and (ORIG bid − DUAL ask).
- When spread exceeds MIN_ARB_SPREAD, sends paired IOC orders to capture the dislocation.
- Position-aware sizing: reduces volume near position limits.

**B.2 Passive Quoting**
- Posts two-sided limit orders in the DUAL book, priced around ORIG mid.
- Inventory skew: shifts quotes to discourage further accumulation in the overweight direction.
- Periodic cancel-and-refresh to track ORIG mid updates.

**B.3 Hybrid Combined**
- Unifies B.1 (active arb) + B.2 (passive quoting) + TWAP inventory liquidation.
- Layer C: when net inventory exceeds a soft threshold, a slow TWAP liquidation trims positions every few seconds without impact.

### Part C: Options Market Making Strategies

Market making on European equity options with delta hedging. Platform enforces position limits and delta risk constraints (|delta| ≥ 100 for >10s triggers forced liquidation).

**C.1 Options MM (Advanced)**
- Black-Scholes pricing with implied volatility calibration.
- Greeks-aware quoting: adjusts bid/ask width by vega exposure.
- Continuous delta hedging via the underlying stock.

**C.2 Beast Mode (Championship Strategy)**
- Aggressive quoting with critical safety fixes from prior versions.
- Pre-trade delta impact check blocks quotes exceeding soft limits.
- Per-option position caps (30 lots) prevent concentrated risk.
- Emergency handler actively IOC-closes delta-contributing options.
- Dual hedging across both ORIG and DUAL stock for 2× headroom.

**C.3 All-Assets Strategy**
- Extends market making across multiple underlying assets simultaneously.
- Cross-asset risk monitoring and capital allocation.
