# Execution Stream

Subscribe to real-time execution updates for your trades.

## Topics

### All-In-One Topic
```
execution
```
Receive updates for all categories (spot, linear, inverse, option)

### Categorised Topics
```
execution.spot      // Spot executions only
execution.linear    // Linear executions only
execution.inverse   // Inverse executions only
execution.option    // Option executions only
```

> **Note:** You cannot subscribe to All-In-One and Categorised topics in the same request.

## Message Format

```typescript
{
    "id": string,           // Message ID
    "topic": string,        // Topic name
    "creationTime": number, // Data timestamp (ms)
    "data": {
        "category": string,       // Product type
        "symbol": string,         // Symbol name
        "isLeverage": string,     // Whether to borrow (Unified spot only)
        "orderId": string,        // Order ID
        "orderLinkId": string,    // User custom order ID
        "side": string,           // "Buy" or "Sell"
        "orderPrice": string,     // Order price
        "orderQty": string,       // Order quantity
        "leavesQty": string,      // Remaining quantity
        "createType": string,     // Order create type
        "orderType": string,      // Order type
        "stopOrderType": string,  // Stop order type
        "execFee": string,        // Execution fee
        "execId": string,         // Execution ID
        "execPrice": string,      // Execution price
        "execQty": string,        // Execution quantity
        "execPnl": string,        // Execution PnL
        "execType": string,       // Execution type
        "execValue": string,      // Execution value
        "execTime": string,       // Execution timestamp
        "isMaker": boolean,       // Is maker order
        "feeRate": string,        // Fee rate
        "tradeIv": string,        // Option implied volatility
        "markIv": string,         // Mark price implied volatility
        "markPrice": string,      // Mark price
        "indexPrice": string,     // Index price
        "underlyingPrice": string, // Underlying price
        "blockTradeId": string,   // Block trade ID
        "closedSize": string,     // Closed position size
        "seq": number            // Cross sequence
    }
}
```

## Implementation Example (Python)

```python
from pybit.unified_trading import WebSocket
from time import sleep

def process_execution(message):
    """Process execution messages"""
    data = message["data"]
    
    print(f"""
        Execution:
        Symbol: {data['symbol']}
        Order ID: {data['orderId']}
        Side: {data['side']}
        Price: {data['execPrice']}
        Quantity: {data['execQty']}
        Value: {data['execValue']}
        Fee: {data['execFee']}
        Is Maker: {data['isMaker']}
        Time: {data['execTime']}
    """)

    # Handle specific execution types
    if data["execType"] == "Trade":
        handle_trade_execution(data)
    elif data["execType"] == "Funding":
        handle_funding_execution(data)
    elif data["execType"] == "AdlTrade":
        handle_adl_execution(data)
        
    # Track PnL
    if data["execPnl"]:
        track_pnl(data)

def handle_trade_execution(data):
    """Handle regular trade executions"""
    print(f"""
        Trade Execution:
        PnL: {data['execPnl']}
        Closed Size: {data['closedSize']}
        Fee Rate: {data['feeRate']}
    """)

def handle_funding_execution(data):
    """Handle funding fee executions"""
    print(f"Funding Fee: {data['execFee']}")

def handle_adl_execution(data):
    """Handle auto-deleveraging executions"""
    print(f"""
        ADL Execution:
        Closed Size: {data['closedSize']}
        PnL: {data['execPnl']}
    """)

def track_pnl(data):
    """Track execution PnL"""
    print(f"""
        PnL Update:
        Symbol: {data['symbol']}
        PnL: {data['execPnl']}
        Mark Price: {data['markPrice']}
    """)

# Initialize WebSocket connection
ws = WebSocket(
    testnet=True,
    api_key="YOUR_API_KEY",
    api_secret="YOUR_API_SECRET"
)

# Subscribe to execution updates
ws.execution_stream(
    callback=process_execution
)

# Keep connection alive
while True:
    sleep(1)
```

## Important Notes

1. **Execution Updates**
   - Multiple executions may occur for one order
   - Each execution has a unique execId
   - Cross sequence (seq) helps track related updates

2. **Account Types**
   - UTA 2.0 & 1.0: Full support for all categories
   - Classic account: Limited support for spot trading
   - Some fields are specific to certain categories

3. **Fee Calculation**
   - Fee rates vary by maker/taker
   - Spot trading has specific fee currency rules
   - Fee calculation may differ by account type

4. **Options Trading**
   - Additional fields for options (IV, Greeks)
   - Underlying price information available
   - Mark and index price tracking

## Best Practices

1. **Execution Tracking**
   - Track all executions by execId
   - Associate executions with orders
   - Monitor execution sequences

2. **Fee Management**
   - Calculate and track trading fees
   - Monitor fee rates
   - Handle different fee currencies

3. **Performance**
   - Process executions asynchronously
   - Buffer updates if needed
   - Monitor processing time

4. **Data Analysis**
   - Track execution statistics
   - Monitor trading costs
   - Analyze PnL performance
