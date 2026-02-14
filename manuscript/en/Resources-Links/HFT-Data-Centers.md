# HFT Data Centers and Co-location Services

> High-frequency trading (HFT) requires deploying servers in data centers near exchanges to achieve minimum latency. This article introduces co-location services at major exchanges and communication service providers.

---

## Major Service Providers

| **Provider** | **Services** | **Features** |
|---|---|---|
| **Equinix** | Co-location, bare metal rental, dedicated lines | Most comprehensive coverage, HFT standard |
| **Hudson Fiber Network** | Dedicated fiber | Low-latency routes to US East Coast exchanges |
| **McKay Brothers** | Microwave communication | Extreme low latency, cross-continental routes (e.g., London-Chicago) |
| **Colt** | Asia-Pacific high-speed fiber | Tokyo, Hong Kong, Singapore data centers |
| **Dedicated Low-Latency Fiber Providers** | Dedicated fiber (e.g., Chicago to New York) | ~13ms latency (e.g., Jump Trading's network) |

**United States**

| **Exchange** | **Co-location Data Center** | **Notes** |
|---|---|---|
| **NASDAQ** | Carteret, NJ (Primary Matching Engine) | NASDAQ-owned data center, supports co-location |
| **Multi-Exchange Hub** | Equinix NY4/NY5 (Secaucus, NJ) | Neutral data center connecting multiple exchanges |
| **NYSE/ARCA** | Mahwah, NJ | Direct co-location only, no third-party hosting |
| **CME** | Aurora, IL | Major US futures HFT hub |
| **BATS / CBOE** | Equinix NY5 (Secaucus, NJ) | Shared data center with multiple exchanges |

**Japan**

| **Exchange** | **Co-location Data Center** |
|---|---|
| **JPX (Tokyo Stock Exchange)** | **@Tokyo CC1/CC2 Data Center (Koto-ku, Tokyo)** |

_HFT traders must deploy here (JPX Arrowhead matching system)_

**United Kingdom**

| **Exchange** | **Data Center** |
|---|---|
| LSE (London Stock Exchange) | Equinix LD4 (Slough) |

**Summary**

| **Scenario** | **Recommended Deployment** |
|---|---|
| US Stocks/Futures | Carteret (NASDAQ), Mahwah (NYSE), Aurora (CME) |
| Japanese Stocks | @Tokyo CC1 (JPX co-location) |
| Hong Kong Stocks | HKEX co-location service (Tseung Kwan O data center) |
| Communication Providers | McKay Brothers (microwave), Hudson Fiber, Colt, Equinix |
