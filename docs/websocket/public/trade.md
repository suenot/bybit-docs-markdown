# Trade Stream

Subscribe to real-time trade data stream to receive information about executed trades.

## Topic Format
```
publicTrade.{symbol}
```
For options, use baseCoin, e.g., `publicTrade.BTC`

## Features
- Real-time trade updates
- Push frequency: Instant (real-time)
- Sorted by matching time in ascending order

## Message Format

```typescript
{
    "id": string,        // Message ID (unique for options)
    "topic": string,     // Topic name
    "type": string,      // Always "snapshot"
    "ts": number,        // System timestamp (ms)
    "data": [
        {
            "T": number,     // Trade timestamp (ms)
            "s": string,     // Symbol name
            "S": string,     // Taker side ("Buy" or "Sell")
            "v": string,     // Trade size
            "p": string,     // Trade price
            "L": string,     // Price change direction (for Perpetuals & Futures)
            "i": string,     // Trade ID
            "BT": boolean,   // Is block trade
            "RPI": boolean,  // Is RPI trade
            "mP": string,    // Mark price (options only)
            "iP": string,    // Index price (options only)
            "mIv": string,   // Mark IV (options only)
            "iv": string     // IV (options only)
        }
    ]
}
```

## Implementation Example (Python)

```python
from pybit.unified_trading import WebSocket
from time import sleep

def process_trade(message):
    """Process trade messages"""
    if message["type"] == "snapshot":
        for trade in message["data"]:
            print(f"""
                Trade Details:
                Symbol: {trade['s']}
                Price: {trade['p']}
                Size: {trade['v']}
                Side: {trade['S']}
                Time: {trade['T']}
                ID: {trade['i']}
            """)

# Initialize WebSocket connection
ws = WebSocket(
    testnet=True,
    channel_type="linear",
)

# Subscribe to trade stream
ws.trade_stream(
    symbol="BTCUSDT",
    callback=process_trade
)

# Keep connection alive
while True:
    sleep(1)
```

## Example Response

```json
{
    "topic": "publicTrade.BTCUSDT",
    "type": "snapshot",
    "ts": 1672304486868,
    "data": [
        {
            "T": 1672304486865,
            "s": "BTCUSDT",
            "S": "Buy",
            "v": "0.001",
            "p": "16578.50",
            "L": "PlusTick",
            "i": "20f43950-d8dd-5b31-9112-a178eb6023af",
            "BT": false
        }
    ]
}
```

## Best Practices

1. **Data Processing**
   - Process trades in sequence
   - Validate timestamps for order
   - Handle large trade volumes efficiently

2. **Performance**
   - Buffer messages if needed
   - Implement proper error handling
   - Monitor processing latency

3. **Analysis**
   - Track trade patterns
   - Calculate volume statistics
   - Monitor price movements

4. **Resource Management**
   - Clean up old trade data
   - Monitor memory usage
   - Implement rate limiting if needed

## Additional Information

- Trade data includes both regular and block trades
- RPI (Retail Price Improvement) trades are marked separately
- Options trades include additional fields for mark price, index price, and implied volatility
- Price direction (L) field helps track price movement patterns
