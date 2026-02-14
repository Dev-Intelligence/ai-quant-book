# Broker Trading Platforms and APIs

> This article introduces commonly used broker platforms and API interfaces in quantitative trading, including institutional-grade Prime Brokers and retail-grade trading APIs.

---

## 1. Prime Brokers

### Which Broker Platforms Do Top Funds Use?

Top quantitative hedge funds like Renaissance do not use retail broker platforms (like IBKR retail or Futu). Instead, they execute trades through **Prime Brokers**.

Common Prime Brokers include:

- Goldman Sachs
- Morgan Stanley
- JPMorgan Chase
- **IBKR Prime Services** (more common for small to medium-sized funds; note: this is IBKR's institutional service, different from retail TWS)

These prime brokers provide financing, securities lending, clearing, reporting, and other services, and support low-latency execution for high-frequency/algorithmic trading. Renaissance likely uses multiple prime brokers to diversify risk and customizes connections (co-location, low-latency lines) to exchanges.

---

## 2. Retail-Grade Quantitative APIs

### Does Futu NiuNiu Have This Kind of API Support?

Yes, **Futu NiuNiu provides a powerful OpenAPI that supports programmatic trading and automated order placement**.

**Futu OpenAPI** is designed for quantitative investment, including:

- Market data interface (real-time quotes, K-lines, tick data, etc.)
- Trading interface (place orders, cancel orders, modify orders, query orders/positions, etc.)

Supported markets: Hong Kong stocks, US stocks, A-share Connect, futures, options, etc.

Supported languages: Python, Java, C#, C++, JavaScript, etc. (official SDKs available).

Architecture: Requires running a local/cloud gateway program **Futu OpenD**, then connecting via API. Supports live trading and paper trading.

Many individual quants and developers use it to build algorithmic trading systems (such as dual moving average strategies, high-frequency frameworks). The community has many open-source projects (like the futu-api Python package, futu_algo framework).

**Limitations**: Suitable for retail/individual quantitative trading, not suitable for institutional-level ultra-high-frequency (higher latency, no co-location), but very friendly for medium-low frequency strategies and free/low-cost.

---

## 3. API Latency Analysis

### Prime Broker (e.g., IB) API Latency Analysis

Yes, **Interactive Brokers (IBKR) provides multiple API interfaces**:

- **TWS API**: Connects via TWS or IB Gateway, supports Python/Java/C++, etc.
- **Client Portal API**: RESTful HTTP interface, requires OAuth authentication
- **FIX CTCI**: Institutional-grade FIX protocol interface (separate from TWS, requires separate application)

Note: These three are **separate interfaces**—TWS API itself does not use FIX protocol.

**Is there higher latency?**: Yes, especially for retail traders. IB API has higher latency (relative to institutional-grade). Retail round-trip latency (from signal to execution) is typically **50-100ms** (milliseconds), depending on network, location, and optimization.

- **Retail vs. Institutional Comparison**:
    - **Retail traders**: Higher latency (~70ms+) due to routing multiple hops, no co-location (servers near exchanges). Suitable for medium-low frequency strategies (e.g., day trading), not suitable for HFT (high-frequency, requires <1ms). Can use VPS (like QuantVPS) to optimize to <1ms to IB, but still ~3-5ms to exchanges.
    - **Institutional Prime Broker**: Like Goldman Sachs/JPMorgan, provides low latency (<1ms) for large funds through dedicated lines, co-location, and custom optimization. IB also provides similar for institutional clients, but retail version has limitations.

**Summary**: IB API is sufficient for your simulations (non-HFT), but if pursuing Medallion-style short-term signals, latency is a bottleneck. Recommend starting with low-frequency strategies or using professional VPS to reduce latency.
