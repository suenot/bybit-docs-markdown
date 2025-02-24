# Position Stream

Subscribe to real-time position updates for your account.

## Topics

### All-In-One Topic
```
position
```
Receive updates for all categories (linear, inverse, option)

### Categorised Topics
```
position.linear    // Linear positions only
position.inverse   // Inverse positions only
position.option    // Option positions only
```

> **Note:** You cannot subscribe to All-In-One and Categorised topics in the same request.

## Message Format

```typescript
{
    "id": string,           // Message ID
    "topic": string,        // Topic name
    "creationTime": number, // Data timestamp (ms)
    "data": {
        "category": string,      // Product type
        "symbol": string,        // Symbol name
        "side": string,         // Position side: "Buy"(long), "Sell"(short), ""(empty)
        "size": string,         // Position size
        "positionIdx": number,  // Position index
        "tradeMode": number,    // Trade mode (0: cross, 1: isolated)
        "positionValue": string, // Position value
        "riskId": number,       // Risk tier ID
        "riskLimitValue": string, // Risk limit value
        "entryPrice": string,   // Entry price
        "markPrice": string,    // Mark price
        "leverage": string,     // Position leverage
        "positionBalance": string, // Position margin
        "autoAddMargin": number, // Auto-add margin (0: false, 1: true)
        "positionIM": string,   // Initial margin
        "positionMM": string,   // Maintenance margin
        "liqPrice": string,     // Liquidation price
        "bustPrice": string,    // Bankruptcy price
        "tpslMode": string,     // TP/SL mode
        "takeProfit": string,   // Take profit price
        "stopLoss": string,     // Stop loss price
        "trailingStop": string, // Trailing stop
        "unrealisedPnl": string, // Unrealised PnL
        "curRealisedPnl": string, // Current realised PnL
        "sessionAvgPrice": string, // Session average price
        "delta": string,        // Option delta
        "gamma": string,        // Option gamma
        "vega": string,         // Option vega
        "theta": string,        // Option theta
        "cumRealisedPnl": string, // Cumulative realised PnL
        "positionStatus": string, // Position status
        "adlRankIndicator": number, // Auto-deleverage rank
        "isReduceOnly": boolean // Is reduce-only position
    }
}
```

## Implementation Example (Python)

```python
from pybit.unified_trading import WebSocket
from time import sleep

def process_position_update(message):
    """Process position update messages"""
    data = message["data"]
    
    print(f"""
        Position Update:
        Symbol: {data['symbol']}
        Side: {data['side']}
        Size: {data['size']}
        Entry Price: {data['entryPrice']}
        Mark Price: {data['markPrice']}
        Unrealised PnL: {data['unrealisedPnl']}
        Leverage: {data['leverage']}
    """)

    # Handle specific position statuses
    if data["positionStatus"] == "Liq":
        handle_liquidation(data)
    elif data["positionStatus"] == "Adl":
        handle_auto_deleveraging(data)
    
    # Monitor risk levels
    monitor_risk_levels(data)

def handle_liquidation(data):
    """Handle position liquidation"""
    print(f"""
        Position Liquidation:
        Symbol: {data['symbol']}
        Liquidation Price: {data['liqPrice']}
        Bankruptcy Price: {data['bustPrice']}
    """)

def handle_auto_deleveraging(data):
    """Handle auto-deleveraging"""
    print(f"""
        Auto-Deleveraging:
        Symbol: {data['symbol']}
        ADL Rank: {data['adlRankIndicator']}
    """)

def monitor_risk_levels(data):
    """Monitor position risk levels"""
    # Calculate margin ratio
    if float(data['positionIM']) > 0:
        margin_ratio = float(data['positionBalance']) / float(data['positionIM'])
        print(f"Margin Ratio: {margin_ratio}")
    
    # Check if approaching liquidation
    if data['liqPrice'] and float(data['markPrice']) * 0.9 < float(data['liqPrice']):
        print("Warning: Position approaching liquidation price")

# Initialize WebSocket connection
ws = WebSocket(
    testnet=True,
    api_key="YOUR_API_KEY",
    api_secret="YOUR_API_SECRET"
)

# Subscribe to position updates
ws.position_stream(
    callback=process_position_update
)

# Keep connection alive
while True:
    sleep(1)
```

## Important Notes

1. **Position Updates**
   - Updates occur on any order action (create/amend/cancel)
   - Updates may occur even without actual position changes
   - Side field behavior varies by account type and mode

2. **Account Types**
   - UTA 2.0: Full support for all features
   - UTA 1.0: Some limitations for inverse positions
   - Classic: Limited feature set

3. **Margin Modes**
   - Cross margin: Shared margin across positions
   - Isolated margin: Separate margin per position
   - Portfolio margin: Some fields return empty or zero

4. **Risk Management**
   - Monitor liquidation price
   - Track ADL rank
   - Watch margin ratios
   - Check risk limits

## Best Practices

1. **Position Tracking**
   - Maintain position state locally
   - Update on WebSocket messages
   - Validate position changes

2. **Risk Monitoring**
   - Monitor margin levels
   - Track liquidation prices
   - Watch ADL indicators
   - Check leverage limits

3. **Performance**
   - Process updates asynchronously
   - Buffer messages if needed
   - Monitor processing time

4. **Error Handling**
   - Handle missing fields
   - Validate calculations
   - Monitor connection status
