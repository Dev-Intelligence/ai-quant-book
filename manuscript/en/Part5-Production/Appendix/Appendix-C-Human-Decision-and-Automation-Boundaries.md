# Appendix C: Human Decision and Automation Boundaries

> **Which decisions should never be automated?**

---

## Why We Need These Boundaries

The ultimate temptation of quant systems is: **automate everything.**

But history tells us that full automation is dangerous:
- **2010 Flash Crash**: High-frequency algorithms fed back on each other, Dow dropped 1000 points in 5 minutes
- **Knight Capital**: Lost $440 million in 45 minutes, no one intervened in time
- **LTCM**: Models said "safe," human judgment was ignored, result was blowup

This appendix helps you draw boundaries:
- What can be automated?
- What needs human-in-the-loop?
- What must be human decision?

---

## Decision Classification Framework

```
+-------------------------------------------------------------+
|                    Automation Level Spectrum                 |
+-------------------------------------------------------------+
|                                                             |
|  Full Auto    High Auto    Human Loop    Human Approve    Manual    |
|      |          |            |              |             |         |
|      v          v            v              v             v         |
|   Execute    Stop Loss    Strategy    Param Change    Capital       |
|   Orders     Take Profit   Switch    System Upgrade    Allocation  |
|   Data Clean  Position    Risk       New Strategy    Compliance    |
|   Signal Gen  Adjust     Downgrade   Launch          Legal         |
|              Rebalance   Exception                                  |
|                          Handling                                   |
|                                                                    |
|  <----------------------------------------------------------->     |
|   High Efficiency                              Low Risk            |
|   High Risk                                    Low Efficiency      |
|                                                                    |
+-------------------------------------------------------------+
```

---

## Level 1: Full Automation

### Definition
System makes decisions and executes autonomously, no human confirmation needed.

### Applicable Scenarios

| Scenario | Reason | Condition |
|----------|--------|-----------|
| Order execution | Speed required | Within preset rules |
| Data cleaning | Rules are clear | Has anomaly alerts |
| Signal generation | Model output | Doesn't directly trade |
| Logging | Pure recording | No decision component |

### Risk Controls
```
Even with full automation, you need:
[ ] Hard limits (per-trade max, daily loss max)
[ ] Anomaly detection (alert on deviation from historical behavior)
[ ] Post-hoc audit logs
[ ] Emergency stop button
```

---

## Level 2: High Automation

### Definition
System auto-executes but with preset safety boundaries.

### Applicable Scenarios

| Scenario | Auto Part | Human Trigger Part |
|----------|-----------|-------------------|
| Stop loss/take profit | Auto-execute after trigger | Modifying stop levels needs approval |
| Position adjustment | Auto within limits | Exceeding limits needs human confirmation |
| Rebalancing | Auto-execute by rules | Deviation from threshold needs human |

### Case: Smart Stop Loss

```
Rules:
- 2% loss -> Auto stop 50% of position
- 5% loss -> Auto stop remaining position
- 10% loss -> Auto stop + notify human

Human Reserved Rights:
- Can manually adjust stop level before loss
- But cannot cancel stop after loss occurs
```

---

## Level 3: Human-in-the-Loop

### Definition
System recommends, executes after human confirmation.

### Applicable Scenarios

| Scenario | What System Does | What Human Does |
|----------|------------------|-----------------|
| Strategy switch | Detect regime, recommend switch | Confirm switch |
| Risk downgrade | Detect anomaly, recommend downgrade | Approve downgrade |
| Abnormal trade | Detect anomaly, pause execution | Decide continue or cancel |
| Large orders | Split order recommendation | Approve execution |

### Design Principles

```
1. Time Limit
   - Human must respond within X minutes
   - Timeout triggers safe default action

2. Sufficient Information
   - System must provide enough info for decision
   - Not just "confirm/cancel"

3. Accountability Mechanism
   - Record who decided what and when
   - Enables post-mortem review
```

---

## Level 4: Human Approval

### Definition
Requires explicit human approval to execute.

### Applicable Scenarios

| Scenario | Why Human Needed |
|----------|------------------|
| Parameter modification | May affect system behavior |
| System upgrade | Needs testing and rollback plan |
| New strategy launch | Needs complete review process |
| Capital allocation | Involves large funds |
| Permission changes | Security related |

### Approval Process Example

```
Parameter Modification Process:

1. Researcher submits modification request
   - Modification content
   - Modification reason
   - Expected impact
   - Rollback plan

2. Risk control review
   - Does it increase risk
   - Does it comply with limits

3. CTO/PM approval
   - Is it necessary
   - Is timing appropriate

4. Execution
   - First test in simulation environment
   - Then test with small capital live
   - Finally full deployment

5. Monitoring
   - Close monitoring for 24 hours post-launch
   - Rollback immediately on anomaly
```

---

## Level 5: Manual Only

### Definition
Should not be automated, must be entirely human decision.

### Applicable Scenarios

| Scenario | Why Cannot Automate |
|----------|---------------------|
| Capital distribution | Involves company/client funds |
| Compliance decisions | Legal liability |
| Crisis response | Requires comprehensive judgment |
| External communication | Requires human interaction |
| Strategic decisions | Involves company direction |

### Crisis Response Case

```
March 2020 Market Crash

Automated system can do:
- Detect crisis state
- Trigger preset deleveraging rules
- Send alerts

Must be human decision:
- Whether to fully liquidate
- Whether to pause all strategies
- Whether to communicate with investors
- Whether to redeem other funds
- How to report to regulators
```

---

## Boundary Judgment Checklist

When unsure if you should automate, ask yourself:

```
[ ] Is this decision reversible?
  - Reversible -> Lean toward automation
  - Irreversible -> Lean toward manual

[ ] Is there time pressure on this decision?
  - Millisecond level -> Must automate
  - Minute level -> Can be human-in-loop
  - Hour level or above -> Can be pure manual

[ ] Does this decision have legal/compliance implications?
  - Yes -> Lean toward manual
  - No -> Lean toward automation

[ ] How much capital does this decision affect?
  - Small amount -> Can automate
  - Large amount -> Needs human confirmation

[ ] Does this decision require contextual judgment?
  - Requires integrating multiple information sources -> Lean toward manual
  - Rules are clear -> Lean toward automation

[ ] What's the cost of error?
  - Low cost, recoverable -> Lean toward automation
  - High cost, unrecoverable -> Lean toward manual
```

---

## Human-Machine Collaboration Best Practices

### 1. Clear Division of Responsibilities

```
System's Responsibilities:
- Collect and process information
- Execute rule-based operations
- Provide decision recommendations
- Record all operations

Human's Responsibilities:
- Set goals and constraints
- Handle exception situations
- Make value judgments
- Bear ultimate responsibility
```

### 2. Design Good Interactions

```
Bad Interaction:
  "Execute? [Yes/No]"

Good Interaction:
  "Detected the following situation:
   - Market state: Crisis
   - Current drawdown: 8%
   - Recommended action: Reduce to 50%

   Historical similar situations:
   - 2020.03: Deleveraging avoided 15% loss
   - 2018.12: Deleveraging missed 5% rebound

   Options:
   1. Execute deleveraging (recommended)
   2. Don't execute, continue monitoring
   3. Modify deleverage target
   4. Discuss with team"
```

### 3. Trust But Verify

```
Trust the System:
- In normal conditions, execute per system recommendation
- Don't frequently override system based on "feelings"

Verify the System:
- Regularly audit quality of system decisions
- Track results of human override vs. following system
- If human override win rate < 50%, consider reducing intervention
```

### 4. Maintain Emergency Channels

```
No matter how automated, always need:

[ ] One-click stop button
[ ] Emergency contact list
[ ] Disaster recovery process
[ ] Backup operation methods (like manual order entry)
[ ] 24/7 on-call arrangements (at least during important periods)
```

---

## Automation Maturity Assessment

Assess your system's automation level:

| Dimension | Level 1 | Level 2 | Level 3 | Level 4 | Level 5 |
|-----------|---------|---------|---------|---------|---------|
| Execution | Manual orders | Semi-auto | Auto + confirm | Full auto | Smart splitting |
| Monitoring | Manual inspection | Scheduled reports | Real-time dashboard | Auto alerts | Predictive alerts |
| Risk Control | Post-hoc check | Threshold alerts | Auto downgrade | Auto circuit breaker | Smart risk control |
| Exception Handling | Fully manual | Rule-based | Human-machine collab | Auto + escalation | Self-healing |

**Recommended Evolution Path**:
1. First achieve automation at execution layer
2. Then monitoring and alerting
3. Then rule-based risk control
4. Finally exception handling
5. Always maintain human override channel

---

## Summary

```
Automation Golden Rules:

1. Automate what can be automated (efficiency)
2. Irreversible decisions need human (safety)
3. Always maintain human intervention channel (emergency)
4. Human decisions need accountability mechanism (responsibility)
5. Regularly audit automated decision quality (improvement)

Ultimate Goal:
  Not to replace humans, but to amplify human capability
  Let systems handle tedious tasks, let humans handle important ones
```

---

> **Remember**: Fully automated systems are very efficient in normal times, very dangerous in abnormal times. The best systems are human-machine collaboration - machines handle speed and scale, humans handle judgment and responsibility.
