# Background: Algorithmic Trading Regulations (2024-2025)

> In 2024-2025, global algorithmic trading regulation entered a new phase. China established the world's strictest regulatory framework, while the US strengthened AI application compliance reviews. Understanding the regulatory environment is a prerequisite for quant system productionization.

---

## I. China Algorithmic Trading Regulations

### 1.1 Regulatory Milestones

| Time | Event | Impact |
|------|-------|--------|
| May 2024 | "Securities Market Algorithmic Trading Management Regulations (Trial)" released | First systematic regulation |
| October 8, 2024 | Regulations officially implemented | Era of strict regulation begins |
| February 20, 2025 | Lingjun Investment penalized (first case under new rules) | Warning effect |
| July 7, 2025 | Shanghai, Shenzhen, Beijing exchanges release "Implementation Details" | Refined execution standards |

### 1.2 High-Frequency Trading Identification Criteria

**Meeting any of the following conditions qualifies as high-frequency trading**:

| Condition | Threshold | Description |
|-----------|-----------|-------------|
| Instantaneous order frequency | **300+ per second** | Single account orders + cancellations combined |
| Intraday order frequency | **20,000+ per day** | Single account orders + cancellations combined |

**Reference Comparison**: This standard is far stricter than US markets, reflecting regulatory focus on high-frequency trading risks.

### 1.3 Four Types of Abnormal Trading Behavior

| Abnormal Type | Definition | Typical Manifestation |
|---------------|------------|----------------------|
| **Abnormal instantaneous order rate** | Large volume of orders in short time | Millisecond-level order floods |
| **Frequent instant cancellations** | Targeting "spoofing" | Placing then quickly canceling orders |
| **Frequent price ramping/dumping** | Price manipulation | Organized buy/sell patterns |
| **Large short-term transactions** | May disrupt market order | Large volume trades at open |

**Penalties**:
- Trading restrictions
- Forced suspension
- Differentiated fees
- Severe cases: temporary market halt and report to CSRC

### 1.4 Compliance Reporting Requirements

**All algorithmic trading investors must report**:
- Account basic information
- Capital information
- Trading information
- Software information

**Additional reporting for high-frequency trading**:
- Server physical location
- System test report
- Fault emergency plan

**Core Principle**: "Report first, trade second"

### 1.5 Private Fund Special Requirements

Private fund managers must:
1. Develop dedicated business management and compliance risk control systems
2. Improve algorithmic trading order review and monitoring systems
3. Establish risk prevention and control mechanisms

### 1.6 Enforcement Case: Lingjun Investment

**Date**: February 20, 2025

**Background**:
- Shenzhen and Shanghai exchanges issued regulatory measure decisions
- This was the **first penalty** under the new algorithmic trading rules

**Violation**:
- Issues with buy/sell trading within first minute of open on February 19, 2024
- Although overall net buy was 187 million yuan for the day
- Abnormal trading at open triggered regulatory red line

**Warning Significance**: Even if overall direction is correct, instantaneous trading behavior must be compliant

### 1.7 2024 Enforcement Intensity

| Metric | 2024 | YoY Change |
|--------|------|------------|
| Penalty decisions | 592 cases | +10% |
| Penalized parties | 1,327 person-times | +24% |
| Market bans | 118 persons | +39% |

---

## II. US AI Quantitative Trading Regulations

### 2.1 Regulatory Architecture

| Regulator | Responsibilities |
|-----------|------------------|
| **SEC** | Securities and Exchange Commission, overall market regulation |
| **FINRA** | Financial Industry Regulatory Authority, member firm oversight |

**Core Rules**:
- FINRA Rule 3110 (Supervision Rule)
- FINRA Rule 3120 (Supplemental Supervision Responsibilities)

### 2.2 AI Application Compliance Requirements

**June 27, 2024, FINRA released Regulatory Notice 24-09**:

| Requirement | Description |
|-------------|-------------|
| AI doesn't exempt traditional compliance obligations | Using AI doesn't mean responsibility transfer |
| AI tools included in supervision framework | Treated same as traditional systems |
| Continuous testing and monitoring | Test under "various market conditions" |

### 2.3 AI-Washing Enforcement (2024 Focus)

**Definition**: False claims about AI capabilities

**SEC Enforcement**: Filed lawsuits against two investment advisory firms
- Charged with violating Marketing Rule
- False claims about AI technology application in investment decisions

**Compliance Requirements**:
- Truthfully disclose actual AI technology application
- Cannot exaggerate or mislead investors
- Strict anti-fraud review

### 2.4 Major Penalty Case: Two Sigma

**Date**: January 16, 2025

**Penalty Amount**: **$90 million** (industry record)

**Violation Reasons**:
- Failed to address algorithm vulnerabilities
- Other violations
- Supervision failures

**Warning**: Even top quant institutions face severe penalties for inadequate algorithm risk control

### 2.5 SEC Fiscal Year 2024 Enforcement Data

| Metric | Data | YoY |
|--------|------|-----|
| Enforcement actions | 583 | -26% |
| Record-keeping case fines | >$600M | - |
| Algorithmic trading-related cases | Significantly increased | - |

**Trend**: Enforcement focus shifting from penalty amounts to case volume and deterrent effect

---

## III. EU MiFID II Framework

### 3.1 Regulatory Evolution

| Date | Event |
|------|-------|
| March 28, 2024 | MiFID II/MiFIR amendments effective |
| September 29, 2025 | Member state transposition deadline |

### 3.2 MiFID RTS 6 Requirements (Algorithmic Trading Regulatory Technical Standards)

| Requirement | Description |
|-------------|-------------|
| Thorough algorithm testing | Comprehensive testing before launch |
| Retain operation records | Audit traceability |
| Market disruption prevention rules | Circuit breakers, rate limits |
| Algorithmic trading control systems | Real-time risk control |

### 3.3 FCA Review Report (August 2024)

UK Financial Conduct Authority released multi-firm review report on algorithmic trading controls:

**Key Requirements**:
- Comply with MiFID RTS 6 requirements
- Strengthen algorithmic trading risk management and monitoring
- Improve system resilience and emergency response capability

---

## IV. Regulatory Impact on Strategies

### 4.1 Impact Level Analysis

| Strategy Type | Impact Level | Reason |
|---------------|--------------|--------|
| High-frequency alpha (200x+ turnover) | **High** | Directly touches regulatory red lines |
| Futures-spot arbitrage (basket stocks) | **High** | Frequent trading characteristics |
| Medium-low frequency index enhancement | **Low** | Turnover typically below limits |
| CTA/Trend following | **Low** | Lower trading frequency |

### 4.2 Leading Institution Response

**Actual Situation**:
- Alpha strategy turnover of large quant institutions is generally not high
- Most leading strategies have turnover below implementation detail limits
- Can basically meet new regulations

### 4.3 Industry Frequency Reduction Trend

**Driving Factors**:
1. Regulatory constraints (hard limits)
2. Capacity considerations (high-frequency cannot support billions in AUM)

**Results**:
- Medium-low frequency strategies gain importance
- Excess returns will inevitably decline
- Requires managers to continuously innovate in strategy depth and breadth

---

## V. Compliance System Design Recommendations

### 5.1 Trading Frequency Monitoring

```python
# Example: Trading frequency monitor
class TradingFrequencyMonitor:
    """
    Monitor trading frequency to ensure not triggering
    high-frequency trading identification criteria
    """

    # China regulatory thresholds
    CHINA_SECOND_LIMIT = 300   # Per-second order+cancel limit
    CHINA_DAILY_LIMIT = 20000  # Daily order+cancel limit

    def __init__(self):
        self.second_counter = 0
        self.daily_counter = 0
        self.last_second = None

    def check_order(self, timestamp: datetime) -> dict:
        """
        Check if approaching regulatory threshold
        """
        # Update counter logic...

        return {
            'second_usage': self.second_counter / self.CHINA_SECOND_LIMIT,
            'daily_usage': self.daily_counter / self.CHINA_DAILY_LIMIT,
            'warning': self._should_warn(),
            'block': self._should_block()
        }

    def _should_warn(self) -> bool:
        """Warn at 80% threshold"""
        return (self.second_counter > self.CHINA_SECOND_LIMIT * 0.8 or
                self.daily_counter > self.CHINA_DAILY_LIMIT * 0.8)

    def _should_block(self) -> bool:
        """Block at 95% threshold"""
        return (self.second_counter > self.CHINA_SECOND_LIMIT * 0.95 or
                self.daily_counter > self.CHINA_DAILY_LIMIT * 0.95)
```

### 5.2 Abnormal Trading Detection

```python
# Example: Abnormal trading behavior detection
class AbnormalTradingDetector:
    """
    Detect four types of abnormal trading behavior
    """

    def detect_spoofing(self, orders: list) -> bool:
        """
        Detect spoofing (frequent instant cancellations)
        """
        # Calculate cancel rate within short time window
        cancel_rate = self._calculate_cancel_rate(orders, window_seconds=1)
        return cancel_rate > 0.9  # 90%+ cancel rate considered abnormal

    def detect_layering(self, orderbook_changes: list) -> bool:
        """
        Detect layering (frequent ramping/dumping)
        """
        # Analyze order book change patterns
        pass

    def detect_burst_volume(self, trades: list,
                           window_seconds: int = 60) -> bool:
        """
        Detect short-term large volume
        """
        # Calculate volume anomaly within time window
        pass
```

### 5.3 Compliance Report Generation

Suggested daily compliance report content:

| Report Item | Content |
|-------------|---------|
| Trading frequency statistics | Max per second, daily total |
| Cancel ratio | Cancel/order ratio |
| Abnormal trading detection results | Detection records for four types |
| Position changes | Intraday net position change |
| Risk control trigger records | Any risk control rule triggers |

---

## VI. Regulatory Trend Outlook

### 6.1 China Market

**Positive Impacts**:
- "Spoofing" and other improper behaviors suppressed
- "Fake quant" and market-disrupting behaviors cleaned up
- Enhanced market vitality and resilience

**Long-term Outlook**:
- Standardized regulatory environment will eliminate inferior institutions
- Raise overall industry standards
- Protect investor interests

### 6.2 Global Trends

| Trend | Description |
|-------|-------------|
| AI transparency requirements | Require disclosure of AI's actual role in investment decisions |
| Algorithm explainability | Regulators may require explaining algorithm logic |
| Cross-border coordination | Multi-country regulators strengthening cooperation |
| Real-time monitoring | Shift from post-hoc review to real-time monitoring |

---

## VII. Further Reading

### Official Documents
- CSRC: "Securities Market Algorithmic Trading Management Regulations (Trial)"
- Shanghai/Shenzhen/Beijing Exchanges: "Algorithmic Trading Management Implementation Details"
- FINRA: Regulatory Notice 24-09
- SEC: 2024 Examination Priorities
- ESMA: MiFID II/MiFIR Technical Standards

### Industry Reports
- FINRA 2025 Annual Regulatory Report
- SEC 2024 Fiscal Year Enforcement Results

---

> **Core Insight**: Regulation is the "second layer of risk control" for quantitative trading. Compliance is not a burden but protection - protecting market fairness and ensuring your strategy can run long-term.
