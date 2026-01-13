# Background: High-Frequency Trading and Market Microstructure

> High-frequency trading (HFT) is an extreme form of quantitative trading. Retail traders cannot participate, but understanding it helps you recognize how markets actually work.

---

## 1. What is High-Frequency Trading

### 1.1 Definition

**High-Frequency Trading (HFT)**:
- Holding period: Milliseconds to seconds
- Trade frequency: Tens of thousands to millions per day
- Profit source: Tiny spreads × Huge volume
- Core competency: **Speed**

### 1.2 HFT vs Regular Quant

| Dimension | HFT | Regular Quant |
|-----------|-----|---------------|
| Holding period | Milliseconds-seconds | Minutes-days-weeks |
| Latency requirement | Microsecond-level | Seconds acceptable |
| Capital threshold | $10M+ | $10K+ |
| Technical barrier | Extreme (FPGA, microwave) | Medium (Python) |
| Competitors | Top institutions | All market participants |
| Information advantage | Speed advantage | Analysis advantage |

---

## 2. Core HFT Strategies

### 2.1 Market Making

**Principle**: Simultaneously place buy and sell orders, earn the bid-ask spread

```
Bid Level 1: $100.00 (your buy order)
Ask Level 1: $100.02 (your sell order)

Spread = $0.02 = Your profit (if both sides fill)
```

**Challenges**:
- Adverse selection: Informed traders only take the losing side
- Inventory risk: One-sided fills create exposure
- Fierce competition: Spreads compressed to minimum

### 2.2 Statistical Arbitrage

**High-frequency version of pairs trading**:
- Find highly correlated instruments (e.g., ETF and constituents)
- Trade quickly when spread deviates
- Millisecond-level mean reversion

### 2.3 Latency Arbitrage

**Principle**: Exploit price update delays between different exchanges

```
Exchange A: $100.00 (already updated)
Exchange B: $99.98  (not yet updated)

Buy on B, Sell on A, lock in $0.02
```

**Controversy**: Criticized as "taxing" rather than "adding value"

### 2.4 Order Flow Prediction

**Principle**: Predict large order execution direction

- Analyze order book change patterns
- Identify institutional order slicing behavior
- Position ahead

---

## 3. Technical Infrastructure

### 3.1 Latency Levels

| Level | Latency | Technology | Cost |
|-------|---------|------------|------|
| Seconds | 1000+ ms | Cloud server + Python | $ |
| Milliseconds | 1-100 ms | Dedicated server + C++ | $$ |
| Microseconds | 1-1000 μs | Co-location + C++ | $$$ |
| Nanoseconds | < 1000 ns | FPGA + Microwave | $$$$ |

### 3.2 Co-location

**Data center colocation**: Place servers in exchange data center

- Physical distance: Meters vs thousands of kilometers
- Latency difference: Microseconds vs milliseconds
- Cost: $10,000-$100,000/month

### 3.3 Network Optimization

**Traditional Fiber** vs **Microwave Communication**:

```
Chicago → New York
Fiber: ~14.5 ms
Microwave: ~8.5 ms (99% of light speed)

6ms difference = Huge advantage
```

**Laser Communication**: Has trade-offs with microwave—laser offers higher bandwidth but is more affected by fog/rain, while microwave is more weather-resistant but has lower bandwidth. Both are used for different scenarios.

### 3.4 Hardware Acceleration

**FPGA (Field-Programmable Gate Array)**:
- Process trading logic directly in hardware circuits
- Latency: Nanosecond-level
- Development cost: $1M+
- Team required: Hardware engineers + Quant traders

**GPU**:
- Parallel computation for many strategies
- ML model inference
- Latency: Microsecond-level

---

## 4. Market Microstructure

### 4.1 Order Book

**L1 Data**:
- Best Bid
- Best Ask
- Corresponding sizes

**L2 Data**:
```
Ask Level 5: $100.10 x 500
Ask Level 4: $100.08 x 300
Ask Level 3: $100.06 x 200
Ask Level 2: $100.04 x 100
Ask Level 1: $100.02 x 50    ← Best Ask
-------------------------
Bid Level 1: $100.00 x 80    ← Best Bid
Bid Level 2: $99.98  x 150
Bid Level 3: $99.96  x 400
Bid Level 4: $99.94  x 600
Bid Level 5: $99.92  x 1000
```

**L3 Data**: Each individual order at each price level (Order ID, timestamp)

### 4.2 Order Types

| Type | Behavior | Use Case |
|------|----------|----------|
| Market | Execute immediately, price uncertain | Priority on speed |
| Limit | Specified price, may not execute | Priority on price |
| IOC | Fill what you can, cancel rest | Probe liquidity |
| FOK | Fill all or cancel all | Large order execution |
| Iceberg | Display only partial quantity | Hide intent |

### 4.3 Price Formation Mechanism

**Continuous Matching**:
- Orders matched in real-time
- Price priority, then time priority

**Call Auction**:
- Accumulate orders then match uniformly
- Used for opening, closing

### 4.4 Liquidity Concepts

**Liquidity Provider vs Liquidity Taker**:

- Maker (resting order): Provides liquidity, usually gets fee rebate
- Taker (aggressor): Consumes liquidity, usually pays fee

**Slippage**: Large orders "eat through" multiple price levels

```
Buy 1000 shares, current ask level 1 only has 50 shares
→ Need to eat into level 5 or deeper
→ Average execution price > Ask level 1
→ This is slippage
```

---

## 5. Order Flow and Kyle's Lambda

### 5.1 Order Flow Imbalance

**Definition**: Net difference between buy and sell orders per unit time

```
OFI = Buy-initiated volume - Sell-initiated volume

OFI > 0: Buy pressure dominates, price tends to rise
OFI < 0: Sell pressure dominates, price tends to fall
```

**How to determine trade direction?**

Use Lee-Ready algorithm:
```
If trade price > mid price: Buy-initiated (Taker is buyer)
If trade price < mid price: Sell-initiated (Taker is seller)
If trade price = mid price: Use previous trade's direction
```

**Intuitive explanation**:

> Imagine the order book as a street. Between bid level 1 and ask level 1 is "no man's land" (the spread).
> If someone is willing to "cross no man's land" to buy at the ask, they're urgent—usually informed traders.
> Their direction often represents true price pressure.

### 5.2 Kyle's Lambda (λ)

**Source**: Albert Kyle's classic 1985 paper

**Core idea**: Price sensitivity to order flow

```
ΔP = λ × OrderFlow

Where:
  ΔP = Price change
  λ  = Price impact coefficient (Kyle's Lambda)
  OrderFlow = Signed order flow (buys - sells)
```

**λ Interpretation**:

| λ Value | Market State | Trader Response |
|---------|--------------|-----------------|
| Low λ | Good liquidity, large orders have small impact | Can place large orders |
| High λ | Poor liquidity, large orders have big impact | Should slice orders |

### 5.3 λ Estimation Methods

**Method 1: Linear Regression**

```
Collect data over a period:
  - Price change ΔP every 5 minutes
  - Net order flow OFI every 5 minutes

Regression: ΔP = α + λ × OFI + ε

λ is the regression coefficient
```

**Paper exercise**:

Suppose you collected this data:

| Period | OFI ($Million) | ΔP (%) |
|--------|----------------|--------|
| 09:30-09:35 | +2.0 | +0.10% |
| 09:35-09:40 | -1.5 | -0.08% |
| 09:40-09:45 | +3.0 | +0.14% |
| 09:45-09:50 | -0.5 | -0.02% |
| 09:50-09:55 | +1.0 | +0.06% |

Simple estimate: λ ≈ 0.05% / $1M = **5 bps per million**

Interpretation: Every $1 million net buying pushes price up about 5 basis points.

**Method 2: Estimate from Order Book Depth**

```
λ ≈ Spread / (2 × Depth)

Where:
  Spread = Bid-ask spread
  Depth  = Order book depth (e.g., bid level 1 + ask level 1 total)
```

**Example**:
```
Spread = $0.02
Bid Level 1 = 5000 shares × $100 = $500,000
Ask Level 1 = 5000 shares × $100 = $500,000
Depth = $1,000,000

λ ≈ $0.02 / (2 × $1,000,000)
  ≈ 0.00001 per dollar
  ≈ 1 bp per $100,000
```

### 5.4 Applications of λ

**Application 1: Execution Cost Estimation**

```
Planning to execute $500,000 buy order
λ = 1 bp / $100,000

Expected impact = λ × OrderSize
         = 1 bp × 5
         = 5 bps = 0.05%

If stock price $100, expected execution price ≈ $100.05
```

**Application 2: Optimal Execution Strategy**

```
Large order slicing principle:

Single order size < Depth × (Acceptable impact / λ)

Example:
  - Acceptable impact: 10 bps
  - λ = 2 bps / $100,000
  - Single order limit = 10 / 2 × $100,000 = $500,000
```

**Application 3: Liquidity Monitoring**

```python
def calculate_lambda(price_changes: list,
                     order_flows: list) -> float:
    """
    Estimate Kyle's Lambda using linear regression
    """
    import numpy as np
    from scipy import stats

    # Filter outliers
    valid = [(dp, of) for dp, of in zip(price_changes, order_flows)
             if abs(of) > 0]

    if len(valid) < 10:
        return float('nan')

    dp = np.array([v[0] for v in valid])
    of = np.array([v[1] for v in valid])

    slope, intercept, r_value, p_value, std_err = stats.linregress(of, dp)

    return slope  # This is λ


def monitor_liquidity(historical_lambda: float,
                      current_lambda: float,
                      threshold: float = 2.0) -> dict:
    """
    Monitor liquidity changes
    """
    ratio = current_lambda / historical_lambda

    return {
        'lambda_ratio': ratio,
        'liquidity_warning': ratio > threshold,
        'suggested_action': 'reduce_order_size' if ratio > threshold else 'normal'
    }
```

### 5.5 λ and Market States

| Market Event | λ Change | Reason |
|--------------|----------|--------|
| Pre-earnings | ↑ Rises | Market makers withdraw, depth decreases |
| High volatility | ↑ Rises | Uncertainty increases |
| Open/Close | ↑ Rises | Liquidity concentrated |
| Calm trading day | ↓ Falls | Market makers quote aggressively |
| Index rebalancing | ↑↑ Significant rise | Passive fund large orders flood in |

### 5.6 Common Misconceptions

**Misconception 1: λ is constant**

λ varies with time:
- Intraday: High at open, low mid-day, high at close
- Event-driven: Spikes during news releases
- Seasonal: Generally higher during earnings season

**Misconception 2: Only look at λ, ignore Spread**

Complete trading cost = Spread + λ × OrderSize

Small orders mainly affected by Spread, large orders mainly by λ.

**Misconception 3: Estimate λ with daily data**

λ is a microstructure concept, should use tick or minute data. Daily data loses intraday information.

---

## 6. Why Retail Cannot Do HFT

### 6.1 Speed Gap

```
You: 100ms latency (already fast)
HFT: 0.01ms latency

By the time your order arrives, market has changed 10000 times
```

### 6.2 Cost Gap

| Item | HFT Institution | Retail |
|------|-----------------|--------|
| Co-location | $50K/month | Cannot access |
| Data fees | $100K/year | Free delayed data |
| Tech team | $5M/year | Yourself |
| Capital | $100M+ | $10K |

### 6.3 Information Gap

- L3 data: Institution exclusive
- Dark pool data: Institution exclusive
- Order flow data: Market maker exclusive

### 6.4 Practical Impact

**HFT's impact on retail**:
- Positive: Provides liquidity, tightens spreads
- Negative: Gets "taxed" (slightly worse prices)

**Coping strategies**:
- Use limit orders instead of market orders
- Avoid trading during open/close volatility
- Don't do ultra-short-term intraday

---

## 7. History and Controversy of HFT

### 7.1 Key Events

| Year | Event |
|------|-------|
| 2005 | Reg NMS implemented, fragmented trading begins |
| 2010 | Flash Crash, Dow drops 1000 points in 5 minutes |
| 2012 | Knight Capital loses $460M in 45 minutes |
| 2014 | *Flash Boys* published, HFT controversy peaks |
| 2015 | Citadel becomes largest market maker |

### 7.2 Regulation and Controversy

**Supporters' view**:
- Provides liquidity
- Tightens bid-ask spreads
- Increases market efficiency

**Critics' view**:
- Systemic risk
- Unfair to regular investors
- Flash crash risk

**Regulatory trends**:
- Circuit breakers
- Minimum tick sizes
- Order cancellation fees

---

## 8. What Retail Should Focus On

### 8.1 Understand, Don't Participate

- Know HFT exists, but don't try to compete with it
- Understand your orders may be "taxed"
- Accept this as part of market costs

### 8.2 Suitable Time Scales for Retail

| Time Scale | Feasibility | Reason |
|------------|-------------|--------|
| Nanoseconds-milliseconds | Impossible | HFT dominates |
| Seconds-minutes | Difficult | Still speed disadvantage |
| Hours-days | Feasible | Information analysis advantage |
| Weeks-months | Best | Patience advantage |

### 8.3 Real Advantages

Retail advantages aren't in speed, but in:
- **Patience**: Can wait for best opportunities
- **Flexibility**: Not constrained by scale
- **Focus**: Can specialize deeply
- **No pressure**: Don't need quarterly performance reports

---

## 9. Further Reading

### Books
- *Flash Boys* - Michael Lewis (Popular read)
- *Trading and Exchanges* - Larry Harris (Market microstructure classic)
- *Algorithmic and High-Frequency Trading* - Cartea, Jaimungal, Penalva (Academic treatise)

### Data Sources
- Lobster (Academic order book data)
- TAQ (NYSE Trades and Quotes data)
- Exchange L2/L3 data subscriptions

---

> **Core Insight**: HFT is an extreme competition of technology and capital. The right strategy for retail is not to join this race, but to choose time scales where HFT cannot compete—win with patience and analytical ability.
