# Background: Cryptocurrency Trading Characteristics

> Cryptocurrency markets differ significantly from traditional financial markets. Understanding these characteristics is a prerequisite for developing crypto quantitative strategies.

---

## 1. Market Characteristics Overview

| Characteristic | Cryptocurrency | Traditional Stocks |
|----------------|----------------|-------------------|
| Trading Hours | 24/7/365 | Weekday fixed hours |
| Volatility | High (typically 2-8% daily, can exceed 20% in extreme events) | Lower (1-3% daily) |
| Regulation | Weak/chaotic | Strict |
| Shorting | Easy (perpetual contracts) | Requires borrowing |
| Leverage | Up to 125x | Usually 2-4x |
| Market Efficiency | Low (many arbitrage opportunities) | High |

---

## 2. Impact of 24/7 Trading

### 2.1 Advantages

- **No overnight risk definition**: No traditional "overnight" concept
- **Instant reaction**: Can trade immediately after news
- **More opportunities**: More time = more opportunities

### 2.2 Challenges

- **Never-ending**: Cannot completely step away from market
- **Liquidity fluctuation**: Significant differences between Asian, European, American sessions
- **Ops pressure**: Systems need to be online 24/7

### 2.3 Liquidity Distribution by Session

```
UTC Time     Dominant Market    Liquidity
00:00-08:00  Asia               Medium
08:00-16:00  Europe             High
16:00-24:00  Americas           Highest
Weekend      -                  Low
```

**Strategy suggestion**:
```python
def get_liquidity_multiplier(hour_utc, is_weekend):
    if is_weekend:
        return 0.5  # Weekend liquidity halved
    if 16 <= hour_utc < 24:
        return 1.2  # US session best
    elif 8 <= hour_utc < 16:
        return 1.0  # Europe session normal
    else:
        return 0.7  # Asia session lower
```

---

## 3. Volatility Characteristics

### 3.1 Extreme Volatility

| Event | Date | Volatility |
|-------|------|------------|
| March 12 Crash | 2020.3.12 | BTC -50% single day |
| May 19 Crash | 2021.5.19 | BTC -30% single day |
| FTX Collapse | 2022.11 | BTC -25% weekly |
| ETF Launch | 2024.1 | BTC +20% weekly |

### 3.2 Volatility Modeling

Traditional GARCH models need adjustment:
```python
# Crypto volatility estimation needs shorter window
def crypto_volatility(returns, window=24):  # 24-hour window
    return returns.rolling(window).std() * np.sqrt(365 * 24)  # Annualized
```

### 3.3 Impact on Strategies

| Strategy Type | Impact | Response |
|---------------|--------|----------|
| Trend Following | Frequent signals | Increase filter threshold |
| Mean Reversion | Frequent stop-outs | Widen stop-loss range |
| Grid Trading | Suitable | Mainstream strategy |
| Arbitrage | Many opportunities | Requires low latency |

---

## 4. Exchange Ecosystem

### 4.1 Major Exchanges

| Exchange | Type | Characteristics | API Quality |
|----------|------|-----------------|-------------|
| Binance | CEX | Largest liquidity | ⭐⭐⭐⭐⭐ |
| OKX | CEX | Strong derivatives | ⭐⭐⭐⭐ |
| Bybit | CEX | Contract-focused | ⭐⭐⭐⭐ |
| Coinbase | CEX | Good compliance | ⭐⭐⭐ |
| dYdX | DEX | Decentralized derivatives | ⭐⭐⭐ |
| Uniswap | DEX | AMM model | ⭐⭐⭐ |

### 4.2 Exchange Risks

- **Exchange failure**: FTX, Mt.Gox cases
- **API instability**: API may go down during high volatility
- **Rule changes**: Leverage limits, fee adjustments
- **Withdrawal restrictions**: Funds may be frozen

**Countermeasures**:
- Diversify funds across multiple exchanges
- Don't store too much on exchanges
- Use reputable large exchanges

---

## 5. Derivatives Market

### 5.1 Perpetual Contracts

**Characteristics**:
- No expiration date
- Funding rate mechanism
- High leverage (up to 125x)

**Funding Rate**:
```
Funding Rate > 0 → Longs pay shorts → Market bullish
Funding Rate < 0 → Shorts pay longs → Market bearish

Funding Rate Strategy:
- Short high positive rate contracts + Long spot hedge = Earn funding rate
```

### 5.2 Quarterly Futures

- Have expiration dates (weekly, monthly, quarterly)
- Basis spread presents arbitrage opportunities

### 5.3 Options

- Developed later
- Mainly on Deribit, OKX
- Extremely high implied volatility

---

## 6. DeFi Quantitative Trading

### 6.1 AMM Mechanism

**Uniswap Formula**:
```
x * y = k

x = Token A quantity
y = Token B quantity
k = Constant

Price = y / x
```

**Slippage Calculation**:
```python
def calculate_amm_slippage(amount_in, reserve_in, reserve_out, fee=0.003):
    amount_with_fee = amount_in * (1 - fee)
    amount_out = (reserve_out * amount_with_fee) / (reserve_in + amount_with_fee)

    spot_price = reserve_out / reserve_in
    actual_price = amount_out / amount_in
    slippage = (spot_price - actual_price) / spot_price

    return slippage
```

### 6.2 DeFi Strategies

| Strategy | Risk | Return Source |
|----------|------|---------------|
| Liquidity Mining | Impermanent loss | Trading fees + Token incentives |
| Arbitrage | Smart contract risk | Price differences |
| Lending Arbitrage | Liquidation risk | Interest rate spread |
| MEV | High technical barrier | Front-running |

### 6.3 Smart Contract Risks

- Code vulnerabilities
- Flash loan attacks
- Oracle manipulation
- Rug Pulls

---

## 7. Data Characteristics

### 7.1 Data Sources

| Source | Data Type | Characteristics |
|--------|-----------|-----------------|
| Exchange API | Quotes, trades | Real-time, free |
| CoinGecko | Aggregated data | Wide coverage |
| Glassnode | On-chain data | Paid, deep |
| Nansen | Wallet analysis | Paid, premium |

### 7.2 On-Chain Data

Unique data sources:
- Wallet balance changes
- Whale transfers
- Exchange net inflow/outflow
- Active addresses
- Hash rate (PoW)
- Staking amount (PoS)

```python
# On-chain signal example
def on_chain_signal(exchange_netflow, large_tx_count):
    """
    exchange_netflow > 0 → Funds flowing into exchanges → Possible selling pressure
    large_tx_count rising → Whales active → Possible directional move
    """
    if exchange_netflow > threshold_high:
        return -1  # Bearish
    elif exchange_netflow < threshold_low:
        return 1   # Bullish
    return 0
```

---

## 8. Quantitative Strategy Adaptation

### 8.1 High-Frequency Strategies

**Challenges**:
- Latency: Exchange API latency 10-100ms
- Competition: Professional market makers
- Costs: Fees eat into profits

**Recommendation**: Not recommended unless you have infrastructure advantage

### 8.2 Medium-Low Frequency Strategies

**Suitable**:
- Trend following (1-hour to daily)
- Momentum strategies
- Funding rate arbitrage
- Cash-and-carry arbitrage

**Example: Funding Rate Arbitrage**

> **Typical Funding Rate Ranges**:
> - Normal: ±0.01%/8h (±0.0001)
> - Crowded trade: 0.05-0.1%/8h (0.0005-0.001)
> - Extreme sentiment: >0.3%/8h (>0.003)

```python
def funding_rate_arbitrage(funding_rate, threshold=0.0003):
    """
    When funding rate > 0.03% (0.0003):
    1. Short perpetual contract
    2. Long spot to hedge
    3. Collect funding rate every 8 hours

    Note: 0.03%/8h = ~33% annualized, accounting for costs
    """
    if funding_rate > threshold:
        return {
            'perp': 'SHORT',
            'spot': 'LONG',
            'expected_return': funding_rate * 3 * 365  # Annualized
        }
    return None
```

### 8.3 Grid Trading

**Characteristics**:
- Set multiple buy/sell orders within price range
- Profit from volatility by capturing spreads
- Suitable for ranging markets

**Example**:
```python
def create_grid(lower, upper, grids, total_amount):
    grid_size = (upper - lower) / grids
    order_amount = total_amount / grids

    orders = []
    for i in range(grids):
        price = lower + i * grid_size
        orders.append({
            'price': price,
            'amount': order_amount,
            'side': 'BUY'
        })
        orders.append({
            'price': price + grid_size,
            'amount': order_amount,
            'side': 'SELL'
        })
    return orders
```

---

## 9. Risk Management

### 9.1 Special Risks

| Risk | Description | Response |
|------|-------------|----------|
| Exchange Risk | Exchange failure/hack | Diversify funds |
| Extreme Volatility | 50%+ single-day swings | Reduce leverage |
| Liquidity Drought | Can't execute in extreme conditions | Reserve buffer |
| Regulatory Risk | Sudden policy changes | Monitor news |
| Manipulation Risk | Whale market control | Choose major coins |

### 9.2 Leverage Recommendations

| Experience Level | Recommended Leverage |
|------------------|---------------------|
| Beginner | 1x (no leverage) |
| Experienced | 2-3x |
| Professional | 5-10x |
| Never Recommended | > 20x |

### 9.3 Risk Control Code

```python
class CryptoRiskManager:
    def __init__(self):
        self.max_position_pct = 0.1   # Single position max 10%
        self.max_leverage = 3         # Max leverage
        self.max_daily_loss = 0.05    # Max daily loss 5%
        self.stop_loss = 0.02         # Per-trade stop loss 2%

    def check_order(self, order, portfolio):
        # Check position limit
        if order.value / portfolio.total > self.max_position_pct:
            return False, "Exceeds single position limit"

        # Check leverage
        if order.leverage > self.max_leverage:
            return False, "Exceeds leverage limit"

        # Check daily loss
        if portfolio.daily_pnl < -self.max_daily_loss:
            return False, "Daily loss limit reached"

        return True, "Passed"
```

---

## 10. Practical Recommendations

### 10.1 Getting Started Path

1. **Learn basics**: Understand blockchain, wallets, exchanges
2. **Small-scale practice**: Get familiar with trading flow with small capital
3. **Backtest validation**: Verify strategy with historical data
4. **Paper trading**: Use testnet or simulated account
5. **Small live trading**: Gradually increase capital

### 10.2 Recommended Tools

| Purpose | Tool |
|---------|------|
| API Wrapper | CCXT (unified interface) |
| Backtesting | Freqtrade, VectorBT |
| Data | CoinGecko API, Binance API |
| On-chain Analysis | Glassnode, Dune Analytics |

### 10.3 Pitfall Avoidance Guide

1. **Don't use high leverage**: Too many bloody lessons
2. **Don't chase or panic sell**: High volatility doesn't mean more opportunities
3. **Don't ignore fees**: HFT costs accumulate quickly
4. **Don't go all in**: Diversify, control position size
5. **Don't neglect security**: Protect private keys and API keys

---

> **Core Principle**: Crypto markets have high volatility and many opportunities, but also high risk. Conservative position management and strict risk control are keys to survival. Living long is more important than earning big.
