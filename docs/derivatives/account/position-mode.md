# Switch Position Mode

Switch between One-Way Mode and Hedge Mode for derivatives trading. In One-Way Mode, you can only hold one position direction (Buy or Sell). In Hedge Mode, you can hold both Buy and Sell positions simultaneously.

### Position Mode Support
| Contract Type | Position Modes |
|:-------------|:---------------|
|USDT Perpetual |One-Way & Hedge Mode |
|USDT Futures |One-Way Mode only |
|USDC Perpetual |One-Way Mode only |
|USDC Futures |One-Way Mode only |
|Inverse Perpetual |One-Way Mode only |
|Inverse Futures |One-Way & Hedge Mode* |

_* UTA 2.0 accounts support One-Way Mode only for Inverse Futures_

### HTTP Request
POST `/v5/position/switch-mode`

### Request Parameters
| Parameter | Required | Type | Comments|
|:----- |:-------|:-----|------ |
|category |**true** |string |Product type: `linear` (USDT Perpetual), `inverse` (Inverse Futures) |
|symbol |false |string |Symbol name, e.g., `BTCUSDT`. Either `symbol` or `coin` required |
|coin |false |string |Coin name, e.g., `USDT`. Either `symbol` or `coin` required |
|mode |**true** |integer |Position mode<ul><li>`0`: One-Way Mode</li><li>`3`: Hedge Mode</li></ul>|

### Response Parameters
None

### Mode Configuration Priority
1. Symbol level (highest)
2. Coin level
3. System default (lowest, One-Way Mode)

### Request Example

```http
POST /v5/position/switch-mode HTTP/1.1
Host: api.bybit.com
X-BAPI-SIGN: XXXXX
X-BAPI-API-KEY: XXXXX
X-BAPI-TIMESTAMP: 1672132480085
X-BAPI-RECV-WINDOW: 5000
Content-Type: application/json

{
    "category": "linear",
    "symbol": "BTCUSDT",
    "mode": 3
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
