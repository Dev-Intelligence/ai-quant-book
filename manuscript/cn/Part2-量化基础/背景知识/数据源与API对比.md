# 背景知识：数据源与API对比

> 数据是量化交易的基础。选择正确的数据源直接影响策略开发效率和实盘表现。

---

## 一、主流数据源概览

| 数据源 | 类型 | 价格 | 延迟 | 适用场景 |
|-------|------|------|------|---------|
| **Binance API** | 加密货币 | 免费 | 实时 | 加密货币策略 |
| **Yahoo Finance** | 股票/ETF | 免费 | 15-20分钟 | 学习/回测 |
| **Alpha Vantage** | 多资产 | 免费/付费 | 15分钟 | 原型开发 |
| **Polygon.io** | 美股 | $29-199/月 | 实时 | 美股实盘 |
| **Alpaca Markets** | 美股 | 免费/付费 | 实时 | 美股开发/交易 |
| **Bloomberg** | 全资产 | $2400+/月 | 实时 | 机构级 |
| **Refinitiv** | 全资产 | $1800+/月 | 实时 | 机构级 |
| **Nasdaq Data Link** | 另类数据 | 按需定价 | 日频 | 因子研究 |

> ⚠️ **重要提醒**：IEX Cloud 已于 2024 年 8 月停止服务。如果你的代码依赖 IEX Cloud，需要迁移到其他数据源（推荐 Polygon.io 或 Alpaca）。

---

## 二、免费数据源详解

### 2.1 Binance API

**优点**：
- 完全免费，无需付费
- 实时数据，延迟极低
- 支持 WebSocket 实时推送
- 历史数据完整（可追溯多年）
- REST + WebSocket 双接口

**缺点**：
- 仅限加密货币
- Rate Limit：1200次/分钟（REST），每个连接限制订阅数量（WebSocket）
- 高波动时 API 可能不稳定

**Rate Limit 处理**：
```python
import time
from binance.exceptions import BinanceAPIException

def fetch_with_retry(func, max_retries=3):
    for attempt in range(max_retries):
        try:
            return func()
        except BinanceAPIException as e:
            if e.code == -1003:  # Rate limit
                time.sleep(60)  # 等待1分钟
            else:
                raise
```

**适用场景**：加密货币策略开发、7x24小时交易系统

---

### 2.2 Yahoo Finance (yfinance)

**优点**：
- 完全免费
- 覆盖全球股票、ETF、指数
- 历史数据丰富（可追溯数十年）
- Python 库简单易用

**缺点**：
- 数据延迟 15-20 分钟
- 无官方 API，依赖网页抓取（可能被封）
- 数据质量不保证（偶有错误）
- 无 Level-2 数据

**使用示例**：
```python
import yfinance as yf

# 获取股票数据
aapl = yf.Ticker("AAPL")
df = aapl.history(period="1y", interval="1d")

# 批量获取
data = yf.download(["AAPL", "GOOGL", "MSFT"], period="1mo")
```

**适用场景**：学习、回测、日频策略研究

---

### 2.3 Alpha Vantage

**优点**：
- 免费层可用（25次/天，2024年后大幅收紧）
- 覆盖股票、外汇、加密货币
- 提供技术指标 API
- 有官方支持

**缺点**：
- 免费层限制严格
- 付费版 $49.99/月起
- 数据延迟 15 分钟
- 历史数据深度有限

**Rate Limit 对比**（2024-2025 定价）：
| 层级 | 价格 | 请求限制 |
|-----|------|---------|
| 免费 | $0 | 25次/天 |
| 基础 | $49.99/月 | 75次/分钟 |
| 专业 | $149.99/月 | 300次/分钟 |
| 企业 | $249.99/月 | 600次/分钟 |

> **注意**：Alpha Vantage 免费层请求限制已大幅收紧，仅适合轻量学习使用。

**适用场景**：原型开发、多资产研究

---

### 2.4 Alpaca Markets

**优点**：
- 免费实时美股数据（需开户）
- 同时提供交易 API
- WebSocket 实时推送
- 支持 Paper Trading（模拟交易）
- Python SDK 完善

**缺点**：
- 需要开设 Alpaca 账户
- 仅限美股和加密货币
- 历史数据需付费（更深度）

**使用示例**：
```python
from alpaca.data import StockHistoricalDataClient
from alpaca.data.requests import StockBarsRequest
from alpaca.data.timeframe import TimeFrame

client = StockHistoricalDataClient(api_key, secret_key)
request = StockBarsRequest(
    symbol_or_symbols=["AAPL"],
    timeframe=TimeFrame.Day,
    start="2024-01-01"
)
bars = client.get_stock_bars(request)
```

**适用场景**：美股策略开发与实盘交易一体化

---

## 三、付费数据源详解

### 3.1 Polygon.io

**优点**：
- 美股实时数据
- 历史 Tick 数据
- WebSocket 实时推送
- 价格合理（$29/月起）

**缺点**：
- 仅限美股
- 基础版无 Level-2
- 需要美国支付方式

**定价**：
| 层级 | 价格 | 功能 |
|-----|------|-----|
| 基础 | $29/月 | 延迟数据 + 历史 |
| 开发者 | $79/月 | 实时数据 |
| 高级 | $199/月 | Level-2 + 全功能 |

---

### 3.2 Bloomberg Terminal

**优点**：
- 全球最全面的金融数据
- 实时 + 历史 + 新闻 + 研究
- Level-2、订单簿数据
- 专业分析工具

**缺点**：
- 价格昂贵（$2000+/月/终端）
- 需要专用硬件
- API 使用复杂

**适用场景**：机构投资者、专业量化团队

---

### 3.3 Refinitiv (原 Thomson Reuters)

**优点**：
- 全球市场覆盖
- Tick 数据质量高
- 历史数据深度好
- Eikon API 易用

**缺点**：
- 价格昂贵
- 合同复杂

**适用场景**：机构级策略、高频交易

---

## 四、另类数据源

### 4.1 Nasdaq Data Link（原 Quandl）

提供非传统金融数据：
- 卫星图像数据
- 情感分析数据
- 宏观经济数据
- 供应链数据

### 4.2 新闻与情感数据

| 来源 | 类型 | 用途 |
|-----|------|-----|
| NewsAPI | 新闻聚合 | 情感分析 |
| Twitter/X API | 社交媒体 | 市场情绪 |
| Reddit API | 论坛 | 散户情绪 |
| SEC EDGAR | 监管文件 | 基本面分析 |

---

## 五、数据源选择决策树

```
你的交易品种是什么？
│
├─ 加密货币 → Binance API (免费、实时)
│
├─ 美股
│   ├─ 学习/回测 → Yahoo Finance (免费)
│   ├─ 原型开发 → Alpha Vantage (付费) 或 Alpaca (免费)
│   ├─ 实盘+数据 → Alpaca Markets (免费开户)
│   └─ 高级数据 → Polygon.io ($29+/月)
│
├─ A股
│   ├─ 学习/回测 → Tushare / AKShare (免费)
│   └─ 实盘交易 → 券商 API / Wind
│
└─ 机构级需求 → Bloomberg / Refinitiv
```

> **2024-2025 推荐**：对于美股开发者，Alpaca Markets 已成为最受欢迎的免费选择，因其同时提供数据和交易 API。

---

## 六、常见陷阱

### 6.1 数据质量问题

- **缺失值**：节假日、停牌导致的数据空洞
- **异常值**：拆股、分红未调整
- **时区问题**：不同交易所时区不同

### 6.2 Survivorship Bias

免费数据源通常只包含当前存在的股票，已退市股票数据缺失。

### 6.3 Look-Ahead Bias

某些数据源的"历史数据"可能包含后来修正的值（如财报重述）。

---

## 七、实用建议

1. **起步阶段**：Yahoo Finance + Binance API（免费）
2. **原型开发**：Alpaca Markets（免费且同时支持交易）
3. **美股实盘**：Alpaca（数据+交易一体）或 Polygon.io（纯数据）
4. **机构级**：Bloomberg / Refinitiv（全面但昂贵）
5. **始终验证**：对比多个数据源，检查数据质量

> ⚠️ **避坑提醒**：Alpha Vantage 免费层现在只有 25 次/天，已不适合正式开发使用。

---

> **核心原则**：数据质量 > 数据数量。宁愿用少量高质量数据，也不要用大量有问题的数据训练模型。
