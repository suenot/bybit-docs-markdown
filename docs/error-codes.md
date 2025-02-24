# Error Codes

## HTTP Status Codes
| Status Code | Description |
|------------|-------------|
| 200 | Success |
| 403 | Forbidden |
| 404 | Not Found |
| 429 | Too Many Requests |
| 500 | Internal Server Error |

## General Error Format
All API errors are returned in the following format:

```json
{
    "retCode": 10001,
    "retMsg": "Error message description",
    "result": {},
    "retExtInfo": {},
    "time": 1671183384841
}
```

## Common Error Codes

### Authentication Errors (10xxx)
| Error Code | Description | Solution |
|------------|-------------|----------|
| 10001 | Invalid API key | Check your API key is correct and active |
| 10002 | Invalid signature | Verify your signature generation process |
| 10003 | Invalid parameter | Check the required and optional parameters |
| 10004 | Unknown error | Contact support if persistent |
| 10005 | Invalid API key permissions | Verify API key has required permissions |
| 10006 | Too many visits | Respect rate limits |
| 10007 | API key expired | Generate a new API key |
| 10010 | IP address not allowed | Add your IP to the whitelist |
| 10016 | Server error | Try again later |
| 10017 | Request path not found | Check the API endpoint URL |
| 10018 | IP request rate limit exceeded | Respect rate limits |

### Order Errors (11xxx)
| Error Code | Description | Solution |
|------------|-------------|----------|
| 11001 | Order does not exist | Check order ID |
| 11002 | Order size too large | Reduce order size |
| 11003 | Order price out of range | Adjust order price |
| 11004 | Insufficient balance | Add funds or reduce order size |
| 11005 | Position does not exist | Check position ID |
| 11006 | Order would trigger immediate liquidation | Adjust order parameters |
| 11007 | Cannot create more positions | Close some positions first |
| 11008 | Insufficient available balance | Add funds or reduce position size |
| 11009 | Order has been filled or canceled | Check order status |
| 11010 | Order would cross | Adjust order price |
| 11011 | Order would trigger immediate liquidation | Adjust leverage or order size |
| 11012 | Order price too high | Reduce order price |
| 11013 | Order price too low | Increase order price |
| 11014 | Invalid order type | Check supported order types |
| 11015 | Invalid side | Use "Buy" or "Sell" |

### Account Errors (12xxx)
| Error Code | Description | Solution |
|------------|-------------|----------|
| 12001 | Insufficient margin | Add margin or reduce position |
| 12002 | Cannot transfer | Check transfer requirements |
| 12003 | Transfer amount exceeds limit | Reduce transfer amount |
| 12004 | User account not found | Verify account ID |
| 12005 | Account status abnormal | Contact support |
| 12006 | Account has positions | Close positions first |
| 12007 | Invalid account type | Check account types |

### Market Errors (13xxx)
| Error Code | Description | Solution |
|------------|-------------|----------|
| 13001 | Symbol not found | Check symbol name |
| 13002 | Symbol suspended | Trading temporarily disabled |
| 13003 | Market closed | Wait for market to open |
| 13004 | Market data unavailable | Try again later |
| 13005 | Invalid interval | Check supported intervals |
| 13006 | No market data | Try again later |
| 13007 | Market too volatile | Wait for stability |
| 13008 | Market paused | Wait for resumption |

### WebSocket Errors (14xxx)
| Error Code | Description | Solution |
|------------|-------------|----------|
| 14001 | Invalid WS auth | Check authentication |
| 14002 | Invalid subscription topic | Check topic name |
| 14003 | Too many subscriptions | Reduce subscriptions |
| 14004 | Connection limit exceeded | Reduce connections |
| 14005 | Connection expired | Reconnect |
| 14006 | Already subscribed | Check subscriptions |
| 14007 | Not subscribed | Subscribe first |

## Best Practices
1. Always implement proper error handling in your code
2. Log error codes and messages for debugging
3. Handle rate limit errors with exponential backoff
4. Implement automatic reconnection for WebSocket errors
5. Monitor error patterns to identify potential issues
6. Use testnet to validate error handling
7. Keep documentation of custom error handling

## Additional Resources
- [API Documentation](https://bybit-exchange.github.io/docs/v5/errors)
- [Rate Limits](./rate-limits.md)
- [API Support](https://discord.gg/bybit)
