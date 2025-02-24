# Wallet Stream

Subscribe to real-time wallet updates for your account.

## Topic
```
wallet
```

> **Note:**
> - No snapshot event is provided on subscription
> - Unrealised PnL changes do not trigger events

## Message Format

```typescript
{
    "id": string,           // Message ID
    "topic": string,        // Topic name
    "creationTime": number, // Data timestamp (ms)
    "data": {
        "accountType": string,    // Account type
        "accountIMRate": string,  // Account initial margin rate
        "accountMMRate": string,  // Account maintenance margin rate
        "totalEquity": string,    // Total equity (USD)
        "totalWalletBalance": string, // Total wallet balance (USD)
        "totalMarginBalance": string, // Total margin balance (USD)
        "totalAvailableBalance": string, // Total available balance (USD)
        "totalPerpUPL": string,   // Total perpetual unrealized PnL (USD)
        "totalInitialMargin": string, // Total initial margin (USD)
        "totalMaintenanceMargin": string, // Total maintenance margin (USD)
        "coin": [
            {
                "coin": string,           // Coin name (e.g., BTC, ETH)
                "equity": string,         // Coin equity
                "usdValue": string,       // USD value of coin
                "walletBalance": string,  // Wallet balance
                "free": string,          // Available balance (Spot only)
                "locked": string,        // Locked balance
                "spotHedgingQty": string, // Spot hedging quantity
                "borrowAmount": string,   // Borrowed amount
                "availableToWithdraw": string, // Available to withdraw
                "accruedInterest": string, // Accrued interest
                "totalOrderIM": string,   // Total order initial margin
                "totalPositionIM": string, // Total position initial margin
                "totalPositionMM": string, // Total position maintenance margin
                "unrealisedPnl": string,  // Unrealized PnL
                "cumRealisedPnl": string, // Cumulative realized PnL
                "bonus": string,          // Bonus amount
                "collateralSwitch": boolean, // Platform collateral status
                "marginCollateral": boolean  // User collateral status
            }
        ]
    }
}
```

## Implementation Example (Python)

```python
from pybit.unified_trading import WebSocket
from time import sleep

def process_wallet_update(message):
    """Process wallet update messages"""
    data = message["data"]
    
    # Process account-wide information
    print(f"""
        Account Status:
        Total Equity: ${data['totalEquity']}
        Wallet Balance: ${data['totalWalletBalance']}
        Available Balance: ${data['totalAvailableBalance']}
        Unrealized PnL: ${data['totalPerpUPL']}
        Initial Margin Rate: {data['accountIMRate']}%
        Maintenance Margin Rate: {data['accountMMRate']}%
    """)

    # Process individual coins
    for coin in data["coin"]:
        process_coin_status(coin)

def process_coin_status(coin):
    """Process individual coin status"""
    print(f"""
        Coin: {coin['coin']}
        Balance: {coin['walletBalance']}
        USD Value: ${coin['usdValue']}
        Available to Withdraw: {coin['availableToWithdraw']}
        Borrowed: {coin['borrowAmount']}
        Unrealized PnL: {coin['unrealisedPnl']}
    """)

    # Monitor margin levels
    if float(coin['totalPositionIM']) > 0:
        margin_ratio = float(coin['walletBalance']) / float(coin['totalPositionIM'])
        print(f"Margin Ratio: {margin_ratio}")

    # Check collateral status
    if coin['marginCollateral']:
        print(f"Collateral Status: Active (Platform: {coin['collateralSwitch']})")

def monitor_risk_levels(data):
    """Monitor account risk levels"""
    if float(data['accountIMRate']) > 0.8:
        print("Warning: High initial margin usage")
    if float(data['accountMMRate']) > 0.5:
        print("Warning: High maintenance margin usage")

# Initialize WebSocket connection
ws = WebSocket(
    testnet=True,
    api_key="YOUR_API_KEY",
    api_secret="YOUR_API_SECRET"
)

# Subscribe to wallet updates
ws.wallet_stream(
    callback=process_wallet_update
)

# Keep connection alive
while True:
    sleep(1)
```

## Important Notes

1. **Account Types**
   - UTA 2.0: Unified account
   - UTA 1.0: Unified (spot/linear/options) and Contract (inverse)
   - Classic: Contract and Spot accounts

2. **Margin Calculations**
   - Account-wide fields not applicable to:
     - UTA 2.0 (isolated margin)
     - UTA 1.0 (isolated margin, CONTRACT)
     - Classic accounts

3. **Collateral Management**
   - Platform collateral switch (collateralSwitch)
   - User collateral preference (marginCollateral)
   - Available borrowing deprecated (use REST API)

4. **Balance Types**
   - Wallet balance
   - Margin balance
   - Available balance
   - Locked balance
   - Bonus balance

## Best Practices

1. **Balance Monitoring**
   - Track all balance changes
   - Monitor margin ratios
   - Watch collateral status
   - Track borrowing positions

2. **Risk Management**
   - Monitor margin usage
   - Track leverage levels
   - Watch liquidation risks
   - Monitor collateral value

3. **Performance**
   - Process updates asynchronously
   - Buffer frequent updates
   - Monitor processing time

4. **Data Analysis**
   - Track equity changes
   - Monitor PnL performance
   - Analyze margin usage
   - Track fee accrual
