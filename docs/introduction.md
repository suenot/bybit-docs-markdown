# Introduction to Bybit API v5

Bybit API v5 provides a comprehensive suite of API endpoints for trading various financial instruments including Spot, Derivatives (USDT Perpetual, USDC Perpetual, Inverse Perpetual), and Options.

## API Base URLs

### REST API
**Mainnet (Production)**:
- `https://api.bybit.com`

**Testnet**:
- `https://api-testnet.bybit.com`

### WebSocket
**Mainnet (Production)**:
- Public Channel (Spot): `wss://stream.bybit.com/v5/public/spot`
- Public Channel (USDT & USDC Perpetual): `wss://stream.bybit.com/v5/public/linear`
- Public Channel (Inverse Perpetual): `wss://stream.bybit.com/v5/public/inverse`
- Public Channel (Option): `wss://stream.bybit.com/v5/public/option`
- Private Channel: `wss://stream.bybit.com/v5/private`

**Testnet**:
- Public Channel (Spot): `wss://stream-testnet.bybit.com/v5/public/spot`
- Public Channel (USDT & USDC Perpetual): `wss://stream-testnet.bybit.com/v5/public/linear`
- Public Channel (Inverse Perpetual): `wss://stream-testnet.bybit.com/v5/public/inverse`
- Public Channel (Option): `wss://stream-testnet.bybit.com/v5/public/option`
- Private Channel: `wss://stream-testnet.bybit.com/v5/private`

## Available Products

### Spot Trading
- Spot trading pairs
- Margin trading
- Spot grid trading
- Spot DCA bot

### Derivatives
- USDT Perpetual
- USDC Perpetual
- Inverse Perpetual
- Futures grid trading
- Futures DCA bot

### Options
- USDC Options

## API Features
- Market Data: Real-time access to order book, trades, tickers, and klines
- Trading: Full trading functionality including placing, canceling, and querying orders
- Account Management: Access to account information, balances, and positions
- WebSocket Streams: Real-time data feeds for market data and account/position updates
- Copy Trading: Follow and copy other traders' positions

## Getting Started
1. [Create a Bybit account](https://www.bybit.com/register)
2. [Generate API keys](https://www.bybit.com/app/user/api-management)
3. Read the [Authentication](./authentication.md) guide
4. Check [Rate Limits](./rate-limits.md) to understand API constraints
5. Review [Error Codes](./error-codes.md) for troubleshooting

## API Libraries
Official API libraries are available in multiple programming languages:
- [Python](./sdk/python.md)
- [Go](./sdk/go.md)
- [Node.js](./sdk/nodejs.md)
- [Java](./sdk/java.md)
- [C#](./sdk/csharp.md)

## Best Practices
1. Always use testnet first for testing your trading strategies
2. Keep your API keys secure and never share them
3. Implement proper error handling using our [Error Codes](./error-codes.md)
4. Stay within [Rate Limits](./rate-limits.md) to avoid trading interruptions
5. Use WebSocket connections for real-time data instead of repeatedly calling REST endpoints
6. Subscribe to our [API updates](https://t.me/BybitAPI) channel for important announcements

## Support
- [API Documentation](https://bybit-exchange.github.io/docs/v5/intro)
- [API Updates Telegram](https://t.me/BybitAPI)
- [Discord Community](https://discord.gg/bybit)
- [GitHub Issues](https://github.com/bybit-exchange/api-usage-examples/issues)
