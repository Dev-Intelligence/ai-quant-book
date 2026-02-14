# Tick and Level-2 Order Book Data Sources

> Level-2 data is an advanced data source in quantitative trading, containing order book depth, tick-by-tick trades, and more. This article introduces channels for obtaining L2 data and related technologies.

---

## 1. What Level-2 Order Book Data Contains

| **Type** | **Example Fields** |
|---|---|
| **Bid/Ask Depth** | Bid1-Bid10 prices and quantities, Ask1-Ask10 |
| **Order Details (some platforms)** | Queue size and order count at each price level |
| **Tick-by-tick Trades + Aggressor Side** | Trade price, direction (active buy or sell) |
| **Order Book Movement Indicators** | Internal/external volume, bid-ask ratio, volume ratio, bid-ask spread, etc. |

## 2. Level-2 Data Acquisition Channels

### 2.1 Exchange Official Authorization (Most Authoritative)

| **Exchange** | **How to Obtain** | **Notes** |
|---|---|---|
| **SSE / SZSE (China)** | Through **Level-2 market data authorized service providers** | Real-time data requires paid authorization, tick/trade-by-trade, bid-ask queues |
| **CFFEX (China Financial Futures Exchange)** | For futures and options Level-2 data | Essential for HFT options/arbitrage |
| **HKEX (Hong Kong Exchange)** | Real-time quotes, 5+ levels of bid/ask | Requires connection to "HKEX Orion Market Data Platform" |
| **US Stocks (NASDAQ, NYSE)** | NASDAQ TotalView / NYSE OpenBook | Provides full-depth Order Book (subscription required) |
| **Crypto Exchanges (e.g., Binance)** | Free API for order book snapshot and websocket real-time stream | Generally supports top 20 to full book |

Note: In mainland China, Level-2 real-time data requires authorization through **exchange-certified third-party data service providers**, which is relatively expensive. Delayed data can be obtained through some channels.

### 2.2 Major Level-2 Data Service Providers

| **Platform** | **Data Type** | **Availability** | **Notes** |
|---|---|---|---|
| **Wind** | A-share Level-2 (depth + matching) | Paid | Structured interface and terminal visualization |
| **Tonghuashun iFinD** | Level-2 depth + tick-by-tick | Enterprise | Some content requires separate paid authorization |
| **JoinQuant** | Level-2 Tick (limited accounts) | Paid, restricted | Non-public interface, requires permission negotiation |
| **RiceQuant** | Level-2 real-time data | Paid | Supports trading/backtesting, suitable for options trading |
| **Juling, Hundsun** | Professional private fund use | Embedded in OMS | Financial institution partnerships, expensive |
| **Xueqiu, NetEase Finance, etc.** | Display only bid5/ask5 | No API | Viewable but no full-depth data download |

### 2.3 International Market Level-2 Sources

| **Market** | **Platform/API** | **Latency** |
|---|---|---|
| US Stocks | NASDAQ TotalView, [Polygon.io](http://Polygon.io), Databento | Millisecond level (internet API); co-location can achieve microseconds |
| Hong Kong Stocks | TickerPlant, Wind, Bloomberg | Generally millisecond to second level |
| Cryptocurrency | Binance, OKX, Coinbase Advanced Trade API | Real-time free, most accessible |

## 3. Level-2 Data Costs (Approximate)

| **Platform** | **Monthly Cost (Reference)** | **Notes** |
|---|---|---|
| **Wind Level-2 (A-shares)** | CNY 20,000-50,000+/month | Includes API access and usage license |
| **RiceQuant Level-2 (Live trading)** | CNY 3,000+/month | Data stream available before/after market open |
| **JoinQuant Level-2 (Enterprise)** | CNY 10,000+/month | Not for individual developers |
| **HKEX L2** | HKD 5,000-10,000/month | Subscribe through certified service providers |
| **US Stocks L2 (NASDAQ TotalView)** | $70-150/month | Not free for individuals |

## 4. FPGA and DMA (Market Access)

### 4.1 What is FPGA?

**FPGA (Field Programmable Gate Array)** is a **programmable hardware chip** that can directly execute specific tasks, such as **parsing market data, making decisions, and sending orders**, **without OS or CPU involvement**.

**Role in Quantitative/High-Frequency Trading:**

| **Task** | **FPGA Capabilities** |
|---|---|
| Market Data Processing | Nanosecond-level parsing of Level-2 data, Order Book construction |
| Signal Decision | Execute strategy decisions directly in hardware |
| Order Matching | Send trading instructions, connect directly to trading gateway |
| Latency Reduction | Achieve total response time < 1 microsecond (far better than traditional software) |

_Extremely low latency (nanosecond level), complex programming (requires Verilog/VHDL), extreme low-latency market making, ETF arbitrage, options HFT microstructure arbitrage._

### 4.2 What is Direct Market Access (DMA)?

**DMA** refers to technology where trading systems **bypass traditional broker front-end systems** and send trading requests directly to the **exchange matching system (or trading gateway)**.

| **Type** | **Description** |
|---|---|
| **DMA (Direct Market Access)** | Through broker's trading channel, bypassing UI and risk controls, direct to exchange |
| **SA (Sponsored Access)** | Broker "sponsored" access, client fully controls order system |
| **ULSA (Ultra Low-latency SA)** | Nanosecond latency, almost zero intervention, self-managed risk, requires institutional compliance endorsement |

_**HFT market making/arbitrage almost always relies on DMA to be profitable**_

### 4.3 DMA + FPGA Combination

> In HFT scenarios,
>
> **DMA provides "channel access," FPGA provides "speed and decision-making power"**

Institutions deploy at Equinix, @Tokyo, and other exchange-adjacent locations:

- **FPGA cards (for order book + strategy)**
- **DMA lines (shortest fiber/microwave routes)**
- **Risk control services (self-managed or minimized intermediaries)**

## Reference Links

**CIIS Sample L2 Data**
https://www.ciis.com.hk/hongkong/sc/historicaldata1/sampledata/index.shtml

**Shanghai Stock Exchange L2**
https://english.sse.com.cn/markets/dataservice/products/

**Shenzhen Stock Exchange**
https://www.szse.cn/market/stock/indicator/index.html

**SZSE L2 Authorization**
http://www.szsi.cn/cpfw/fwsq/hq/yw-2.htm

**Hong Kong Stock Exchange**
https://www.hkex.com.hk/Services/Market-Data-Services/Real-Time-Data-Services/Overview/Real_time-Datafeeds?sc_lang=en

- LSEG, Bloomberg, Refinitiv, East Money, Huatai Securities
- HKEX Co-location Hosting: https://www.hkex.com.hk/Services/Connectivity/Hosting-Services?sc_lang=en
