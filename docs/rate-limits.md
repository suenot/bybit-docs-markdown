# Rate Limits

## Understanding Rate Limits
Bybit implements rate limits to ensure platform stability and fair usage. Rate limits are applied based on:
- API key
- IP address
- User ID
- Endpoint category

## Rate Limit Headers
Every response includes headers with rate limit information:
```
X-RateLimit-Limit: Maximum requests allowed in the time window
X-RateLimit-Remaining: Remaining requests in the current window
X-RateLimit-Reset: Time until the rate limit resets (UTC timestamp in seconds)
```

## REST API Rate Limits

Bybit V5 API employs different rate limits based on endpoint type and your VIP level or if you are a Pro user.

- **Standard Account Users (Non-VIP, VIP-1, VIP-2, VIP-3):** Limits are the same.
- **VIP-4, VIP-5, Supreme VIP, Pro-1, Pro-2, Pro-3:** Limits are the same.
- **Pro-4, Pro-5:** Limits are the same.

Limits are typically measured per UID per second (req/s).

### Endpoint Limit Tiers (Requests per Second per UID)

| Endpoint Path Pattern | Standard User | VIP-4/5, Supreme, Pro-1/2/3 | Pro-4/5 |
|-----------------------|---------------|-----------------------------|---------|
| `/v5/order/create` | 10 req/s | 10 req/s | 10 req/s |
| `/v5/order/amend` | 10 req/s | 10 req/s | 10 req/s |
| `/v5/order/cancel` | 10 req/s | 10 req/s | 10 req/s |
| `/v5/order/create-batch` | 10 req/s | 10 req/s | 10 req/s |
| `/v5/order/amend-batch` | 10 req/s | 10 req/s | 10 req/s |
| `/v5/order/cancel-batch` | 10 req/s | 10 req/s | 10 req/s |
| `/v5/order/cancel-all` | 1 req/s | 1 req/s | 1 req/s |
| `/v5/position/set-leverage` | 10 req/s | 10 req/s | 10 req/s |
| `/v5/position/...` (excluding set-leverage) | 10 req/s | 20 req/s | 20 req/s |
| `/v5/execution/list` | 10 req/s | 20 req/s | 20 req/s |
| `/v5/order/realtime` | 10 req/s | 20 req/s | 20 req/s |
| `/v5/order/history` | 10 req/s | 20 req/s | 20 req/s |
| `/v5/account/...` | 10 req/s | 20 req/s | 20 req/s |
| `/v5/asset/...` | 10 req/s | 20 req/s | 20 req/s |
| `/v5/user/query-api` | 10 req/s | 10 req/s | 10 req/s |
| `/v5/user/...` (excluding query-api) | 10 req/s | 20 req/s | 20 req/s |
| `/v5/spot-leverage-token/...` | 10 req/s | 20 req/s | 20 req/s |
| `/v5/spot-margin-trade/...` | 10 req/s | 20 req/s | 20 req/s |
| `/v5/ins-loan/...` | 10 req/s | 20 req/s | 20 req/s |

### Public Endpoints (Market Data)

Limits are typically measured per IP per second (req/s).

| Endpoint Path Pattern | Limit |
|-----------------------|-------|
| `/v5/market/kline` | 10 req/s |
| `/v5/market/mark-price-kline` | 10 req/s |
| `/v5/market/index-price-kline` | 10 req/s |
| `/v5/market/premium-index-price-kline` | 10 req/s |
| `/v5/market/instruments-info` | 10 req/s |
| `/v5/market/orderbook` | 10 req/s |
| `/v5/market/tickers` | 10 req/s |
| `/v5/market/funding/history` | 10 req/s |
| `/v5/market/recent-trade` | 10 req/s |
| `/v5/market/open-interest` | 10 req/s |
| `/v5/market/historical-volatility` | 10 req/s |
| `/v5/market/insurance` | 10 req/s |
| `/v5/market/risk-limit-info` | 10 req/s |
| `/v5/market/delivery-price` | 10 req/s |
| `/v5/market/account-ratio` | 10 req/s |
| `/v5/market/time` | 10 req/s |
| `/v5/market/announcements` | 10 req/s |

### Request Limit Summary (per UID)

| VIP Level | Private Endpoint Limit (req/s) | Public Endpoint Limit (req/s) |
|-----------|--------------------------------|-------------------------------|
| Non-VIP | 10 | 10 |
| VIP-1 | 10 | 10 |
| VIP-2 | 10 | 10 |
| VIP-3 | 10 | 10 |
| VIP-4 | 20 | 10 |
| VIP-5 | 20 | 10 |
| Supreme VIP | 20 | 10 |
| Pro-1 | 20 | 10 |
| Pro-2 | 20 | 10 |
| Pro-3 | 20 | 10 |
| Pro-4 | 20 | 10 |
| Pro-5 | 20 | 10 |

_Note: Order placement/amendment/cancellation endpoints have a fixed limit of 10 req/s regardless of VIP level._

### Old Rate Limit Categories (Deprecated - For Context Only)
_(This section describes the previous structure, which might still be reflected in older code or discussions, but the tables above represent the current V5 limits)._

#### Market Data Endpoints
| Endpoint Type | Rate Limit (requests/min) |
|--------------|---------------------------|
| Order Book | 120 |
| Recent Trades | 120 |
| Kline/Candlestick | 120 |
| Tickers | 120 |
| Symbols Info | 120 |
| Public Trading History | 120 |

#### Account & Trading Endpoints
| Endpoint Type | Rate Limit (requests/min) |
|--------------|---------------------------|
| Place Order | 100 |
| Cancel Order | 100 |
| Modify Order | 100 |
| Query Order | 600 |
| Position Info | 120 |
| Account Info | 120 |
| Wallet Info | 120 |

#### Special Endpoints
| Endpoint Type | Rate Limit (requests/min) |
|--------------|---------------------------|
| Transfer | 60 |
| Withdraw | 60 |
| API Key Info | 600 |

### IP Rate Limits
| Connection Type | Limit |
|----------------|-------|
| Single IP | 600 requests per 5 seconds (equivalent to 7200 req/min average) |
| WebSocket connections per IP | 50 |

## WebSocket Rate Limits

### Connection Limits
- Maximum 10 WebSocket connections per UID (including main and sub-accounts).
- Maximum 10 authentication requests per minute per connection.
- Maximum 1000 subscriptions per connection.

### Subscription Limits
- No specific per-topic limits, but total subscriptions per connection cannot exceed 1000.

### Request Limits (e.g., for Order Entry via WebSocket)
- **Order Placement/Amendment/Cancellation:** 10 requests per second per UID.

### Heartbeat
- Server sends ping every 20 seconds.
- Client must respond with pong within 20 seconds.
- Connection will be closed if no response.

## Best Practices

### Handling Rate Limits
1. Implement exponential backoff when rate limited
```python
import time
import random

def exponential_backoff(retry_count, base_delay=1):
    delay = min(300, (2 ** retry_count) * base_delay + random.uniform(0, 0.1))
    time.sleep(delay)
```

2. Track remaining rate limits
```python
class RateLimitTracker:
    def __init__(self):
        self.limits = {}
    
    def update_limits(self, headers):
        self.limits = {
            'limit': int(headers.get('X-RateLimit-Limit', 0)),
            'remaining': int(headers.get('X-RateLimit-Remaining', 0)),
            'reset': int(headers.get('X-RateLimit-Reset', 0))
        }
    
    def should_retry(self):
        return self.limits.get('remaining', 0) > 0
```

### Optimization Tips
1. Batch operations when possible
2. Use WebSocket for real-time data instead of REST API polling
3. Implement request queuing
4. Cache frequently accessed data
5. Use multiple API keys for different purposes
6. Monitor rate limit headers
7. Implement circuit breakers

## Error Handling
When rate limited (HTTP 429):
1. Read the response headers for reset time
2. Implement exponential backoff
3. Consider reducing request frequency
4. Log rate limit incidents
5. Alert on persistent rate limiting

## Monitoring
Monitor these metrics:
- Request rates per endpoint
- Rate limit remaining
- Rate limit reset times
- Number of 429 responses
- WebSocket connection count
- Subscription count per connection

## Additional Resources
- [Error Codes](./error-codes.md)
- [Official Rate Limit Documentation](https://bybit-exchange.github.io/docs/v5/rate-limit)
- [API Best Practices](https://bybit-exchange.github.io/docs/v5/guide)
