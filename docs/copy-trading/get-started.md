# Get Started with Copy Trading

Learn how to start copy trading on Bybit, either as a Master Trader or a Follower.

## Become a Master Trader

### Requirements
1. Complete account verification
2. Meet minimum trading volume requirements
3. Have a proven track record
4. Create API keys with required permissions

### Application Process
1. Visit [Bybit Copy Trading](https://www.bybit.com/copyTrade/)
2. Submit application with trading history
3. Wait for approval
4. Set up your trading strategy

## Start as a Follower

### Requirements
1. Have a funded Bybit account
2. Enable Copy Trading feature
3. Complete risk acknowledgment

### Steps to Follow
1. Browse Master Traders
2. Review performance metrics
3. Choose copy amount
4. Set risk parameters
5. Start copying trades

## API Integration

### Required Permissions
- "Contract - Orders & Positions" (mandatory)
- Read-only access for monitoring

### API Key Setup
1. Generate API key in account settings
2. Enable required permissions
3. Store credentials securely

## Risk Management

Copy Trading offers various risk management tools. Some are configured via the Bybit platform, while others can be managed via API.

### Position Settings (Platform & API)
1. **Copy Ratio Customization:** (Typically set via platform interface)
2. **Take Profit/Stop Loss Levels:** Can be set per-order via the `takeProfit` and `stopLoss` parameters in the [Place Order](./api-endpoints.md#place-copy-trade) API call.
3. **Maximum Position Size:** (Typically set via platform interface)
4. **Drawdown Limits:** (Typically set via platform interface)

### Monitoring Tools (via API & WebSocket)
1. **Real-time Performance Tracking:** Monitor P&L via position updates.
2. **Position Status Updates:** Use the [Position List endpoint](./../derivatives/account/position-info.md) and the [Private Position WebSocket Stream](./websocket.md#position-updates).
3. **P&L Monitoring:** Use the [Closed PnL endpoint](https://bybit-exchange.github.io/docs/v5/position/close-pnl) and monitor position stream PnL fields.
4. **Risk Exposure Analysis:** Analyze position values and margin requirements from account/position endpoints and streams.
