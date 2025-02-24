# Order Stream

Subscribe to real-time order updates for your account.

## Topics

### All-In-One Topic
```
order
```
Receive updates for all categories (spot, linear, inverse, option)

### Categorised Topics
```
order.spot      // Spot orders only
order.linear    // Linear orders only
order.inverse   // Inverse orders only
order.option    // Option orders only
```

> **Note:** You cannot subscribe to All-In-One and Categorised topics in the same request.

## Message Format

```typescript
{
    "id": string,           // Message ID
    "topic": string,        // Topic name
    "creationTime": number, // Data timestamp (ms)
    "data": {
        "category": string,        // Product type
        "orderId": string,        // Order ID
        "orderLinkId": string,    // User custom order ID
        "isLeverage": string,     // Whether to borrow (Unified spot only)
        "blockTradeId": string,   // Block trade ID
        "symbol": string,         // Symbol name
        "price": string,          // Order price
        "qty": string,            // Order quantity
        "side": string,           // "Buy" or "Sell"
        "positionIdx": number,    // Position index
        "orderStatus": string,    // Order status
        "createType": string,     // Order create type (linear/inverse only)
        "cancelType": string,     // Cancel type
        "rejectReason": string,   // Reject reason
        "avgPrice": string,       // Average filled price
        "leavesQty": string,      // Remaining quantity
        "leavesValue": string,    // Remaining value
        "cumExecQty": string,     // Cumulative executed quantity
        "cumExecValue": string,   // Cumulative executed value
        "cumExecFee": string,     // Cumulative executed fee
        "closedPnl": string,      // Closed PnL
        "feeCurrency": string,    // Fee currency (spot only)
        "timeInForce": string,    // Time in force
        "orderType": string,      // Order type
        "stopOrderType": string,  // Stop order type
        "ocoTriggerBy": string,   // OCO trigger type (Unified spot only)
        "orderIv": string,        // Implied volatility
        "marketUnit": string,     // Market order unit (Unified spot only)
        "triggerPrice": string,   // Trigger price
        "takeProfit": string,     // Take profit price
        "stopLoss": string,       // Stop loss price
        "tpslMode": string,       // TP/SL mode
        "tpLimitPrice": string,   // TP limit price
        "slLimitPrice": string,   // SL limit price
        "tpTriggerBy": string,    // TP trigger type
        "slTriggerBy": string,    // SL trigger type
        "triggerDirection": number, // 1: rise, 2: fall
        "triggerBy": string,      // Trigger price type
        "lastPriceOnCreated": string // Last price when order placed
    }
}
```

## Implementation Example (Python)

```python
from pybit.unified_trading import WebSocket
from time import sleep

def process_order_update(message):
    """Process order update messages"""
    data = message["data"]
    
    print(f"""
        Order Update:
        Symbol: {data['symbol']}
        Order ID: {data['orderId']}
        Status: {data['orderStatus']}
        Side: {data['side']}
        Price: {data['price']}
        Quantity: {data['qty']}
        Executed Qty: {data['cumExecQty']}
        Average Price: {data['avgPrice']}
    """)

    # Handle specific order statuses
    if data["orderStatus"] == "Filled":
        handle_filled_order(data)
    elif data["orderStatus"] == "Cancelled":
        handle_cancelled_order(data)
    elif data["orderStatus"] == "Rejected":
        handle_rejected_order(data)

def handle_filled_order(data):
    """Handle filled orders"""
    print(f"""
        Order Filled:
        Execution Fee: {data['cumExecFee']}
        Total Value: {data['cumExecValue']}
        Closed PnL: {data['closedPnl']}
    """)

def handle_cancelled_order(data):
    """Handle cancelled orders"""
    print(f"""
        Order Cancelled:
        Cancel Type: {data['cancelType']}
        Remaining Qty: {data['leavesQty']}
    """)

def handle_rejected_order(data):
    """Handle rejected orders"""
    print(f"""
        Order Rejected:
        Reason: {data['rejectReason']}
    """)

# Initialize WebSocket connection
ws = WebSocket(
    testnet=True,
    api_key="YOUR_API_KEY",
    api_secret="YOUR_API_SECRET"
)

# Subscribe to order updates
ws.order_stream(
    callback=process_order_update
)

# Keep connection alive
while True:
    sleep(1)
```

## Important Notes

1. **Order Status Updates**
   - You may receive two "Filled" status messages when a cancel request coincides with execution
   - First message: "orderStatus=Filled, rejectReason=EC_NoError"
   - Second message: "orderStatus=Filled, cancelType=CancelByUser, rejectReason=EC_OrigClOrdIDDoesNotExist"

2. **Account Types**
   - UTA 2.0 & 1.0: Supports all categories (spot, linear, inverse, option)
   - Classic account: Supports spot, linear, inverse only

3. **Field Availability**
   - Some fields are specific to certain categories or account types
   - Classic spot accounts have limited field support
   - Unified accounts have additional features like leverage and OCO orders

## Best Practices

1. **Order Tracking**
   - Maintain order state locally
   - Update state based on WebSocket messages
   - Handle all possible order statuses

2. **Error Handling**
   - Monitor reject reasons
   - Handle cancel type scenarios
   - Process execution reports properly

3. **Performance**
   - Process messages asynchronously
   - Buffer updates if needed
   - Monitor message processing time

4. **Resource Management**
   - Clean up old order data
   - Monitor memory usage
   - Handle reconnection scenarios
