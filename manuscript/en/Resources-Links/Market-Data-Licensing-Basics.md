# Market Data Licensing Basics

> Market data licensing and usage restrictions are often overlooked but critically important compliance issues in quantitative trading. This document covers basic concepts and common pitfalls.

---

## 1. Professional vs Non-Professional Users

Exchanges and data vendors typically classify users into two categories with vastly different pricing:

| Type | Definition | Typical Monthly Fee (US Real-time Data) |
|------|------------|----------------------------------------|
| **Non-Professional** | Individual investor, data used solely for personal investment decisions | $0-15/month |
| **Professional** | Managing money for others, working at a financial institution, or using data for commercial purposes | $100-500+/month |

**Common Pitfalls**:
- Registering as Non-Pro while actually managing others' money → Violation
- Using Non-Pro rates under a corporate account → Violation
- Displaying real-time quotes on blog/YouTube → May require Pro license

> **SEC Rule**: If you are registered in the securities industry, manage money for others, or use data for commercial purposes, you are a Professional User.

---

## 2. Redistribution Restrictions

Almost all market data has **redistribution restrictions**:

| Activity | Additional License Required? |
|----------|------------------------------|
| Viewing real-time quotes yourself | ❌ Basic subscription sufficient |
| Displaying to team in internal systems | ⚠️ May require multi-user license |
| Displaying publicly on website/app | ✅ Requires Display license |
| Providing to clients | ✅ Requires Redistribution license |
| Storing and reselling to others | ✅ Requires Vendor license |

**Cost Magnitude**:
- Display license: $500-5,000/month and up
- Redistribution license: $5,000-50,000+/month
- Exchanges will audit your usage

---

## 3. SIP/OPRA vs Direct Feeds

### Two Sources for US Stock Data

| Source | Description | Latency | Cost |
|--------|-------------|---------|------|
| **SIP (Consolidated Feed)** | Aggregated feed from all exchanges | ~1-10ms | Lower ($50-200/month) |
| **Direct Feed** | Direct from individual exchange | ~50-200μs | High ($5,000+/month/exchange) |

### Options Data (OPRA)

- **OPRA** is the consolidated options data feed for US markets
- Massive data volume: peaks at **1M+ messages/second**
- Full OPRA subscription: **$2,000-10,000+/month**
- Many vendors offer simplified "Top of Book" versions at lower cost

---

## 4. Common Compliance Pitfalls

### 1. Delayed Data ≠ Unrestricted
- 15-minute delayed data **still has usage restrictions**
- Cannot be used for commercial display (without license)
- Cannot be stored and redistributed at scale

### 2. Free API ≠ Commercially Free
- Free APIs like Yahoo Finance, Alpha Vantage typically prohibit commercial use
- Using for commercial backtesting systems may violate terms
- Recommendation: Use properly licensed paid data sources for commercial purposes

### 3. Historical Data Has Restrictions Too
- Cannot resell purchased historical data
- Cannot use to build competing products
- Some data has usage time limits

### 4. Cross-Border Usage
- Using US data in other countries may have data export compliance requirements
- Using foreign market data may require specific regional licenses

---

## 5. Compliance Recommendations

1. **Clarify Purpose**: Personal learning, internal research, or commercial product?
2. **Keep Records**: Save subscription agreements and authorization emails
3. **Regular Audits**: Check if data usage exceeds license scope
4. **Ask Vendors**: When uncertain, ask the data vendor directly
5. **Budget Buffer**: Compliance costs can exceed data costs

---

## 6. Key Points by Market

| Market | Regulator/Data Source | Key Considerations |
|--------|----------------------|---------------------|
| US Stocks | SEC / SIP / Exchanges | Strict Pro vs Non-Pro distinction |
| US Options | OPRA | High data volume, full subscription expensive |
| US Futures | CME Group | Charged by product line |
| China A-shares | SSE/SZSE | Must obtain through authorized vendors |
| Hong Kong | HKEX | Real-time data requires separate payment |
| Cryptocurrency | Various exchanges | Relatively relaxed, but still has restrictions |

---

## Further Reading

- [SEC Market Data Infrastructure Rule](https://www.sec.gov/rules/final/2020/34-90610.pdf)
- [NASDAQ Global Data Agreement](https://www.nasdaqtrader.com/Trader.aspx?id=DPAgreement)
- [CME Market Data Policies](https://www.cmegroup.com/market-data/policies.html)

---

> **Disclaimer**: This document is for reference only and does not constitute legal advice. For specific compliance questions, please consult a professional lawyer or data vendor.
