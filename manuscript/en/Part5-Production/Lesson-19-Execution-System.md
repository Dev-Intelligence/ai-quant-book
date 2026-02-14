# Lesson 19: Execution System - From Signal to Real Fill

> **If you treat "market price" as "execution price," what you've trained is not a trading system, but an illusion.**

---

## A Common Fatal Misconception

Many strategy backtests look beautiful because they assume an impossible premise:

> Whenever a signal appears, I can always execute at the ideal price (like the candle Close).

Once you go live, you immediately encounter three realities: **slippage, latency, and partial/no fills**. These are not "optimization items" - they are the life-or-death line that determines whether a strategy can be deployed.

This chapter will help you deeply understand what **Real Execution Data** is, and why it is the foundation of the Execution Agent.

---

## 19.1 Core Definitions

So-called "real execution data" is not the displayed price you see on candlestick charts, but rather:

> **Core Logic**: If you actually placed an order at that moment, at what price did you ultimately execute, how much quantity was filled, and how much delay did you experience.

This is the most important dividing line between theoretical backtesting and live trading.

---

## 19.2 Four Key Components of Real Execution

To accurately simulate real trading, you must consider these four key dimensions:

### 19.2.1 Execution Price

The actual execution price usually differs significantly from candlestick prices (OHLC).
- **Differentiation**: The close price of a 5-minute candle is just the last trade record - it doesn't mean your large order can fill entirely at this price.
- **Splitting**: In large trades, your order may be split into multiple fills at slightly different prices, ultimately presenting a **volume-weighted average price**.

### 19.2.2 Slippage

Slippage is the deviation between theoretical execution price and actual execution price. It's the main hidden cost in quantitative trading, primarily from:
- **Liquidity Depth**: Bid/ask depth insufficient to support order volume.
- **Market Impact**: Your own order changes short-term supply/demand, moving prices unfavorably.
- **Price Volatility**: In fast-moving markets, prices change instantly.

### 19.2.3 Fill Probability and Latency

In real markets, placing an order doesn't guarantee a fill:
- **Market Orders**: Pursue "immediate execution" at the cost of often bearing larger slippage.
- **Limit Orders**: Pursue "price certainty" but face these risks:
  - **No fill**: Price never reaches your level.
  - **Partial fill**: Too many orders ahead of you, liquidity exhausted by the time you're reached.

### 19.2.4 Friction Costs and Trading Rules

Beyond price itself, you must pay mandatory "entry fees":
- **Explicit Costs**: Commissions, stamp duty, transfer fees.
- **Rule Constraints**: Minimum tick size, price limits, specific matching rules (price priority, time priority).

---

## 19.3 Strategy Perspective: Why High-Frequency Trend Strategies Are Extremely Sensitive

For strategies using **5-minute trends + high-frequency trading** logic, the importance of real execution data is magnified several times.

### 19.3.1 Long-Term Accumulation of Errors

If your daily trading frequency is tens to hundreds of trades, even a **0.01%** execution price deviation per trade will fundamentally distort the long-term backtest curve due to compounding and high frequency.

### 19.3.2 The Illusion of "Candle Close Execution"

If you simply use "candle Close price" as execution price when training AI Agents or backtesting, the model learns extremely dangerous logic:
- **Misjudgment**: "When a candle ends, I can always execute instantly at the close price."
- **Illusion**: "After a breakout, I can always buy at the optimal breakout price."

> **Reality**: In reality, after observing a signal at the current candle's end, you can only place an order at the **next candle's** start at the earliest. In volatile markets, you can almost never execute at the observed Close price.

---

## 19.4 Typical Traps: Live Trading Disconnect from Lack of Real Data

When systematically overestimating signal quality, strategies (especially RL models) develop a series of fatal biases:

### 19.4.1 Preference for "Unrealizable" Entry Timing

Agents evolve a pathological preference for "just-closed breakout" signals. In the simulated world, the breakout point is the perfect entry price; but in reality, since the next candle often has gaps or severe slippage, the Agent learns **ideal timing that can never be bought in the real world**.

### 19.4.2 Becoming a "Fee Harvester"

Under assumptions of no slippage and low friction, AI thinks "one more trade = one more profit," frantically increasing trading frequency. However, in live trading, high-frequency trading is often a **cost amplifier**, ultimately causing live performance to completely decouple from simulation results.

### 19.4.3 Wrong Causal Inference

The most dangerous situation is when RL learns wrong causal relationships. For example:
- **Wrong Inference**: "When indicators reach threshold and just closed, buying must profit."
- **True Causality**: This is merely a statistical artifact amplified by **idealized execution assumptions**, not real market profit logic.

---

## 19.5 Two Data Dimensions: Market-Level Environment vs. Strategy Execution Performance

To obtain real execution data, first clarify which type of data you need. We can divide it into two dimensions:

- **Dimension A: Market Real Execution Environment Data** (Public Environment): This data answers "If an order was placed in that market environment, what would **generally** happen?"
- **Dimension B: Strategy's Real Market Execution Data** (Private Performance): This data answers "When **my** Agent places orders in real markets, what **specifically** happened?"

> **Core Strategy**: If you want to train "execution-type" or "online RL Agents," the ultimate goal is definitely to obtain **Dimension B** data. Dimension A can only serve as an approximation for early algorithm development and cold-start phases.

---

## 19.6 Acquisition Paths: How to Obtain High-Fidelity Trading Data?

### 19.6.1 First Type: Purchasable or API-Accessible "Market-Level Data"

This data represents "market facts," reflecting overall market liquidity at the time.

1. **Level-2 / Order Book Data** (Most Critical):
   - **Content**: Price and quantity at each bid/ask level, millisecond-level tick/trade records.
   - **Uses**: Estimating market order average slippage, limit order fill probability, and building **Execution Simulators**.
   - **Sources**: Official exchanges (most authoritative but expensive), professional data vendors (like Refinitiv), or some advanced broker APIs.

2. **Tick Data**:
   - Can't see order book queuing, but can see each actual trade's price and volume, far more accurate than candlesticks.

3. **Paper Trading Environments**:
   - **Warning**: Most paper trading environments have overly ideal slippage and don't consider your order's market impact. It can only serve as a transition - don't over-rely on it.

> **Data Sources: Learning vs. Production**
>
> Yahoo Finance is sufficient for daily-bar learning exercises and backtesting at daily frequency. However, it is **not suitable for production**: data arrives with variable delay, historical adjustments can change retroactively, and real-time streaming is unreliable. For production systems requiring real-time L1/L2 data, you need institutional-grade providers such as **Databento** (normalized multi-exchange tick data), **Polygon.io** (now rebranded to Massive) (US equities), or **direct exchange feeds** (lowest latency, highest cost). The jump in data quality is matched by a jump in cost -- plan for this as a real line item, not an afterthought.

### 19.6.2 Second Type: Real Execution Data That Must Be "Generated by You"

This is the only data that truly reflects the "your Agent + market" interaction, containing the complete order lifecycle:
- **Full Logs**: Order time, order type, expected price, actual execution price (possibly multiple fills), execution latency, partial fill/unfilled records, fees, etc.

---

## 19.7 Brutal Truth: Why Core Data Cannot Be Purchased

Many developers ask: "Can I just buy this slippage-included real data without actually trading?"

> **Core Logic**: The answer is **no**.

The reason is: **Slippage is not purely a market attribute, but a joint product of "your behavior + market feedback."**

Your order method (market/limit), order size, and order timing all create different chemical reactions in the order book. There is no "universal, executed-for-you" real dataset.

---

## 19.8 Evolution Path: Three Stages from Simulator to Live Feedback

For developers pursuing real-world effectiveness, the recommended engineering path is:

1. **Stage 1: Build Execution Simulator**
   - Use Level-2/Tick data to build a "conservative" simulator.
   - Goal: Eliminate 80% of strategies that profit under ideal assumptions but lose under conservative assumptions.

2. **Stage 2: Small Capital Live Trading (Cold Start)**
   - Enter live trading with minimal position size, not to profit, but to collect full logs.
   - Goal: Obtain real execution failures, extreme slippage, and latency samples.

3. **Stage 3: Data Closed-Loop Calibration**
   - Use live-generated Type B data to reverse-validate Stage 1's simulator, making the simulated environment increasingly close to your real execution performance.

---

## 19.9 Canonical Data Model and Symbol Normalization

### 19.9.1 Why You Need a Canonical Data Model

Different brokers and data providers use different symbology: Interactive Brokers uses `conId`, Bloomberg uses FIGI, Yahoo Finance uses its own ticker format, and exchanges use native codes. If you let broker-specific formats leak into your core logic, changing venues or adding a second broker becomes a rewrite.

The solution is a **normalized internal format** with a thin adapter per venue:

```
Internal symbol format: SYMBOL.EXCHANGE
Examples: AAPL.NASDAQ, 0700.HKEX, TSLA.NASDAQ, 9988.HKEX

Normalizer interface (per venue):
  to_internal(broker_symbol) -> canonical_symbol
  to_broker(canonical_symbol) -> broker_symbol
```

This canonical model is what enables multi-venue trading. Your Signal Agent, Risk Agent, and portfolio logic all operate on normalized symbols. Only the Execution Agent's venue adapter translates to broker-native format at the boundary.

### 19.9.2 Symbol Resolution Gotchas

Symbol mapping sounds simple until edge cases destroy your PnL:

| Gotcha | What Happens | Defense |
|--------|-------------|---------|
| **Delisted tickers** | Lookup returns stale or null data silently | Validate against active symbol list daily |
| **Corporate action splits** | GOOG becomes GOOGL, ticker changes mid-history | Track symbol change events, maintain alias table |
| **Multi-part tickers** | `BRK.B` conflicts with `SYMBOL.EXCHANGE` format | Use a delimiter that doesn't conflict (e.g., `BRK-B.NYSE`) |
| **Stale quotes** | Data provider returns yesterday's close as "current" | Timestamp every quote; reject if age > threshold |
| **Dual-listed securities** | Same company, different symbols across exchanges | Map to canonical entity ID, not just ticker string |

> **Rule of Thumb**: Never trust a ticker string as a stable identifier. Tickers change, get reused, and vary across venues. Build your system around a canonical ID with ticker as a display-layer convenience.

---

## 19.10 Venue Selection: The Practical Default

For small to mid-sized quantitative funds, **Interactive Brokers (IB)** is the practical default venue. It covers US and HK markets through a single API, provides native algorithmic order types (TWAP, VWAP, Adaptive), and exposes real execution data for TCA. Its commission structure is competitive, and the TWS API is well-documented if not elegant.

**Alpaca** and similar platforms are useful stepping stones for paper trading and learning. However, they are not production endpoints -- limited market coverage, simplified order types, and less granular execution reporting make them insufficient for serious live trading.

| Feature | Interactive Brokers | Alpaca | Paper Trading |
|---------|-------------------|--------|---------------|
| Markets | US, HK, EU, Asia | US only | Simulated |
| Algo orders | TWAP, VWAP, Adaptive | Basic market/limit | N/A |
| TCA data | Full fill reports | Limited | None |
| Short selling | Yes (with borrow) | Limited | Simulated |
| Use case | Production | Learning / prototyping | Strategy validation |

---

## 19.11 Practical Recommendations: Realistic Solutions for 5-Minute High-Frequency Strategies

Combining the characteristics of 5-minute trend strategies, my most realistic recommendations are:

- **Prefer to Overestimate Risk**: When building models, prefer to overestimate slippage and underestimate fill probability.
- **Separate RL Training**: Initially let RL Agents learn "execution logic" (e.g., how much to order, whether to chase, when to abandon) rather than rushing to learn "direction prediction."
- **Enter Live Early**: Once strategy performs stably in "conservative simulator," start small capital live trading as soon as possible.

---

## 19.12 Summary

**Display Price + Slippage + Probability Latency + Trading Friction = Real Execution Data**

Only by incorporating all these details and avoiding the "idealized execution" trap can quantitative strategy (especially AI-driven strategy) backtest results have genuine practical reference value. The leap from "market data" to "execution data" is the necessary path for professional quantitative traders.

---

## 19.13 Multi-Agent Perspective

- **Execution Agent**: Translates "signals" into executable orders (splitting/limit/chasing/canceling), and continuously measures execution quality (slippage, fill rate, latency).
- **Risk Agent**: Transforms "can execute" into "worth executing," triggering position reduction or order rejection for high slippage/low liquidity/abnormal latency.
- **Data/Telemetry Agent**: Precipitates execution data closed-loop (order -> fill -> cost -> PnL), providing material for subsequent replay, attribution, and online learning.

---

## Lesson Deliverables

After completing this lesson, you will have:

1. **Execution System Key Concept Framework**: Execution price, slippage, latency, fill rate, friction costs
2. **Data Closed-Loop Collection Checklist**: Know what must be recorded in live trading to feed back to execution and RL
3. **An Actionable Evolution Path**: Conservative simulator -> Small capital live sampling -> Reverse calibrate simulator

---

## Key Takeaways

- [x] Real execution data = Execution price + Slippage + Probability/Latency + Trading rules/Friction costs
- [x] Using candle Close as execution price systematically overestimates strategy realizability
- [x] Type A data (market environment) can only approximate; Type B data (your live logs) is what can be used for calibration and evolution

---

## Further Reading

- [Background: Exchanges and Order Book Mechanics](../Part2-Quant-Fundamentals/Background/Exchanges-and-Order-Book-Mechanics.md) - Understanding real matching semantics of limit/market orders
- [Background: Execution Simulator Implementation](Background/Execution-Simulator-Implementation.md) - Code implementation of 4-level execution simulator
- [Background: Tick-Level Backtest Framework](../Part2-Quant-Fundamentals/Background/Tick-Level-Backtest-Framework.md) - Event-driven backtesting and queue position simulation
- [FIX Protocol Introduction](../Resources-Links/FIX-Protocol-Introduction.md) - FIX 4.4 message structure and QuickFIX implementation
- [Appendix A: Live Trading Logging Standards](Appendix/Appendix-A-Live-Trading-Logging-Standards.md) - Which fields you should record to form a closed loop

---

## Next Lesson Preview

**Lesson 20: Production Operations**

When the system runs in real markets, the question is no longer "is the strategy correct" but "is the system running correctly": Is the data pipeline healthy? Is PnL abnormal? Has drawdown triggered circuit breaker? In the next lesson, we'll truly "operate" this system.
