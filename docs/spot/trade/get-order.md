# Get Order

Query a specific order's details using either the order ID or custom order ID.

:::info Important
- You must specify either `orderId` or `orderLinkId`
- If both are provided, the system will prioritize `orderId`
- For real-time order updates, consider using the WebSocket order stream
:::

## Endpoint

```
GET /v5/order/realtime
```

## Parameters

### Request Parameters

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | Product type. For spot trading: `spot` |
| symbol | Yes | string | Trading pair name, e.g., `BTCUSDT` |
| orderId | No* | string | Order ID (*Required if orderLinkId not provided) |
| orderLinkId | No* | string | Custom order ID (*Required if orderId not provided) |
| orderFilter | No | string | Order filter: `Order` (active orders), `StopOrder` (conditional orders), `tpslOrder` (TP/SL orders), `OcoOrder` (OCO orders), `BidirectionalTpslOrder` (bidirectional TPSL orders) |

### Response Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| category | string | Product type |
| list | array | Array containing single order object |
|> orderId | string | Order ID |
|> orderLinkId | string | Custom order ID |
|> symbol | string | Trading pair name |
|> price | string | Order price |
|> qty | string | Order quantity |
|> side | string | Order side: `Buy` or `Sell` |
|> isLeverage | string | Whether margin is used. `0`: no, `1`: yes |
|> orderStatus | string | Order status |
|> cancelType | string | Cancel type |
|> rejectReason | string | Reject reason |
|> avgPrice | string | Average filled price |
|> leavesQty | string | Remaining quantity not executed |
|> leavesValue | string | Remaining value not executed |
|> cumExecQty | string | Cumulative executed quantity |
|> cumExecValue | string | Cumulative executed value |
|> cumExecFee | string | Cumulative executed fee |
|> timeInForce | string | Time in force |
|> orderType | string | Order type |
|> stopOrderType | string | Stop order type |
|> orderIv | string | Implied volatility |
|> triggerPrice | string | Trigger price |
|> takeProfit | string | Take profit price |
|> stopLoss | string | Stop loss price |
|> tpTriggerBy | string | Take profit trigger type |
|> slTriggerBy | string | Stop loss trigger type |
|> createTime | string | Order creation time |
|> updateTime | string | Last update time |

## Examples

### Query Order by Order ID

```python
import requests
import time
import hashlib
import hmac

# Authentication
api_key = "YOUR_API_KEY"
api_secret = "YOUR_API_SECRET"
timestamp = str(int(time.time() * 1000))
recv_window = "5000"

# Request parameters
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "orderId": "1321003749386327552"
}

# Generate signature
param_str = timestamp + api_key + recv_window + str(params)
signature = hmac.new(
    bytes(api_secret, "utf-8"),
    param_str.encode("utf-8"),
    hashlib.sha256
).hexdigest()

# Headers
headers = {
    "X-BAPI-API-KEY": api_key,
    "X-BAPI-SIGN": signature,
    "X-BAPI-TIMESTAMP": timestamp,
    "X-BAPI-RECV-WINDOW": recv_window,
    "Content-Type": "application/json"
}

# Send request
response = requests.get(
    "https://api-testnet.bybit.com/v5/order/realtime",
    headers=headers,
    params=params
)

print(response.json())
```

### Query Order by Order Link ID

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "orderLinkId": "spot-test-order"
}
```

### Query TP/SL Order

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "orderId": "1321003749386327552",
    "orderFilter": "tpslOrder"
}
```

## Response Example

```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "list": [
            {
                "orderId": "1321003749386327552",
                "orderLinkId": "spot-test-order",
                "symbol": "BTCUSDT",
                "price": "35000",
                "qty": "0.001",
                "side": "Buy",
                "isLeverage": "0",
                "orderStatus": "New",
                "cancelType": "UNKNOWN",
                "rejectReason": "EC_NoError",
                "avgPrice": "",
                "leavesQty": "0.001",
                "leavesValue": "35",
                "cumExecQty": "0",
                "cumExecValue": "0",
                "cumExecFee": "0",
                "timeInForce": "GTC",
                "orderType": "LIMIT",
                "stopOrderType": "UNKNOWN",
                "orderIv": "",
                "triggerPrice": "",
                "takeProfit": "",
                "stopLoss": "",
                "tpTriggerBy": "",
                "slTriggerBy": "",
                "createTime": "1672506400000",
                "updateTime": "1672506400000"
            }
        ]
    }
}
```

## Order Status

| Status | Description |
|--------|-------------|
| Created | Order created but not yet active |
| New | Order placed and active |
| Rejected | Order rejected |
| PartiallyFilled | Order partially filled |
| Filled | Order fully filled |
| Cancelled | Order cancelled |
| PendingCancel | Order is being cancelled |
| Deactivated | Conditional order deactivated |

## Best Practices

1. **Order Identification**
   - Store both orderId and orderLinkId
   - Use consistent orderLinkId format
   - Track order status changes

2. **Error Handling**
   - Handle "order not found" gracefully
   - Validate response data
   - Implement retry logic

3. **Real-time Updates**
   - Use WebSocket for live updates
   - Monitor order status changes
   - Track execution progress

4. **Performance**
   - Cache frequently queried orders
   - Implement request throttling
   - Handle rate limits properly
