# Appendix A: Live Trading Logging Standards

In the practice of quantitative trading, raw trading logs are the only bridge connecting "simulation" and "live trading." This appendix details the minimum log checklist that a professional quant system must record, aimed at helping you collect high-quality data that can directly feed back into strategy training (especially reinforcement learning RL).

---

## A1.1 Core Data Structure

A complete trading record should cover at least three dimensions of information to ensure every trade is traceable.

### A1.1.1 Order Level Information

Record details of every instruction sent to the exchange:
- **Identifiers**: `order_id` (globally unique ID), `symbol` (trading code).
- **Order Parameters**: `side` (buy/sell), `order_type` (market/limit), `order_price` (order price), `order_qty` (order quantity).
- **Lifecycle**: `submit_ts` (order timestamp), `cancel_ts` (cancel timestamp, if applicable).

### A1.1.2 Fill Level Information

Since one order may correspond to multiple fills, every specific fill detail must be recorded:
- **Association Identifiers**: `fill_id`, `order_id`.
- **Fill Details**: `fill_price` (fill unit price), `fill_qty` (fill quantity), `fill_ts` (millisecond-precision fill timestamp).

### A1.1.3 Derived Key Metrics

This is the most critical part for feeding back to strategy, calculated from the above base data:
- **Expected Price** (`expected_price`): Theoretical price when signal triggered.
- **Slippage Cost** (`slippage`): Deviation between actual average fill price and theoretical price.
- **Execution Latency** (`latency_ms`): Time from order submission to first fill.
- **Fill Rate** (`fill_ratio`): Ratio of actual filled quantity to planned quantity.

---

## A1.2 Cost Accounting and Market Snapshot

### A1.2.1 Financial P&L and Friction Costs
- **Explicit Costs**: `commission` (trading fees), `tax` (stamp duty/regulatory fees).
- **Final Returns**: `realized_pnl` (realized profit and loss).

### A1.2.2 Market State at Decision Time
Record market background at moment of order placement, for analyzing environment's impact on execution:
- **Bar Data**: `bar_ts`, `bar_open/high/low/close`, `bar_vwap`.
- **Volatility Indicators**: `atr_5min`.
- **(Optional) Liquidity Depth**: Recommend recording at least top-of-book bid/ask (`bid1`/`ask1`).

---

## A1.3 Agent Decision Metadata (RL-Specific)

To enable RL models to learn true causal relationships, decision context must be recorded:
- **Version Identifier**: `agent_id` / `version`.
- **Action Details**: `action` (buy/sell/hold), `target_position` (target position).
- **Confidence Measure**: `confidence` or model output prediction score.

---

## A1.4 Summary: Why These Fields Are Essential

A standardized log checklist is not just for financial reconciliation; its core value lies in:
1. **Revealing slippage truth**: Precisely distinguish "signal error" from "execution deviation."
2. **Training execution logic**: Provide real Reward signals for execution-type RL (such as execution delay and fill rate).
3. **Breaking data dependency**: Without expensive Level-2 data, rely solely on live logs to build your own execution simulator.

---

> **Core Principle**: If you don't completely record the full process of "order -> fill -> delay -> failure," your live data has no value for strategy evolution.
