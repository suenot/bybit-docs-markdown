# Get Settlement History

Query settlement records for options trading.

### HTTP Request
GET `/v5/option/settlement`

### Request Parameters
| Parameter | Required | Type | Comments|
|:----- |:-------|:-----|------ |
|category |**true** |string |Product type. `option` |
|symbol |false |string |Symbol name, e.g., `BTC-24MAR23-16000-P` |
|expDate |false |string |Expiry date. Format: `25MAR23` |
|startTime |false |integer |Start timestamp (ms) <ul><li>Default: 7 days before current time</li><li>Max range: 30 days</li></ul>|
|endTime |false |integer |End timestamp (ms) |
|limit |false |integer |Limit for data size per page. [`1`, `50`]. Default: `20` |
|cursor |false |string |Cursor for pagination |

### Response Parameters
| Parameter | Type | Comments|
|:----- |:-----|----- |
|category |string |Product type |
|list |array |Object |
|> symbol |string |Symbol name |
|> side |string |Position side. `Buy`, `Sell` |
|> size |string |Position size |
|> sessionAvgPrice |string |Settlement price |
|> markPrice |string |Mark price |
|> realisedPnl |string |Realised PnL |
|> createdTime |string |Settlement time (ms) |
|nextPageCursor |string |Cursor for next page |

### Request Example

```http
GET /v5/option/settlement?category=option&symbol=BTC-24MAR23-16000-P HTTP/1.1
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
        "list": [
            {
                "symbol": "BTC-24MAR23-16000-P",
                "side": "Buy",
                "size": "1",
                "sessionAvgPrice": "1000.00",
                "markPrice": "1100.00",
                "realisedPnl": "100.00",
                "createdTime": "1672132480085"
            }
        ],
        "nextPageCursor": ""
    },
    "retExtInfo": {},
    "time": 1672132480085
}
