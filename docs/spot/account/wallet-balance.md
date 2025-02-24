# Get Wallet Balance

Get wallet balance information for your Spot account.

### HTTP Request
GET `/v5/account/wallet-balance`

### Request Parameters
| Parameter | Required | Type | Comments|
|:----- |:-------|:-----|------ |
|accountType |**true** |string |Account type. Use `UNIFIED` for UTA accounts, `SPOT` for Classic Spot account |
|coin |false |string |Coin name, uppercase only. Examples: `BTC`, `ETH`, `USDT` <ul><li>If not passed, returns non-zero asset info</li><li>Can pass multiple coins, separated by comma, e.g., `USDT,USDC`</li></ul>|

### Response Parameters
| Parameter | Type | Comments|
|:----- |:-----|----- |
|list |array |Object |
|> accountType |string |Account type|
|> totalEquity |string |Account total equity (USD)|
|> totalWalletBalance |string |Account wallet balance (USD)|
|> totalMarginBalance |string |Account margin balance (USD)|
|> totalAvailableBalance |string |Account available balance (USD)|
|> totalPerpUPL |string |Account unrealised P&L (USD)|
|> totalInitialMargin |string |Account initial margin (USD)|
|> totalMaintenanceMargin |string |Account maintenance margin (USD)|
|> coin |array |Object |
|>> coin |string |Coin name, e.g., BTC, ETH, USDT |
|>> equity |string |Equity of coin |
|>> usdValue |string |USD value of coin|
|>> walletBalance |string |Wallet balance of coin |
|>> free |string |Available balance for Spot wallet |
|>> locked |string |Locked balance due to open orders |
|>> borrowAmount |string |Borrow amount of current coin |
|>> availableToWithdraw |string |Available amount to withdraw |
|>> accruedInterest |string |Accrued interest |
|>> totalOrderIM |string |Pre-occupied margin for orders |
|>> totalPositionIM |string |Initial margin for positions |
|>> unrealisedPnl |string |Unrealised P&L |
|>> cumRealisedPnl |string |Cumulative Realised P&L |

### Request Example

```http
GET /v5/account/wallet-balance?accountType=UNIFIED&coin=USDT HTTP/1.1
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
        "list": [
            {
                "accountType": "UNIFIED",
                "totalEquity": "10.05007099",
                "totalWalletBalance": "10.05007099",
                "totalMarginBalance": "10.05007099",
                "totalAvailableBalance": "10.05007099",
                "totalPerpUPL": "0",
                "totalInitialMargin": "0",
                "totalMaintenanceMargin": "0",
                "coin": [
                    {
                        "coin": "USDT",
                        "equity": "10.05007099",
                        "usdValue": "10.05007099",
                        "walletBalance": "10.05007099",
                        "free": "10.05007099",
                        "locked": "0",
                        "borrowAmount": "0",
                        "availableToWithdraw": "10.05007099",
                        "accruedInterest": "0",
                        "totalOrderIM": "0",
                        "totalPositionIM": "0",
                        "unrealisedPnl": "0",
                        "cumRealisedPnl": "0"
                    }
                ]
            }
        ]
    },
    "retExtInfo": {},
    "time": 1672132480085
}
