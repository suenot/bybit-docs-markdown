# Bybit API v5 Documentation
[![Context7 Docs](https://badgen.net/badge/Context7/Bybit%20Docs/blue)](https://context7.com/suenot/bybit-docs-markdown)

## Getting Started
- [Introduction](./docs/introduction.md)
- [Authentication](./docs/authentication.md)
- [Error Codes](./docs/error-codes.md)
- [Rate Limits](./docs/rate-limits.md)

## SDK Examples
- [Python SDK](./docs/sdk/python.md)
- [Go SDK](./docs/sdk/go.md)
- [Node.js SDK](./docs/sdk/nodejs.md)
- [Java SDK](./docs/sdk/java.md)
- [C# SDK](./docs/sdk/csharp.md)

## WebSocket API
- [Connection](./docs/websocket/connect.md)
- Public Channels
  - [Order Book](./docs/websocket/public/orderbook.md)
  - [Ticker](./docs/websocket/public/ticker.md)
  - [Trades](./docs/websocket/public/trade.md)
- Private Channels
  - [Execution](./docs/websocket/private/execution.md)
  - [Order](./docs/websocket/private/order.md)
  - [Position](./docs/websocket/private/position.md)
  - [Wallet](./docs/websocket/private/wallet.md)

## Spot Trading
### REST API
- Market Data
  - [Get Symbols](./docs/spot/market.md)
  - [Get Ticker](./docs/spot/market.md)
  - [Get Order Book](./docs/spot/market.md)
  - [Get Recent Trades](./docs/spot/market.md)
  - [Get Candlestick Data](./docs/spot/market.md)
- Trading
  - [Place Order](./docs/spot/trade/place-order.md)
  - [Cancel Order](./docs/spot/trade/cancel-order.md)
  - [Get Order](./docs/spot/trade/get-order.md)
  - [Open Orders](./docs/spot/trade/open-orders.md)
  - [Order History](./docs/spot/trade/order-history.md)
- Account
  - [Get Account Info](./docs/spot/account/account-info.md)
  - [Get Wallet Balance](./docs/spot/account/wallet-balance.md)
  - [Get Transaction Log](./docs/spot/account/transaction-log.md)
- Margin Trading
  - [Borrow](./docs/spot/margin/borrow.md)
  - [Repay](./docs/spot/margin/repay.md)
  - [Get Margin Info](./docs/spot/margin/margin-info.md)

## Derivatives Trading
### REST API
- Market Data
  - [Get Instruments Info](./docs/derivatives/market.md)
  - [Get Order Book](./docs/derivatives/market.md)
  - [Get Recent Trades](./docs/derivatives/market.md)
  - [Get Kline Data](./docs/derivatives/market.md)
- Account
  - [Get Position Info](./docs/derivatives/account/position-info.md)
  - [Switch Position Mode](./docs/derivatives/account/position-mode.md)
  - [Set Leverage](./docs/derivatives/account/set-leverage.md)

## Options Trading
### REST API
- Market Data
  - [Get Options Info](./docs/options/market.md)
  - [Get Order Book](./docs/options/market.md)
  - [Get Tickers](./docs/options/market.md)
  - [Get Public Trades](./docs/options/market.md)
- Account
  - [Get Account Info](./docs/options/account/account-info.md)
  - [Get Positions](./docs/options/account/positions.md)
  - [Get Settlement History](./docs/options/account/settlement.md)

## Copy Trading
- [Introduction](./docs/copy-trading/introduction.md)
- [Get Started](./docs/copy-trading/get-started.md)
- [API Endpoints](./docs/copy-trading/api-endpoints.md)
- [WebSocket Streams](./docs/copy-trading/websocket.md)

## Additional Resources
- [Official API Documentation](https://bybit-exchange.github.io/docs/v5/intro)
- [API Status](https://status.bybit.com/)
- [Bybit Blog](https://blog.bybit.com/)
- [Telegram Group](https://t.me/BybitAPI)
- [Discord Server](https://discord.gg/bybit)
