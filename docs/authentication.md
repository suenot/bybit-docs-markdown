# Authentication

## API Key Creation
1. Log in to your Bybit account
2. Navigate to API Management under Account Settings
3. Click "Create New Key"
4. Select permissions for your API key:
   - Read-only: Market data and account information
   - Trade: Place and cancel orders
   - Transfer: Asset transfers between sub-accounts
   - View: View wallet balance and trading history
   - Options: Trade options contracts
5. Enable IP restrictions (recommended)
6. Save your API key and secret key securely

:::info Important
- Never share your API keys or secret keys
- Store API keys securely, never in source code
- Use environment variables or secure key management systems
- Enable IP restrictions when possible
:::

## REST API Authentication

Every private REST request must be signed and include the following HTTP headers:

```
X-BAPI-API-KEY: your api key
X-BAPI-SIGN: signature
X-BAPI-SIGN-TYPE: 2
X-BAPI-TIMESTAMP: timestamp
X-BAPI-RECV-WINDOW: recv window
```

### Parameters Explanation
- `X-BAPI-API-KEY`: Your API key
- `X-BAPI-SIGN`: Signature generated using the request parameters and your API secret
- `X-BAPI-SIGN-TYPE`: Always 2 for v5 API
- `X-BAPI-TIMESTAMP`: Current timestamp (milliseconds)
- `X-BAPI-RECV-WINDOW`: Request valid time window (milliseconds), default 5000

### Signature Generation
The signature is generated using HMAC SHA256:

1. Create timestamp string (milliseconds)
2. Create parameter string:
   - For GET requests: Concatenate timestamp + API key + recv_window + query string
   - For POST requests: Concatenate timestamp + API key + recv_window + request body
3. Sort query parameters alphabetically for GET requests
4. Sign the parameter string using HMAC SHA256 with your API secret

Example Python code:
```python
import hmac
import hashlib
import time
import json
import requests
from urllib.parse import urlencode

def generate_signature(api_key, api_secret, recv_window, method, params=None):
    """
    Generate signature for API request
    :param api_key: Your API key
    :param api_secret: Your API secret
    :param recv_window: Request valid time window
    :param method: HTTP method (GET or POST)
    :param params: Request parameters
    :return: Dict with authentication headers
    """
    timestamp = str(int(time.time() * 1000))
    param_str = ""
    
    if method == "GET":
        if params:
            param_str = urlencode(sorted(params.items()))
    else:  # POST
        if params:
            param_str = json.dumps(params)
            
    sign_str = timestamp + api_key + str(recv_window) + param_str
    signature = hmac.new(
        bytes(api_secret, "utf-8"),
        bytes(sign_str, "utf-8"),
        hashlib.sha256
    ).hexdigest()
    
    return {
        "X-BAPI-API-KEY": api_key,
        "X-BAPI-SIGN": signature,
        "X-BAPI-SIGN-TYPE": "2",
        "X-BAPI-TIMESTAMP": timestamp,
        "X-BAPI-RECV-WINDOW": str(recv_window)
    }

# Example GET request
def get_wallet_balance(api_key, api_secret):
    endpoint = "https://api.bybit.com/v5/account/wallet-balance"
    params = {"accountType": "UNIFIED"}
    headers = generate_signature(
        api_key=api_key,
        api_secret=api_secret,
        recv_window=5000,
        method="GET",
        params=params
    )
    
    response = requests.get(
        endpoint,
        headers=headers,
        params=params
    )
    return response.json()

# Example POST request
def place_order(api_key, api_secret, order_params):
    endpoint = "https://api.bybit.com/v5/order/create"
    headers = generate_signature(
        api_key=api_key,
        api_secret=api_secret,
        recv_window=5000,
        method="POST",
        params=order_params
    )
    headers["Content-Type"] = "application/json"
    
    response = requests.post(
        endpoint,
        headers=headers,
        json=order_params
    )
    return response.json()
```

## WebSocket Authentication

For private WebSocket channels, authentication is required when establishing the connection:

```json
{
    "req_id": "10001",
    "op": "auth",
    "args": [
        "api_key",
        "timestamp",
        "signature"
    ]
}
```

### WebSocket Signature Generation
1. Generate expires timestamp (current time + 1 second in milliseconds)
2. Create signature string: `GET/realtime{expires}`
3. Sign using HMAC SHA256

Example Python code:
```python
import hmac
import time
import json
import websocket

def generate_ws_signature(api_key, api_secret):
    """
    Generate WebSocket authentication signature
    :param api_key: Your API key
    :param api_secret: Your API secret
    :return: Dict with authentication message
    """
    expires = int((time.time() + 1) * 1000)
    signature = hmac.new(
        bytes(api_secret, "utf-8"),
        bytes(f"GET/realtime{expires}", "utf-8"),
        hashlib.sha256
    ).hexdigest()
    
    return {
        "req_id": str(int(time.time() * 1000)),
        "op": "auth",
        "args": [api_key, expires, signature]
    }

def on_message(ws, message):
    print(f"Received: {message}")

def on_error(ws, error):
    print(f"Error: {error}")

def on_close(ws, close_status_code, close_msg):
    print("WebSocket connection closed")

def on_open(ws):
    print("WebSocket connection opened")
    # Authenticate
    auth_data = generate_ws_signature(api_key, api_secret)
    ws.send(json.dumps(auth_data))
    
    # Subscribe to private topics after successful authentication
    subscribe_msg = {
        "req_id": str(int(time.time() * 1000)),
        "op": "subscribe",
        "args": ["order", "position"]
    }
    ws.send(json.dumps(subscribe_msg))

# Create WebSocket connection
ws = websocket.WebSocketApp(
    url="wss://stream.bybit.com/v5/private",
    on_message=on_message,
    on_error=on_error,
    on_close=on_close,
    on_open=on_open
)

# Start WebSocket connection
ws.run_forever()
```

## Error Handling

Common authentication errors:

| Error Code | Description | Solution |
|------------|-------------|----------|
| 10001 | Invalid API key | Check API key is correct and active |
| 10002 | Invalid signature | Verify signature generation process |
| 10003 | Invalid timestamp | Ensure server time is synchronized |
| 10004 | Invalid recv_window | Use smaller recv_window value |
| 10005 | Permission denied | Check API key permissions |

## Best Practices

1. **Security**
   - Never share your API keys or secret keys
   - Use appropriate permissions for your API keys
   - Enable IP restrictions when possible
   - Rotate your API keys periodically
   - Store API keys securely using environment variables

2. **Performance**
   - Keep recv_window as small as practical (1000-5000ms)
   - Use persistent WebSocket connections
   - Implement proper connection handling and auto-reconnect

3. **Development**
   - Use testnet for development and testing
   - Implement proper error handling
   - Log API requests and responses
   - Monitor rate limits
   - Use appropriate timeouts

4. **WebSocket**
   - Implement heartbeat mechanism
   - Handle reconnection with exponential backoff
   - Process messages asynchronously
   - Maintain single WebSocket connection when possible
