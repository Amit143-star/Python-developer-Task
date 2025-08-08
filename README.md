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
