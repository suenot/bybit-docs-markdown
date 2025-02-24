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

### General Rules
- Rate limits are calculated using a sliding window
- Different endpoints have different rate limits
- Exceeding rate limits results in HTTP 429 error
- Server timestamp is used for rate limit calculations

### Rate Limit Categories

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
| Single IP | 1000 requests per minute |
| WebSocket connections per IP | 50 |

## WebSocket Rate Limits

### Connection Limits
- Maximum 50 WebSocket connections per IP address
- Maximum 10 authentication requests per minute per connection
- Maximum 240 subscriptions per connection

### Subscription Limits
| Topic Type | Maximum Subscriptions |
|------------|---------------------|
| Order Book | 40 per connection |
| Trade | 40 per connection |
| Ticker | 40 per connection |
| Kline | 40 per connection |
| Private Topics | No limit |

### Heartbeat
- Server sends ping every 20 seconds
- Client must respond with pong within 20 seconds
- Connection will be closed if no response

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
