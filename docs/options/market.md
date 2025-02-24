# Options Market Data

This documentation covers market data endpoints specifically for Options trading.

## Get Instruments Info
Get detailed information about available options instruments.

### Endpoint
```http
GET /v5/market/instruments-info
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | Must be "option" |
| symbol | No | string | Symbol name |
| baseCoin | No | string | Base coin. Default: BTC |
| limit | No | integer | Limit for data size. [`1`, `1000`]. Default: `500` |
| cursor | No | string | Cursor for pagination |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "option",
        "list": [
            {
                "symbol": "BTC-30DEC22-18000-C",
                "optionsType": "Call",
                "status": "Trading",
                "baseCoin": "BTC",
                "quoteCoin": "USD",
                "settleCoin": "USDC",
                "launchTime": "1672214400000",
                "deliveryTime": "1672387200000",
                "expiryPrice": "16500",
                "strikePrice": "18000",
                "leverageFilter": {
                    "minLeverage": "1",
                    "maxLeverage": "10",
                    "leverageStep": "0.1"
                },
                "priceFilter": {
                    "minPrice": "0.1",
                    "maxPrice": "10000",
                    "tickSize": "0.1"
                },
                "lotSizeFilter": {
                    "maxOrderQty": "200",
                    "minOrderQty": "0.01",
                    "qtyStep": "0.01"
                }
            }
        ]
    }
}
```

## Get Tickers
Get latest price snapshot, best bid/ask price, and trading volume.

### Endpoint
```http
GET /v5/market/tickers
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | Must be "option" |
| symbol | No | string | Symbol name |
| baseCoin | No | string | Base coin. Default: BTC |
| expDate | No | string | Expiry date. e.g., 25DEC22 |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "option",
        "list": [
            {
                "symbol": "BTC-30DEC22-18000-C",
                "bid1Price": "0.0",
                "bid1Size": "0.0",
                "bid1Iv": "0",
                "ask1Price": "0.0",
                "ask1Size": "0.0",
                "ask1Iv": "0",
                "lastPrice": "0.0",
                "highPrice24h": "0.0",
                "lowPrice24h": "0.0",
                "markPrice": "0.0",
                "indexPrice": "0.0",
                "markIv": "0",
                "underlyingPrice": "0.0",
                "openInterest": "0.0",
                "turnover24h": "0.0",
                "volume24h": "0.0",
                "totalVolume": "0.0",
                "totalTurnover": "0.0",
                "delta": "0",
                "gamma": "0",
                "vega": "0",
                "theta": "0",
                "predictedDeliveryPrice": "0.0",
                "change24h": "0.0"
            }
        ]
    }
}
```

## Get Order Book
Get order book data.

### Endpoint
```http
GET /v5/market/orderbook
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | Must be "option" |
| symbol | Yes | string | Symbol name |
| limit | No | integer | Limit for data size. [`1`, `500`]. Default: `25` |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "s": "BTC-30DEC22-18000-C",
        "b": [
            ["0.001", "10"]  // [price, size]
        ],
        "a": [
            ["0.002", "10"]
        ],
        "ts": 1672764159969,
        "u": 1
    }
}
```

## Get Historical Volatility
Query option historical volatility.

### Endpoint
```http
GET /v5/market/historical-volatility
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | Must be "option" |
| baseCoin | No | string | Base coin. Default: BTC |
| quoteCoin | No | string | Quote coin. `USD` or `USDT`. Default: USD |
| period | No | integer | Period. Default: 7 days |
| startTime | No | integer | Start timestamp (ms) |
| endTime | No | integer | End timestamp (ms) |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "option",
        "list": [
            {
                "period": 7,
                "value": "0.7",
                "time": "1672764000000"
            }
        ]
    }
}
```

## Get Recent Trades
Get recent trades data.

### Endpoint
```http
GET /v5/market/recent-trade
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | Must be "option" |
| symbol | Yes | string | Symbol name |
| limit | No | integer | Limit for data size. [`1`, `1000`]. Default: `25` |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "option",
        "list": [
            {
                "execId": "2100000000007764263",
                "symbol": "BTC-30DEC22-18000-C",
                "price": "0.001",
                "size": "10",
                "side": "Buy",
                "time": "1672764159969"
            }
        ]
    }
}
```

## Error Codes
For error codes and messages, please refer to the [Error Codes](../error-codes.md) documentation.

## Rate Limits
For rate limits information, please refer to the [Rate Limits](../rate-limits.md) documentation.
