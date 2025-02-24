# Node.js SDK

The official Node.js SDK for Bybit API v5 is `bybit-api`. It provides a complete integration with both REST API and WebSocket API, with TypeScript support.

## Installation

```bash
npm install --save bybit-api
```

:::tip
Always use the latest version of bybit-api for full v5 API support
:::

## REST API Usage

### Client Initialization
```javascript
const { RestClientV5 } = require('bybit-api');

// Create client for mainnet
const client = new RestClientV5({
    key: 'YOUR_API_KEY',
    secret: 'YOUR_API_SECRET',
    testnet: false,
    recv_window: 5000,                // Optional: custom recv_window
    enable_time_sync: true,           // Optional: auto-sync time
    strict_param_validation: true,     // Optional: enable strict validation
    baseUrl: 'https://api.bybit.com'  // Optional: custom base URL
});

// For testnet
const testnetClient = new RestClientV5({
    key: 'YOUR_API_KEY',
    secret: 'YOUR_API_SECRET',
    testnet: true
});

// For public-only API calls, you can skip the key and secret
const publicClient = new RestClientV5({});
```

### Market Data Examples

```javascript
// Get instruments info
client.getInstrumentsInfo({ 
    category: 'spot', 
    symbol: 'BTCUSDT' 
})
    .then(result => {
        console.log("Instruments info:", result);
    })
    .catch(err => {
        console.error("Error getting instruments info:", err);
    });

// Get orderbook (depth: 1-200)
client.getOrderbook({ 
    category: 'spot', 
    symbol: 'BTCUSDT',
    limit: 50  // Default 25, max 200
})
    .then(result => {
        console.log("Orderbook:", result);
    })
    .catch(err => {
        console.error("Error getting orderbook:", err);
    });

// Get tickers
client.getTickers({ 
    category: 'spot', 
    symbol: 'BTCUSDT' 
})
    .then(result => {
        console.log("Tickers:", result);
    })
    .catch(err => {
        console.error("Error getting tickers:", err);
    });

// Get kline/candlestick data
client.getKline({
    category: 'spot',
    symbol: 'BTCUSDT',
    interval: 'D',    // Available: 1,3,5,15,30,60,120,240,360,720,D,M,W
    limit: 200,       // Default 200, max 1000
    start: Date.now() - 24 * 60 * 60 * 1000  // Optional: start time
})
    .then(result => {
        console.log("Kline data:", result);
    })
    .catch(err => {
        console.error("Error getting kline data:", err);
    });
```

### Trading Examples

```javascript
// Place a market order
client.submitOrder({
    category: 'spot',
    symbol: 'BTCUSDT',
    side: 'Buy',
    orderType: 'Market',
    qty: '0.001',
    orderLinkId: 'test-order-001'  // Optional: custom order ID
})
    .then(result => {
        console.log("Market order placed:", result);
    })
    .catch(err => {
        console.error("Error placing market order:", err);
    });

// Place a limit order
client.submitOrder({
    category: 'spot',
    symbol: 'BTCUSDT',
    side: 'Buy',
    orderType: 'Limit',
    qty: '0.001',
    price: '35000',
    timeInForce: 'GTC',
    postOnly: true,  // Optional: ensure maker order
    reduceOnly: false  // Optional: reduce position only
})
    .then(result => {
        console.log("Limit order placed:", result);
    })
    .catch(err => {
        console.error("Error placing limit order:", err);
    });

// Place a stop order
client.submitOrder({
    category: 'spot',
    symbol: 'BTCUSDT',
    side: 'Buy',
    orderType: 'Limit',
    qty: '0.001',
    price: '35000',
    timeInForce: 'GTC',
    triggerPrice: '34800',
    triggerBy: 'LastPrice',  // LastPrice, IndexPrice, MarkPrice
})
    .then(result => {
        console.log("Stop order placed:", result);
    })
    .catch(err => {
        console.error("Error placing stop order:", err);
    });

// Cancel an order
client.cancelOrder({
    category: 'spot',
    symbol: 'BTCUSDT',
    orderId: '1234567890'  // Either orderId or orderLinkId is required
})
    .then(result => {
        console.log("Order cancelled:", result);
    })
    .catch(err => {
        console.error("Error cancelling order:", err);
    });

// Get open orders
client.getOpenOrders({
    category: 'spot',
    symbol: 'BTCUSDT',
    limit: 50,  // Default 50, max 200
    orderStatus: ['New', 'PartiallyFilled']  // Optional filter
})
    .then(result => {
        console.log("Open orders:", result);
    })
    .catch(err => {
        console.error("Error getting open orders:", err);
    });

// Get order history
client.getHistoricOrders({
    category: 'spot',
    symbol: 'BTCUSDT',
    limit: 50,  // Default 50, max 200
    orderStatus: ['Filled', 'Cancelled']  // Optional filter
})
    .then(result => {
        console.log("Order history:", result);
    })
    .catch(err => {
        console.error("Error getting order history:", err);
    });
```

### Account Examples

```javascript
// Get wallet balance
client.getWalletBalance({
    accountType: 'UNIFIED',  // UNIFIED, CONTRACT, SPOT
    coin: 'USDT'  // Optional
})
    .then(result => {
        console.log("Wallet balance:", result);
    })
    .catch(err => {
        console.error("Error getting wallet balance:", err);
    });

// Get positions
client.getPositions({
    category: 'linear',  // linear, inverse
    symbol: 'BTCUSDT',  // Optional
    limit: 200  // Default 200
})
    .then(result => {
        console.log("Positions:", result);
    })
    .catch(err => {
        console.error("Error getting positions:", err);
    });

// Set leverage
client.setLeverage({
    category: 'linear',
    symbol: 'BTCUSDT',
    buyLeverage: '2',
    sellLeverage: '2'
})
    .then(result => {
        console.log("Leverage set:", result);
    })
    .catch(err => {
        console.error("Error setting leverage:", err);
    });

// Get account info
client.getAccountInfo()
    .then(result => {
        console.log("Account info:", result);
    })
    .catch(err => {
        console.error("Error getting account info:", err);
    });
```

## WebSocket API Usage

### Market Data WebSocket
```javascript
const { WebsocketClient } = require('bybit-api');

// Create WebSocket client for public channels
const wsClient = new WebsocketClient({
    market: 'v5',
    pongTimeout: 1000,
    pingInterval: 10000,
    reconnectTimeout: 500,
    restartTimeout: 500,
});

// Handle connection events
wsClient.on('update', data => {
    console.log('Message received:', data);
});

wsClient.on('error', err => {
    console.error('WebSocket error:', err);
});

wsClient.on('close', () => {
    console.log('WebSocket connection closed');
});

wsClient.on('reconnect', () => {
    console.log('WebSocket reconnecting...');
});

// Subscribe to multiple topics
wsClient.subscribe([
    'orderbook.1.BTCUSDT',  // Depth 1
    'orderbook.50.BTCUSDT', // Depth 50
    'trade.BTCUSDT',        // Trade updates
    'kline.D.BTCUSDT',      // Daily kline
    'tickers.BTCUSDT'       // 24hr ticker
]);

// Unsubscribe example
setTimeout(() => {
    wsClient.unsubscribe(['orderbook.1.BTCUSDT']);
}, 60000);
```

### Private WebSocket
```javascript
const { WebsocketClient } = require('bybit-api');

// Create WebSocket client for private channels
const wsClient = new WebsocketClient({
    market: 'v5',
    key: 'YOUR_API_KEY',
    secret: 'YOUR_API_SECRET',
    testnet: false,
    pongTimeout: 1000,
    pingInterval: 10000,
    reconnectTimeout: 500,
});

// Handle connection events
wsClient.on('update', data => {
    console.log('Private message received:', data);
});

wsClient.on('error', err => {
    console.error('WebSocket error:', err);
});

wsClient.on('authenticated', () => {
    console.log('WebSocket authenticated successfully');
});

// Subscribe to private topics
wsClient.subscribe([
    'order',           // Order updates
    'position',        // Position updates
    'wallet',          // Wallet updates
    'execution'        // Trade execution updates
]);
```

## Error Handling

```javascript
// Using async/await with try/catch
async function placeOrder() {
    try {
        const order = await client.submitOrder({
            category: 'spot',
            symbol: 'BTCUSDT',
            side: 'Buy',
            orderType: 'Limit',
            qty: '0.001',
            price: '35000'
        });
        console.log("Order placed:", order);
    } catch (err) {
        if (err.response) {
            // The request was made and the server responded with error
            console.error("API Error:", {
                code: err.response.data.retCode,
                message: err.response.data.retMsg
            });
        } else if (err.request) {
            // The request was made but no response received
            console.error("Network Error:", err.message);
        } else {
            // Other errors
            console.error("Error:", err.message);
        }
    }
}

// Handle rate limits
client.on('rate-limit-exceeded', (limitInfo) => {
    console.warn('Rate limit exceeded:', limitInfo);
});
```

## Best Practices

### 1. Rate Limiting
```javascript
const { RateLimiter } = require('bybit-api');

// Create rate limiter
const limiter = new RateLimiter({
    max: 50,           // Max requests
    period: 1000,      // Per second
    concurrency: 10    // Max concurrent requests
});

// Use rate limiter
async function batchOrders(orders) {
    for (const order of orders) {
        await limiter.schedule(() => 
            client.submitOrder(order)
        );
    }
}
```

### 2. WebSocket Management
```javascript
// Implement reconnection logic
function setupWebSocket() {
    const wsClient = new WebsocketClient({
        market: 'v5',
        // ... other config
    });

    // Monitor connection health
    let heartbeat = Date.now();
    wsClient.on('update', () => {
        heartbeat = Date.now();
    });

    // Check connection health periodically
    setInterval(() => {
        if (Date.now() - heartbeat > 30000) {
            console.log('WebSocket connection stale, reconnecting...');
            wsClient.reconnect();
        }
    }, 30000);

    return wsClient;
}
```

### 3. Error Recovery
```javascript
// Implement retry logic
async function withRetry(fn, maxRetries = 3) {
    for (let i = 0; i < maxRetries; i++) {
        try {
            return await fn();
        } catch (err) {
            if (i === maxRetries - 1) throw err;
            
            // Exponential backoff
            const delay = Math.pow(2, i) * 1000;
            await new Promise(resolve => setTimeout(resolve, delay));
        }
    }
}

// Example usage
const order = await withRetry(() => 
    client.submitOrder({
        category: 'spot',
        symbol: 'BTCUSDT',
        side: 'Buy',
        orderType: 'Limit',
        qty: '0.001',
        price: '35000'
    })
);
```

### 4. Logging
```javascript
// Implement structured logging
const logger = {
    info: (msg, data) => {
        console.log(JSON.stringify({
            timestamp: new Date().toISOString(),
            level: 'info',
            message: msg,
            ...data
        }));
    },
    error: (msg, error, data = {}) => {
        console.error(JSON.stringify({
            timestamp: new Date().toISOString(),
            level: 'error',
            message: msg,
            error: error.message,
            stack: error.stack,
            ...data
        }));
    }
};

// Example usage
try {
    const order = await client.submitOrder({/*...*/});
    logger.info('Order placed', { order });
} catch (err) {
    logger.error('Failed to place order', err, {
        symbol: 'BTCUSDT',
        side: 'Buy'
    });
}
