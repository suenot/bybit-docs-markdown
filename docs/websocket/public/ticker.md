# Ticker Stream

Subscribe to real-time ticker data for market information updates.

## Topic Format
```
tickers.{symbol}
```

## Features
- Push frequency:
  - Derivatives & Options: 100ms
  - Spot: Real-time
- Spot & Option messages are snapshot only
- Linear/Inverse markets support both snapshot and delta updates

## Message Format

### Linear/Inverse Markets

```typescript
{
    "topic": string,     // Topic name
    "type": string,      // "snapshot" or "delta"
    "cs": number,        // Cross sequence
    "ts": number,        // System timestamp (ms)
    "data": {
        "symbol": string,            // Symbol name
        "tickDirection": string,     // Tick direction
        "price24hPcnt": string,     // 24h price change percentage
        "lastPrice": string,        // Last traded price
        "prevPrice24h": string,     // Price 24h ago
        "highPrice24h": string,     // 24h high
        "lowPrice24h": string,      // 24h low
        "prevPrice1h": string,      // Price 1h ago
        "markPrice": string,        // Mark price
        "indexPrice": string,       // Index price
        "openInterest": string,     // Open interest size
        "openInterestValue": string, // Open interest value
        "turnover24h": string,      // 24h turnover
        "volume24h": string,        // 24h volume
        "nextFundingTime": string,  // Next funding timestamp (ms)
        "fundingRate": string,      // Funding rate
        "bid1Price": string,        // Best bid price
        "bid1Size": string,         // Best bid size
        "ask1Price": string,        // Best ask price
        "ask1Size": string,         // Best ask size
        "deliveryTime": string      // Delivery time (futures only)
    }
}
```

### Spot Markets

```typescript
{
    "topic": string,     // Topic name
    "type": string,      // Always "snapshot"
    "ts": number,        // System timestamp (ms)
    "data": {
        "symbol": string,            // Symbol name
        "lastPrice": string,        // Last traded price
        "highPrice24h": string,     // 24h high
        "lowPrice24h": string,      // 24h low
        "prevPrice24h": string,     // Price 24h ago
        "volume24h": string,        // 24h volume
        "turnover24h": string,      // 24h turnover
        "price24hPcnt": string,     // 24h price change percentage
        "usdIndexPrice": string,    // USD index price
        "bid1Price": string,        // Best bid price
        "bid1Size": string,         // Best bid size
        "ask1Price": string,        // Best ask price
        "ask1Size": string         // Best ask size
    }
}
```

### Options Markets

```typescript
{
    "topic": string,     // Topic name
    "type": string,      // Always "snapshot"
    "ts": number,        // System timestamp (ms)
    "data": {
        "symbol": string,            // Symbol name
        "bidPrice": string,         // Best bid price
        "bidSize": string,          // Best bid size
        "bidIv": string,           // Bid IV
        "askPrice": string,         // Best ask price
        "askSize": string,          // Best ask size
        "askIv": string,           // Ask IV
        "lastPrice": string,        // Last traded price
        "highPrice24h": string,     // 24h high
        "lowPrice24h": string,      // 24h low
        "markPrice": string,        // Mark price
        "indexPrice": string,       // Index price
        "markPriceIv": string,     // Mark price IV
        "underlyingPrice": string,  // Underlying price
        "openInterest": string,     // Open interest size
        "turnover24h": string,      // 24h turnover
        "volume24h": string,        // 24h volume
        "totalVolume": string,      // Total volume
        "totalTurnover": string,    // Total turnover
        "delta": string,           // Delta
        "gamma": string,           // Gamma
        "vega": string,            // Vega
        "theta": string,           // Theta
        "predictedDeliveryPrice": string  // Predicted delivery price
    }
}
```

## Implementation Example (Python)

```python
from pybit.unified_trading import WebSocket
from time import sleep

def process_ticker(message):
    """Process ticker messages"""
    data = message["data"]
    
    print(f"""
        Ticker Update:
        Symbol: {data['symbol']}
        Last Price: {data['lastPrice']}
        24h Change: {data['price24hPcnt']}%
        24h High: {data['highPrice24h']}
        24h Low: {data['lowPrice24h']}
        24h Volume: {data['volume24h']}
    """)

# Initialize WebSocket connection
ws = WebSocket(
    testnet=True,
    channel_type="linear",
)

# Subscribe to ticker stream
ws.ticker_stream(
    symbol="BTCUSDT",
    callback=process_ticker
)

# Keep connection alive
while True:
    sleep(1)
```

## Best Practices

1. **Data Processing**
   - Handle both snapshot and delta updates (for Linear/Inverse)
   - Process messages in sequence
   - Validate data consistency

2. **Performance**
   - Buffer messages if needed
   - Handle high update frequencies
   - Monitor processing latency

3. **Analysis**
   - Track price movements
   - Monitor funding rates
   - Calculate market statistics

4. **Resource Management**
   - Clean up old data
   - Monitor memory usage
   - Implement rate limiting if needed

## Additional Information

- Delta updates only contain changed fields
- Funding information available for perpetual contracts
- Options data includes Greeks and IVs
- Delivery time available for futures contracts
