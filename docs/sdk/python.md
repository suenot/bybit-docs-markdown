# Python SDK

The official Python SDK for Bybit API v5 is `pybit`. It provides a comprehensive interface to interact with both REST API and WebSocket API.

## Installation

pybit requires Python 3.9.1 or higher. You can install it via pip:

```bash
pip install pybit
```

:::tip
Always use the latest version of pybit for full v5 API support
:::

## REST API Usage

### Initialization
```python
from pybit.unified_trading import HTTP

# Create session for mainnet
session = HTTP(
    testnet=False,
    api_key="your_api_key",
    api_secret="your_api_secret"
)

# For testnet
testnet_session = HTTP(
    testnet=True,
    api_key="your_api_key",
    api_secret="your_api_secret"
)
```

### Market Data Examples

```python
# Get instruments info
response = session.get_instruments_info(
    category="spot",
    symbol="BTCUSDT"
)

# Get tickers
response = session.get_tickers(
    category="spot",
    symbol="BTCUSDT"
)

# Get orderbook
response = session.get_orderbook(
    category="spot",
    symbol="BTCUSDT",
    limit=25  # Default 25, max 200
)

# Get kline/candlestick data
response = session.get_kline(
    category="spot",
    symbol="BTCUSDT",
    interval="D",  # Available intervals: 1,3,5,15,30,60,120,240,360,720,D,M,W
    limit=200  # Default 200, max 1000
)

# Get recent trades
response = session.get_public_trading_history(
    category="spot",
    symbol="BTCUSDT",
    limit=100  # Default 100, max 1000
)
```

### Trading Examples

```python
# Place limit order
response = session.place_order(
    category="spot",
    symbol="BTCUSDT",
    side="Buy",
    orderType="Limit",
    qty="0.001",
    price="20000",
    timeInForce="GTC",
    orderLinkId="test-order-001"  # Optional custom order ID
)

# Place market order
response = session.place_order(
    category="spot",
    symbol="BTCUSDT",
    side="Buy",
    orderType="Market",
    qty="0.001"
)

# Place stop order
response = session.place_order(
    category="spot",
    symbol="BTCUSDT",
    side="Buy",
    orderType="Limit",
    qty="0.001",
    price="20000",
    timeInForce="GTC",
    triggerPrice="19500",
    triggerBy="LastPrice",  # LastPrice, IndexPrice, MarkPrice
    orderLinkId="test-stop-001"
)

# Cancel order
response = session.cancel_order(
    category="spot",
    symbol="BTCUSDT",
    orderId="1234567890"  # Either orderId or orderLinkId is required
)

# Cancel all orders
response = session.cancel_all_orders(
    category="spot",
    symbol="BTCUSDT"
)

# Get order details
response = session.get_order_details(
    category="spot",
    symbol="BTCUSDT",
    orderId="1234567890"  # Either orderId or orderLinkId is required
)

# Get open orders
response = session.get_open_orders(
    category="spot",
    symbol="BTCUSDT",
    limit=50  # Default 50, max 200
)

# Get order history
response = session.get_order_history(
    category="spot",
    symbol="BTCUSDT",
    limit=50,  # Default 50, max 200
    orderStatus="Filled,Cancelled"  # Optional filter
)

# Modify order
response = session.amend_order(
    category="spot",
    symbol="BTCUSDT",
    orderId="1234567890",
    price="20100",
    qty="0.002"  # Optional
)
```

### Account Examples

```python
# Get wallet balance
response = session.get_wallet_balance(
    accountType="UNIFIED",  # UNIFIED, CONTRACT, SPOT
    coin="USDT"  # Optional
)

# Get all positions
response = session.get_positions(
    category="linear",  # linear, inverse
    symbol="BTCUSDT"  # Optional
)

# Set leverage
response = session.set_leverage(
    category="linear",
    symbol="BTCUSDT",
    buyLeverage="2",
    sellLeverage="2"
)

# Get transaction log
response = session.get_transaction_log(
    category="spot",
    symbol="BTCUSDT",
    limit=50,  # Default 50, max 200
    startTime=1672506400000,  # Optional
    endTime=1672592800000  # Optional
)
```

## WebSocket API Usage

### Market Data WebSocket
```python
from pybit.unified_trading import WebSocket
from time import sleep

def handle_message(message):
    print(f"Received message: {message}")

def handle_error(error):
    print(f"Error: {error}")

# Subscribe to public topics
ws_public = WebSocket(
    testnet=False,
    channel_type="spot",
    handle_error=handle_error
)

# Subscribe to orderbook
ws_public.orderbook_stream(
    symbol="BTCUSDT",
    callback=handle_message
)

# Subscribe to trades
ws_public.trade_stream(
    symbol="BTCUSDT",
    callback=handle_message
)

# Subscribe to klines
ws_public.kline_stream(
    symbol="BTCUSDT",
    interval="1",  # 1,3,5,15,30,60,120,240,360,720,D,M,W
    callback=handle_message
)

# Keep connection alive
while True:
    sleep(1)
```

### Private WebSocket
```python
# Subscribe to private topics
ws_private = WebSocket(
    testnet=False,
    api_key="your_api_key",
    api_secret="your_api_secret",
    channel_type="private",
    handle_error=handle_error
)

def handle_private_message(message):
    if "data" in message:
        print(f"Received private update: {message['data']}")

# Subscribe to order updates
ws_private.order_stream(
    callback=handle_private_message
)

# Subscribe to position updates
ws_private.position_stream(
    callback=handle_private_message
)

# Subscribe to wallet updates
ws_private.wallet_stream(
    callback=handle_private_message
)

# Keep connection alive
while True:
    sleep(1)
```

## Error Handling

```python
from pybit.exceptions import (
    FailedRequestError,
    InvalidRequestError,
    ConnectionError,
    PermissionError
)

try:
    response = session.place_order(
        category="spot",
        symbol="BTCUSDT",
        side="Buy",
        orderType="Limit",
        qty="0.001",
        price="20000"
    )
except InvalidRequestError as e:
    print(f"Invalid request parameters: {e}")
except PermissionError as e:
    print(f"API key permission denied: {e}")
except ConnectionError as e:
    print(f"Connection error: {e}")
except FailedRequestError as e:
    print(f"Request failed: {e}")
```

## Best Practices

1. **Rate Limiting**
   ```python
   from time import sleep
   
   # Implement rate limiting
   def rate_limited_request(func, *args, **kwargs):
       try:
           return func(*args, **kwargs)
       except FailedRequestError as e:
           if "too many visits" in str(e):
               sleep(1)  # Wait 1 second
               return func(*args, **kwargs)
           raise
   ```

2. **WebSocket Connection Management**
   ```python
   def maintain_ws_connection(ws):
       while True:
           try:
               # Check connection status
               if not ws.is_connected():
                   print("Reconnecting...")
                   ws.reconnect()
               sleep(5)
           except Exception as e:
               print(f"Error in WS connection: {e}")
               sleep(1)
   ```

3. **Order Management**
   ```python
   def place_order_with_retry(session, order_params, max_retries=3):
       for attempt in range(max_retries):
           try:
               return session.place_order(**order_params)
           except (ConnectionError, FailedRequestError) as e:
               if attempt == max_retries - 1:
                   raise
               print(f"Retrying order placement: {e}")
               sleep(1)
   ```

4. **Logging**
   ```python
   import logging

   logging.basicConfig(level=logging.INFO)
   logger = logging.getLogger(__name__)

   def log_response(response):
       logger.info(f"API Response: {response}")
       if "retCode" in response and response["retCode"] != 0:
           logger.error(f"API Error: {response}")
   ```

## Additional Resources

- [Official pybit Repository](https://github.com/bybit-exchange/pybit)
- [PyPI Package](https://pypi.org/project/pybit/)
- [API Documentation](https://bybit-exchange.github.io/docs/v5/intro)
- [Examples Repository](https://github.com/bybit-exchange/pybit/tree/master/examples)
- [Telegram Support](https://t.me/BybitAPI)
