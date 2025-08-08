# Python-developer-Task
Build a Simplified Trading Bot  Register and activate a Binance Testnet account  Generate API credentials
 Setup Instructions

1. Create Binance Futures Testnet Account:

Sign up: https://testnet.binancefuture.com

Generate API key and Secret key



2. Install Required Library:



pip install python-binance


---

Folder Structure

trading_bot/
├── bot.py
├── config.py
├── main.py
├── logger.py
└── utils.py


---

1. config.py

API_KEY = "your_testnet_api_key"
API_SECRET = "your_testnet_api_secret"
TESTNET_URL = "https://testnet.binancefuture.com"


---

 2. logger.py

import logging

def setup_logger():
    logging.basicConfig(
        filename='trading_bot.log',
        level=logging.INFO,
        format='%(asctime)s:%(levelname)s:%(message)s'
    )
    return logging.getLogger()


---

 3. utils.py

def validate_order_input(symbol, side, order_type, quantity, price=None):
    if side not in ["BUY", "SELL"]:
        raise ValueError("Side must be 'BUY' or 'SELL'")
    if order_type not in ["MARKET", "LIMIT"]:
        raise ValueError("Order type must be 'MARKET' or 'LIMIT'")
    if order_type == "LIMIT" and price is None:
        raise ValueError("Price required for LIMIT order")
    return True


---

 4. bot.py

from binance.client import Client
from binance.enums import *
from logger import setup_logger
from config import API_KEY, API_SECRET, TESTNET_URL
from utils import validate_order_input

class BasicBot:
    def __init__(self, api_key, api_secret, testnet=True):
        self.logger = setup_logger()
        self.client = Client(api_key, api_secret)
        if testnet:
            self.client.FUTURES_URL = TESTNET_URL
        self.client.API_URL = TESTNET_URL
        self.logger.info("Initialized bot with testnet=%s", testnet)

    def place_order(self, symbol, side, order_type, quantity, price=None):
        try:
            validate_order_input(symbol, side, order_type, quantity, price)

            order_params = {
                "symbol": symbol,
                "side": side,
                "type": order_type,
                "quantity": quantity
            }
            if order_type == "LIMIT":
                order_params["price"] = price
                order_params["timeInForce"] = TIME_IN_FORCE_GTC

            self.logger.info("Placing order: %s", order_params)
            response = self.client
Requirements

1. Binance Futures Testnet account


2. API Key & Secret from Testnet


3. Install required libraries:



pip install python-binance


---

Project Structure

binance_trading_bot/
├── bot.py                 # CLI logic and execution
├── trader.py              # Binance Futures API logic
├── logger.py              # Logging setup
├── config.py              # Configuration (API keys, URLs)
└── README.md              # Documentation


---

 config.py

# config.py
API_KEY = 'YOUR_TESTNET_API_KEY'
API_SECRET = 'YOUR_TESTNET_API_SECRET'

BASE_URL = 'https://testnet.binancefuture.com'


---

 logger.py

# logger.py
import logging

def setup_logger(name='trading_bot'):
    logger = logging.getLogger(name)
    logger.setLevel(logging.DEBUG)

    file_handler = logging.FileHandler('trading_bot.log')
    console_handler = logging.StreamHandler()

    formatter = logging.Formatter('[%(asctime)s] [%(levelname)s] %(message)s')
    file_handler.setFormatter(formatter)
    console_handler.setFormatter(formatter)

    logger.addHandler(file_handler)
    logger.addHandler(console_handler)
    return logger


---

🤖 trader.py

# trader.py
from binance.client import Client
from binance.enums import *
from config import API_KEY, API_SECRET, BASE_URL
from logger import setup_logger

logger = setup_logger()

class BinanceTrader:
    def __init__(self):
        self.client = Client(API_KEY, API_SECRET)
        self.client.FUTURES_URL = BASE_URL
        self.client.API_URL = BASE_URL

    def get_balance(self):
        try:
            balance = self.client.futures_account_balance()
            usdt_balance = next(item for item in balance if item['asset'] == 'USDT')
            return float(usdt_balance['balance'])
        except Exception as e:
            logger.error(f"Error fetching balance: {e}")
            return None

    def place_order(self, symbol, side, order_type, quantity, price=None):
        try:
            params = {
                'symbol': symbol,
                'side': SIDE_BUY if side.lower() == 'buy' else SIDE_SELL,
                'type': order_type,
                'quantity': quantity,
            }
            if order_type == ORDER_TYPE_LIMIT:
                if price is None:
                    raise ValueError("Limit orders require a price.")
                params.update({
                    'price': str(price),
                    'timeInForce': TIME_IN_FORCE_GTC,
                })

            response = self.client.futures_create_order(**params)
            logger.info(f"Order placed: {response}")
            return response
        except Exception as e:
            logger.error(f"Error placing order: {e}")
            return None


---

 bot.py (CLI interface)

# bot.py
from trader import BinanceTrader
from binance.enums import ORDER_TYPE_MARKET, ORDER_TYPE_LIMIT

def main():
    trader = BinanceTrader()
    print("Binance Futures CLI Trading Bot")
    
    while True:
        print("\n--- Menu ---")
        print("1. Check Balance")
        print("2. Place Market Order")
        print("3. Place Limit Order")
        print("4. Exit")

        choice = input("Enter choice: ").strip()

        if choice == '1':
            balance = trader.get_balance()
            if balance is not None:
                print(f"USDT Balance: {balance}")
            else:
                print("Failed to fetch balance.")

        elif choice in ['2', '3']:
            symbol = input("Enter symbol (e.g., BTCUSDT): ").upper()
            side = input("Buy or Sell: ").lower()
            quantity = float(input("Quantity: "))

            if choice == '2':
                trader.place_order(symbol, side, ORDER_TYPE_MARKET, quantity)
            elif choice == '3':
                price = float(input("Limit Price: "))
                trader.place_order(symbol, side, ORDER_TYPE_LIMIT, quantity, price)

        elif choice == '4':
            print("Exiting...")
            break

        else:
            print("Invalid choice. Try again.")

if __name__ == "__main__":
    main()


---

 README.md (Documentation)

# Binance USDT-M Futures Trading Bot (CLI)

A command-line trading bot for Binance USDT-M Futures supporting Market and Limit orders using the Binance Testnet.

##  Setup

1. Clone this repository.
2. Install dependencies:

pip install python-binance

3. Get API keys from [Binance Futures Testnet](https://testnet.binancefuture.com).

4. Edit `config.py` with your API credentials.

##  Run

```bash
python bot.py

Features

Market & Limit Order Support

Balance check

Logging of all actions

Simple CLI interface


 File Structure

bot.py — Main CLI interface

trader.py — Order and balance logic

logger.py — Logging setup

config.py — Configuration


---

##  Sample Output

Binance Futures CLI Trading Bot

--- Menu ---

1. Check Balance


2. Place Market Order


3. Place Limit Order


4. Exit Enter choice: 1



USDT Balance: 10000.0
