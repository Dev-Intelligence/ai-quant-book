# Background: Exchanges and Order Book Mechanics

> Understanding how exchanges operate is the foundation for understanding slippage, execution, and market microstructure.

---

## 1. Types of Exchanges

### 1.1 Centralized Exchanges (CEX)

**Examples**: NYSE, NASDAQ, Shanghai Stock Exchange, Binance

**Characteristics**:
- Unified matching engine
- Central order book
- High liquidity
- Regulated

### 1.2 Decentralized Exchanges (DEX)

**Examples**: Uniswap, dYdX

**Characteristics**:
- Smart contract matching
- AMM or order book
- No trusted intermediary
- Higher latency

---

## 2. Order Book

### 2.1 Basic Structure

```
Sell Side (Ask)        Price          Buy Side (Bid)
                      $101.50
50 shares             $101.25
100 shares            $101.00
                      $100.75         200 shares
                      $100.50         150 shares
                      $100.25         80 shares
```

- **Bid**: The highest price buyers are willing to pay
- **Ask**: The lowest price sellers are willing to accept
- **Spread**: Best Ask - Best Bid

### 2.2 Order Book Depth

**Level 1**: Only best bid/ask
```
Best Bid: $100.75 × 200
Best Ask: $101.00 × 100
```

**Level 2**: Multiple price levels
```
Bid:
  $100.75 × 200
  $100.50 × 150
  $100.25 × 80

Ask:
  $101.00 × 100
  $101.25 × 50
  $101.50 × 30
```

**Level 3**: Individual order details (usually not public)

---

## 3. Order Types

### 3.1 Market Order

**Definition**: Execute immediately at the current best price

**Pros**: Guaranteed execution
**Cons**: Price not guaranteed, may incur slippage

**Example**:
```
Order Book:
  Ask: $101.00 × 100, $101.25 × 50

Market buy 120 shares:
  100 shares @ $101.00
  20 shares @ $101.25

Average execution price = (100×101.00 + 20×101.25) / 120 = $101.04
Slippage = $101.04 - $101.00 = $0.04
```

### 3.2 Limit Order

**Definition**: Execute only at specified price or better

**Pros**: Price certainty
**Cons**: May not execute

**Example**:
```
Limit buy 100 shares @ $100.75

If current Best Ask = $101.00:
  → Order enters bid queue
  → Wait for seller willing to sell at $100.75
```

### 3.3 Other Order Types

| Type | Description | Use Case |
|------|-------------|----------|
| Stop Order | Becomes market order when trigger price hit | Stop loss |
| Stop-Limit | Becomes limit order when triggered | Precise stop loss |
| Iceberg | Only displays partial quantity | Hide large orders |
| IOC | Immediate or Cancel | Avoid resting orders |
| FOK | Fill or Kill | Large order execution |
| GTC | Good Till Cancelled | Long-term resting orders |

---

## 4. Matching Mechanism

### 4.1 Price Priority, Time Priority

**Rules**:
1. Better-priced orders execute first
2. At same price, earlier orders execute first

**Example**:
```
Bid queue:
  1. 09:00:01 bid @ $100.75
  2. 09:00:05 bid @ $100.75
  3. 09:00:03 bid @ $100.50

Seller market sells → First #1 executes, then #2
```

### 4.2 Continuous Matching vs Call Auction

**Continuous Matching**:
- Orders matched in real-time
- Used during normal trading hours

**Call Auction**:
- Collects orders over a period, then determines opening/closing price uniformly
- **A-share opening auction (9:15-9:25)**:
    - **9:15-9:20**: Can place and cancel orders (often fake orders to probe pressure)
    - **9:20-9:25**: Can place orders, **cannot cancel** (true intentions revealed)
- Reduces price manipulation, increases opening liquidity

---

## 5. Market Makers

### 5.1 Role

- Continuously provide bid and ask quotes
- Provide liquidity
- Earn the Bid-Ask Spread

### 5.2 Market Making Strategy

```
Market maker quotes:
  Bid: $100.00 × 1000
  Ask: $100.10 × 1000

Spread = $0.10

If buy and sell orders are balanced:
  Buy 1000 shares @ $100.00
  Sell 1000 shares @ $100.10
  Profit = 1000 × $0.10 = $100
```

### 5.3 Risks

- **Inventory risk**: Holding too much one-sided position
- **Adverse selection**: Being "picked off" by informed traders

---

## 6. Market Microstructure

### 6.1 Determinants of Spread

| Factor | Impact |
|--------|--------|
| Liquidity | High liquidity → Small spread |
| Volatility | High volatility → Large spread |
| Information asymmetry | High asymmetry → Large spread |
| Volume | High volume → Small spread |

### 6.2 Market Impact

**Definition**: The effect of large orders on price

**Temporary impact**: Price displacement during execution, recovers afterward
**Permanent impact**: Order carries information, price changes permanently

**Estimation formula** (simplified):
```
Market Impact ≈ σ × √(Q / ADV)

σ = Daily volatility
Q = Order quantity
ADV = Average daily volume
```

### 6.3 Liquidity Measures

| Metric | Formula | Meaning |
|--------|---------|---------|
| Bid-Ask Spread | Ask - Bid | Trading cost |
| Depth | Order book depth | Order capacity |
| Resilience | Price recovery speed | Post-impact recovery |
| Turnover | Daily volume / Float | Activity level |

---

## 7. High-Frequency Trading (HFT)

### 7.1 Characteristics

- Extremely short holding periods (milliseconds)
- Large number of orders (thousands per second)
- Low latency (microseconds)
- High win rate, low profit per trade

### 7.2 Common Strategies

| Strategy | Description |
|----------|-------------|
| Market Making | Provide liquidity, earn spread |
| Arbitrage | Cross-market price differential |
| Momentum | Detect and follow large orders |
| Statistical Arbitrage | High-frequency mean reversion |

### 7.3 Technical Requirements

- Low-latency network (Co-location)
- FPGA / ASIC hardware
- Nanosecond-level clock synchronization
- Dedicated exchange interfaces

---

## 8. Importance of Latency

### 8.1 Latency Sources

| Source | Typical Latency |
|--------|-----------------|
| Network transmission (fiber) | 1-50 ms (cross-city) |
| Exchange processing (matching) | 10-100 μs (microseconds) |
| Local processing (decision) | 1-10 μs (microseconds) |
| FPGA hardware acceleration | 100-500 ns (nanoseconds) |

### 8.2 Impact of Latency on Strategies

```
Strategy Type           Acceptable Latency
Core HFT Market Making    < 10 μs (microseconds)
Cross-market Arbitrage    < 5 ms (physical limit)
Intraday Quant Trend      < 10-100 ms
Daily/Swing Strategies    < 1 second
```

---

## 9. Practical Advice

### 9.1 For Non-HFT Strategies

1. **Focus on liquidity**: Choose liquid instruments
2. **Split execution**: Break large orders into smaller ones
3. **Prefer limit orders**: Avoid market order slippage
4. **Avoid volatile periods**: Opening/closing have poor liquidity

### 9.2 Slippage Control

```python
# Simple slippage estimation
def estimate_slippage(order_size, avg_daily_volume, volatility):
    """
    order_size: Order quantity (shares)
    avg_daily_volume: Average daily volume
    volatility: Daily volatility
    """
    participation_rate = order_size / avg_daily_volume
    slippage = volatility * (participation_rate ** 0.5)
    return slippage
```

---

## 10. Characteristics of Different Markets

| Market | Trading Hours | Characteristics |
|--------|---------------|-----------------|
| US Stocks | 9:30-16:00 ET | T+1 settlement (2024 rule), can day trade, Dark Pools |
| A-Shares | 9:30-11:30, 13:00-15:00 | T+1 trading (buy today, sell tomorrow), price limits |
| HK Stocks | 9:30-12:00, 13:00-16:00 | T+2 settlement, VCM cooling-off, Closing auction |
| Crypto | 24/7 | Never closes, AMM and order book coexist |

> **T+1 Settlement vs T+1 Trading**: These are fundamentally different concepts. US T+1 settlement (effective May 2024) means fund and share delivery completes the next business day, but you can freely buy and sell the same stock multiple times within the same trading day (day trading). China A-share T+1 trading is a trading restriction: shares purchased today can only be sold on the next trading day, preventing same-day round trips.

---

> **Core Principle**: Understanding the order book and matching mechanism is key to understanding why backtest results differ from live trading performance. Your orders don't execute in a vacuum—they compete with other orders in a complex market ecosystem.
