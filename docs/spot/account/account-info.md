# Get Account Info

Get Spot account information including account type, asset balances, and trading fee rates.

### HTTP Request
GET `/v5/account/info`

### Request Parameters
None

### Response Parameters
| Parameter | Type | Comments|
|:----- |:-----|----- |
|unifiedMarginStatus |integer |Unified margin status <ul><li>`1`: Regular account</li><li>`2`: Unified margin account</li><li>`3`: UTA Pro account</li></ul>|
|marginMode |string |`REGULAR_MARGIN`,`PORTFOLIO_MARGIN`|
|dcpStatus |string |Derivatives Clearing Program status. `REGULAR`,`SIGN`,`PROCESSING`,`DONE`|
|timeWindow |integer |Derivatives Clearing Program time window (min)|
|smpGroup |integer |Smp group ID|
|isMasterTrader |boolean |Whether the account is a master trader|
|spotHedgingStatus |boolean |Whether spot hedging is enabled|
|updatedTime |string |Account update time (ms)|

### Request Example

```http
GET /v5/account/info HTTP/1.1
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
        "dcpStatus": "DONE",
        "timeWindow": 60,
        "smpGroup": 0,
        "isMasterTrader": false,
        "spotHedgingStatus": true,
        "updatedTime": "1672132480000"
    },
    "retExtInfo": {},
    "time": 1672132480085
}
