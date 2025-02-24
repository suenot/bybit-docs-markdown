# Spot Market Data

## Get Instruments Info
Get detailed information about available trading pairs.

### Endpoint
```http
GET /v5/market/instruments-info
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | Must be "spot" |
| symbol | No | string | Symbol name, e.g., "BTCUSDT" |
| status | No | string | Default: "Trading" |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "spot",
        "list": [
            {
                "symbol": "BTCUSDT",
                "baseCoin": "BTC",
                "quoteCoin": "USDT",
                "innovation": "0",
                "status": "Trading",
                "marginTrading": "both",
                "lotSizeFilter": {
                    "basePrecision": "0.000001",
                    "quotePrecision": "0.00000001",
                    "minOrderQty": "0.000001",
                    "maxOrderQty": "63.01197227",
                    "minOrderAmt": "1",
                    "maxOrderAmt": "2000000"
                },
                "priceFilter": {
                    "tickSize": "0.01"
                }
            }
        ]
    }
}
```

## Get Tickers
Get latest price snapshot, best bid/ask price, and trading volume in the last 24 hours.

### Endpoint
```http
GET /v5/market/tickers
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | Must be "spot" |
| symbol | No | string | Symbol name, e.g., "BTCUSDT" |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "spot",
        "list": [
            {
                "symbol": "BTCUSDT",
                "lastPrice": "50000.00",
                "highPrice24h": "51000.00",
                "lowPrice24h": "49000.00",
                "prevPrice24h": "49500.00",
                "volume24h": "100.0",
                "turnover24h": "5000000.00",
                "price24hPcnt": "0.0101",
                "usdIndexPrice": "50000.00"
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
| category | Yes | string | Must be "spot" |
| symbol | Yes | string | Symbol name |
| limit | No | integer | Limit for data size. [`1`, `200`]. Default: `25` |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "s": "BTCUSDT",
        "b": [
            ["50000.00", "1.000000"]  // [price, size]
        ],
        "a": [
            ["50100.00", "1.000000"]
        ],
        "ts": 1672764159969,
        "u": 1
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
| category | Yes | string | Must be "spot" |
| symbol | Yes | string | Symbol name |
| limit | No | integer | Limit for data size. [`1`, `1000`]. Default: `25` |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "spot",
        "list": [
            {
                "execId": "2100000000007764263",
                "symbol": "BTCUSDT",
                "price": "50000.00",
                "size": "0.100000",
                "side": "Buy",
                "time": "1672764159969"
            }
        ]
    }
}
```

## Get Kline/Candlestick Data
Get kline/candlestick data.

### Endpoint
```http
GET /v5/market/kline
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | Must be "spot" |
| symbol | Yes | string | Symbol name |
| interval | Yes | string | Kline interval. `1`,`3`,`5`,`15`,`30`,`60`,`120`,`240`,`360`,`720`,`D`,`M`,`W` |
| start | No | integer | Start timestamp (ms) |
| end | No | integer | End timestamp (ms) |
| limit | No | integer | Limit for data size. [`1`, `1000`]. Default: `200` |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "spot",
        "symbol": "BTCUSDT",
        "list": [
            [
                "1672764000000",  // timestamp
                "50000.00",       // open price
                "51000.00",       // high price
                "49000.00",       // low price
                "50500.00",       // close price
                "100.0",          // volume
                "5000000.00"      // turnover
            ]
        ]
    }
}
```

## Error Codes
For error codes and messages, please refer to the [Error Codes](../error-codes.md) documentation.

## Rate Limits
For rate limits information, please refer to the [Rate Limits](../rate-limits.md) documentation.
