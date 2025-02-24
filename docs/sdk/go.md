# Go SDK

The official Go SDK for Bybit API v5 is `bybit.go.api`. It provides a robust and high-performance interface to interact with both REST API and WebSocket API.

## Requirements

- Go 1.21.0 or higher
- Dependencies:
  ```go
  require (
      github.com/google/uuid v1.4.0
      github.com/gorilla/websocket v1.5.1
      github.com/stretchr/testify v1.8.4
  )
  ```

## Installation

Add the following to your `go.mod` file:
```go
require github.com/bybit-exchange/bybit.go.api latest
```

:::tip
Always use the latest version of bybit.go.api for full v5 API support
:::

## REST API Usage

### Client Initialization
```go
import "github.com/bybit-exchange/bybit.go.api"

// Create client for mainnet
client := bybit.NewBybitHttpClient(
    "YOUR_API_KEY",
    "YOUR_API_SECRET",
    bybit.WithBaseURL(bybit.MAINNET)
)

// For testnet
testnetClient := bybit.NewBybitHttpClient(
    "YOUR_API_KEY",
    "YOUR_API_SECRET",
    bybit.WithBaseURL(bybit.TESTNET)
)

// Optional: Configure client options
client.WithTimeout(5 * time.Second)  // Set request timeout
client.WithDebug(true)  // Enable debug logging
```

### Market Data Examples

```go
// Get instruments info
params := map[string]interface{}{
    "category": "spot",
    "symbol": "BTCUSDT",
}
result, err := client.NewMarketDataService().
    GetInstrumentsInfo(context.Background(), params)
if err != nil {
    log.Printf("Error getting instruments info: %v", err)
    return
}
fmt.Println(bybit.PrettyPrint(result))

// Get orderbook (depth: 1-200)
params = map[string]interface{}{
    "category": "spot",
    "symbol": "BTCUSDT",
    "limit": 25,  // Default 25, max 200
}
result, err = client.NewMarketDataService().
    GetOrderbook(context.Background(), params)
if err != nil {
    log.Printf("Error getting orderbook: %v", err)
    return
}
fmt.Println(bybit.PrettyPrint(result))

// Get kline/candlestick data
params = map[string]interface{}{
    "category": "spot",
    "symbol": "BTCUSDT",
    "interval": "D",  // Available: 1,3,5,15,30,60,120,240,360,720,D,M,W
    "limit": 200,     // Default 200, max 1000
    "start": time.Now().Add(-24*time.Hour).UnixMilli(),
}
result, err = client.NewMarketDataService().
    GetKline(context.Background(), params)
if err != nil {
    log.Printf("Error getting kline data: %v", err)
    return
}
fmt.Println(bybit.PrettyPrint(result))
```

### Trading Examples

```go
// Place a limit order
params := map[string]interface{}{
    "category": "spot",
    "symbol": "BTCUSDT",
    "side": "Buy",
    "orderType": "Limit",
    "qty": "0.001",
    "price": "35000",
    "timeInForce": "GTC",
    "orderLinkId": "test-order-001",  // Optional custom order ID
}
orderResult, err := client.NewTradeService().
    PlaceOrder(context.Background(), params)
if err != nil {
    log.Printf("Error placing order: %v", err)
    return
}
fmt.Println(bybit.PrettyPrint(orderResult))

// Place a market order
params = map[string]interface{}{
    "category": "spot",
    "symbol": "BTCUSDT",
    "side": "Buy",
    "orderType": "Market",
    "qty": "0.001",
}
orderResult, err = client.NewTradeService().
    PlaceOrder(context.Background(), params)
if err != nil {
    log.Printf("Error placing market order: %v", err)
    return
}
fmt.Println(bybit.PrettyPrint(orderResult))

// Place a stop order
params = map[string]interface{}{
    "category": "spot",
    "symbol": "BTCUSDT",
    "side": "Buy",
    "orderType": "Limit",
    "qty": "0.001",
    "price": "35000",
    "timeInForce": "GTC",
    "triggerPrice": "34800",
    "triggerBy": "LastPrice",  // LastPrice, IndexPrice, MarkPrice
}
orderResult, err = client.NewTradeService().
    PlaceOrder(context.Background(), params)
if err != nil {
    log.Printf("Error placing stop order: %v", err)
    return
}
fmt.Println(bybit.PrettyPrint(orderResult))

// Cancel an order
params = map[string]interface{}{
    "category": "spot",
    "symbol": "BTCUSDT",
    "orderId": "1234567890",  // Either orderId or orderLinkId is required
}
cancelResult, err := client.NewTradeService().
    CancelOrder(context.Background(), params)
if err != nil {
    log.Printf("Error canceling order: %v", err)
    return
}
fmt.Println(bybit.PrettyPrint(cancelResult))

// Get order history
params = map[string]interface{}{
    "category": "spot",
    "symbol": "BTCUSDT",
    "limit": 50,  // Default 50, max 200
    "orderStatus": "Filled,Cancelled",  // Optional filter
}
historyResult, err := client.NewTradeService().
    GetOrderHistory(context.Background(), params)
if err != nil {
    log.Printf("Error getting order history: %v", err)
    return
}
fmt.Println(bybit.PrettyPrint(historyResult))
```

### Account Examples

```go
// Get wallet balance
params := map[string]interface{}{
    "accountType": "UNIFIED",  // UNIFIED, CONTRACT, SPOT
    "coin": "USDT",           // Optional
}
balanceResult, err := client.NewAccountService().
    GetWalletBalance(context.Background(), params)
if err != nil {
    log.Printf("Error getting wallet balance: %v", err)
    return
}
fmt.Println(bybit.PrettyPrint(balanceResult))

// Get positions
params = map[string]interface{}{
    "category": "linear",  // linear, inverse
    "symbol": "BTCUSDT",  // Optional
}
positionResult, err := client.NewAccountService().
    GetPositions(context.Background(), params)
if err != nil {
    log.Printf("Error getting positions: %v", err)
    return
}
fmt.Println(bybit.PrettyPrint(positionResult))

// Set leverage
params = map[string]interface{}{
    "category": "linear",
    "symbol": "BTCUSDT",
    "buyLeverage": "2",
    "sellLeverage": "2",
}
leverageResult, err := client.NewAccountService().
    SetLeverage(context.Background(), params)
if err != nil {
    log.Printf("Error setting leverage: %v", err)
    return
}
fmt.Println(bybit.PrettyPrint(leverageResult))
```

## WebSocket API Usage

### Market Data WebSocket
```go
import (
    "context"
    "log"
    "time"
)

// Create WebSocket client for public channels
ws := bybit.NewBybitWebSocket(
    bybit.WebSocketConfig{
        BaseURL: "wss://stream.bybit.com/v5/public/spot",
        DebugMode: true,
        AutoReconnect: true,
        ReconnectInterval: time.Second * 5,
    },
)

// Message handler
handler := func(message []byte) error {
    log.Printf("Received message: %s", string(message))
    return nil
}

// Error handler
errHandler := func(err error) {
    log.Printf("WebSocket error: %v", err)
}

// Connect and subscribe
ctx := context.Background()
topics := []string{
    "orderbook.1.BTCUSDT",  // Depth 1
    "trade.BTCUSDT",        // Trade updates
    "kline.D.BTCUSDT",      // Daily kline
}

if err := ws.Subscribe(ctx, topics, handler, errHandler); err != nil {
    log.Printf("Error subscribing: %v", err)
    return
}

// Keep connection alive
select {}
```

### Private WebSocket
```go
// Create WebSocket client for private channels
ws := bybit.NewBybitWebSocket(
    bybit.WebSocketConfig{
        BaseURL: "wss://stream.bybit.com/v5/private",
        APIKey: "YOUR_API_KEY",
        APISecret: "YOUR_API_SECRET",
        DebugMode: true,
        AutoReconnect: true,
        ReconnectInterval: time.Second * 5,
    },
)

// Message handler
handler := func(message []byte) error {
    log.Printf("Received private message: %s", string(message))
    return nil
}

// Error handler
errHandler := func(err error) {
    log.Printf("WebSocket error: %v", err)
}

// Connect and subscribe to private topics
ctx := context.Background()
topics := []string{
    "order",           // Order updates
    "position",        // Position updates
    "wallet",          // Wallet updates
}

if err := ws.Subscribe(ctx, topics, handler, errHandler); err != nil {
    log.Printf("Error subscribing to private topics: %v", err)
    return
}

// Keep connection alive
select {}
```

## Error Handling

```go
import (
    "errors"
    "log"
)

// Handle API errors
func handleAPIError(err error) {
    var apiErr *bybit.APIError
    if errors.As(err, &apiErr) {
        log.Printf("API Error: Code=%d, Message=%s", 
            apiErr.Code, apiErr.Message)
        return
    }
    log.Printf("Unexpected error: %v", err)
}

// Example usage
params := map[string]interface{}{
    "category": "spot",
    "symbol": "BTCUSDT",
    "side": "Buy",
    "orderType": "Limit",
    "qty": "0.001",
    "price": "35000",
}

result, err := client.NewTradeService().
    PlaceOrder(context.Background(), params)
if err != nil {
    handleAPIError(err)
    return
}
```

## Best Practices

1. **Error Handling**
```go
// Implement retry logic
func withRetry(fn func() error, maxRetries int) error {
    var err error
    for i := 0; i < maxRetries; i++ {
        if err = fn(); err == nil {
            return nil
        }
        time.Sleep(time.Second * time.Duration(i+1))
    }
    return err
}

// Example usage
err := withRetry(func() error {
    _, err := client.NewTradeService().
        PlaceOrder(context.Background(), params)
    return err
}, 3)
```

2. **WebSocket Management**
```go
// Implement heartbeat check
func maintainWebSocket(ws *bybit.WebSocket) {
    ticker := time.NewTicker(time.Second * 30)
    defer ticker.Stop()

    for range ticker.C {
        if !ws.IsConnected() {
            log.Println("Reconnecting WebSocket...")
            ws.Reconnect()
        }
    }
}
```

3. **Rate Limiting**
```go
// Implement rate limiter
type RateLimiter struct {
    ticker *time.Ticker
    stop   chan struct{}
}

func NewRateLimiter(interval time.Duration) *RateLimiter {
    return &RateLimiter{
        ticker: time.NewTicker(interval),
        stop:   make(chan struct{}),
    }
}

func (r *RateLimiter) Wait() {
    <-r.ticker.C
}

// Example usage
limiter := NewRateLimiter(time.Millisecond * 100)
for _, order := range orders {
    limiter.Wait()
    client.NewTradeService().PlaceOrder(context.Background(), order)
}
```

4. **Logging**
```go
// Implement structured logging
type Logger struct {
    debug bool
}

func (l *Logger) Log(level string, msg string, fields map[string]interface{}) {
    if !l.debug && level == "debug" {
        return
    }
    log.Printf("[%s] %s %v", level, msg, fields)
}

// Example usage
logger := &Logger{debug: true}
logger.Log("info", "Placing order", map[string]interface{}{
    "symbol": "BTCUSDT",
    "side":   "Buy",
    "price":  "35000",
})
