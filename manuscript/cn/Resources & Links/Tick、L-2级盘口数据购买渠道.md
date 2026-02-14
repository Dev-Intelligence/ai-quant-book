# Tick 与 Level-2 盘口数据购买渠道

> Level-2 数据是量化交易中的高级数据源，包含订单簿深度、逐笔成交等信息。本文介绍 L2 数据的获取渠道和相关技术。

---

## 一、Level-2 订单簿数据包含内容

|**类型**|**示例字段**|
|---|---|
|**买卖挂单深度**|买1买10价格和挂单量、卖1卖10|
|**挂单明细（部分平台）**|每个价格档位的排队订单数量、委托笔数|
|**逐笔成交 + 撮合方向**|成交价格、方向（主动买or卖）|
|**盘口异动指标**|内外盘、委比、量比、委差等衍生指标|

**📦 二、Level-2 数据的获取渠道**

**🔶 1.  交易所官方授权渠道（最权威）**

|**交易所**|**获取方式**|**说明**|
|---|---|---|
|**上交所 / 深交所（中国）**|通过 **Level-2 市场数据授权服务商**|实时数据需付费授权，Tick/逐笔成交、买卖队列|
|**CFFEX / 中金所**|用于期货和期权 Level-2 数据|高频期权/套利者必备|
|**港交所（HKEX)**|实时报价、买卖五档以上|需接入“HKEX Orion Market Data平台”|
|**美股（NASDAQ, NYSE）**|NASDAQ TotalView / NYSE OpenBook|提供全深度 Order Book（需订阅）|
|**币圈交易所（如 Binance）**|免费 API 获取 order book snapshot 和 websocket 实时流|一般支持 top 20 ~ full book|

⚠️ 中国大陆 Level-2 实时数据需要经过 **交易所认证的第三方数据服务商授权**，价格较贵，延迟数据可以通过一些渠道获得。

**🔷 2.  主流 Level-2 数据服务商**

|**平台**|**数据类型**|**可用性**|**说明**|
|---|---|---|---|
|**Wind**|A股 Level-2（深度+撮合）|✅（付费）|提供结构化接口与终端可视化|
|**同花顺 iFinD**|Level-2 深度+逐笔|✅（企业级）|部分内容需独立付费授权|
|**聚宽（JoinQuant）**|Level-2 Tick（限部分账户）|❌（付费且受限）|非公开接口，需沟通开通权限|
|**掘金量化（RiceQuant）**|Level-2 实时数据|✅（付费）|支持交易/回测，适合期权交易|
|**巨灵、恒生电子**|专业私募机构使用|✅（嵌入 OMS）|金融机构合作接入，贵|
|**雪球、网易财经等**|仅展示买5卖5|❌（不开放API）|可浏览但不提供全档级数据下载|

**🌐 3.  国外市场 Level-2 来源**

|**市场**|**平台/API**|**延迟**|
|---|---|---|
|美股|NASDAQ TotalView, [Polygon.io](http://Polygon.io), Databento|毫秒级（互联网 API）；共址可达微秒级|
|港股|TickerPlant, Wind, Bloomberg|一般为毫秒至秒级|
|加密货币|Binance, OKX, Coinbase Advanced Trade API|实时免费，最友好|

**💰 三、Level-2 数据费用（粗略）**

|**平台**|**月费用（参考）**|**备注**|
|---|---|---|
|**Wind Level-2（A股）**|￥20,000～50,000+/月|含接口权限和使用许可|
|**掘金 Level-2（实盘）**|￥3,000+/月|开盘前后可订阅数据流|
|**聚宽 Level-2（企业级）**|￥10,000+/月|非个人开发者使用|
|**港交所 L2**|HKD $5,000～10,000/月|通过认证服务商订阅|
|**美股 L2（NASDAQ TotalView）**|$70～150/月|非个人免费|

## **FPGA 与 DMA（市场准入）**

**一、什么是 FPGA？**

**FPGA（Field Programmable Gate Array）**，中文叫“现场可编程门阵列”，是一种**可编程的硬件芯片**，可直接用来执行特定任务，比如**解析行情数据、做决策、发订单**，**无需操作系统或 CPU 参与**。

**在量化/高频交易中的作用：**

|**任务**|**FPGA 可实现的功能**|
|---|---|
|市场数据处理|纳秒级解析 Level-2 数据、构建 Order Book|
|信号决策|直接在硬件中执行策略判断|
|下单撮合|发出交易指令，直接接入交易网关|
|延迟压缩|实现总响应时间 < 1 微秒（远优于传统软件）|

_延迟极低（纳秒级）、编程复杂（需使用 Verilog/VHDL）、极致低延迟做市、ETF套利、期权高频微结构套利。_

**二、什么是直接市场准入（DMA，Direct Market Access）**

**DMA** 是指交易系统 **绕过传统券商前置系统**，直接将交易请求发送到 **交易所撮合系统（或交易网关）** 的技术。

|**类型**|**简介**|
|---|---|
|**DMA（Direct Market Access）**|通过券商的交易通道，跳过 UI 和风控，直连交易所|
|**SA（Sponsored Access）**|券商“担保”的访问，客户完全控制订单系统|
|**ULSA（Ultra Low-latency SA）**|纳秒级延迟，几乎零干预，风险完全自管，需机构级合规背书|

_**高频做市/套利几乎都靠 DMA 才能盈利**_

### **DMA + FPGA 的组合**

> 在 HFT 场景中，
> 
> **DMA 提供“通道权”，FPGA 提供“速度与决策力”**

机构会在 Equinix、@Tokyo 等交易所附近部署：

- **FPGA卡（做 order book + 策略）**
- **DMA线路（走最短光纤/微波）**
- **风控服务（自管或最小化中间环节）**

【CIIS样本L2数据】

[https://www.ciis.com.hk/hongkong/sc/historicaldata1/sampledata/index.shtml](https://www.ciis.com.hk/hongkong/sc/historicaldata1/sampledata/index.shtml)

【上海证券交易所L2】

[https://english.sse.com.cn/markets/dataservice/products/](https://english.sse.com.cn/markets/dataservice/products/)

【深圳证券交易所】

[https://www.szse.cn/market/stock/indicator/index.html](https://www.szse.cn/market/stock/indicator/index.html)

【深交所L2授权】

[http://www.szsi.cn/cpfw/fwsq/hq/yw-2.htm](http://www.szsi.cn/cpfw/fwsq/hq/yw-2.htm)

【香港证券交易所】

[https://www.hkex.com.hk/Services/Market-Data-Services/Real-Time-Data-Services/Overview/Real_time-Datafeeds?sc_lang=en](https://www.hkex.com.hk/Services/Market-Data-Services/Real-Time-Data-Services/Overview/Real_time-Datafeeds?sc_lang=en)

- LSEG、Bloomberg、Refinitiv、东财、华泰证券
- HKEX同址托管：[https://www.hkex.com.hk/Services/Connectivity/Hosting-Services?sc_lang=en](https://www.hkex.com.hk/Services/Connectivity/Hosting-Services?sc_lang=en)