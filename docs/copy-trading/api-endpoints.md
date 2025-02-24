# Copy Trading API Endpoints

API endpoints for Copy Trading operations on Bybit.

## Place Copy Trade

Place a copy trading order to follow a Master Trader's position.

### HTTP Request
POST `/v5/order/create`

### Request Parameters
| Parameter | Required | Type | Comments|
|:----- |:-------|:-----|------ |
|category |**true** |string |Product type. Use `linear` |
|symbol |**true** |string |Symbol name, e.g., `BTCUSDT` |
|side |**true** |string |Order side. `Buy`, `Sell` |
|orderType |**true** |string |Order type. `Market`, `Limit` |
|qty |**true** |string |Order quantity |
|price |false |string |Order price. Required for `Limit` orders |
|timeInForce |**true** |string |Time in force. `GTC`, `IOC`, `FOK`, `PostOnly` |
|positionIdx |**true** |integer |Position index. `0`: One-Way Mode, `1`: Buy side of Hedge Mode, `2`: Sell side of Hedge Mode |
|takeProfit |false |string |Take profit price |
|stopLoss |false |string |Stop loss price |

### Response Parameters
| Parameter | Type | Comments|
|:----- |:-----|----- |
|orderId |string |Order ID |
|orderLinkId |string |Custom order ID |

### Request Example

```http
POST /v5/order/create HTTP/1.1
Host: api.bybit.com
X-BAPI-SIGN: XXXXX
X-BAPI-API-KEY: XXXXX
X-BAPI-TIMESTAMP: 1672132480085
X-BAPI-RECV-WINDOW: 5000
Content-Type: application/json

{
    "category": "linear",
    "symbol": "BTCUSDT",
    "side": "Buy",
    "orderType": "Limit",
    "qty": "0.1",
    "price": "29000",
    "timeInForce": "GTC",
    "positionIdx": 1
}
```

### Response Example

```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "orderId": "1234567890",
        "orderLinkId": "test-001"
    },
    "retExtInfo": {},
    "time": 1672132480085
}
```

## Get Copy Trading Symbols

Get the list of symbols that support copy trading.

### HTTP Request
GET `/v5/market/instruments-info`

### Request Parameters
| Parameter | Required | Type | Comments|
|:----- |:-------|:-----|------ |
|category |**true** |string |Product type. Use `linear` |
|symbol |false |string |Symbol name, e.g., `BTCUSDT` |

### Response Parameters
| Parameter | Type | Comments|
|:----- |:-----|----- |
|list |array |Object |
|> symbol |string |Symbol name |
|> copyTrading |string |Whether copy trading is supported. `1`: supported, `0`: not supported |

### Request Example

```http
GET /v5/market/instruments-info?category=linear HTTP/1.1
Host: api.bybit.com
```

### Response Example

```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "list": [
            {
                "symbol": "BTCUSDT",
                "copyTrading": "1"
            }
        ]
    },
    "retExtInfo": {},
    "time": 1672132480085
}
