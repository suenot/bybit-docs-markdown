# Repay Margin

Repay borrowed margin funds.

### HTTP Request
POST `/v5/spot-margin-trade/repay`

### Request Parameters
| Parameter | Required | Type | Comments|
|:----- |:-------|:-----|------ |
|coin |**true** |string |Coin name to repay |
|amount |**true** |string |Amount to repay |

### Response Parameters
| Parameter | Type | Comments|
|:----- |:-----|----- |
|transactId |string |Transaction ID |

### Request Example

```http
POST /v5/spot-margin-trade/repay HTTP/1.1
Host: api.bybit.com
X-BAPI-SIGN: XXXXX
X-BAPI-API-KEY: XXXXX
X-BAPI-TIMESTAMP: 1672132480085
X-BAPI-RECV-WINDOW: 5000
Content-Type: application/json

{
    "coin": "BTC",
    "amount": "0.1"
}
```

### Response Example

```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "transactId": "12345"
    },
    "retExtInfo": {},
    "time": 1672132480085
}
