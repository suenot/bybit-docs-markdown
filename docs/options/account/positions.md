# Get Options Positions

Get current options positions information, including position size, unrealized PnL, and strike prices.

### HTTP Request
GET `/v5/option/position/list`

### Request Parameters
| Parameter | Required | Type | Comments|
|:----- |:-------|:-----|------ |
|category |**true** |string |Product type. `option` |
|symbol |false |string |Symbol name, e.g., `BTC-24MAR23-16000-P` |
|baseCoin |false |string |Base coin, e.g., `BTC`, `ETH` |
|expDate |false |string |Expiry date. Format: `25MAR23`. Default returns all expiry dates |
|direction |false |string |`Forward` (default), `Backward` |
|limit |false |integer |Limit for data size per page. [`1`, `50`]. Default: `20` |
|cursor |false |string |Cursor for pagination |

### Response Parameters
| Parameter | Type | Comments|
|:----- |:-----|----- |
|category |string |Product type |
|nextPageCursor |string |Cursor for next page |
|list |array |Object |
|> symbol |string |Symbol name |
|> leverage |string |Fixed as `1` |
|> updatedTime |string |Position data updated timestamp (ms) |
|> side |string |Position side. `Buy`, `Sell` |
|> positionValue |string |Position value in USD |
|> size |string |Position size |
|> avgPrice |string |Average entry price |
|> markPrice |string |Mark price |
|> markValue |string |Position value calculated by mark price |
|> unrealisedPnl |string |Unrealized PnL |
|> curRealisedPnl |string |Realized PnL for current holding position |
|> cumRealisedPnl |string |Cumulative realized PnL |
|> createdTime |string |Position created timestamp (ms) |
|> strikePrice |string |Option strike price |
|> expTime |string |Option expiry timestamp (ms) |
|> optionType |string |Option type. `Call`, `Put` |
|> delta |string |Delta |
|> gamma |string |Gamma |
|> vega |string |Vega |
|> theta |string |Theta |

### Request Example

```http
GET /v5/option/position/list?category=option&baseCoin=BTC HTTP/1.1
Host: api.bybit.com
X-BAPI-SIGN: XXXXX
X-BAPI-API-KEY: XXXXX
X-BAPI-TIMESTAMP: 1672132480085
X-BAPI-RECV-WINDOW: 5000
```

### Response Example

```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "option",
        "nextPageCursor": "",
        "list": [
            {
                "symbol": "BTC-24MAR23-16000-P",
                "leverage": "1",
                "updatedTime": "1672132480085",
                "side": "Buy",
                "positionValue": "1000.00",
                "size": "1",
                "avgPrice": "1000.00",
                "markPrice": "1100.00",
                "markValue": "1100.00",
                "unrealisedPnl": "100.00",
                "curRealisedPnl": "0",
                "cumRealisedPnl": "500.00",
                "createdTime": "1672132400000",
                "strikePrice": "16000",
                "expTime": "1679616000000",
                "optionType": "Put",
                "delta": "0.5",
                "gamma": "0.1",
                "vega": "0.2",
                "theta": "-0.1"
            }
        ]
    },
    "retExtInfo": {},
    "time": 1672132480085
}
