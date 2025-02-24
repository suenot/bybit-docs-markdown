# Copy Trading WebSocket Streams

WebSocket streams for real-time copy trading data.

## Position Updates

Subscribe to position updates for copy trading positions.

### Request Parameters
| Parameter | Type | Required | Comments |
|:---------|:-----|:---------|:---------|
|op |string |**true** |Operation. `subscribe` |
|args |array |**true** |Channel names |

### Response Parameters
| Parameter | Type | Comments |
|:---------|:-----|:---------|
|topic |string |Topic name |
|data |array |Position data |
|> symbol |string |Symbol name |
|> side |string |Position side |
|> size |string |Position size |
|> entryPrice |string |Average entry price |
|> leverage |string |Position leverage |
|> markPrice |string |Mark price |
|> positionValue |string |Position value |
|> unrealisedPnl |string |Unrealized PnL |

### Subscribe Example
```json
{
    "op": "subscribe",
    "args": [
        "copyTrade.position"
    ]
}
```

### Stream Example
```json
{
    "topic": "copyTrade.position",
    "data": [
        {
            "symbol": "BTCUSDT",
            "side": "Buy",
            "size": "0.1",
            "entryPrice": "29000",
            "leverage": "10",
            "markPrice": "29100",
            "positionValue": "2910",
            "unrealisedPnl": "10"
        }
    ]
}
```

## Order Updates

Subscribe to order updates for copy trading orders.

### Request Parameters
| Parameter | Type | Required | Comments |
|:---------|:-----|:---------|:---------|
|op |string |**true** |Operation. `subscribe` |
|args |array |**true** |Channel names |

### Response Parameters
| Parameter | Type | Comments |
|:---------|:-----|:---------|
|topic |string |Topic name |
|data |array |Order data |
|> symbol |string |Symbol name |
|> orderId |string |Order ID |
|> side |string |Order side |
|> orderType |string |Order type |
|> price |string |Order price |
|> qty |string |Order quantity |
|> orderStatus |string |Order status |
|> execQty |string |Executed quantity |
|> execPrice |string |Execution price |
|> execFee |string |Execution fee |
|> createTime |string |Order creation time |

### Subscribe Example
```json
{
    "op": "subscribe",
    "args": [
        "copyTrade.order"
    ]
}
```

### Stream Example
```json
{
    "topic": "copyTrade.order",
    "data": [
        {
            "symbol": "BTCUSDT",
            "orderId": "1234567890",
            "side": "Buy",
            "orderType": "Limit",
            "price": "29000",
            "qty": "0.1",
            "orderStatus": "Filled",
            "execQty": "0.1",
            "execPrice": "29000",
            "execFee": "0.1",
            "createTime": "1672132480085"
        }
    ]
}
