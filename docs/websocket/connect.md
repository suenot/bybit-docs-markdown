# WebSocket API

The Bybit WebSocket API provides real-time market data and account updates. There are three types of WebSocket streams:

## Endpoints

### Public Streams
**Mainnet:**
- Spot: `wss://stream.bybit.com/v5/public/spot`
- USDT & USDC Perpetual/Futures: `wss://stream.bybit.com/v5/public/linear`
- Inverse Contract: `wss://stream.bybit.com/v5/public/inverse`
- USDC Option: `wss://stream.bybit.com/v5/public/option`

**Testnet:**
- Spot: `wss://stream-testnet.bybit.com/v5/public/spot`
- USDT & USDC Perpetual: `wss://stream-testnet.bybit.com/v5/public/linear`
- Inverse Contract: `wss://stream-testnet.bybit.com/v5/public/inverse`
- USDC Option: `wss://stream-testnet.bybit.com/v5/public/option`

### Private Streams
**Mainnet:**
- `wss://stream.bybit.com/v5/private`

**Testnet:**
- `wss://stream-testnet.bybit.com/v5/private`

### Trade Streams (Order Entry)
**Mainnet:**
- `wss://stream.bybit.com/v5/trade`

**Testnet:**
- `wss://stream-testnet.bybit.com/v5/trade`

> **Note:** If your account is registered from www.bybit-tr.com, use `stream.bybit-tr.com` for mainnet access.
> If your account is registered from www.bybit.kz, use `stream.bybit.kz` for mainnet access.

## Connection Settings

### Private Connection Duration
For private streams and order entry, you can customize the connection alive duration:
- Use the `max_active_time` parameter
- Minimum value: `30s` (30 seconds)
- Maximum value: `600s` (10 minutes)
- Can use minute format: `1m`, `2m`, etc.

Example:
```
wss://stream-testnet.bybit.com/v5/private?max_active_time=1m
```

By default, if there is no "ping-pong" and no stream data, the connection will close after 10 minutes.

## Authentication

> Public topics do not require authentication. Authentication is only required for private topics.

### Authentication Process

When establishing a private connection, send an authentication request:

```json
{
    "req_id": "10001",  // optional
    "op": "auth",
    "args": [
        "api_key",
        1662350400000,  // expires timestamp
        "signature"
    ]
}
```

### Authentication Example (Python)

```python
import hmac
import json
import time
import websocket

api_key = "YOUR_API_KEY"
api_secret = "YOUR_API_SECRET"

# Generate expires timestamp
expires = int((time.time() + 1) * 1000)

# Generate signature
signature = str(hmac.new(
    bytes(api_secret, "utf-8"),
    bytes(f"GET/realtime{expires}", "utf-8"),
    digestmod="sha256"
).hexdigest())

# Create WebSocket connection
ws = websocket.WebSocketApp(
    url="YOUR_WEBSOCKET_URL",
    # ... other parameters ...
)

# Send authentication request
ws.send(
    json.dumps({
        "op": "auth",
        "args": [api_key, expires, signature]
    })
)
```

### Using SDK Libraries

If you're using official SDK libraries like `pybit` or `bybit-api`, authentication is handled automatically for you.

## Best Practices

1. **Connection Management**
   - Implement automatic reconnection logic
   - Handle connection errors gracefully
   - Monitor connection health with ping/pong

2. **Authentication**
   - Never hardcode API keys
   - Use environment variables for sensitive data
   - Regularly rotate API keys

3. **Data Handling**
   - Process messages asynchronously
   - Implement proper error handling
   - Buffer messages if needed

4. **Resource Management**
   - Close unused connections
   - Subscribe only to needed topics
   - Monitor memory usage

## Additional Resources

- [Public WebSocket API Documentation](https://bybit-exchange.github.io/docs/v5/websocket/public/orderbook)
- [Private WebSocket API Documentation](https://bybit-exchange.github.io/docs/v5/websocket/private/order)
- [WebSocket Trade Documentation](https://bybit-exchange.github.io/docs/v5/websocket/trade/guideline)
