# FIX Protocol Introduction

> "FIX is the HTTP of finance - you don't need to fully understand it, but you need to know how it affects your orders."

---

## 1. What is FIX Protocol?

### 1.1 Definition

**FIX (Financial Information eXchange)**: A financial information exchange protocol that serves as the industry-standard communication protocol for electronic trading.

```
Your Trading System <──FIX Messages──> Broker/Exchange

Send: "I want to buy 100 shares of AAPL, limit price $185"
Receive: "Order received, ID 12345"
Receive: "50 shares filled at $185.00"
Receive: "Remaining 50 shares filled at $185.01"
```

### 1.2 Why Do We Need FIX?

| Problem | Without FIX | With FIX |
|-----|---------|--------|
| Connect to new broker | Redevelop integration | Just change config |
| Multi-broker trading | N sets of code | 1 set of code |
| Order status sync | Different formats per broker | Standardized messages |
| Troubleshooting | Different logs per broker | Unified protocol analysis |

### 1.3 FIX Protocol Versions

| Version | Release Year | Main Use |
|-----|---------|---------|
| FIX 4.0 | 1996 | Legacy version |
| FIX 4.2 | 2000 | Still in use |
| FIX 4.4 | 2003 | **Most common** |
| FIX 5.0 | 2006 | New features |
| FIXT 1.1 | 2008 | Transport layer separation |

Most brokers and exchanges support **FIX 4.4**.

---

## 2. FIX Message Structure

### 2.1 Message Format

FIX messages are sequences of **key-value pairs**, separated by SOH (ASCII 01):

```
8=FIX.4.4|9=176|35=D|49=SENDER|56=TARGET|34=2|52=20240101-09:30:00.000|
11=ORD001|21=1|55=AAPL|54=1|60=20240101-09:30:00.000|38=100|40=2|44=185.00|
59=0|10=123|
```

**Human-readable version**:
```
8=FIX.4.4        # Protocol version
9=176            # Body length
35=D             # Message type (D=New Order)
49=SENDER        # Sender ID
56=TARGET        # Target ID
34=2             # Message sequence number
52=20240101-09:30:00.000  # Sending time
11=ORD001        # Client Order ID
21=1             # Handling instruction (1=automated)
55=AAPL          # Symbol
54=1             # Side (1=Buy)
60=20240101-09:30:00.000  # Transaction time
38=100           # Order quantity
40=2             # Order type (2=Limit)
44=185.00        # Limit price
59=0             # Time in force (0=Day)
10=123           # Checksum
```

### 2.2 Message Layers

```
+-------------------------------------------------------------+
|                     FIX Message Structure                    |
+-------------------------------------------------------------+
|                                                             |
|  +-----------------------------------------------------+   |
|  | Header                                               |   |
|  |   8=Version  9=Length  35=Type  49=Sender  56=Target |   |
|  |   34=SeqNum  52=Time                                 |   |
|  +-----------------------------------------------------+   |
|                            |                               |
|  +-----------------------------------------------------+   |
|  | Body                                                 |   |
|  |   Varies by message type (Tag 35)                    |   |
|  |   Order msg: 55=Symbol 54=Side 38=Qty 44=Price...    |   |
|  +-----------------------------------------------------+   |
|                            |                               |
|  +-----------------------------------------------------+   |
|  | Trailer                                              |   |
|  |   10=Checksum                                        |   |
|  +-----------------------------------------------------+   |
|                                                             |
+-------------------------------------------------------------+
```

---

## 3. Core Message Types

### 3.1 Session Layer Messages

| MsgType (35) | Name | Purpose |
|-------------|------|------|
| A | Logon | Establish session |
| 5 | Logout | Disconnect session |
| 0 | Heartbeat | Connection health check |
| 1 | TestRequest | Test connection |
| 2 | ResendRequest | Request message resend |
| 4 | SequenceReset | Reset sequence number |

### 3.2 Application Layer Messages (Order-related)

| MsgType (35) | Name | Direction | Purpose |
|-------------|------|------|------|
| D | NewOrderSingle | Client -> Broker | Submit new order |
| F | OrderCancelRequest | Client -> Broker | Cancel order |
| G | OrderCancelReplaceRequest | Client -> Broker | Modify order |
| 8 | ExecutionReport | Broker -> Client | Order status/fill report |
| 9 | OrderCancelReject | Broker -> Client | Cancel rejection |

### 3.3 Market Data Messages

| MsgType (35) | Name | Purpose |
|-------------|------|------|
| V | MarketDataRequest | Subscribe to market data |
| W | MarketDataSnapshotFullRefresh | Market data snapshot |
| X | MarketDataIncrementalRefresh | Market data incremental update |

---

## 4. Key Tags Explained

### 4.1 Side (Tag 54)

| Value | Meaning |
|----|------|
| 1 | Buy |
| 2 | Sell |
| 5 | Sell Short |
| 6 | Sell Short Exempt |

### 4.2 Order Type (Tag 40 - OrdType)

| Value | Meaning | Required Tags |
|----|------|---------|
| 1 | Market | None |
| 2 | Limit | 44=Price |
| 3 | Stop | 99=Stop Price |
| 4 | Stop Limit | 44=Limit Price, 99=Stop Price |
| P | Pegged | Specific fields |

### 4.3 Time in Force (Tag 59)

| Value | Meaning | Description |
|----|------|------|
| 0 | Day | Valid for current day |
| 1 | GTC | Good Till Cancel |
| 2 | OPG | At the Opening |
| 3 | IOC | Immediate or Cancel |
| 4 | FOK | Fill or Kill |
| 6 | GTD | Good Till Date |

### 4.4 Order Status (Tag 39 - OrdStatus)

| Value | Meaning |
|----|------|
| 0 | New (Accepted) |
| 1 | Partially Filled |
| 2 | Filled |
| 4 | Canceled |
| 8 | Rejected |
| C | Expired |

### 4.5 Execution Type (Tag 150 - ExecType)

| Value | Meaning |
|----|------|
| 0 | New (Order confirmation) |
| F | Trade (Fill) |
| 4 | Canceled (Cancel confirmation) |
| 8 | Rejected |
| C | Expired |

---

## 5. Typical Message Flows

### 5.1 Normal Order Flow

```
Client                                  Broker
  |                                      |
  | ────── NewOrderSingle (35=D) ──────> |
  |        11=ORD001, 55=AAPL,           |
  |        54=1, 38=100, 40=2, 44=185    |
  |                                      |
  | <── ExecutionReport (35=8) ───────── |
  |     150=0 (New), 39=0 (New)          |
  |     Order accepted                   |
  |                                      |
  | <── ExecutionReport (35=8) ───────── |
  |     150=F (Trade), 39=1 (PartFilled) |
  |     31=185.00, 32=50                 |
  |     50 shares filled at $185.00      |
  |                                      |
  | <── ExecutionReport (35=8) ───────── |
  |     150=F (Trade), 39=2 (Filled)     |
  |     31=185.01, 32=50                 |
  |     Remaining 50 shares at $185.01   |
  |                                      |
```

### 5.2 Cancel Order Flow

```
Client                                  Broker
  |                                      |
  | ── OrderCancelRequest (35=F) ──────> |
  |    11=CANCEL001, 41=ORD001           |
  |    (41=Original Order ID)            |
  |                                      |
  | <── ExecutionReport (35=8) ───────── |
  |     150=4 (Canceled), 39=4           |
  |     Cancel successful                |
  |                                      |

Or cancel rejected:

  | <── OrderCancelReject (35=9) ─────── |
  |     102=1 (Unknown order)            |
  |     Cancel failed: Order not found   |
  |                                      |
```

### 5.3 Session Establishment

```
Client                                  Broker
  |                                      |
  | ────────── Logon (35=A) ───────────> |
  |            98=0 (No encryption)      |
  |            108=30 (Heartbeat 30s)    |
  |                                      |
  | <────────── Logon (35=A) ─────────── |
  |             Session established      |
  |                                      |
  | <───────── Heartbeat (35=0) ──────── |
  |             Every 30 seconds         |
  | ────────── Heartbeat (35=0) ───────> |
  |                                      |
```

---

## 6. Python Implementation Example

### 6.1 Using QuickFIX

```python
import quickfix as fix
import quickfix44 as fix44

class TradingApplication(fix.Application):
    """FIX Trading Application"""

    def __init__(self):
        super().__init__()
        self.session_id = None
        self.order_id = 0

    def onCreate(self, session_id):
        """Session created"""
        self.session_id = session_id
        print(f"Session created: {session_id}")

    def onLogon(self, session_id):
        """Logged in successfully"""
        print(f"Logged on: {session_id}")

    def onLogout(self, session_id):
        """Logged out"""
        print(f"Logged out: {session_id}")

    def toAdmin(self, message, session_id):
        """Callback before sending admin message"""
        pass

    def fromAdmin(self, message, session_id):
        """Admin message received"""
        pass

    def toApp(self, message, session_id):
        """Callback before sending application message"""
        print(f"Sending: {message}")

    def fromApp(self, message, session_id):
        """Application message received"""
        msg_type = fix.MsgType()
        message.getHeader().getField(msg_type)

        if msg_type.getValue() == fix.MsgType_ExecutionReport:
            self._handle_execution_report(message)

    def _handle_execution_report(self, message):
        """Handle execution report"""
        exec_type = fix.ExecType()
        message.getField(exec_type)

        if exec_type.getValue() == fix.ExecType_FILL:
            # Fill report
            order_id = fix.ClOrdID()
            last_px = fix.LastPx()
            last_qty = fix.LastQty()

            message.getField(order_id)
            message.getField(last_px)
            message.getField(last_qty)

            print(f"Fill: {order_id.getValue()} "
                  f"{last_qty.getValue()} @ {last_px.getValue()}")

    def send_new_order(self, symbol: str, side: str,
                       quantity: int, price: float):
        """Send new order"""
        self.order_id += 1
        cl_ord_id = f"ORD{self.order_id:06d}"

        order = fix44.NewOrderSingle()

        # Required fields
        order.setField(fix.ClOrdID(cl_ord_id))
        order.setField(fix.Symbol(symbol))
        order.setField(fix.Side(fix.Side_BUY if side == 'buy'
                                else fix.Side_SELL))
        order.setField(fix.OrderQty(quantity))
        order.setField(fix.OrdType(fix.OrdType_LIMIT))
        order.setField(fix.Price(price))
        order.setField(fix.TimeInForce(fix.TimeInForce_DAY))
        order.setField(fix.TransactTime())

        fix.Session.sendToTarget(order, self.session_id)

        return cl_ord_id

    def cancel_order(self, orig_cl_ord_id: str, symbol: str, side: str):
        """Cancel order"""
        self.order_id += 1
        cl_ord_id = f"CXL{self.order_id:06d}"

        cancel = fix44.OrderCancelRequest()

        cancel.setField(fix.ClOrdID(cl_ord_id))
        cancel.setField(fix.OrigClOrdID(orig_cl_ord_id))
        cancel.setField(fix.Symbol(symbol))
        cancel.setField(fix.Side(fix.Side_BUY if side == 'buy'
                                 else fix.Side_SELL))
        cancel.setField(fix.TransactTime())

        fix.Session.sendToTarget(cancel, self.session_id)

        return cl_ord_id
```

### 6.2 Configuration File

```ini
# fix_client.cfg

[DEFAULT]
ConnectionType=initiator
ReconnectInterval=5
FileStorePath=./store
FileLogPath=./log
StartTime=00:00:00
EndTime=00:00:00
UseDataDictionary=Y
DataDictionary=./FIX44.xml
ValidateUserDefinedFields=N

[SESSION]
BeginString=FIX.4.4
SenderCompID=YOUR_CLIENT_ID
TargetCompID=BROKER_ID
SocketConnectHost=fix.broker.com
SocketConnectPort=9876
HeartBtInt=30
```

### 6.3 Starting the Client

```python
def main():
    settings = fix.SessionSettings("fix_client.cfg")
    application = TradingApplication()
    store_factory = fix.FileStoreFactory(settings)
    log_factory = fix.FileLogFactory(settings)

    initiator = fix.SocketInitiator(
        application, store_factory, settings, log_factory
    )

    initiator.start()

    try:
        # Wait for login
        import time
        time.sleep(2)

        # Send order
        order_id = application.send_new_order(
            symbol="AAPL",
            side="buy",
            quantity=100,
            price=185.00
        )
        print(f"Order submitted: {order_id}")

        # Keep running
        while True:
            time.sleep(1)

    except KeyboardInterrupt:
        initiator.stop()
```

---

## 7. Common Issues and Troubleshooting

### 7.1 Sequence Number Out of Sync

```
Problem: "MsgSeqNum too low, expecting 100 but received 50"

Cause: Sequence numbers between client and server are out of sync

Solutions:
1. Send SequenceReset (35=4) to reset sequence number
2. Or set ResetSeqNumFlag (141=Y) in Logon message
3. Production: Use persistent storage to maintain sequence numbers
```

### 7.2 Heartbeat Timeout

```
Problem: Connection dropped, logs show heartbeat timeout

Cause: Network latency or blocking

Solutions:
1. Check network connection
2. Increase HeartBtInt value (but not too much)
3. Ensure application isn't blocking for extended periods
```

### 7.3 Order Rejected

```
Problem: ExecutionReport shows OrdStatus=8 (Rejected)

Troubleshooting steps:
1. Check Tag 58 (Text) for rejection reason
2. Common causes:
   - Insufficient funds
   - Symbol not found
   - Price out of range
   - Invalid quantity
```

### 7.4 Message Parsing Tools

```python
def parse_fix_message(raw_message: str) -> dict:
    """Parse FIX message to dictionary"""
    # Replace SOH with visible character
    if '\x01' in raw_message:
        raw_message = raw_message.replace('\x01', '|')

    fields = {}
    for pair in raw_message.split('|'):
        if '=' in pair:
            tag, value = pair.split('=', 1)
            fields[int(tag)] = value

    return fields


def format_fix_message(fields: dict) -> str:
    """Format FIX message for printing"""
    tag_names = {
        8: 'BeginString',
        9: 'BodyLength',
        35: 'MsgType',
        49: 'SenderCompID',
        56: 'TargetCompID',
        34: 'MsgSeqNum',
        52: 'SendingTime',
        11: 'ClOrdID',
        55: 'Symbol',
        54: 'Side',
        38: 'OrderQty',
        40: 'OrdType',
        44: 'Price',
        39: 'OrdStatus',
        150: 'ExecType',
        31: 'LastPx',
        32: 'LastQty',
        10: 'CheckSum',
    }

    lines = []
    for tag, value in sorted(fields.items()):
        name = tag_names.get(tag, f'Tag{tag}')
        lines.append(f"  {tag:>3} ({name}): {value}")

    return '\n'.join(lines)
```

---

## 8. Security Considerations

### 8.1 Production Requirements

| Requirement | Description |
|-----|------|
| TLS/SSL | Must use encrypted connections |
| IP Whitelist | Brokers typically restrict connection IPs |
| Certificate Auth | Some brokers require client certificates |
| Sequence Persistence | Prevent sequence conflicts after restart |
| Message Logging | All messages must be logged for audit |

### 8.2 Test Environment

```
1. Most brokers provide UAT (User Acceptance Testing) environment
2. Test all message types in UAT first
3. Simulate various exception scenarios (network disconnect, message out-of-order, etc.)
4. Verify complete order lifecycle
```

---

## 9. Common Misconceptions

**Misconception 1: FIX protocol is too complex, only professional institutions need it**

For quantitative traders connecting directly to brokers, understanding FIX is necessary. Even when using wrapped APIs, understanding the underlying protocol helps with troubleshooting.

**Misconception 2: All brokers' FIX implementations are the same**

Although FIX is a standard protocol, different brokers may have:
- Custom Tags
- Different required field requirements
- Specific message flows

**Misconception 3: Session layer management can be ignored**

Proper handling of the session layer (heartbeat, sequence numbers) is fundamental to stable operation. Ignoring these leads to connection instability and message loss.

---

## 10. Summary

| Key Point | Description |
|-----|------|
| Core Use | Order submission, fill reports, cancellations |
| Message Structure | Header + Body + Trailer, key-value format |
| Key Messages | D (New Order), 8 (Execution Report), F (Cancel) |
| Implementation | QuickFIX is the most commonly used open-source library |
| Production Requirements | TLS, sequence persistence, complete logging |

---

## Extended Reading

- [FIX Protocol Official Website](https://www.fixtrading.org/) - Official documentation and specifications
- [QuickFIX Documentation](https://quickfixengine.org/) - Open-source FIX engine
- [Background: Execution Simulator Implementation](../Part5-Production/Background/Execution-Simulator-Implementation.md) - Execution layer simulation and testing
- [Background: Exchanges and Order Book Mechanics](../Part2-Quant-Fundamentals/Background/Exchanges-and-Order-Book-Mechanics.md) - Order types and matching mechanisms
- [Lesson 19: Execution System](../Part5-Production/Lesson-19-Execution-System.md) - Execution Agent design
