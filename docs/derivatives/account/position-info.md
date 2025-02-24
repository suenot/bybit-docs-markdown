# Get Position Info

Query real-time position data for derivatives trading, including position size, leverage, and PnL information.

### HTTP Request
GET `/v5/position/list`

### Request Parameters
| Parameter | Required | Type | Comments|
|:----- |:-------|:-----|------ |
|category |**true** |string |Product type: `linear` (USDT Perpetual), `inverse` (Inverse Contracts) |
|symbol |false |string |Symbol name, e.g., `BTCUSDT`. Uppercase only.<ul><li>If specified, returns data regardless of position</li><li>If not specified, returns only positions with size > 0</li></ul>|
|settleCoin |false |string |Settle coin. For `linear` contracts, either `symbol` or `settleCoin` is required|
|limit |false |integer |Limit for data size per page. [`1`, `200`]. Default: `20`|
|cursor |false |string |Cursor for pagination|

### Response Parameters
| Parameter | Type | Comments|
|:----- |:-----|----- |
|category |string |Product type |
|nextPageCursor |string |Cursor for next page |
|list |array |Object |
|> positionIdx |integer |Position index<ul><li>`0`: One-Way Mode</li><li>`1`: Buy side of Hedge Mode</li><li>`2`: Sell side of Hedge Mode</li></ul>|
|> riskId |integer |Risk limit ID |
|> riskLimitValue |string |Risk limit value |
|> symbol |string |Symbol name |
|> side |string |Position side. `Buy`, `Sell`, or `None` for empty positions|
|> size |string |Position size (always positive) |
|> avgPrice |string |Average entry price |
|> positionValue |string |Position value |
|> leverage |string |Position leverage |
|> markPrice |string |Mark price |
|> liqPrice |string |Liquidation price |
|> bustPrice |string |Bankruptcy price |
|> positionIM |string |Initial margin |
|> positionMM |string |Maintenance margin |
|> unrealisedPnl |string |Unrealised PnL |
|> cumRealisedPnl |string |Cumulative realised PnL |
|> createdTime |string |Position created timestamp (ms) |
|> updatedTime |string |Position updated timestamp (ms) |

### Request Example

```http
GET /v5/position/list?category=linear&symbol=BTCUSDT HTTP/1.1
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
        "category": "linear",
        "nextPageCursor": "",
        "list": [
            {
                "positionIdx": 0,
                "riskId": 1,
                "riskLimitValue": "2000000",
                "symbol": "BTCUSDT",
                "side": "Buy",
                "size": "0.01",
                "avgPrice": "16800",
                "positionValue": "168",
                "leverage": "10",
                "markPrice": "16800.00",
                "liqPrice": "15000.00",
                "bustPrice": "14500.00",
                "positionIM": "16.8",
                "positionMM": "1.68",
                "unrealisedPnl": "0",
                "cumRealisedPnl": "-100.23",
                "createdTime": "1672132500000",
                "updatedTime": "1672132500000"
            }
        ]
    },
    "retExtInfo": {},
    "time": 1672132480085
}
