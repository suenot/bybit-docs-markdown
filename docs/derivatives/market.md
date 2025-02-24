# Derivatives Market Data

This documentation covers market data endpoints for USDT Perpetual, USDC Perpetual, and Inverse Perpetual contracts.

## Get Instruments Info
Get detailed information about available trading instruments.

### Endpoint
```http
GET /v5/market/instruments-info
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | `linear` for USDT/USDC, `inverse` for Inverse |
| symbol | No | string | Symbol name, e.g., "BTCUSDT" |
| status | No | string | Symbol status filter. Default: "Trading" |
| baseCoin | No | string | Base coin, e.g., "BTC" |
| limit | No | integer | Limit for data size. [`1`, `1000`]. Default: `500` |
| cursor | No | string | Cursor for pagination |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "linear",
        "list": [
            {
                "symbol": "BTCUSDT",
                "contractType": "LinearPerpetual",
                "status": "Trading",
                "baseCoin": "BTC",
                "quoteCoin": "USDT",
                "launchTime": "1585526400000",
                "deliveryTime": "0",
                "deliveryFeeRate": "",
                "priceScale": "2",
                "leverageFilter": {
                    "minLeverage": "1",
                    "maxLeverage": "100",
                    "leverageStep": "0.01"
                },
                "priceFilter": {
                    "minPrice": "0.10",
                    "maxPrice": "199999.80",
                    "tickSize": "0.10"
                },
                "lotSizeFilter": {
                    "maxOrderQty": "100.000",
                    "minOrderQty": "0.001",
                    "qtyStep": "0.001"
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
| category | Yes | string | `linear` for USDT/USDC, `inverse` for Inverse |
| symbol | No | string | Symbol name |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "linear",
        "list": [
            {
                "symbol": "BTCUSDT",
                "lastPrice": "50000.00",
                "indexPrice": "50010.00",
                "markPrice": "50005.00",
                "prevPrice24h": "49000.00",
                "price24hPcnt": "0.0204",
                "highPrice24h": "51000.00",
                "lowPrice24h": "48800.00",
                "volume24h": "1000.000",
                "turnover24h": "50000000.00",
                "fundingRate": "0.0001",
                "nextFundingTime": "1672764000000"
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
| category | Yes | string | `linear` for USDT/USDC, `inverse` for Inverse |
| symbol | Yes | string | Symbol name |
| limit | No | integer | Limit for data size. [`1`, `500`]. Default: `25` |

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

## Get Funding Rate History
Get historical funding rates.

### Endpoint
```http
GET /v5/market/funding/history
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | `linear` for USDT/USDC, `inverse` for Inverse |
| symbol | Yes | string | Symbol name |
| startTime | No | integer | Start timestamp (ms) |
| endTime | No | integer | End timestamp (ms) |
| limit | No | integer | Limit for data size. [`1`, `200`]. Default: `200` |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "linear",
        "list": [
            {
                "symbol": "BTCUSDT",
                "fundingRate": "0.0001",
                "fundingRateTimestamp": "1672764000000"
            }
        ]
    }
}
```

## Get Open Interest
Get the open interest data.

### Endpoint
```http
GET /v5/market/open-interest
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | `linear` for USDT/USDC, `inverse` for Inverse |
| symbol | Yes | string | Symbol name |
| intervalTime | Yes | string | Interval. `5min`,`15min`,`30min`,`1h`,`4h`,`1d` |
| startTime | No | integer | Start timestamp (ms) |
| endTime | No | integer | End timestamp (ms) |
| limit | No | integer | Limit for data size. [`1`, `200`]. Default: `50` |
| cursor | No | string | Cursor for pagination |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "linear",
        "symbol": "BTCUSDT",
        "list": [
            {
                "openInterest": "100.000",
                "timestamp": "1672764000000"
            }
        ],
        "nextPageCursor": "cursor"
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
| category | Yes | string | `linear` for USDT/USDC, `inverse` for Inverse |
| symbol | Yes | string | Symbol name |
| limit | No | integer | Limit for data size. [`1`, `1000`]. Default: `25` |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "linear",
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

## Error Codes
For error codes and messages, please refer to the [Error Codes](../error-codes.md) documentation.

## Rate Limits
For rate limits information, please refer to the [Rate Limits](../rate-limits.md) documentation.
