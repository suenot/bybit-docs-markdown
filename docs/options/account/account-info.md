# Get Options Account Info

Get account information for options trading, including account balance, positions, and risk metrics.

### HTTP Request
GET `/v5/account/info`

### Request Parameters
| Parameter | Required | Type | Comments|
|:----- |:-------|:-----|------ |
|baseCoin |false |string |Base coin, e.g., `BTC`, `ETH`. Default returns all base coins |

### Response Parameters
| Parameter | Type | Comments|
|:----- |:-----|----- |
|unifiedMarginStatus |integer |Unified margin status<ul><li>`1`: Regular account</li><li>`2`: Unified margin account</li><li>`3`: UTA Pro account</li></ul>|
|marginMode |string |`REGULAR_MARGIN`,`PORTFOLIO_MARGIN`|
|optionsEnabled |boolean |Whether options trading is enabled|
|totalEquity |string |Total account equity in USD|
|totalWalletBalance |string |Total wallet balance in USD|
|totalUnrealizedProfit |string |Total unrealized PnL in USD|
|totalPnl |string |Total PnL (realized + unrealized) in USD|
|totalMaintenanceMargin |string |Total maintenance margin in USD|
|totalInitialMargin |string |Total initial margin in USD|
|totalAvailableBalance |string |Total available balance in USD|
|baseCoin |array |Base coin information|
|> coin |string |Base coin name|
|> equity |string |Equity of the coin|
|> usdValue |string |USD value of the coin|
|> walletBalance |string |Wallet balance|
|> unrealizedPnl |string |Unrealized PnL|
|> realizedPnl |string |Realized PnL|
|> cumRealisedPnl |string |Cumulative realized PnL|
|> marginBalance |string |Margin balance|
|> availableBalance |string |Available balance|
|> marginRate |string |Margin rate|
|> maintenanceMargin |string |Maintenance margin|
|> initialMargin |string |Initial margin|

### Request Example

```http
GET /v5/account/info?baseCoin=BTC HTTP/1.1
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
        "unifiedMarginStatus": 3,
        "marginMode": "PORTFOLIO_MARGIN",
        "optionsEnabled": true,
        "totalEquity": "1000.00",
        "totalWalletBalance": "980.00",
        "totalUnrealizedProfit": "20.00",
        "totalPnl": "120.00",
        "totalMaintenanceMargin": "50.00",
        "totalInitialMargin": "100.00",
        "totalAvailableBalance": "880.00",
        "baseCoin": [
            {
                "coin": "BTC",
                "equity": "1.00",
                "usdValue": "40000.00",
                "walletBalance": "0.98",
                "unrealizedPnl": "0.02",
                "realizedPnl": "0.10",
                "cumRealisedPnl": "1.50",
                "marginBalance": "1.00",
                "availableBalance": "0.88",
                "marginRate": "0.1",
                "maintenanceMargin": "0.05",
                "initialMargin": "0.10"
            }
        ]
    },
    "retExtInfo": {},
    "time": 1672132480085
}
