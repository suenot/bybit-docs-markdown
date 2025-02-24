# Place Order

Create a new spot trading order.

:::info Important
- For UTA (Unified Trading Account) and Classic accounts, the system logic for stop orders is different:
  - Classic account: When a stop order is created, you get an order ID. After triggering, you get a new order ID
  - UTA account: When a stop order is created, you get an order ID. After triggering, the order ID remains unchanged
- Market Buy orders: By default, the quantity is in quote currency (e.g., USDT). You can specify `marketUnit` to choose between base or quote currency
- Order limits: Maximum 500 orders total, including up to 30 open TP/SL orders and 30 conditional orders per symbol
:::

## Endpoint

```
POST /v5/order/create
```

## Parameters

### Request Parameters

| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | Product type. For spot trading: `spot` |
| symbol | Yes | string | Trading pair name, e.g., `BTCUSDT` |
| side | Yes | string | Order side: `Buy` or `Sell` |
| orderType | Yes | string | Order type: `Market` or `Limit` |
| qty | Yes | string | Order quantity. For Market Buy orders, by default in quote currency |
| marketUnit | No | string | For Market orders only. Unit for qty: `baseCoin` or `quoteCoin` |
| price | No | string | Order price. Required for Limit orders |
| timeInForce | No | string | Time in force. Default: `GTC`. Options: `GTC`, `IOC`, `FOK`, `PostOnly` |
| orderLinkId | No | string | Custom order ID. Max 36 characters |
| isLeverage | No | integer | For margin trading: `0` (spot), `1` (margin) |
| orderFilter | No | string | Order filter: `Order` (default), `tpslOrder` (TP/SL), `StopOrder` (conditional) |
| triggerPrice | No | string | Trigger price for TP/SL and conditional orders |
| takeProfit | No | string | Take profit price. UTA only |
| stopLoss | No | string | Stop loss price. UTA only |

### Response Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| orderId | string | Order ID |
| orderLinkId | string | Custom order ID |
| symbol | string | Trading pair name |
| createTime | string | Order creation timestamp |
| orderPrice | string | Order price |
| orderQty | string | Order quantity |
| orderType | string | Order type |
| side | string | Order side |
| status | string | Order status |
| timeInForce | string | Time in force |
| accountId | string | Account ID |
| execQty | string | Executed quantity |
| triggerPrice | string | Trigger price if applicable |

## Examples

### Place a Limit Order

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
    "side": "Buy",
    "orderType": "Limit",
    "qty": "0.001",
    "price": "35000",
    "timeInForce": "GTC",
    "orderLinkId": "spot-test-limit"
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
    "https://api-testnet.bybit.com/v5/order/create",
    headers=headers,
    json=params
)

print(response.json())
```

### Place a Market Order (by Quote Currency)

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "side": "Buy",
    "orderType": "Market",
    "qty": "100",  # Buy $100 worth of BTC
    "marketUnit": "quoteCoin"
}
```

### Place a Market Order (by Base Currency)

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "side": "Buy",
    "orderType": "Market",
    "qty": "0.001",  # Buy 0.001 BTC
    "marketUnit": "baseCoin"
}
```

### Place a TP/SL Order

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "side": "Buy",
    "orderType": "Limit",
    "qty": "0.001",
    "price": "35000",
    "timeInForce": "GTC",
    "orderFilter": "tpslOrder",
    "triggerPrice": "40000",  # Trigger at $40,000
    "orderLinkId": "spot-test-tpsl"
}
```

### Place a Margin Order

```python
params = {
    "category": "spot",
    "symbol": "BTCUSDT",
    "side": "Buy",
    "orderType": "Limit",
    "qty": "0.001",
    "price": "35000",
    "timeInForce": "GTC",
    "isLeverage": 1,  # Enable margin trading
    "orderLinkId": "spot-test-margin"
}
```

## Response Example

```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "orderId": "1321003749386327552",
        "orderLinkId": "spot-test-limit"
    }
}
```

## Order Types

### Limit Orders
- Requires both quantity and price
- Can be combined with various time in force options
- Supports TP/SL for UTA accounts

### Market Orders
- Executes immediately at best available price
- Quantity can be in base or quote currency
- Uses IOC time in force automatically

### TP/SL Orders
- Set `orderFilter="tpslOrder"`
- Assets are reserved immediately
- Requires trigger price

### Conditional Orders
- Set `orderFilter="StopOrder"`
- Assets not reserved until triggered
- Requires trigger price

## Time in Force Options

| Option | Description |
|--------|-------------|
| GTC | Good Till Cancel - order remains active until cancelled |
| IOC | Immediate or Cancel - fills what it can, cancels the rest |
| FOK | Fill or Kill - fills entire order or cancels completely |
| PostOnly | Ensures maker order - cancels if would match immediately |

## Common Errors

| Error Code | Description | Solution |
|------------|-------------|----------|
| 10001 | Parameter error | Check request parameters |
| 10002 | Insufficient balance | Ensure sufficient funds |
| 10003 | Invalid signature | Verify signature generation |
| 10004 | Unknown order type | Check orderType parameter |
| 10005 | Too many open orders | Stay within order limits |

## Best Practices

1. **Order Management**
   - Use unique orderLinkId for tracking
   - Monitor order status via WebSocket
   - Handle partial fills appropriately

2. **Risk Management**
   - Set appropriate TP/SL levels
   - Monitor account balance
   - Use market orders carefully

3. **Error Handling**
   - Implement proper error handling
   - Add request retries
   - Log all responses

4. **Rate Limiting**
   - Stay within API rate limits
   - Implement request throttling
   - Handle rate limit errors
