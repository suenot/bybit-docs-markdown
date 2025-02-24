# Set Leverage

Set the leverage for derivatives positions. The leverage determines the amount of margin required to open and maintain positions.

### HTTP Request
POST `/v5/position/set-leverage`

### Request Parameters
| Parameter | Required | Type | Comments|
|:----- |:-------|:-----|------ |
|category |**true** |string |Product type: `linear` (USDT Perpetual), `inverse` (Inverse Contracts) |
|symbol |**true** |string |Symbol name, e.g., `BTCUSDT` |
|buyLeverage |**true** |string |Buy side leverage, from `1` to max leverage allowed |
|sellLeverage |**true** |string |Sell side leverage, from `1` to max leverage allowed |

### Response Parameters
None

### Notes
1. The maximum allowed leverage depends on the risk limit setting and the trading pair
2. For One-Way Mode, `buyLeverage` and `sellLeverage` must be the same
3. For Hedge Mode, you can set different leverage values for Buy and Sell positions
4. Cannot change leverage when you have open positions or pending orders

### Request Example

```http
POST /v5/position/set-leverage HTTP/1.1
Host: api.bybit.com
X-BAPI-SIGN: XXXXX
X-BAPI-API-KEY: XXXXX
X-BAPI-TIMESTAMP: 1672132480085
X-BAPI-RECV-WINDOW: 5000
Content-Type: application/json

{
    "category": "linear",
    "symbol": "BTCUSDT",
    "buyLeverage": "10",
    "sellLeverage": "10"
}
```

### Response Example

```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {},
    "retExtInfo": {},
    "time": 1672132480085
}
