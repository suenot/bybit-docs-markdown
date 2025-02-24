# Get Margin Info

Get margin trading information including borrowable amounts, interest rates, and collateral ratios.

### HTTP Request
GET `/v5/spot-cross-margin-trade/data`

### Request Parameters
| Parameter | Required | Type | Comments|
|:----- |:-------|:-----|------ |
|coin |false |string |Coin name |
|vipLevel |false |string |VIP level |

### Response Parameters
| Parameter | Type | Comments|
|:----- |:-----|----- |
|vipCoinList |array |Object |
|> list |array |Object |
|>> borrowable |boolean |Whether coin can be borrowed |
|>> collateralRatio |string |Collateral ratio |
|>> coin |string |Coin name |
|>> hourlyBorrowRate |string |Hourly borrow interest rate |
|>> liquidationOrder |string |Liquidation order |
|>> marginCollateral |boolean |Whether coin can be used as margin collateral |
|>> maxBorrowingAmount |string |Maximum borrowing amount |
|> vipLevel |string |VIP level |

### Additional Information
* The collateral ratio determines how much of the coin's value can be used as collateral
* The liquidation order determines the sequence in which coins are liquidated
* The hourly borrow rate is used to calculate interest on borrowed funds

### Request Example

```http
GET /v5/spot-cross-margin-trade/data?coin=BTC&vipLevel=VIP1 HTTP/1.1
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
        "vipCoinList": [
            {
                "list": [
                    {
                        "borrowable": true,
                        "collateralRatio": "0.95",
                        "coin": "BTC",
                        "hourlyBorrowRate": "0.000003964522",
                        "liquidationOrder": "2",
                        "marginCollateral": true,
                        "maxBorrowingAmount": "5"
                    }
                ],
                "vipLevel": "VIP1"
            }
        ]
    },
    "retExtInfo": {},
    "time": 1672132480085
}
