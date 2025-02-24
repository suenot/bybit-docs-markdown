# Get Transaction Log

Query transaction logs for your Spot account. Supports data up to 2 years.

### HTTP Request
GET `/v5/account/transaction-log`

### Request Parameters
| Parameter | Required | Type | Comments|
|:----- |:-------|:-----|------ |
|accountType |false |string |Account Type. `UNIFIED` for UTA accounts, `SPOT` for Classic Spot account|
|category |false |string |Product type. Use `spot` for Spot trading|
|currency |false |string |Currency name, uppercase only. Example: `BTC`, `ETH`, `USDT`|
|baseCoin |false |string |Base coin name, uppercase only. Example: `BTC` for BTCUSDT|
|type |false |string |Transaction type. Available values: `TRANSFER`, `TRADE`, `SETTLEMENT`, `DELIVERY`, `BLOCK_TRADE`, `BONUS`, `FEE_REFUND`, `INTEREST`, `CURRENCY_BUY`, `CURRENCY_SELL`|
|startTime |false |integer |Start timestamp (ms) <ul><li>If not passed, returns last 7 days by default</li><li>If only startTime passed, returns startTime+7 days</li><li>If only endTime passed, returns endTime-7 days</li><li>Max range: 7 days</li></ul>|
|endTime |false |integer |End timestamp (ms)|
|limit |false |integer |Limit for data size per page. [`1`, `50`]. Default: `20`|
|cursor |false |string |Cursor for pagination. Use the `nextPageCursor` from response|

### Response Parameters
| Parameter | Type | Comments|
|:----- |:-----|----- |
|list |array |Object |
|> id |string |Unique transaction ID |
|> symbol |string |Trading pair |
|> category |string |Product type |
|> side |string |Trade side: `Buy`, `Sell`, `None` |
|> transactionTime |string |Transaction timestamp (ms)|
|> type |string |Transaction type |
|> qty |string |Transaction quantity <ul><li>Negative: decreased</li><li>Positive: increased</li></ul>|
|> size |string |Position size after trade (with direction)|
|> currency |string |Currency |
|> tradePrice |string |Trade price |
|> funding |string |Funding fee <ul><li>Positive: received</li><li>Negative: paid</li></ul>|
|> fee |string |Trading fee <ul><li>Positive: paid</li><li>Negative: rebate</li></ul>|
|> cashFlow |string |Cash flow from transaction |
|> change |string |Total change = cashFlow + funding - fee |
|> cashBalance |string |Wallet balance after transaction|
|> feeRate |string |Fee rate for trade or funding |
|> bonusChange |string |Bonus change amount |
|> tradeId |string |Trade ID |
|> orderId |string |Order ID |
|> orderLinkId |string |User custom order ID |
|nextPageCursor |string |Cursor for next page |

### Request Example

```http
GET /v5/account/transaction-log?accountType=UNIFIED&category=spot&currency=USDT HTTP/1.1
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
                "id": "592324_USDT_1672132500",
                "symbol": "BTCUSDT",
                "category": "spot",
                "side": "Buy",
                "transactionTime": "1672132500000",
                "type": "TRADE",
                "qty": "0.001",
                "size": "0.001",
                "currency": "USDT",
                "tradePrice": "16600",
                "funding": "0",
                "fee": "0.00001",
                "cashFlow": "-16.6",
                "change": "-16.60001",
                "cashBalance": "983.39999",
                "feeRate": "0.001",
                "bonusChange": "0",
                "tradeId": "592324",
                "orderId": "1523445134",
                "orderLinkId": "test-001"
            }
        ],
        "nextPageCursor": "592324_USDT_1672132500"
    },
    "retExtInfo": {},
    "time": 1672132480085
}
