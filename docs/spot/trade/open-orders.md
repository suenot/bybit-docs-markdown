# Open Orders

Query unfilled or partially filled orders in real-time. Also supports querying recent closed orders (Cancelled, Filled) for UTA accounts.
For older order records, use the order history endpoint.

:::info Important
- UTA accounts can query up to 500 recent closed orders (Filled, Cancelled, Rejected)
- You can query by symbol, baseCoin, orderId and orderLinkId
- Priority: orderId > orderLinkId > symbol > baseCoin
- Records are sorted by creation time from newest to oldest
- After server restart, closed orders should be queried through order history endpoint
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
| symbol | No | string | Trading pair name, e.g., `BTCUSDT` |
| baseCoin | No | string | Base coin, e.g., `BTC` |
| orderId | No | string | Order ID |
| orderLinkId | No | string | Custom order ID |
| openOnly | No | integer | `0`: Query open orders only (default)<br/>`1`: Query recent 500 closed orders (UTA accounts only) |
| orderFilter | No | string | Order filter: `Order` (active orders), `StopOrder` (conditional orders), `tpslOrder` (TP/SL orders), `OcoOrder` (OCO orders), `BidirectionalTpslOrder` (bidirectional TPSL orders) |
| limit | No | integer | Limit for data size per page. Range: [1, 50]. Default: 20 |
| cursor | No | string | Cursor for pagination |

### Response Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| category | string | Product type |
| list | array | Array of order objects |
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
| nextPageCursor | string | Cursor for next page |

## Examples

### Query Open Orders

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
    "limit": 50,
    "openOnly": 0
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

### Query Recent Closed Orders (UTA Only)

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "limit": 50,
    "openOnly": 1
}
```

### Query TP/SL Orders

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "orderFilter": "tpslOrder",
    "limit": 50
}
```

### Query Conditional Orders

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "orderFilter": "StopOrder",
    "limit": 50
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
        ],
        "nextPageCursor": "eyJza2lwX2xvY2FsX3N5bWJvbCI6ZmFsc2UsInBhZ2VfdG9rZW4iOiJleUpoYkdjaU9pSklVekkxTmlJc0luUjVjQ0k2SWtwWFZDSjkuZXlKbGVIQWlPakUyT0RFeE1qYzJNREFzSW1saGRDSTZNVFk0TVRFME5EWXdNQ3dpYVhOeklqb2lZbTl2YkdWaGJpSXNJbk4xWWlJNklrSlVRMVZUUkZRaUxDSnlaV1lp"
    }
}
```

## Order Types

### Active Orders
- Set `orderFilter="Order"` (default)
- Regular limit and market orders
- No special handling required

### TP/SL Orders
- Set `orderFilter="tpslOrder"`
- Take profit/stop loss orders
- Requires trigger price

### Conditional Orders
- Set `orderFilter="StopOrder"`
- Orders that execute at specified conditions
- Requires trigger price

### OCO Orders
- Set `orderFilter="OcoOrder"`
- One-Cancels-Other orders
- Two orders linked together

### Bidirectional TPSL Orders
- Set `orderFilter="BidirectionalTpslOrder"`
- Take profit and stop loss in both directions
- Requires trigger prices

## Best Practices

1. **Real-time Monitoring**
   - Use WebSocket for live order updates
   - Implement order status tracking
   - Handle order state transitions

2. **Error Handling**
   - Handle rate limiting errors
   - Implement exponential backoff
   - Validate response data

3. **Order Management**
   - Track order IDs and link IDs
   - Monitor partial fills
   - Update local order state

4. **Performance**
   - Use appropriate filters
   - Implement pagination
   - Cache frequently used data
