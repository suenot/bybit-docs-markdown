# Cancel Order

Cancel an existing spot trading order.

:::info Important
- You must specify either `orderId` or `orderLinkId` to cancel the order
- If both are provided, the system will prioritize `orderId`
- You can only cancel unfilled or partially filled orders
- The acknowledgment of a cancel request means the request was accepted. Use WebSocket order stream to confirm the actual order status
:::

## Endpoint

```
POST /v5/order/cancel
```

## Parameters

### Request Parameters

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | Product type. For spot trading: `spot` |
| symbol | Yes | string | Trading pair name, e.g., `BTCUSDT` |
| orderId | No* | string | Order ID (*Required if orderLinkId not provided) |
| orderLinkId | No* | string | Custom order ID (*Required if orderId not provided) |
| orderFilter | No | string | Order filter: `Order` (default), `tpslOrder`, `StopOrder` |

### Response Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| orderId | string | Order ID |
| orderLinkId | string | Custom order ID |

## Examples

### Cancel Order by Order ID

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
    "orderId": "1523347543495541248"
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
response = requests.post(
    "https://api-testnet.bybit.com/v5/order/cancel",
    headers=headers,
    json=params
)

print(response.json())
```

### Cancel Order by Order Link ID

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "orderLinkId": "spot-test-cancel"
}
```

### Cancel TP/SL Order

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "orderId": "1523347543495541248",
    "orderFilter": "tpslOrder"
}
```

### Cancel Conditional Order

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "orderId": "1523347543495541248",
    "orderFilter": "StopOrder"
}
```

## Response Example

```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "orderId": "1523347543495541248",
        "orderLinkId": "spot-test-cancel"
    }
}
```

## Order Types

### Normal Orders
- Set `orderFilter="Order"` (default)
- Regular limit and market orders
- No special handling required

### TP/SL Orders
- Set `orderFilter="tpslOrder"`
- For cancelling take profit/stop loss orders
- Must match the filter used when creating the order

### Conditional Orders
- Set `orderFilter="StopOrder"`
- For cancelling conditional orders
- Must match the filter used when creating the order

## Common Errors

| Error Code | Description | Solution |
|------------|-------------|----------|
| 10001 | Order not found | Verify orderId/orderLinkId |
| 10002 | Order already cancelled | No action needed |
| 10003 | Order already filled | Cannot cancel filled orders |
| 10004 | Invalid order status | Order cannot be cancelled in current state |
| 10005 | Invalid parameters | Check request parameters |

## Best Practices

1. **Order Identification**
   - Store both orderId and orderLinkId
   - Use consistent orderLinkId format
   - Track order status changes

2. **Error Handling**
   - Handle "order not found" gracefully
   - Verify order status before cancelling
   - Implement retry logic for network issues

3. **Rate Limiting**
   - Monitor API rate limits
   - Implement request throttling
   - Batch cancellations when possible

4. **Order Tracking**
   - Update local order state after cancellation
   - Monitor WebSocket for order status changes
   - Verify cancellation through order status endpoint
