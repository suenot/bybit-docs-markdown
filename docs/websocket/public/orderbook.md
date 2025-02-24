# WebSocket Public Channels

## Orderbook

Subscribe to real-time orderbook data with different depths.

### Available Depths & Update Frequency

**Linear & Inverse Markets:**
- Level 1: 10ms update frequency
- Level 50: 20ms update frequency
- Level 200: 100ms update frequency
- Level 500: 100ms update frequency

**Spot Markets:**
- Level 1: 10ms update frequency
- Level 50: 20ms update frequency
- Level 200: 200ms update frequency

**Options Markets:**
- Level 25: 20ms update frequency
- Level 100: 100ms update frequency

### Topic Format
```
orderbook.{depth}.{symbol}
```
Example: `orderbook.1.BTCUSDT`

### Processing Orderbook Data

The WebSocket stream provides two types of messages:
1. **Snapshot** - Full orderbook state
2. **Delta** - Incremental updates

#### Processing Rules:

1. Initial Connection:
   - You receive a full `snapshot` of the orderbook
   - Store this as your local orderbook state

2. Delta Updates:
   - Process incoming `delta` messages to update your local orderbook
   - For amount = 0: Delete the price level
   - For new price level: Insert it
   - For existing price level: Update the amount

3. New Snapshots:
   - If you receive a new `snapshot`, reset your local orderbook
   - This happens if there's an issue on Bybit's end

> **Note:** For Linear & Inverse Level 1 data, if no changes occur for 3 seconds, a new `snapshot` is sent with the same update ID (`u`).

### Message Format

```typescript
{
    "topic": string,      // Topic name
    "type": string,       // "snapshot" or "delta"
    "ts": number,         // System timestamp (ms)
    "data": {
        "s": string,      // Symbol name
        "b": [            // Bids array (sorted by price descending)
            [string, string]  // [price, size]
        ],
        "a": [            // Asks array (sorted by price ascending)
            [string, string]  // [price, size]
        ],
        "u": number,      // Update ID
        "seq": number,    // Cross sequence
        "cts": number     // Match engine timestamp
    }
}
```

### Example Implementation (Python)

```python
from pybit.unified_trading import WebSocket
from time import sleep

def process_orderbook(message):
    """Process orderbook messages"""
    if message["type"] == "snapshot":
        # Reset local orderbook
        process_snapshot(message["data"])
    elif message["type"] == "delta":
        # Update local orderbook
        process_delta(message["data"])

def process_snapshot(data):
    """Process full orderbook snapshot"""
    orderbook = {
        "bids": {price: size for price, size in data["b"]},
        "asks": {price: size for price, size in data["a"]}
    }
    return orderbook

def process_delta(data):
    """Process orderbook delta update"""
    # Update bids
    for price, size in data["b"]:
        if float(size) == 0:
            orderbook["bids"].pop(price, None)
        else:
            orderbook["bids"][price] = size
    
    # Update asks
    for price, size in data["a"]:
        if float(size) == 0:
            orderbook["asks"].pop(price, None)
        else:
            orderbook["asks"][price] = size

# Initialize WebSocket connection
ws = WebSocket(
    testnet=True,
    channel_type="linear",
)

# Subscribe to orderbook
ws.orderbook_stream(
    depth=50,
    symbol="BTCUSDT",
    callback=process_orderbook
)

# Keep connection alive
while True:
    sleep(1)
```

### Best Practices

1. **Data Processing**
   - Always process messages in sequence
   - Validate update IDs to ensure no messages are missed
   - Handle reconnection scenarios properly

2. **Performance**
   - Choose appropriate depth based on your needs
   - Consider using lower depths for higher update frequency
   - Buffer messages if processing can't keep up

3. **Error Handling**
   - Implement proper error recovery
   - Reset local orderbook on new snapshots
   - Monitor for connection issues

4. **Resource Management**
   - Clean up old data
   - Monitor memory usage
   - Implement rate limiting if needed
