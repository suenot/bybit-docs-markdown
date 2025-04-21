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

## Get Kline (Candlestick Data)
Get historical kline data.

### Endpoint
```http
GET /v5/market/kline
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | `linear` for USDT/USDC, `inverse` for Inverse |
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
        "symbol": "BTCUSDT",
        "category": "linear",
        "list": [
            [
                "1672502400000", // Start time
                "16500.00",    // Open price
                "16550.50",    // High price
                "16480.00",    // Low price
                "16520.50",    // Close price
                "1000.000",    // Volume
                "16500000.00"  // Turnover
            ]
        ]
    },
    "retExtInfo": {},
    "time": 1672764159969
}
```

## Get Mark Price Kline
Get historical mark price kline data.

### Endpoint
```http
GET /v5/market/mark-price-kline
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | `linear` for USDT/USDC, `inverse` for Inverse |
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
        "symbol": "BTCUSDT",
        "category": "linear",
        "list": [
            [
                "1672502400000", // Start time
                "16500.00",    // Open price
                "16550.50",    // High price
                "16480.00",    // Low price
                "16520.50"     // Close price
            ]
        ]
    },
    "retExtInfo": {},
    "time": 1672764159969
}
```

## Get Index Price Kline
Get historical index price kline data.

### Endpoint
```http
GET /v5/market/index-price-kline
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | `linear` for USDT/USDC, `inverse` for Inverse |
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
        "symbol": "BTCUSDT",
        "category": "linear",
        "list": [
            [
                "1672502400000", // Start time
                "16500.00",    // Open price
                "16550.50",    // High price
                "16480.00",    // Low price
                "16520.50"     // Close price
            ]
        ]
    },
    "retExtInfo": {},
    "time": 1672764159969
}
```

## Get Premium Index Price Kline
Get historical premium index price kline data.

### Endpoint
```http
GET /v5/market/premium-index-price-kline
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | `linear` for USDT/USDC, `inverse` for Inverse |
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
        "symbol": "BTCUSDT",
        "category": "linear",
        "list": [
            [
                "1672502400000", // Start time
                "16500.00",    // Open price
                "16550.50",    // High price
                "16480.00",    // Low price
                "16520.50"     // Close price
            ]
        ]
    },
    "retExtInfo": {},
    "time": 1672764159969
}
```

## Get Risk Limit Info
Get risk limit information for derivatives contracts.

### Endpoint
```http
GET /v5/market/risk-limit-info
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | `linear` for USDT/USDC, `inverse` for Inverse |
| symbol | Yes | string | Symbol name |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "linear",
        "list": [
            {
                "id": 1,
                "symbol": "BTCUSDT",
                "riskLimitValue": "2000000",
                "maintenanceMargin": "0.005",
                "initialMargin": "0.01",
                "isLowestRisk": 1,
                "maxLeverage": "100.00"
            }
        ]
    },
    "retExtInfo": {},
    "time": 1672764159969
}
```

## Get Delivery Price
Get delivery price for options and futures.

### Endpoint
```http
GET /v5/market/delivery-price
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | `linear` for USDT/USDC Futures, `inverse` for Inverse Futures, `option` for Options |
| symbol | No | string | Symbol name |
| baseCoin | No | string | Base coin, e.g., `BTC`. For `option` category only |
| limit | No | integer | Limit for data size. Default: `50` |
| cursor | No | string | Cursor for pagination |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "category": "linear",
        "nextPageCursor": "...",
        "list": [
            {
                "symbol": "BTCUSDT",
                "deliveryPrice": "16500.00",
                "deliveryTime": "1672387200000"
            }
        ]
    },
    "retExtInfo": {},
    "time": 1672764159969
}
```

## Get Long Short Ratio
Get account long short ratio.

### Endpoint
```http
GET /v5/market/account-ratio
```

### Parameters
| Parameter | Required | Type | Description |
|-----------|----------|------|-------------|
| category | Yes | string | `linear` for USDT/USDC, `inverse` for Inverse |
| symbol | Yes | string | Symbol name |
| period | Yes | string | Data cycle. `5min`, `15min`, `30min`, `1h`, `4h`, `1d` |
| limit | No | integer | Limit for data size. [`1`, `500`]. Default: `50` |

### Response
```json
{
    "retCode": 0,
    "retMsg": "OK",
    "result": {
        "list": [
            {
                "symbol": "BTCUSDT",
                "buyRatio": "0.5",
                "sellRatio": "0.5",
                "timestamp": "1672764000000"
            }
        ]
    },
    "retExtInfo": {},
    "time": 1672764159969
}
```

## Error Codes
For error codes and messages, please refer to the [Error Codes](../error-codes.md) documentation.

## Rate Limits
For rate limits information, please refer to the [Rate Limits](../rate-limits.md) documentation.
