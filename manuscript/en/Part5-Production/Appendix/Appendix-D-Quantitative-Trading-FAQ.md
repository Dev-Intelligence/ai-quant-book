# Appendix D: Quantitative Trading FAQ

> This appendix collects the most common confusions and misconceptions from learners, helping you avoid typical cognitive traps.

---

## I. Returns and Risk

### Q1: Does a high Sharpe ratio equal low risk?

**Not exactly.**

Sharpe ratio measures risk-adjusted returns, but has two blind spots:
- **Doesn't reflect tail risk**: A 2.0 Sharpe strategy may hide 30% maximum drawdown
- **Sensitive to leverage**: Leverage can artificially boost Sharpe, but risk scales proportionally

**Correct approach**: Look at Sharpe ratio together with maximum drawdown and Calmar Ratio.

---

### Q2: Backtest shows 50% annual return, how much can I make live?

**Rule of thumb: Live returns ~ Backtest returns x 0.3-0.6**

Reasons:
- Backtest assumes ideal execution, live has slippage and impact costs
- Backtest has no emotional interference, live has fear and greed
- Backtest environment is stable, live encounters various failures

**Correct approach**: If backtest returns halved are still acceptable, then consider live trading.

---

### Q3: Why does my strategy backtest great but lose money live?

Three most common reasons:

| Reason | Manifestation | Solution |
|--------|---------------|----------|
| Look-Ahead Bias | Used future data | Check signal generation time vs. execution time |
| Overfitting | Training set >> Test set | Walk-Forward validation |
| Cost underestimation | Ignored slippage, impact | Use conservative cost assumptions |

---

## II. Strategy Selection

### Q4: Which is better, trend or mean reversion strategy?

**There is no "better," only "more suitable."**

| Market State | Trend Strategy | Mean Reversion |
|--------------|----------------|----------------|
| Trending market | Big wins | Big losses |
| Range-bound market | Frequent stop-losses | Stable profits |

**Correct approach**: Identify market regime (Regime Detection), use the right strategy at the right time.

---

### Q5: Can machine learning predict stock price movements?

**Almost cannot.**

- Financial data has extremely low signal-to-noise ratio, top models only achieve 52-55% accuracy
- 52% accuracy after costs may result in net losses
- Deep learning needs massive data, quant data usually insufficient

**Correct positioning**: ML is not for "predicting up or down," but for extracting **weak but robust signals** from noise.

---

### Q6: Are high-frequency strategies easier to make money?

**For institutions yes, for retail no.**

High-frequency barriers:
- Hardware: Dedicated lines, co-located servers (hundreds of thousands USD/year)
- Software: Sub-millisecond latency (requires C++/FPGA)
- Cost: Being "eaten" by slippage is common in high-frequency

**Retail is better suited for**: Medium-low frequency strategies (daily/weekly), using cognitive edge rather than speed edge.

---

## III. Risk Control

### Q7: What's the right stop-loss level?

**No standard answer, but there's a calculation method:**

```
Stop-loss range = k x Volatility

k = 2-3 (too tight gets stopped out easily, too loose loses too much)
```

| Asset Type | Daily Volatility | Suggested Stop-Loss |
|------------|------------------|---------------------|
| Large cap (SPY) | 0.8% | 2-3% |
| Tech stock (TSLA) | 3.5% | 7-10% |
| Cryptocurrency | 5%+ | 10-15% |

---

### Q8: Can diversification eliminate risk?

**In normal times yes, in crisis it fails.**

| Period | AAPL-MSFT Correlation | Stock-Bond Correlation |
|--------|----------------------|------------------------|
| Normal | 0.7 | -0.3 |
| Crisis | 0.9 | 0.6 or -0.5 |

**LTCM's lesson**: They assumed stable correlation, result was crisis correlations spiked and all assets fell together, diversification failed.

**Correct approach**: Assume crisis correlations spike to 0.9, stress test with this assumption.

---

### Q9: Can the Kelly formula be used directly?

**Cannot use directly, recommend "Half Kelly."**

Reasons:
- Win rate and odds are estimates, may be inaccurate
- Full Kelly has too much volatility, psychologically unbearable
- In finance positions are correlated, violating Kelly's independence assumption

**Practical recommendation**: Kelly/2 as position upper limit, combined with other constraints.

---

## IV. Data and Systems

### Q10: Is free data sufficient?

**Sufficient for development stage, depends for production.**

| Data Type | Free Viability | Recommended Solution |
|-----------|----------------|---------------------|
| Daily OHLCV | Sufficient | Yahoo Finance, Alpha Vantage |
| Minute bars | Limited | Broker APIs (requires account) |
| Tick/L2 | Impossible | Must pay (thousands-tens of thousands USD/year) |

---

### Q11: Is Python fast enough?

**Sufficient for medium-low frequency, not for high-frequency.**

| Strategy Frequency | Latency Requirement | Python Suitability |
|-------------------|---------------------|-------------------|
| Daily/Weekly | Second-level | Fully sufficient |
| Minute-level | Millisecond-level | Marginally usable |
| Sub-second | Microsecond-level | Must use C++/FPGA |

---

### Q12: What to do about API rate limits?

**This is a common problem, needs pre-emptive handling.**

Solutions:
1. **Local caching**: Store to database after first fetch, reduce API calls
2. **Rate limiter**: Add `time.sleep()` in code
3. **Backup data sources**: Switch to backup when primary is rate-limited
4. **Pre-loading**: Batch download during non-trading hours

---

## V. Multi-Agent

### Q13: Why do we need multi-agent?

**Single models cannot adapt to all market regimes.**

| Problem | Single Model | Multi-Agent |
|---------|--------------|-------------|
| Trending -> Range-bound | Strategy fails | Regime Agent switches experts |
| Model overload | Does everything, does nothing well | Expert division of labor |
| Single point of failure | Entire system crashes | Other Agents continue running |

---

### Q14: What if Regime Detection misjudges?

**Misjudgment is inevitable, key is controlling consequences.**

Design principles:
1. **Soft switching**: Use probability weighting not 0/1 switching
2. **Confirmation delay**: State must persist N days before confirming switch
3. **Conservative during transition**: Reduce positions when uncertain
4. **Fast crisis response**: Rather misjudge as crisis than miss it

---

### Q15: Can Risk Agent be overridden?

**Absolutely not. This is a hard constraint of system design.**

Risk Agent's veto power:
- Position exceeds limit -> Force reduction
- Drawdown triggers -> Force deleveraging
- Circuit breaker triggers -> Prohibit new positions

Even if Signal Agent has "better reasons," cannot bypass Risk Agent.

---

## VI. Learning Path

### Q16: Can I learn quant without programming background?

**Can understand concepts, but difficult to practice.**

Suggested path:
1. First learn Python basics (2-4 weeks)
2. Learn pandas/numpy data processing (2 weeks)
3. Then learn strategy parts of this course
4. Learn by coding and verifying

---

### Q17: How much math background is needed?

**High school math + intro statistics is enough to get started.**

Core concepts:
- Mean, standard deviation, correlation
- Probability distributions (normal, fat tails)
- Log returns, compound interest calculation

Not needed:
- Advanced calculus (unless doing options pricing)
- Linear algebra (unless going deep into ML)

---

### Q18: How long until I can go live?

**Conservative recommendation: 6-12 months.**

| Stage | Time | Goal |
|-------|------|------|
| Learn basics | 2-3 months | Understand concepts, run backtests |
| Strategy development | 2-3 months | Have a strategy that passes Quality Gate |
| Paper trading | 2-3 months | Verify system stability |
| Small capital live | Ongoing | Use 1-5% of capital to verify |

---

## VII. Common Mindset Issues

### Q19: 10 consecutive stop-losses, is strategy failing?

**Not necessarily.**

Trend strategy typical characteristics:
- Win rate only 30-40%
- Profit/loss ratio 3:1 or higher
- 10 consecutive stop-losses is normal

**Judgment criteria**: Check if drawdown exceeds 1.5x historical max drawdown. If not, continue executing.

---

### Q20: Can I directly use others' strategies?

**Can reference, cannot copy.**

Reasons:
- Public strategies are already arbitraged, Alpha has decayed
- Parameters may be overfit to specific period
- You don't understand the strategy's risk characteristics

**Correct approach**: Understand logic -> Backtest yourself -> Adjust parameters -> Small capital verification.

---

## Key Takeaways From This Appendix

1. **No Holy Grail**: Any strategy will fail at some point
2. **Risk First**: Surviving is more important than making more
3. **Assumptions Will Be Wrong**: Model assumptions fail in extreme situations
4. **Continuous Learning**: Markets change, strategies must evolve with them
