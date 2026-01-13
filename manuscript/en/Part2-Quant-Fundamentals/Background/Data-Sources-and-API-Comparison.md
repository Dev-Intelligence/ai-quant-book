# Background: Data Sources and API Comparison

> Data is the foundation of quantitative trading. Choosing the right data source directly affects strategy development efficiency and live trading performance.

---

## 1. Major Data Sources Overview

| Data Source | Type | Price | Latency | Use Case |
|-------------|------|-------|---------|----------|
| **Binance API** | Crypto | Free | Real-time | Crypto strategies |
| **Yahoo Finance** | Stocks/ETFs | Free | 15-20 min | Learning/backtesting |
| **Alpha Vantage** | Multi-asset | Free/Paid | 15 min | Prototyping |
| **Polygon.io** | US Stocks | $29-199/mo | Real-time | US stock live trading |
| **Alpaca Markets** | US Stocks | Free | Real-time | US stock trading |
| **Bloomberg** | All assets | $2400+/mo | Real-time | Institutional |
| **Refinitiv** | All assets | $1800+/mo | Real-time | Institutional |
| **Nasdaq Data Link** | Alternative | Custom pricing | Daily | Factor research |

---

## 2. Free Data Sources Detailed

### 2.1 Binance API

**Pros**:
- Completely free, no payment required
- Real-time data, minimal latency
- WebSocket real-time streaming support
- Complete historical data (years of history)
- REST + WebSocket dual interfaces

**Cons**:
- Crypto only
- Rate Limit: 1200 requests/minute (REST), subscription limits per connection (WebSocket)
- API may be unstable during high volatility

**Rate Limit Handling**:
```python
import time
from binance.exceptions import BinanceAPIException

def fetch_with_retry(func, max_retries=3):
    for attempt in range(max_retries):
        try:
            return func()
        except BinanceAPIException as e:
            if e.code == -1003:  # Rate limit
                time.sleep(60)  # Wait 1 minute
            else:
                raise
```

**Use Case**: Crypto strategy development, 24/7 trading systems

---

### 2.2 Yahoo Finance (yfinance)

**Pros**:
- Completely free
- Covers global stocks, ETFs, indices
- Rich historical data (decades of history)
- Easy-to-use Python library

**Cons**:
- 15-20 minute data delay
- No official API, relies on web scraping (may be blocked)
- Data quality not guaranteed (occasional errors)
- No Level-2 data

**Usage Example**:
```python
import yfinance as yf

# Get stock data
aapl = yf.Ticker("AAPL")
df = aapl.history(period="1y", interval="1d")

# Batch download
data = yf.download(["AAPL", "GOOGL", "MSFT"], period="1mo")
```

**Use Case**: Learning, backtesting, daily frequency strategy research

---

### 2.3 Alpha Vantage

**Pros**:
- Free tier available (25 requests/day)
- Covers stocks, forex, crypto
- Provides technical indicator APIs
- Official support

**Cons**:
- Free tier severely restricted (only 25 calls/day as of 2024)
- Paid version starts at $49.99/month
- 15-minute data delay
- Limited historical depth

**Rate Limit Comparison**:
| Tier | Price | Request Limit |
|------|-------|---------------|
| Free | $0 | 25/day (severely restricted) |
| Basic | $49.99/mo | 30/min |
| Premium | $249.99/mo | 120/min |

**Use Case**: Prototyping, multi-asset research

---

### 2.4 Alpaca Markets

**Pros**:
- Free real-time US stock data
- Combined data + trading API (no separate data subscription needed)
- WebSocket real-time streaming
- Paper trading environment included
- Commission-free stock trading
- Supports fractional shares

**Cons**:
- US stocks only
- Requires account registration
- Some features require funded account

**Usage Example**:
```python
from alpaca.data import StockHistoricalDataClient
from alpaca.data.requests import StockBarsRequest
from alpaca.data.timeframe import TimeFrame
from datetime import datetime, timedelta

# Initialize client (no keys needed for free tier)
client = StockHistoricalDataClient()

# Get historical bars
request = StockBarsRequest(
    symbol_or_symbols=["AAPL", "MSFT"],
    timeframe=TimeFrame.Day,
    start=datetime.now() - timedelta(days=30)
)
bars = client.get_stock_bars(request)
```

**Use Case**: US stock strategy development, paper trading, live trading with same API

---

## 3. Paid Data Sources Detailed

### 3.1 Polygon.io

**Pros**:
- US stock real-time data
- Historical tick data
- WebSocket real-time streaming
- Reasonable pricing (starting $29/mo)

**Cons**:
- US stocks only
- Basic tier no Level-2
- Requires US payment method

**Pricing**:
| Tier | Price | Features |
|------|-------|----------|
| Basic | $29/mo | Delayed data + Historical |
| Developer | $79/mo | Real-time data |
| Premium | $199/mo | Level-2 + Full features |

---

### 3.2 Bloomberg Terminal

**Pros**:
- World's most comprehensive financial data
- Real-time + Historical + News + Research
- Level-2, order book data
- Professional analysis tools

**Cons**:
- Expensive ($2400+/month/terminal)
- Requires dedicated hardware
- Complex API usage

**Use Case**: Institutional investors, professional quant teams

---

### 3.3 Refinitiv (formerly Thomson Reuters)

**Pros**:
- Global market coverage
- High-quality tick data
- Deep historical data
- Easy-to-use Eikon API

**Cons**:
- Expensive pricing
- Complex contracts

**Use Case**: Institutional strategies, HFT

---

## 4. Alternative Data Sources

### 4.1 Nasdaq Data Link (formerly Quandl)

Provides non-traditional financial data:
- Satellite imagery data
- Sentiment analysis data
- Macroeconomic data
- Supply chain data

### 4.2 News and Sentiment Data

| Source | Type | Purpose |
|--------|------|---------|
| NewsAPI | News aggregation | Sentiment analysis |
| Twitter/X API | Social media | Market sentiment |
| Reddit API | Forums | Retail sentiment |
| SEC EDGAR | Regulatory filings | Fundamental analysis |

---

## 5. Data Source Selection Decision Tree

```
What asset class are you trading?
│
├─ Cryptocurrency → Binance API (Free, Real-time)
│
├─ US Stocks
│   ├─ Learning/Backtesting → Yahoo Finance (Free)
│   ├─ Prototyping → Alpaca Markets (Free, Real-time)
│   ├─ Data + Trading Combined → Alpaca Markets (Free)
│   └─ Premium Data Only → Polygon.io ($29+/mo)
│
├─ A-Shares (China)
│   ├─ Learning/Backtesting → Tushare / AKShare (Free)
│   └─ Live Trading → Broker API / Wind
│
└─ Institutional Needs → Bloomberg / Refinitiv
```

---

## 6. Common Pitfalls

### 6.1 Data Quality Issues

- **Missing values**: Gaps from holidays, trading halts
- **Outliers**: Unadjusted splits, dividends
- **Timezone issues**: Different exchange timezones

### 6.2 Survivorship Bias

Free data sources typically only include currently existing stocks, missing delisted stocks data.

### 6.3 Look-Ahead Bias

Some data sources' "historical data" may contain later corrections (like earnings restatements).

---

## 7. Practical Recommendations

1. **Starting out**: Yahoo Finance + Binance API (Free)
2. **Prototyping**: Alpaca Markets (Free real-time data + paper trading)
3. **US Stock Live**: Alpaca Markets (Free) or Polygon.io ($29+/mo for premium features)
4. **Institutional**: Bloomberg / Refinitiv (Comprehensive but expensive)
5. **Always validate**: Compare multiple data sources, check data quality

> **Warning**: IEX Cloud shut down their public API service in August 2024. If you have legacy code using IEX Cloud, migrate to Alpaca Markets or Polygon.io.

---

> **Core Principle**: Data quality > Data quantity. Better to use small amounts of high-quality data than large amounts of problematic data to train models.
