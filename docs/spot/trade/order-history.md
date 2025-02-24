# Order History

Query the history of spot trading orders. As order creation/cancellation is asynchronous, the data returned from this endpoint may be delayed.
For real-time order information, use the WebSocket order stream (recommended).

:::info Important
- Orders in the last 7 days: All order statuses except "Cancelled", "Rejected", "Deactivated"
- Orders in the last 24 hours: "Cancelled", "Rejected", "Deactivated" orders are available
- Orders beyond 7 days: Only orders with fills (fully filled or partially filled but cancelled)
- Data retention: Up to 2 years of historical data
:::

## Endpoint

```
GET /v5/order/history
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
| orderFilter | No | string | Order filter: `Order` (active orders), `StopOrder` (conditional orders), `tpslOrder` (TP/SL orders), `OcoOrder` (OCO orders), `BidirectionalTpslOrder` (bidirectional TPSL orders) |
| orderStatus | No | string | Order status. Returns all closed status orders if not specified |
| startTime | No | integer | Start timestamp (ms). Default: 7 days ago |
| endTime | No | integer | End timestamp (ms). Default: current time |
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

### Query Order History

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
    "orderStatus": "Filled"
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
    "https://api-testnet.bybit.com/v5/order/history",
    headers=headers,
    params=params
)

print(response.json())
```

### Query Orders by Base Coin

```python
params = {
    "category": "spot",
    "baseCoin": "BTC",
    "limit": 50
}
```

### Query Orders by Date Range

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "startTime": 1672506400000,  # 2023-01-01
    "endTime": 1672592800000,    # 2023-01-02
    "limit": 50
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
                "orderStatus": "Filled",
                "cancelType": "UNKNOWN",
                "rejectReason": "EC_NoError",
                "avgPrice": "35000",
                "leavesQty": "0",
                "leavesValue": "0",
                "cumExecQty": "0.001",
                "cumExecValue": "35",
                "cumExecFee": "0.00001",
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

1. **Data Retrieval**
   - Use appropriate time ranges to optimize query performance
   - Implement pagination for large result sets
   - Cache frequently accessed data

2. **Error Handling**
   - Handle rate limiting errors
   - Implement exponential backoff for retries
   - Validate response data

3. **Order Tracking**
   - Store order IDs and link IDs
   - Track order status changes
   - Monitor execution details

4. **Performance**
   - Use WebSocket for real-time updates
   - Batch queries when possible
   - Optimize data storage and retrieval
