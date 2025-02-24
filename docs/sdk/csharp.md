# C# SDK

The official C# SDK for Bybit API v5 is `bybit.net.api`. It provides a robust and efficient .NET connector for both REST and WebSocket APIs.

## Features

- Public WebSocket Streaming
- Private WebSocket Streaming
- Market Data Retrieval
- Trade Execution
- Position Management
- Account and Asset Info Retrieval
- User Management
- Upgrade History
- Spot Margin UTA & Classical Service
- Broker Earning Data

## Requirements

- .NET 6 or newer
- Dependencies:
  - Microsoft.Extensions.Logging 7.0.0
  - Newtonsoft 13.0.3

## Installation

### Using dotnet CLI
```bash
dotnet add package bybit.net.api
```

### Using NuGet Package Manager
```powershell
NuGet\Install-Package bybit.net.api
```

### Using Package Reference in .csproj
```xml
<PackageReference Include="bybit.net.api"/>
```

## REST API Usage

### Client Initialization
```csharp
// For mainnet
var tradeService = new BybitTradeService(
    apiKey: "YOUR_API_KEY",
    apiSecret: "YOUR_API_SECRET"
);

// For testnet
var testnetTradeService = new BybitTradeService(
    apiKey: "YOUR_API_KEY",
    apiSecret: "YOUR_API_SECRET",
    useTestnet: true
);

// For public endpoints only
var marketService = new BybitMarketDataService();
```

### Market Data Examples

```csharp
// Initialize market data service
var market = new BybitMarketDataService();

// Get market kline (candlestick) data
var klineInfo = await market.GetMarketKline(
    Category.SPOT,
    "BTCUSDT",
    MarketInterval.OneMinute
);
Console.WriteLine(klineInfo);
```

### Trading Examples

```csharp
// Initialize trade service
var tradeService = new BybitTradeService(
    apiKey: "YOUR_API_KEY",
    apiSecret: "YOUR_API_SECRET"
);

// Place a single market order
var orderInfo = await tradeService.PlaceOrder(
    category: Category.LINEAR,
    symbol: "BLZUSDT",
    side: Side.BUY,
    orderType: OrderType.MARKET,
    qty: "15",
    timeInForce: TimeInForce.GTC
);
Console.WriteLine(orderInfo);

// Place batch orders using Dictionary
Dictionary<string, object> order1 = new()
{
    { "symbol", "XRPUSDT" },
    { "orderType", "Limit" },
    { "side", "Buy" },
    { "qty", "10" },
    { "price", "0.6080" },
    { "timeInForce", "GTC" }
};

Dictionary<string, object> order2 = new()
{
    { "symbol", "BLZUSDT" },
    { "orderType", "Limit" },
    { "side", "Buy" },
    { "qty", "10" },
    { "price", "0.6080" },
    { "timeInForce", "GTC" }
};

List<Dictionary<string, object>> request = new() { order1, order2 };
var batchOrderResult = await tradeService.PlaceBatchOrder(
    category: Category.LINEAR,
    request: request
);
Console.WriteLine(batchOrderResult);

// Place batch orders using OrderRequest class
var order1 = new OrderRequest
{
    Symbol = "XRPUSDT",
    OrderType = OrderType.LIMIT.Value,
    Side = Side.BUY.Value,
    Qty = "10",
    Price = "0.6080",
    TimeInForce = TimeInForce.GTC.Value
};

var order2 = new OrderRequest
{
    Symbol = "BLZUSDT",
    OrderType = OrderType.LIMIT.Value,
    Side = Side.BUY.Value,
    Qty = "10",
    Price = "0.6080",
    TimeInForce = TimeInForce.GTC.Value
};

var request = new List<OrderRequest> { order1, order2 };
var batchOrderResult = await tradeService.PlaceBatchOrder(
    category: Category.LINEAR,
    request: request
);
Console.WriteLine(batchOrderResult);
```

### Account Examples

```csharp
// Initialize account service
var accountService = new BybitAccountService(
    apiKey: "YOUR_API_KEY",
    apiSecret: "YOUR_API_SECRET"
);

// Get account balance
var accountInfo = await accountService.GetAccountBalance(
    accountType: AccountType.Unified
);
Console.WriteLine(accountInfo);

// Initialize position service
var positionService = new BybitPositionService(
    apiKey: "YOUR_API_KEY",
    apiSecret: "YOUR_API_SECRET"
);

// Get position information
var positionInfo = await positionService.GetPositionInfo(
    category: Category.LINEAR,
    symbol: "BLZUSDT"
);
Console.WriteLine(positionInfo);
```

## WebSocket API Usage

### Public WebSocket
```csharp
// Create WebSocket client for public channels
var linearWebsocket = new BybitLinearWebSocket(
    useTestNet: true,
    pingIntevral: 5
);

// Set up message handler
linearWebsocket.OnMessageReceived(
    (data) =>
    {
        Console.WriteLine(data);
        return Task.CompletedTask;
    },
    CancellationToken.None
);

// Connect and subscribe to trade updates
await linearWebsocket.ConnectAsync(
    new string[] { "publicTrade.BTCUSDT" },
    CancellationToken.None
);
```

### Private WebSocket
```csharp
// Create WebSocket client for private channels
var privateWebsocket = new BybitPrivateWebSocket(
    apiKey: "YOUR_API_KEY",
    apiSecret: "YOUR_API_SECRET",
    useTestNet: true,
    pingIntevral: 5,
    maxAliveTime: "120s"
);

// Set up message handler
privateWebsocket.OnMessageReceived(
    (data) =>
    {
        Console.WriteLine(data);
        return Task.CompletedTask;
    },
    CancellationToken.None
);

// Connect and subscribe to order updates
await privateWebsocket.ConnectAsync(
    new string[] { "order" },
    CancellationToken.None
);
```

## Best Practices

1. **Error Handling**
   - Implement proper try-catch blocks
   - Log errors for debugging
   - Use appropriate error recovery strategies

2. **WebSocket Management**
   - Set appropriate ping intervals
   - Implement reconnection logic
   - Handle connection errors gracefully
   - Use CancellationToken for proper cleanup

3. **Rate Limiting**
   - Monitor rate limits
   - Implement exponential backoff
   - Use WebSocket for real-time data

4. **Security**
   - Never hardcode API keys
   - Use environment variables or secure configuration
   - Regularly rotate API keys

## Additional Resources

- [Official Repository](https://github.com/bybit-exchange/bybit.net.api)
- [NuGet Package](https://www.nuget.org/packages/bybit.net.api)
- [API Documentation](https://bybit-exchange.github.io/docs/v5/intro)
- [Examples](https://github.com/bybit-exchange/bybit.net.api/tree/master/Bybit.Net.Sample)
