# Copy Trading WebSocket Streams

WebSocket streams provide real-time data relevant to Copy Trading activities. To receive updates, subscribe to the standard private WebSocket streams.

## Connection
Connect to the **Private Stream** endpoint:
- Mainnet: `wss://stream.bybit.com/v5/private`
- Testnet: `wss://stream-testnet.bybit.com/v5/private`

Remember to authenticate the connection as described in the main [WebSocket Connection](./../websocket/connect.md) guide.

## Position Updates

Subscribe to the standard `position` topic to receive real-time updates on your derivatives positions, including those opened or managed through Copy Trading (Master Trader or Follower). Since Copy Trading currently only supports USDT Perpetuals, subscribing to the specific category topic is recommended.

**Topic:**
```
position.linear
```

**Subscription Example:**
```json
{
    "op": "subscribe",
    "args": [
        "position.linear"
    ]
}
```

**Stream Format:**
The message format is the same as the standard [Private Position Stream](./../websocket/private/position.md). Copy Trading positions will appear with `category` set to `linear`.

**Example Snippet (Conceptual):**
```json
{
    "topic": "position.linear",
    "type": "snapshot", // or "delta"
    "ts": 1672304486868,
    "data": [
        {
            "symbol": "BTCUSDT",
            "category": "linear",
            "side": "Buy",
            "size": "0.1",
            "entryPrice": "29000",
            "leverage": "10",
            "markPrice": "29100",
            "positionValue": "2910",
            "unrealisedPnl": "10",
            // ... other standard position fields
        }
    ]
}
```

Refer to the [Private Position Stream Documentation](./../websocket/private/position.md) for the full message format and details.

## Order Updates

Subscribe to the standard `order` topic to receive real-time updates on your orders, including those related to Copy Trading activities. Subscribing to the specific category topic is recommended.

**Topic:**
```
order.linear
```

**Subscription Example:**
```json
{
    "op": "subscribe",
    "args": [
        "order.linear"
    ]
}
```

**Stream Format:**
The message format is the same as the standard [Private Order Stream](./../websocket/private/order.md). Copy Trading orders will appear with `category` set to `linear`.

**Example Snippet (Conceptual):**
```json
{
    "topic": "order.linear",
    "type": "snapshot", // or "delta"
    "ts": 1672304486868,
    "data": [
         {
            "symbol": "BTCUSDT",
            "category": "linear",
            "orderId": "1234567890",
            "side": "Buy",
            "orderType": "Limit",
            "price": "29000",
            "qty": "0.1",
            "orderStatus": "Filled",
            // ... other standard order fields
            "createTime": "1672132480085"
        }
    ]
}
```

Refer to the [Private Order Stream Documentation](./../websocket/private/order.md) for the full message format and details.
