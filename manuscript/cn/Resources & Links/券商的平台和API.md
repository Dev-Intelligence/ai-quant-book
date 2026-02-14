# 券商交易平台与 API

> 本文介绍量化交易中常用的券商平台和 API 接口，包括机构级 Prime Broker 和零售级交易 API。

---

## 一、Prime Broker（主力经纪商）

### 通过哪个券商的平台？

Renaissance 等顶级量化对冲基金不使用零售券商平台（如 IBKR 零售版或富途），而是通过 **Prime Brokers（主力经纪商）** 执行交易。

常见 Prime Brokers 包括：

- Goldman Sachs
- Morgan Stanley
- JPMorgan Chase
- **IBKR Prime Services**（针对中小型基金，注意：这是 IBKR 的机构服务，与零售版 TWS 不同）

这些prime brokers提供融资、证券借贷、清算、报告等服务，并支持高频/算法交易的低延迟执行。Renaissance可能使用多个prime brokers分散风险，并自定义连接（co-location、低延迟线路）到交易所。

---

## 二、零售级量化 API

### 富途牛牛（Futu NiuNiu）是否有这种API支持？

是的，**富途牛牛提供强大的OpenAPI，支持程序化交易和自动化下单**。

**Futu OpenAPI**专为量化投资设计，包括：

- 行情接口（实时报价、K线、tick数据等）
- 交易接口（下单、撤单、改单、查询订单/持仓等）

支持市场：港股、美股、A股通、期货、期权等。

支持语言：Python、Java、C#、C++、JavaScript等（有官方SDK）。

架构：需运行本地/云端网关程序**Futu OpenD**，然后通过API连接，支持实盘和模拟交易。

许多个人宽客和开发者使用它构建算法交易系统（如双均线策略、高频框架），社区有大量开源项目（如futu-api Python包、futu_algo框架）。

**局限**：适合零售/个人量化，不适合机构级超高频（延迟更高，无co-location），但对于中低频策略非常友好，且免费/低成本。

---

## 三、API 延迟分析

### Prime Broker（如IB）的API延迟分析

是的，**Interactive Brokers (IBKR) 提供多种 API 接口**：

- **TWS API**：通过 TWS 或 IB Gateway 连接，支持 Python/Java/C++ 等
- **Client Portal API**：RESTful HTTP 接口，需 OAuth 认证
- **FIX CTCI**：机构级 FIX 协议接口（独立于 TWS，需单独申请）

注意：这三者是**独立的接口**，TWS API 本身不使用 FIX 协议。

**是否有较高延迟？**：是的，尤其是对零售交易者，IB API有较高延迟（相对于机构级）。零售round-trip latency（从信号到执行）通常在**50-100ms**（毫秒），取决于网络、位置和优化。

- **零售 vs. 机构比较**：
    - **零售交易者**：延迟较高（~70ms+），因路由多跳（hops），无co-location（服务器靠近交易所）。适合中低频策略（如日间交易），不适合HFT（高频，需要<1ms）。可用VPS（如QuantVPS）优化到<1ms到IB，但到交易所仍~3-5ms。
    - **机构Prime Broker**：如Goldman Sachs/JPMorgan，为大基金提供低延迟（<1ms），通过专用线路、co-location和自定义优化。IB对机构客户也提供类似，但零售版有限制。

**总结**：IB API对你的模拟足够（非HFT），但若追求Medallion式短期信号，延迟是瓶颈。推荐从低频策略起步，或用专业VPS降低延迟。