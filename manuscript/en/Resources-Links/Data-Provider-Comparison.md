# Data Provider Comparison Report: Barchart, QuoteMedia, Massive (formerly Polygon.io), EODHD, Wind

> **2025 Update**: Polygon.io officially rebranded to **Massive** (massive.com) in October 2025. The original API endpoint api.polygon.io remains functional, but the new endpoint is api.massive.com. The Python SDK has moved to github.com/massive-com/client-python. References to "Polygon" throughout this report refer to Massive (formerly Polygon.io).

## Supported Markets and Asset Classes

The following table summarizes each data provider's support for major markets and asset classes, including US stocks (NYSE/NASDAQ), Hong Kong stocks (HKEX), China A-shares (SSE/SZSE), US futures (CME/CBOT), ICE futures, stock options, and forex:

| **Data Provider** | **US Stocks (NYSE/NASDAQ)** | **HK Stocks (HKEX)** | **China A-shares (SSE/SZSE)** | **CME/CBOT Futures** | **ICE Futures** | **US Options** | **Forex (FX)** |
|---|---|---|---|---|---|---|---|
| **Barchart** | Supported | Not Supported | Not Supported | Supported (primarily US futures) | Supported (delayed) | Supported | Supported |
| **QuoteMedia** | Supported | Supported | Supported | Supported | (Partial support)* | Supported (covers OPRA) | Supported |
| **Polygon** | Supported | Not Supported | Not Supported | Supported | Not Supported | Supported | Supported |
| **EODHD** | Supported | Not Supported | Supported | Not Supported | Not Supported | Supported (US options data only) | Supported |
| **Wind** | Supported | Supported | Supported | Supported | Supported | Supported | Supported |

> Note: QuoteMedia's website doesn't explicitly list ICE futures support, but their broad global coverage may provide some ICE market data through custom services.

**Analysis**: Barchart covers North American and some international stocks and futures, supporting US stocks (NYSE, NASDAQ) and major futures exchanges (CME Group's CME, CBOT, etc.), but doesn't cover mainland China or Hong Kong stock markets. QuoteMedia has the broadest coverage, providing global multi-market stock data including US, Canada, Europe, and Asian markets (supporting HKEX and SSE/SZSE stocks). QuoteMedia also provides multi-asset data including futures and forex, though support for specific exchanges (like ICE futures) may require custom solutions.

Polygon focuses on US markets, covering all US stock exchanges' real-time data, US stock options, and CME Group futures (e.g., CME/CBOT/NYMEX/COMEX), but **does not provide** China or Hong Kong stock market data. EOD Historical Data (EODHD) provides historical and market data for **70+** global exchanges. It covers US stocks and China A-shares (SSE/SZSE) among most markets, but doesn't include Hong Kong stocks; additionally, EODHD focuses on stocks/funds and has no apparent futures data services. Wind covers nearly all major global markets and is the commonly used data terminal in China's financial industry. Wind not only covers China, US, and HK stocks but also encompasses global stocks, bonds, funds, commodities, indices, forex, options, and all other major asset classes. It provides particularly comprehensive support for China markets, with complete A-share and HK stock data, plus futures, options, and forex real-time quotes.

## Data Types and Frequency Support

**Barchart**: Provides full-frequency data from historical to real-time. Its API supports historical daily bars, minute bars, and tick-level data. Barchart OnDemand can return historical time series for stocks, indices, funds, futures, forex, etc., as well as delayed or real-time quotes. Through Barchart OnDemand and Barchart Stream services, you can get real-time streaming quotes and tick-level data. Overall, Barchart's data frequency covers tick, minute, daily through annual levels.

**QuoteMedia**: Provides both real-time streaming data and historical data access modes. QuoteMedia has a **Streaming Data Feed API** for pushing tick-by-tick real-time quotes, including **tick-level** Level-1 and Level-2 quotes. It also has an **OnDemand API** supporting on-demand historical data requests (JSON/XML, etc.). Its historical database covers fine-grained time series data for **global markets**, providing years of historical quotes, trades, and indicator data. In summary, QuoteMedia can obtain real-time quotes, intraday minute data, and long-term historical data, meeting needs from **tick-level** to **daily-level**.

**Polygon**: Focuses on providing **low-latency** real-time and historical data, primarily for US stocks and related markets. Polygon provides tick-by-tick trades and quote streaming (Level-1) as well as aggregated candlestick data for US stocks. Its Stocks API can get real-time quotes, historical **tick data**, company fundamentals, and news. Regarding frequency, Polygon supports tick-by-tick, second aggregates, minute bars, daily bars, etc. (its advanced plans include second aggregates and tick-by-tick data). Notably, Polygon currently **does not provide** US stock Level-2 order book depth (planned for future), but does provide aggregated multi-exchange Level-2 order book data for cryptocurrencies. Overall, Polygon can provide **real-time tick quotes, trade-by-trade data, intraday candlesticks, and years of historical data**, very useful for high-frequency trading and backtesting.

**EODHD**: Features historical and intraday data. Its name "End of Day Historical Data" indicates its strength in multi-year historical daily data, though it has also expanded intraday and real-time capabilities. EODHD supports 30+ years of historical data and same-day delayed/real-time quotes. Specifically, it provides **daily** and **minute-level** data downloads/APIs, and supports US tick-by-tick historical trades (provides "US Ticks API"). EODHD's real-time capability is mainly through **1-minute delayed quote API** and **Websocket real-time subscription**, limited to US stocks, forex, and cryptocurrencies. In terms of frequency, EODHD can meet **daily, minute-level** needs; tick-level data is currently limited to US markets and requires advanced plans. Overall, EODHD is suitable for getting historical and delayed quotes from global markets, with relatively limited support for real-time high-frequency trading.

**Wind**: Provides **full-frequency, all-asset** data support. Wind Financial Terminal data covers **real-time quotes, historical quotes, and all frequency candlesticks**. Wind supports millisecond tick streaming, time-sharing data, daily and longer period data. Its "quantitative trading" function provides **ultra-fast quotes for all global market stocks and assets**, and supports **historical daily, tick, and minute data replay**. This means Wind can not only provide A-share Level-1/Level-2 tick-by-tick real-time quotes but also retrieve historical tick-by-tick trade sequences. Additionally, Wind has extensive macroeconomic and financial data time series. Therefore, in terms of data types, Wind can provide complete sequences from **tick-level real-time quotes** to **multi-year historical data**, with comprehensive support for research and trading.

## Real-time Interface and Order Book Depth (L1/L2) Support

**Level-1 Quotes**: All compared vendors provide Level-1 real-time quote data (i.e., latest quotes/trades for each market). Barchart provides real-time Level-1 quotes through its streaming data service and WebSockets interface. QuoteMedia has a dedicated **Streaming API** supporting tick-by-tick real-time quote streaming. Polygon provides real-time Level-1 quote streaming (WebSocket) for US stocks, options, etc., including tick-by-tick trades and NBBO quotes. EODHD provides Level-1 real-time/delayed data subscription (WebSocket) for US stocks, etc., but limited to fewer markets with limited frequency. Wind Terminal itself provides Level-1 real-time quotes for major markets; through API, you can subscribe to latest quotes for China, US, HK, and other markets.

**Level-2 Order Book Depth**: Support varies significantly among vendors for order book depth data. QuoteMedia and Barchart both explicitly support providing **Level-2 order book depth** data: QuoteMedia's Streaming API allows subscribing to tick-by-tick **Level-2 bid/ask** (by symbol or full exchange subscription); Barchart can also provide exchange depth data through its Stream and OpenFeed services (supporting Level 1 or Level 2 market depth). For example, Barchart can provide US futures Level-2 depth; subscribers pay corresponding exchange fees for full quotes. Wind has advantages for China markets - Wind provides SSE and SZSE **Level-2 10-level quotes** (requires "Level-2 Quotes" subscription), essential data for domestic quantitative trading. Additionally, Wind can provide depth data or order book information for some overseas markets (such as some HK stocks and futures), though specific depth depends on the exchange.

**Polygon** currently **does not provide** US stock Level-2 order book data. Its positioning focuses more on high-speed Level-1 data and tick-by-tick trades. However, Polygon has implemented aggregated order book streaming for **cryptocurrencies**, and its **futures API** roadmap mentions upcoming order book depth support. **EODHD** doesn't provide Level-2 order book data - its WebSocket only pushes latest quotes without bid/ask levels, as EODHD primarily targets small investors and historical data analysis rather than complex real-time order book data.

In summary, **QuoteMedia and Barchart can provide L1 and L2 quotes (via WebSocket, etc.)**, meeting applications requiring order book depth. Wind provides authoritative Level-2 data for China markets and has some depth information capability for other global markets. Polygon and EODHD are relatively lacking in order book support, where Polygon only has L1 but satisfies most US stock trading strategies with its low latency, while EODHD focuses on historical and L1 quotes without covering depth data.

## Data Accuracy, Latency, and Stability

Each vendor claims high standards in data accuracy and timeliness, but with different emphases based on positioning:

- **Barchart**: As a veteran market data provider, Barchart's data comes directly from official exchange feeds with professional-grade accuracy. Its infrastructure is built on cloud using leading technology, emphasizing **reliability and accuracy**. Barchart has data centers in Chicago and other locations directly connecting to exchanges to reduce latency, and supports co-location at Equinix data centers for raw low-latency data. Their streaming quotes use proprietary compression optimization technology, reducing bandwidth while **ensuring tick-by-tick precision**. In practice, Barchart's data services power many financial websites and trading software, demonstrating **high stability** and enterprise-level SLA over time. Additionally, Barchart provides **exchange-authorized raw quotes** (including CTA/UTP quotes), so key metrics (volume, tick-by-tick trades, etc.) match official data.

- **QuoteMedia**: QuoteMedia serves large stock exchanges and banks with extremely high data quality requirements. Its global data is delivered through distributed cloud architecture. QuoteMedia contracts directly with exchanges and is a partner data provider for NASDAQ, TMX, etc. This means its data accuracy and timeliness undergo rigorous verification meeting exchange standards. While public technical details are limited, its clients (like NASDAQ Stock Exchange website, London Stock Exchange Group, etc.) validate QuoteMedia data's **reliability and professional standards**. In terms of latency, QuoteMedia provides **real-time streaming** services meeting financial terminal and trading platform needs; for global market data, transmission is optimized through cloud infrastructure to reduce latency across different regions. Overall, QuoteMedia's data accuracy and stability have a good reputation in the industry, proven through use by many mainstream financial institutions.

- **Polygon**: Polygon emphasizes **low-latency and high-throughput** US stock data services. Its main servers are deployed in New Jersey Equinix data centers, adjacent to exchanges and receiving quotes through dedicated lines. Polygon combines direct exchange connections with SIP consolidated data to ensure providing **accurate and timely** market data. Since Polygon primarily serves quantitative developers and traders, it maintains strict data integrity standards, claiming to cover **every trade, quote, and market event**. In testing, Polygon has extremely low latency for typical US stock trading scenarios, with data synced and updated with exchanges. Additionally, Polygon's services have been adopted by many quantitative platforms in recent years (like Alpaca broker previously using Polygon data), with community feedback showing **good data completeness**. Note that Polygon targets small to medium quantitative teams and individuals - while not a traditional "big vendor," it still has merit in reliability through modern technology (using redundant data centers for service continuity). Overall, Polygon's data is **accurate and fast** within US stocks, sufficient for most quantitative trading strategy needs.

- **EODHD**: EOD Historical Data focuses on historical and batch data correctness. For historical closing prices, financial data, etc., EODHD has broad coverage and timely updates, with their website stating data comes from global exchanges and information providers. However, for real-time data, EODHD has some trade-offs in precision and speed: it doesn't necessarily provide millisecond-level live quotes; the real-time portion is mainly achieved through aggregated and delayed data, with the website noting "data provided through this site and API may not be real-time or accurate." Therefore, EODHD is suitable as a data source for **research and low-frequency trading**, excelling in comprehensive coverage and low cost, but not suitable for scenarios with extremely high **ultra-low latency and tick accuracy** requirements (like high-frequency trading). Regarding stability, EODHD's service is relatively simple and direct (mainly REST interface), generally maintaining stable responses, with paid user daily 100,000 API call quota indicating the system can handle high request volumes. Overall, EODHD's data accuracy is very reliable at the **daily level**; for real-time details it may not match other professional providers, but still meets most ordinary quantitative analysis needs.

- **Wind**: Wind, as "China's Bloomberg," enjoys a strong reputation for data accuracy and coverage depth. Wind's data comes directly from official channels and exchange releases with authority: for example, its A-share data syncs with SSE/SZSE, financial data comes from listed company announcements, macro data comes from official statistics. Wind has a large data review team with strict quality control processes ensuring data is **precise and error-free**. Regarding latency, when Wind Financial Terminal is used for professional investment, **real-time quotes are nearly synchronized with broker terminals** - especially SSE/SZSE Level-2 and other high-frequency data, available at millisecond level through Wind. While Wind primarily targets research and decision support and its latency may be slightly higher than direct exchange connections, it's negligible for most non-high-speed programmatic trading. For stability, Wind has years of experience serving large financial institutions, with its terminal and API services maintaining high availability over long periods (some institutional users rely solely on Wind data as their primary source, demonstrating its reliability). In summary, Wind's data is **accurate and authoritative**, widely viewed as benchmark in the Chinese financial community. Wind service is stable, but due to its closed software architecture, compared to other open APIs it requires use in specific environments (like installing Wind terminal or Wind API library), which somewhat limits flexibility but ensures stable and secure data transmission.

**Mainstream Quantitative Company Adoption**: QuoteMedia and Wind have been adopted by many mainstream institutions in their respective domains. QuoteMedia provides market data services for large trading platforms like **NASDAQ, TMX Group, London Stock Exchange Group**, and is used by multiple **banks and asset management institutions**. Wind is almost standard equipment for Chinese institutional investors - **public funds, brokerages, proprietary trading, and quantitative private funds** commonly use Wind terminal for research data, with its high price not deterring broad adoption (reportedly Wind terminal single-seat annual fee is about CNY 39,800, yet many institutions still purchase hundreds of accounts for employees). Barchart's data services are also embedded in some trading software and information platforms, like AgenaTrader, Ensign, and other trading systems having integrations. Polygon, being relatively newer, is mainly active in the quantitative developer community, with many US stock quantitative enthusiasts and small trading teams obtaining data through Polygon, enjoying good reputation in programmer circles. Additionally, some emerging fintech companies quickly build quote functionality through Polygon (like early Alpaca platform specifying Polygon as US stock data source). EODHD is commonly seen in quantitative forums and developer communities, used to supplement data for niche markets or as data solution for cost-sensitive projects; major large quantitative funds rarely use EODHD alone, but some financial terminal software may indirectly reference its data.

## API Performance and Concurrency Limits

For requirements supporting **1000 concurrent users**, each provider's capabilities and strategies differ:

- **Barchart**: Adopts **usage-based elastic architecture**. Barchart OnDemand cloud service can dynamically scale based on request volume, with officials stating "can scale up services in seconds." This means Barchart can support high concurrency and large traffic scenarios. For example, its API can return batch quotes on demand, with each request containing multiple symbols (some interfaces like getEquityOptions support up to 5 symbols per query). In practice, Barchart serves some high-concurrency websites (like financial portals), demonstrating enterprise-level capability in **concurrent connections** and **throughput**. For 1000 users simultaneously subscribing to real-time data, Barchart's WebSocket/Socket.IO interface and OpenFeed can both support, plus they offer UDP/TCP dedicated line solutions to improve concurrency performance. Therefore, with proper architecture, Barchart can well meet thousand-user level concurrent data access needs.

- **QuoteMedia**: As an institutional data provider, QuoteMedia is inherently designed for high-concurrency environments. Its clients include large broker and bank websites where concurrent users often number in tens of thousands. For example, Bank of Montreal (BMO) uses QuoteMedia's data services. QuoteMedia uses distributed cloud and CDN for data delivery and provides **Streaming API**, allowing each end user to establish independent streaming connections for real-time pushes. Since QuoteMedia typically charges by licensed user count, it technically doesn't limit API call frequency but ensures authorized users can smoothly access data. Therefore, in **1000 concurrent user** scenarios, as long as sufficient user licenses are purchased, QuoteMedia's infrastructure can support (its service powering NASDAQ official website proves this). Specific API rate limits aren't public, but QuoteMedia offers **full exchange subscription** mode, allowing one subscription for entire exchange streaming, which is very efficient for large concurrent broadcast distribution. In summary, QuoteMedia can support high concurrent access through proprietary solutions; its **enterprise service** positioning determines concurrency capability won't be a bottleneck.

- **Polygon**: Developer-facing Polygon uses **subscription tier** form to control usage. Its basic tier has 5 calls per minute limit (free version); paid **Starter/Developer** and above tiers have **unlimited API calls**. Advanced plans allow users to request as much data as possible, with no official hard concurrent connection limits. However, for individual developer accounts, Polygon may reasonably limit WebSocket connection counts within bounds (e.g., simultaneous connections per API key). For **1000 users real-time subscribing** scenario, if using WebSocket push, Polygon can have each user connect and subscribe to their needed tickers individually. Polygon's Advanced plan already includes **unlimited frequency calls and WebSocket**. If 1000 users each subscribe to different symbols, its servers need to support 1000 sessions - this is a **business-level** requirement, and Polygon has custom plans for enterprises. In fact, Polygon provides **Business plans** for larger companies with negotiable higher concurrency and dedicated support. Therefore, for thousand-user scale simultaneously getting US stock data, Polygon needs enterprise plans or cluster architecture, but technically its cloud service can **horizontally scale** to meet high concurrency (some brokers have used Polygon to serve thousands of client quotes). Note that compared to institutional providers, Polygon's stability and latency under high concurrency needs thorough testing, but overall its **unlimited calls** policy indicates the system has considerable throughput headroom.

- **EODHD**: Uses explicit **quota mechanism**. Each paid account **defaults to maximum 100,000 API calls per day**, with per-minute request limit around **1000 requests**. This means if 1000 users are online simultaneously, total allowed requests per second is 1617, averaging only 1 call per user per minute - insufficient for high-frequency scenarios. If each user needs more frequent data updates, consider expanding with multiple accounts or subscribing to higher EODHD tiers. EODHD also provides Websocket real-time subscription, but mainly for **few ticker** scenarios, not designed as large-scale distribution architecture. Overall, EODHD suits **medium-low concurrency** use: like dozens of researchers querying historical data, or hundreds of terminal users occasionally refreshing quotes. In extreme **1000 users simultaneously refreshing real-time quotes** scenario, EODHD may struggle, requiring application-layer caching, rate limiting, or combining with other data sources. So EODHD has relatively limited concurrency support, more suited for individuals and small teams than large platforms.

- **Wind**: Wind's API is typically provided in **terminal authorization** form, with each terminal account supporting certain request concurrency. Wind doesn't publicly disclose specific API rate limit values, but from user experience, its API (like Wind Python interface) can **batch extract large amounts of data** quite stably. Wind is more commonly used for **batch downloading research data** and **few real-time subscriptions** (like monitoring watchlists), not designed for internet-scale mass concurrent distribution. To distribute Wind data to 1000 terminal users, the typical approach is for institutions to purchase Wind's data wholesale rights and set up local data forwarding platforms. However, Wind also has enterprise-level data interface services providing **customized data solutions for enterprises** (through Wind Financial Data Center API services). Under such arrangements, Wind can negotiate concurrency capability per enterprise needs. Given Wind terminal single account's high cost (nearly CNY 40,000/year), one generally wouldn't have one account per person to serve thousands, but rather institutions centrally obtain then distribute internally. Therefore, for **1000 simultaneous online users**, directly using Wind terminal accounts for individual service isn't realistic, but Wind can serve as backend data source for enterprises to distribute themselves. Simply put, Wind data itself is **stable and responsive**, but its authorization model is more suitable for **internal use** than public concurrent access. If a quantitative platform wants to leverage Wind's data coverage advantages to serve many users, they need to establish data distribution agreements with Wind or use Wind's data interface products for batch data, then push to users themselves.

## Pricing Plans and Cost Comparison

Pricing varies greatly, from tens of dollars for developer packages to tens of thousands for institutional terminals:

- **Barchart**: Uses flexible **usage-based pricing** model. Barchart OnDemand doesn't publish fixed package prices but charges based on API call count and data types. For small users, Barchart has offered starter plans around $49/month (to attract former free users to convert). Enterprise clients can negotiate custom packages with Barchart, paying as needed. Overall, Barchart pricing is **"pay for what you use"**, very economical for low-frequency projects, while large-scale use costs depend on data volume. Note that **exchange fees** for real-time quotes typically need additional payment, like NYSE/NASDAQ professional user monthly fees, CME and other futures real-time also have corresponding fees. Barchart website lists detailed resale and terminal pricing standards for on-demand selection.

- **QuoteMedia**: Takes **enterprise licensing** route without public API pricing. Since its main clients are large institutions, QuoteMedia typically provides **custom quotes** based on data scope and user count. From their financials, 2024 QuoteMedia annual revenue was ~$18.74 million (clients mostly B2B services), suggesting higher service unit prices. Typically, purchasing QuoteMedia data requires contacting their sales team for solutions and quotes. For individual investors, QuoteMedia has **Quotestream** terminal (from $17.95/month), but API isn't offered in cheap retail form. For reference, similar competitor Xignite charges higher by market, with QuoteMedia's all-market coverage solution estimated at tens of thousands of dollars annually. If only specific market data is needed, modular purchase may be possible (like US stocks only, funds only, etc.). In summary, QuoteMedia positions high-end, its **pricing system is expensive** but also **one price includes comprehensive service** (like global quotes + news + financials integration). Public information shows some large clients pay six-figure dollars annually to QuoteMedia, so small-medium quantitative teams need to carefully consider budget, or use their data through third-party platforms.

- **Polygon**: Offers transparent **subscription-based** pricing, developer-friendly. Polygon separates data subscriptions by asset class - **Stocks (US stocks) advanced plan $199/month** includes all US stock real-time and historical data; if needing US options, forex, etc., can separately subscribe to corresponding packages or combine. According to Alpaca's analysis, US stocks + options + forex Polygon advanced combination totals around $449/month. For those not needing real-time, there are also $79/month (10 years history + delayed quotes) and $29/month (5 years history + delayed) mid-tier packages. Additionally, Polygon provides free basic version (5 calls per minute limit) for testing. Note above prices are for non-professional individual users; if classified as professional institutional user, exchange quote licensing calculates separately - Polygon distinguishes Non-Pro and Pro users at purchase, with Pro users possibly needing additional exchange fees or using **enterprise plan**. Overall, Polygon covers full US stock data at **tens to hundreds of dollars monthly**, much cheaper than traditional providers, very suitable for budget-limited quantitative teams or individuals. Its highest-end all-market package at $449/month is still far below Wind and others' often tens of thousands in fees. Of course, Polygon's low price premise is limitation to US stocks and few markets; if needing global stocks, other solutions are needed.

- **EODHD**: Wins on **low pricing**. EOD Historical Data's subscription prices are public and quite affordable. For example, their "All World Extended" package ($29.99/month) gets daily + minute data and real-time/delayed quotes for 70+ global exchanges. Basic "All World" daily package is even lower at $19.99/month. Even the highest full-featured All-In-One package is only $99.99/month (including global historical quotes, fund financials, calendar news, and all other data services). This pricing is very friendly for individuals and small teams. After payment, you get 100,000 daily API call quota and 1000 per minute rate limit, plenty for normal use. Additionally note EODHD's real-time WebSocket service (US stocks, crypto, and Forex) requires Realtime API subscription, possibly included in package or requiring additional purchase, but overall costs remain at tens of dollars monthly level. In comparison, **EODHD's pricing is significantly lower than other providers**, even cheaper than Polygon, though functionality is also somewhat limited. Therefore EODHD is very suitable as data source for research and backtesting, an extremely cost-effective choice for teams needing multi-market data on tight budgets.

- **Wind**: Wind Financial Terminal is known for **high-price full-function**. Wind uses annual fee system, **single terminal account priced at approximately CNY 39,800/year** (about $5,700). Bulk purchases get discounts, like purchasing 200 accounts averages about CNY 24,540/year per account. This fee includes all Wind terminal functions and massive data. Wind API is generally provided as terminal add-on service, with blog sources citing API interface authorization at about **CNY 7-8,000/year** (possibly for activating quantitative API on existing terminal basis). Overall, compared to foreign data sources, Wind pricing is **very expensive**, but in exchange provides **almost all-inclusive** data content and service support. Wind also has enterprise data batch services and professional database (like Wind Financial Database) authorization, with costs potentially higher. If institutions only need China market data, Wind though expensive is often accepted due to being irreplaceable (its A-share depth data and massive economic database are unique). But for quantitative teams mainly doing overseas markets, Wind's high cost is often hard to justify. Simple comparison: Polygon annual all data about $449*12≈$5388, nearly one Wind account price, but Polygon coverage is far less than Wind. If needing Wind-level all-market data while saving costs, may need **multiple low-cost source combinations as replacement**. Therefore Wind's pricing strategy targets more **large financial institutions**, supporting high prices with data quality advantages - a "one-stop" luxury solution when cost sensitivity isn't high.

Summary pricing table:

| **Provider** | **Individual/Small Team Cost** | **Enterprise/Professional Cost** |
|---|---|---|
| **Barchart** | From $49/month (small-scale promo); flexible usage-based scaling | No public standard price, contact for quote (can be small or large); real-time quotes have additional exchange monthly fees |
| **QuoteMedia** | No individual API retail; Quotestream terminal from $18/month (view only) | High-end custom, tens of thousands dollars annually; provides comprehensive solutions for exchanges/banks |
| **Massive (formerly Polygon)** | $0-$199/month (US stocks basic to advanced); all-inclusive ~$449/month | Enterprise plans custom (including professional user authorization); pricing negotiable |
| **EODHD** | $19.99-$29.99/month (global daily/minute); All-In-One $99/month | Same enterprise pricing (can share API among multiple users); bulk discount available for large data needs |
| **Wind** | CNY 39,800/year/terminal (API included ~CNY 7,000) | Bulk terminals get discounts (millions CNY level); enterprise data solutions priced higher, needs negotiation |

(Above prices are compiled from public sources for reference only; actual costs subject to official latest quotes.)

## Data Coverage Gaps and Combination Solutions

Since no single data source can cover everything, practical applications often require **combining multiple vendors** for complete data coverage. Here are suggested combinations for addressing some vendors' coverage gaps:

- **Barchart's Asia-Pacific Gap**: Barchart excels at US stocks and futures but doesn't include China A-shares or HK stocks. If needing Asian stocks coverage, consider combining Barchart with **QuoteMedia**: the latter supplements HKEX and SSE/SZSE quotes. This way US-Europe markets get low-latency data from Barchart, Asian markets get comprehensive coverage from QuoteMedia. With limited budget, can also use **Barchart + EODHD** combination: Barchart provides real-time US stocks and futures, EODHD provides daily/minute data for SSE/SZSE and other emerging markets. Only remaining need is HK stock data, can find free sources or small providers to supplement HK quotes.

- **QuoteMedia's Futures Detail Gap**: QuoteMedia covers almost all aspects of stock markets, but public information on commodity futures is sparse, particularly unclear ICE contract support. If needing ICE energy, soft commodities and other futures depth data, consider **QuoteMedia + Barchart** combination. Barchart has mature data feeds for CME, ICE and other futures; QuoteMedia continues providing global stocks and fundamental information. This combination suits institutions needing "bundled global stocks + futures": use QuoteMedia as primary stock and macro data provider, Barchart supplements commodity futures and options quotes.

- **Polygon's International Market Gap**: Polygon's advantage is US stock high-frequency data but doesn't include Asia-Pacific or European stocks. An economical combination is **Polygon + EODHD**: Polygon handles US stock real-time and options high-speed data, EODHD provides historical quotes and intraday data for other global markets. This way most non-US data needs can be met by EODHD, with US stocks and options real-time trading handled by Polygon. However this combination still lacks HK stock real-time data, can separately purchase HKEX data products or use Wind/QuoteMedia specifically for HK stocks. With ample budget, can also **Polygon + QuoteMedia** combination: Polygon handles US stock high-speed quotes, QuoteMedia captures all other market real-time data. This ensures global coverage while US portion maintains low-latency advantage, but cost also approaches double.

- **EODHD's Real-time Weakness**: EODHD though broad in coverage has limited real-time capability, suitable for complementing with **Polygon or Barchart**. Like **EODHD + Polygon**: EODHD supplies global historical and intraday data, Polygon provides US stock real-time streaming. For those wanting A-share depth data, can add Wind. This way three combined: Polygon handles US ticks, Wind handles A-share Level2, EODHD handles other market dailies. When not needing Wind-level depth, can use QuoteMedia to replace Wind for A-share quotes (slightly higher latency but lower cost than Wind). Overall principle: use EODHD's low price to get **broad historical data**, then stack one strong real-time provider to cover **key market real-time quotes**.

- **Wind's International Real-time Weakness**: Wind excels at Chinese market and macro data, but may not be best choice for covering US-Europe high-frequency trading. In such cases, can use **Wind + Polygon/Barchart** combination: Wind provides comprehensive China market data and global fundamentals, Polygon or Barchart provides US-Europe market real-time quotes. For example, a quantitative fund focused on China-US arbitrage can use Wind for A-share quotes and economic data, Polygon for US high-speed quotes, achieving optimal data sources for both markets. Additionally, Wind lacks cryptocurrency data, while Polygon has this API; if strategy involves digital assets, Wind and Polygon combination can also complement each other. Note Wind data cannot be externally distributed, so Wind typically only used internally, with US-Europe quotes obtained through another provider usable for external products.

**Final Recommendation**: If pursuing "one-stop coverage" with ample budget, **QuoteMedia** is preferred - it supports comprehensive data types across global major stock markets, futures, and forex, provides real-time and historical data, and has complete Level-1/2 quotes and information services, meeting most quantitative research and trading needs. For institutions **primarily focused on China market**, **Wind** is indispensable - it's irreplaceable in A-share depth data and macro research, while also covering overseas data as supplement. Both are high-price high-quality solutions.

With limited budget seeking best **value**, consider **Polygon + EODHD** combination solution: Polygon handles US stock and options real-time high-frequency data and exchange-level accuracy, EODHD provides historical and intraday quotes for most global markets at extremely low cost. Then as needed, specifically supplement parts not covered by Polygon + EODHD (like HK stock real-time or A-share Level2), by adding third parties (like subscribing only to HKEX data or purchasing Wind's single item data). Under this plan, US stock strategies get high-quality data, cross-market macro strategies also have broad data support, overall spending far below purchasing one full-coverage provider.

In summary, choosing data services requires balancing **coverage breadth**, **professional depth**, and **budget**. **QuoteMedia** and **Wind** provide most comprehensive data coverage, suitable for institutions requiring integrated solutions; **Barchart** and **Polygon** cultivate deeply in North American markets, suitable for quantitative trading focused on US stocks and futures; **EODHD** fills niche market and historical data needs with ultra-high value. In practice, based on your strategy's geographic distribution and frequency requirements, combine above services to achieve **all-market, all-asset** data support at reasonable cost. Through clever combinations, users can achieve "no data blind spots" - like **"US high-speed + global multi-market coverage"** or **"China-US market refined + other markets overview"** modes, optimizing input-output ratio while meeting business needs. Official documentation and customer cases from each provider show they've all been successfully integrated into quantitative trading workflows - choose appropriate solutions based on this report's analysis.

---

## Institutional Direct Connections: Exchange Direct Feeds

Large market makers and hedge funds (like Citadel, Jane Street) often **don't go through third parties**, instead subscribing directly to raw quotes from exchanges or data distribution centers (CTP/SIP/OPRA/MDP) for lowest end-to-end latency and fullest depth levels. Typical approaches include:

| **Asset Class** | **Exchange/Platform** | **Native Data Interface** | **Notes** |
|---|---|---|---|
| **US Stock L1/L2** | NASDAQ TotalView-ITCH, NYSE OpenBook, CBOE BYX/BZX | ITCH, OUCH, OUCH-I, SIP A/B | Supports tick-by-tick (ITCH) + multi-level bid/ask (ITCH-Depth or OpenBook) |
| **US Stock Options** | OPRA (Options Price Reporting Authority) | OPRA Feed | Standardized centralized push of all options order book and trades |
| **US Futures** | CME MDP 3.0, CBOT/NYMEX/COMEX via CME | MDP 3.0 | Tick-level, bid/ask levels, and full market depth |
| **ICE Futures** | ICE MDP | ICE MDP Feed | ICE's energy, soft commodities, and financial futures depth quotes |
| **European Futures/Stocks** | Eurex T7 Feed, Euronext Optiq/ITCH | T7 Market Data | European major derivatives and stock market depth |
| **HK Stocks** | HKEX Market Data | HKEX MDS (Level-2) | HKEX 10-level quotes + tick-by-tick |
| **A-shares** | SSE Level-2, SZSE Level-2 | CTP Level-2 | Domestic "Level-2 10-level + tick-by-tick" |
| **Forex (FX)** | EBS Direct, Refinitiv FX | EBS Adapter / RFA+ | Global major currency pairs tick-by-tick and depth quotes |

**Characteristics**

- **Lowest latency**: Servers stationed in exchange data centers (Equinix NY4, CHI, LN1, HK1, SH1, etc.), direct dedicated lines
- **Fullest depth**: Native multi-level order book + tick-by-tick trades
- **High cost and barriers**: Requires exchange membership or distributor authorization, plus co-location/dedicated lines, hardware and network operations

---

## Low-Latency Relay/Aggregators: Ultra-Low-Latency Redistributors

Without conditions for self-building direct connections, another industry standard solution is using professional "low-latency relay providers" - they've already established presence in major exchange data centers, creating cloud or private API/WebSocket services where one connection covers multiple markets with unified depth data subscription:

| **Provider** | **Coverage** | **Main Products/Services** | **Typical Clients and Advantages** |
|---|---|---|---|
| **QuantHouse** | Global stocks, options, futures, forex, crypto | KH API (REST/WebSocket), Co-Location | Direct connections in CME, ICE, LSE data centers; provides multi-level depth and tick-by-tick; used by many market makers and quantitative private funds |
| **Activ Financial** | US stocks, futures, forex, fixed income, derivatives | ActivFeed / Gold | Common "second choice" low-latency source for Bloomberg, Refinitiv clients; global coverage |
| **OneMarketData** | Global stocks, futures, options, forex, ETFs, bonds | MarketMap, LightSpeed | Enterprise-grade high-availability market data bus; used by Morgan Stanley and other major banks |
| **Exegy** | North America, US options, futures, funds | OneTick / Exegy Feed Engine | Built-in time series database + ultra-low latency push; used by many HFT, market makers |
| **DXfeed** | Global stocks, futures, options, forex | DX API (REST/WebSocket) | High throughput, low jitter; customizable data pipelines; validated by some CTAs, Archer Funds teams |
| **Rithmic** | CME/ICE futures, options | RTS API | Specialized futures data |

**Characteristics**

- **Multi-market integration**: One connection gets US stocks + Asia-Pacific + Europe + futures + FX, etc.
- **Low-latency cloudified**: Some offer private cloud or dedicated line options
- **Strong scalability**: No need to maintain own data center, enable market modules as needed
- **Cost threshold between "direct connection" and "lightweight API"**: Generally $50,000-hundreds of thousands annually, includes multi-level depth and tick-by-tick

---

### Selection Recommendations

1. **Extreme low-latency + full depth**: If requirements for latency and depth are extremely high (sub-millisecond / multi-level Level-2 + tick-by-tick) with membership or high budget, self-build direct connections (Exchange Direct Feeds)
2. **Quick deployment + multi-market coverage**: If not wanting to build co-location, recommend using relay providers like **QuantHouse** or **Activ Financial** - get direct-connection level depth while one-stop covering major global exchanges
3. **Cloud developer friendly**: If team prefers cloud-native, flexible deployment, can also consider more modern "cloud-native" relay providers (like DXfeed's WebSocket/API services), but verify latency and stability beforehand

---

> Institutional Examples

- **Citadel / Jane Street / Two Sigma** and other top market makers all deploy their own gateways in major exchange data centers, directly connecting native ITCH/MDP/OPRA
- **Small-medium hedge funds** often use **QuantHouse, Activ Financial, OneMarketData** and other commercial relays, one connection meeting all-market L1/L2 needs
- **Cloud-native quantitative platforms** (like Alpaca, Wealthfront, etc.) are also starting to deploy low-latency Redistributor services to reduce operations costs and iterate quickly

Through above two solutions, you can make optimal choices based on **latency sensitivity, budget, and operations capability**:

- Self-built direct connection is **the ceiling for performance and depth**
- While "low-latency relay" is **the best balance of value and flexibility**

---

## Other Notable Data Providers

### Databento

**Databento** is a next-generation market data provider founded in 2021, focused on providing institutional-grade data services for quantitative teams. It has rapidly gained traction in the HFT and quant community, and received a strategic investment in 2025, further solidifying its market position.

**Features**:
- **Modern API**: REST + WebSocket, Python/Rust SDK, excellent developer experience
- **Transparent Pricing**: Historical data pay-per-volume, real-time data subscription-based
- **Low Latency**: Provides exchange-direct-level historical data (ITCH, OPRA, MDP raw formats)
- **Coverage**: US equities (all exchanges), options (OPRA), futures (CME/ICE), forex

**Major Product Updates (2025)**:
- **Databento US Equities**: Combines 15 US equity exchanges + 30 ATSes into a unified product, providing a complete market view
- **OPRA Standard Plan**: $199/month, with new consolidated quote schemas (CMBP-1, TCBBO)
- **Live Data Pricing Shift**: Starting 2025, all live data moved to subscription-only (usage-based pricing discontinued) — CME (April), ICE (February), OPRA (June) transitioned sequentially

**Pricing (2025-2026)**:
- Historical data: $1-5/GB (varies by dataset); Standard plan $199/month includes 7 years OHLCV + 12 months L0/L1 history
- Real-time data: $199-499/month and up (varies by market and depth, subscription-only)
- Suitable for small-to-medium quant teams and individual developers

**Website**: [databento.com](https://databento.com)

---

### Alpha Vantage

**Alpha Vantage** is a free/low-cost data API for individual developers and small teams, one of the most commonly used data sources for quant beginners.

**Features**:
- **Free Tier**: 25 API calls per day (sufficient for learning and testing)
- **Coverage**: US stocks, forex, cryptocurrencies, technical indicators, fundamental data
- **Easy to Use**: REST API, no complex authentication, JSON/CSV format
- **Limitations**: No Level-2 data, strict free tier limits, 15-minute delayed data

**Pricing (2025-2026)**:
- Free: 25 calls/day
- Premium: $49.99/month (75 calls/minute)
- Premium+: $249.99/month (1200 calls/minute)

**Use Cases**: Learning, backtesting prototypes, low-frequency strategy development

**Website**: [alphavantage.co](https://www.alphavantage.co)

---

### Tiingo

**Tiingo** is another cost-effective data provider with some advantages in non-US market coverage.

**Features**:
- **Free Tier**: 500 API calls per month
- **IEX Real-time Data**: Provides free real-time data from IEX exchange
- **News API**: Provides financial news and sentiment analysis data
- **Cryptocurrency**: Supports major cryptocurrency historical and real-time data

**Pricing**: $10-30/month and up

**Website**: [tiingo.com](https://www.tiingo.com)

---

> **2024 Update**: IEX Cloud shut down its data services in August 2024. Former IEX Cloud users should consider migrating to Massive (formerly Polygon), Databento, or Tiingo.

---

## Key Industry Protocols and Systems

### CTP (Comprehensive Transaction Platform)

CTP is a comprehensive trading platform developed by **Shanghai Futures Information Technology Co., Ltd.**, widely adopted by major Chinese futures exchanges (SHFE, DCE, ZCE, CFFEX, etc.). It provides a C++ based client interface and is the de facto standard for domestic futures and options quantitative trading. CTP client allows traders to connect to futures company counter systems for order placement, cancellation, position queries, and account fund queries.

### SIP (Securities Information Processor)

SIP is critical infrastructure for US securities markets, responsible for processing and distributing trading and quote information from US trading venues. SIP's main purpose is processing, consolidating, and distributing quote and trade data from different US securities exchanges and market centers. In quantitative trading, SIP provides a unified source of market data, enabling traders to get consolidated quote data, especially the US National Best Bid and Offer (NBBO).

### OPRA (Options Price Reporting Authority)

OPRA is the SIP specifically handling options market data. OPRA is responsible for disseminating consolidated latest sales and quote information from national securities exchanges that have received SEC approval to provide exchange-traded securities options markets. It's the exclusive SIP regulated under Regulation NMS, responsible for distributing regulatory market data across all US equity options exchanges. For quantitative traders, OPRA provides critical options market data including latest trades, quotes, and other information.

### MDP (Market Data Platform)

MDP is CME Group's (Chicago Mercantile Exchange) market data platform. It provides a dual-feed, user datagram protocol multicast architecture, allowing direct access to CME Group futures and options market data. CME's MDP 3.0 is based on FIX 5.0 SP2 message specification using Simple Binary Encoding (SBE) technology, providing low-latency market data for quantitative traders.

These systems and protocols play important roles in quantitative trading, providing execution channels and market data sources for trading strategies, forming the foundational components for building automated trading systems.

## References

Barchart OnDemand Documentation - _"Equities data is available for the following exchanges: ... NASDAQ, ICE/NYSE..."_

QuoteMedia Supported Exchange List - _Confirms support for Shanghai Stock Exchange (XSHG) and Shenzhen Stock Exchange (XSHE) stocks._

Barchart Stream Official Introduction - _Mentions ability to provide Level 1 or Level 2 depth streaming data via dedicated line or internet._

QuoteMedia Data Feeds - _"Access real-time streaming tick-by-tick Level 1 and Level 2 quotes... Websocket API"_

Polygon Stocks API Overview - _"Provides US stock full-market real-time and historical data, covering 19 major exchanges..."_

Polygon FAQ - _Explicitly states currently does not provide US stock Level 2 market depth data._

EODHD Supported Exchange List - _States support for 70+ exchanges' historical data and fundamentals._

Wind Official Introduction - _Emphasizes Wind API access to **all global market stocks, bonds, funds, commodities, indices, forex, options** real-time quotes, and supports historical daily, tick, minute data replay._

Barchart OnDemand - _"Our service pricing is based on usage... scale up in seconds."_ (Usage-based elastic pricing)

QuoteMedia Press Release - _Lists QuoteMedia clients: Nasdaq Stock Exchange, TMX Group (TSX), LSE Group, FIS, JPMorgan Chase, etc._

Alpaca Comparison Article - _Gives Polygon advanced plan combination price: stocks + options + forex total $449/month._

EODHD API Documentation - _"Paid plans default to 100,000 API calls per day limit."_

Sina Finance - _Wind terminal standard price: single account CNY 39,800 annually, 200 account bulk discount to CNY 24,540/year/account._
