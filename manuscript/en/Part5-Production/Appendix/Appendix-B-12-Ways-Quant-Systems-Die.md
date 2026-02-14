# Appendix B: 12 Ways Quant Systems Die

> **Beginner courses teach you how to make money; advanced courses teach you how to die.**

---

## Why Learn "How to Die"

In institutional quant training, there's an unwritten rule:

> New hires spend their first week learning the company's historical failure cases.

The reason is simple:
- Successful strategies each have their unique qualities
- Failed strategies often die from the same few causes
- Knowing how to die helps you design systems that won't die

This appendix summarizes the 12 most common ways quant systems die, each including definition, real cases, and prevention measures.

> Note: Cases with specific institution/event names are public events; cases without specific references are synthetic examples to explain mechanisms and risk patterns.

---

## Death Mode 1: Data Contamination Death

### Definition
System uses incorrect, missing, or contaminated data, causing signal distortion.

### Typical Symptoms
- Backtest is great, live trading suddenly loses money
- Strategy behavior becomes abnormal from a certain day
- Signals completely opposite to market

### Case (Illustrative)
> In 2018, a team's strategy suddenly started losing heavily. Investigation revealed the data provider had divided all prices by 100 after an update (should have been yen, mistakenly labeled as USD). The system thought prices crashed 99% and went massively long.

### Prevention Measures
```
[ ] Data quality check pipeline (outliers, missing values, jump detection)
[ ] Multi-source cross-validation
[ ] Real-time vs. historical data consistency checks
[ ] Data change alert mechanism
```

---

## Death Mode 2: Overfitting Death

### Definition
Strategy over-optimizes on historical data, memorizing noise rather than signal.

### Typical Symptoms
- Backtest Sharpe > 3, live Sharpe < 0.5
- Completely different results on different historical periods
- Minor parameter tweaks cause dramatic return changes

### Case (Illustrative)
> An ML team trained a model with 200 features, backtested 80% annual return. Lost 15% in first month of live trading. Post-mortem analysis found the model learned "buy tech stocks on the third Tuesday of every month" - pure historical coincidence.

### Prevention Measures
```
[ ] Strict out-of-sample testing (OOS)
[ ] Limit feature/parameter count
[ ] Cross-validation + Walk-forward analysis
[ ] Maintain skepticism of "too perfect" backtests
```

---

## Death Mode 3: Regime Drift Death

### Definition
Market regime fundamentally changes, but strategy still operates under old regime.

### Typical Symptoms
- Previously effective strategy suddenly fails
- Consecutive losses exceed historical maximum drawdown
- Signal direction consistently opposite to market trend

### Case (Illustrative)
> 2020-2021, many value factor strategies suffered continuous losses. The reason was zero interest rate environment where growth stocks massively outperformed value. Funds sticking to value strategies had 50%+ drawdowns.

### Prevention Measures
```
[ ] Regime Detection module
[ ] Rolling correlation monitoring between strategy returns and benchmark
[ ] Multi-strategy, multi-factor diversification
[ ] Regularly review if strategy assumptions still hold
```

---

## Death Mode 4: Execution Distortion Death

### Definition
Systematic deviation between backtest assumptions and live execution.

### Typical Symptoms
- Live returns far below backtest
- Slippage far exceeds expectations
- Orders frequently don't fill

### Case (Illustrative)
> A high-frequency strategy backtested 200% annual return. Live trading discovered: backtest assumed execution at top-of-book, actual latency caused execution at third level. This 0.1% difference, over 10,000 trades, consumed all profits.

### Prevention Measures
```
[ ] Conservative cost assumptions (slippage, impact, latency)
[ ] Backtest with tick data, not candles
[ ] Small capital live sampling to calibrate backtest model
[ ] Distinguish "predictable Alpha" from "capturable Alpha"
```

---

## Death Mode 5: Risk Control Failure Death

### Definition
Risk control rules have loopholes, are bypassed, or don't execute.

### Typical Symptoms
- Single trade loss exceeds threshold
- Portfolio drawdown triggers circuit breaker but system doesn't respond
- Stop-loss orders don't execute

### Real Case (Public Event)
> In 2012, Knight Capital lost $440 million in 45 minutes due to a software bug causing runaway orders. Risk controls should have blocked abnormal orders, but the bug bypassed checks.

### Prevention Measures
```
[ ] Risk control completely independent from strategy
[ ] Multi-layer risk control (position, portfolio, system)
[ ] Risk control cannot be bypassed by anyone (including founders)
[ ] Regular risk control drills (deliberately trigger circuit breaker)
```

---

## Death Mode 6: Liquidity Evaporation Death

### Definition
Need to exit but discover market has no liquidity.

### Typical Symptoms
- Stop-loss orders cannot execute
- Slippage far exceeds normal levels
- Forced to liquidate at terrible prices

### Real Case (Public Event)
> August 24, 2015, US stock "flash crash." Many ETF prices dropped 20-30% instantly, market makers withdrew causing liquidity vacuum. Stop-losses executed at prices far below expectations.

### Prevention Measures
```
[ ] Avoid concentrated positions in single assets
[ ] Monitor order book depth, not just price
[ ] Extreme scenario liquidity stress tests
[ ] Maintain cash reserves for margin calls
```

---

## Death Mode 7: Correlation Spike Death

### Definition
In crisis, asset correlations approach 1, diversification fails.

### Typical Symptoms
- "Diversified" portfolio plunges entirely
- All strategies lose simultaneously
- Hedges fail

### Real Case (Public Event)
> 1998 LTCM crisis. Their "uncorrelated" strategies all lost simultaneously after Russian debt crisis. All arbitrage strategies faced same liquidity squeeze, correlation spiked from 0.2 to 0.95.

### Prevention Measures
```
[ ] Stress tests use "crisis correlation" (assume correlation -> 1)
[ ] Maintain truly uncorrelated assets (like treasuries, gold)
[ ] Actively reduce risk exposure on crisis warnings
[ ] Leverage multiples consider crisis scenarios
```

---

## Death Mode 8: Leverage Blowup Death

### Definition
Excessive leverage causes one adverse move to wipe out principal.

### Typical Symptoms
- Margin calls cannot be met
- Forced liquidation
- Principal loss exceeds 50%

### Real Case (Public Event)
> In 2021, Archegos Capital used 5x leverage betting on a few stocks. When prices dropped 30%, triggered margin calls and forced liquidation. Final loss approximately $20 billion.

### Prevention Measures
```
[ ] Leverage limit (recommend < 2x)
[ ] Volatility-adjusted leverage (reduce during high vol)
[ ] Maintain 50% margin buffer
[ ] Stress test: assume volatility doubles
```

---

## Death Mode 9: Human Intervention Death

### Definition
Manual intervention in system decisions causes larger losses.

### Typical Symptoms
- Manually cancel stop-loss orders
- Add to losing positions to "average down cost"
- Override system signals based on feelings

### Case (Illustrative)
> A fund manager saw the auto-system about to stop-loss, thought "market will bounce soon," manually canceled stop-loss. Market continued down 20%. Post-calculation showed stop-loss would have avoided 80% of the loss.

### Prevention Measures
```
[ ] Strict operating procedures (dual confirmation)
[ ] Manual intervention requires written justification and approval
[ ] Post-mortem review of all manual intervention results
[ ] If manual intervention win rate < 50%, prohibit intervention
```

---

## Death Mode 10: System Failure Death

### Definition
Technical problems prevent system from running normally.

### Typical Symptoms
- Order submission fails
- Market data feed interrupts
- System crashes or delays

### Real Case (Public Event)
> On Facebook IPO day in 2012, NASDAQ trading system failed. Many orders weren't processed correctly, investors didn't know how many shares they bought. Damage claims exceeded $500 million.

### Prevention Measures
```
[ ] High-availability system design (primary-backup switch)
[ ] Real-time health monitoring and alerts
[ ] Safe mode during failures (can only close positions)
[ ] Regular disaster recovery drills
```

---

## Death Mode 11: Regulatory Change Death

### Definition
Law changes make strategy unviable or illegal.

### Typical Symptoms
- Certain types of trading prohibited
- Tax policy changes profit calculations
- Margin requirements increase

### Case (Illustrative)
> After 2010, many countries banned or restricted naked short selling. Many neutral strategies relying on shorting had to adjust or close. After China's 2021 education industry restrictions, related stocks crashed, many position strategies lost 90%+.

### Prevention Measures
```
[ ] Diversify strategy types and regions
[ ] Monitor regulatory developments
[ ] Avoid over-reliance on single policy environment
[ ] Reserve buffer period for regulatory changes
```

---

## Death Mode 12: Counterparty Adaptation Death

### Definition
Market participants learn your strategy and trade against it.

### Typical Symptoms
- Strategy returns gradually decay
- Someone seems to always trade ahead of you
- Previously effective signals become ineffective

### Case (Illustrative)
> A fund became known for stable mean reversion strategy. Counterparties discovered this and started buying ahead at positions where the fund was expected to buy, driving up prices. Then sold after the fund bought. The original 1% Alpha gradually became -0.5%.

### Prevention Measures
```
[ ] Diversify signal sources and trading times
[ ] Avoid fixed pattern trading
[ ] Monitor strategy capacity and marginal returns
[ ] Continuously develop new strategies to replace decaying ones
[ ] Mind information security (don't reveal strategy details)
```

---

## Death Mode Comprehensive Diagnostic Table

When strategy has problems, troubleshoot in this order:

| Priority | Check Item | Corresponding Death Mode |
|----------|------------|--------------------------|
| 1 | Is data normal? | #1 Data Contamination |
| 2 | Has market regime changed? | #3 Regime Drift |
| 3 | Is execution normal? | #4 Execution Distortion |
| 4 | Has risk control triggered? | #5 Risk Control Failure |
| 5 | Is liquidity sufficient? | #6 Liquidity Evaporation |
| 6 | Is correlation normal? | #7 Correlation Spike |
| 7 | Is leverage too high? | #8 Leverage Blowup |
| 8 | Was there manual intervention? | #9 Human Intervention |
| 9 | Is system running normally? | #10 System Failure |
| 10 | Any regulatory changes? | #11 Regulatory Change |
| 11 | Is strategy being learned? | #12 Counterparty Adaptation |
| 12 | Is there overfitting? | #2 Overfitting |

---

## Weekly Health Check Checklist

```
[ ] Data quality check passed
[ ] Regime state normal
[ ] Execution slippage within expected range
[ ] Risk control thresholds not triggered
[ ] Liquidity indicators normal
[ ] Correlation matrix no anomalies
[ ] Leverage within limits
[ ] No abnormal manual intervention
[ ] System health 100%
[ ] No regulatory changes
[ ] Alpha decay within expected range
[ ] Out-of-sample performance matches expectations
```

---

> **Remember**: Quant systems are not "set and forget." They require continuous monitoring, regular auditing, and constant evolution. Knowing how to die helps you live longer.
