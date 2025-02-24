# Java SDK

The official Java SDK for Bybit API v5 is `bybit-java-api`. It provides a robust and high-performance interface to interact with both REST API and WebSocket API.

## Features

- Market Data Retrieval
- Trade Execution
- Position Management
- Account and Asset Info Retrieval
- User and Upgrade Management
- Public WebSocket Streaming
- Private WebSocket Streaming
- Lending Institution and Client
- Broker Earning Data

## Requirements

- Java 11 or higher

## Installation

### Maven
```xml
<dependency>
    <groupId>io.github.wuhewuhe</groupId>
    <artifactId>bybit-java-api</artifactId>
    <version>1.2.8</version>
</dependency>
```

### Gradle
```groovy
implementation group: 'io.github.wuhewuhe', name: 'bybit-java-api', version: '1.2.8'
```

## REST API Usage

### Client Initialization
```java
// Create client for mainnet
var client = BybitApiClientFactory.newInstance(
    "YOUR_API_KEY",
    "YOUR_API_SECRET",
    BybitApiConfig.MAINNET_DOMAIN,
    true  // Enable debug mode
).newAsyncTradeRestClient();

// For testnet
var testnetClient = BybitApiClientFactory.newInstance(
    "YOUR_API_KEY",
    "YOUR_API_SECRET",
    BybitApiConfig.TESTNET_DOMAIN,
    true
).newAsyncTradeRestClient();

// For public endpoints only
var publicClient = BybitApiClientFactory.newInstance(
    BybitApiConfig.MAINNET_DOMAIN,
    true
).newMarketDataRestClient();
```

### Market Data Examples

```java
// Get market data using builder pattern
var marketKLineRequest = MarketDataRequest.builder()
    .category(CategoryType.LINEAR)
    .symbol("BTCUSDT")
    .marketInterval(MarketInterval.WEEKLY)
    .build();

// Get weekly market kline
var marketKlineResult = client.getMarketLinesData(marketKLineRequest);
System.out.println(marketKlineResult);

// Get weekly market price kline
var marketPriceKlineResult = client.getMarketPriceLinesData(marketKLineRequest);
System.out.println(marketPriceKlineResult);

// Get weekly index price kline
var indexPriceKlineResult = client.getIndexPriceLinesData(marketKLineRequest);
System.out.println(indexPriceKlineResult);

// Get server time
var serverTime = client.getServerTime();
System.out.println(serverTime);
```

### Trading Examples

```java
// Place a single order using object
Map<String, Object> order = Map.of(
    "category", "option",
    "symbol", "BTC-29DEC23-10000-P",
    "side", "Buy",
    "orderType", "Limit",
    "orderIv", "0.1",
    "qty", "0.1",
    "price", "5"
);
client.createOrder(order, System.out::println);

// Place a single order using builder pattern
var newOrderRequest = TradeOrderRequest.builder()
    .category(CategoryType.LINEAR)
    .symbol("XRPUSDT")
    .side(Side.BUY)
    .orderType(TradeOrderType.MARKET)
    .qty("10")
    .timeInForce(TimeInForce.GOOD_TILL_CANCEL)
    .positionIdx(PositionIdx.ONE_WAY_MODE)
    .build();
client.createOrder(newOrderRequest, System.out::println);

// Amend order
var amendOrderRequest = TradeOrderRequest.builder()
    .orderId("1523347543495541248")
    .category(CategoryType.LINEAR)
    .symbol("XRPUSDT")
    .price("0.5")
    .qty("15")
    .build();
var amendedOrder = client.amendOrder(amendOrderRequest);
System.out.println(amendedOrder);

// Place batch orders
var orderRequests = Arrays.asList(
    TradeOrderRequest.builder()
        .category(CategoryType.OPTION)
        .symbol("BTC-10FEB23-24000-C")
        .side(Side.BUY)
        .orderType(TradeOrderType.LIMIT)
        .qty("0.1")
        .price("5")
        .orderIv("0.1")
        .timeInForce(TimeInForce.GOOD_TILL_CANCEL)
        .orderLinkId("9b381bb1-401")
        .mmp(false)
        .reduceOnly(false)
        .build(),
    TradeOrderRequest.builder()
        .category(CategoryType.OPTION)
        .symbol("BTC-10FEB23-24000-C")
        .side(Side.BUY)
        .orderType(TradeOrderType.LIMIT)
        .qty("0.1")
        .price("5")
        .orderIv("0.1")
        .timeInForce(TimeInForce.GOOD_TILL_CANCEL)
        .orderLinkId("82ee86dd-001")
        .mmp(false)
        .reduceOnly(false)
        .build()
);
var createBatchOrders = BatchOrderRequest.builder()
    .category(CategoryType.OPTION)
    .request(orderRequests)
    .build();
client.createBatchOrder(createBatchOrders, System.out::println);
```

### Account Examples

```java
// Get position information
var client = BybitApiClientFactory.newInstance(
    "YOUR_API_KEY",
    "YOUR_API_SECRET",
    BybitApiConfig.TESTNET_DOMAIN
).newAsyncPositionRestClient();

var positionListRequest = PositionDataRequest.builder()
    .category(CategoryType.LINEAR)
    .symbol("BTCUSDT")
    .build();
client.getPositionInfo(positionListRequest, System.out::println);

// Get asset information
var assetClient = BybitApiClientFactory.newInstance(
    "YOUR_API_KEY",
    "YOUR_API_SECRET",
    BybitApiConfig.TESTNET_DOMAIN
).newAsyncAssetRestClient();

var coinExchangeRecordsRequest = AssetDataRequest.builder().build();
assetClient.getAssetCoinExchangeRecords(coinExchangeRecordsRequest, System.out::println);
```

## WebSocket API Usage

### Public WebSocket
```java
// Create WebSocket client
var client = BybitApiClientFactory.newInstance(
    BybitApiConfig.STREAM_TESTNET_DOMAIN,
    true,
    "okhttp3"
).newWebsocketClient(20);

// Subscribe to orderbook
client.getPublicChannelStream(
    List.of("orderbook.50.BTCUSDT"),
    BybitApiConfig.V5_PUBLIC_LINEAR
);

// Subscribe to multiple channels
client.getPublicChannelStream(
    List.of("orderbook.50.BTCUSDT", "orderbook.1.ETHUSDT"),
    BybitApiConfig.V5_PUBLIC_LINEAR
);

// Subscribe to ticker with custom message handler
var tickerClient = BybitApiClientFactory.newInstance(
    BybitApiConfig.STREAM_MAINNET_DOMAIN,
    true
).newWebsocketClient();

tickerClient.setMessageHandler(message -> {
    var tickerData = (new ObjectMapper()).readValue(message, WebSocketTickerMessage.class);
    System.out.println("Websocket Message Data: " + tickerData.getData().toString());
});

tickerClient.getPublicChannelStream(
    List.of("tickers.BTCUSDT"),
    BybitApiConfig.V5_PUBLIC_LINEAR
);
```

### Private WebSocket
```java
// Create WebSocket client for private channels
var client = BybitApiClientFactory.newInstance(
    "YOUR_API_KEY",
    "YOUR_API_SECRET",
    BybitApiConfig.STREAM_TESTNET_DOMAIN,
    true
).newWebsocketClient(20);

// Subscribe to trade updates
client.getTradeChannelStream(
    Map.of(
        "category", "spot",
        "symbol", "XRPUSDT",
        "side", "Buy",
        "orderType", "Market",
        "qty", "10"
    ),
    V5_TRADE
);
```

## Client Configuration Options

```java
// Client factory options
private final String apiKey;        // Your API key
private final String secret;        // Your API secret
private final String baseUrl;       // API base URL
private final Boolean debugMode;    // Enable debug logging
private final String logOption;     // Logging options
private final Long recvWindow;      // Receive window
private final String referer;       // Referer header

// WebSocket client options
private final Integer pingInterval; // WebSocket ping interval
private final String maxAliveTime; // Max alive time for private channels (seconds/minutes)
```

## Best Practices

1. **Error Handling**
   - Implement proper exception handling
   - Log errors for debugging
   - Use appropriate error recovery strategies

2. **WebSocket Management**
   - Set appropriate ping intervals
   - Implement reconnection logic
   - Handle connection errors gracefully

3. **Rate Limiting**
   - Monitor rate limits
   - Implement exponential backoff
   - Use WebSocket for real-time data

4. **Security**
   - Never hardcode API keys
   - Use environment variables
   - Regularly rotate API keys

## Additional Resources

- [Official Repository](https://github.com/bybit-exchange/bybit-java-api)
- [Maven Central](https://central.sonatype.com/artifact/io.github.wuhewuhe/bybit-java-api)
- [API Documentation](https://bybit-exchange.github.io/docs/v5/intro)
- [Examples](https://github.com/bybit-exchange/bybit-java-api/tree/master/src/test/java/com/bybit/api/examples)
