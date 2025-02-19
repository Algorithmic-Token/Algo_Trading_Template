##Withe the help of CHatGPT Code Excution free option
import backtrader as bt
import datetime
import ccxt  # For Binance API
import oandapyV20
import oandapyV20.endpoints.orders as orders

class MovingAverageStrategy(bt.Strategy):
    params = (
        ('short_period', 10),
        ('long_period', 50),
        ('stop_loss', 0.02),  # 2% stop loss
        ('take_profit', 0.05),  # 5% take profit
    )
    
    def __init__(self):
        self.short_ma = bt.indicators.SimpleMovingAverage(period=self.params.short_period)
        self.long_ma = bt.indicators.SimpleMovingAverage(period=self.params.long_period)
    
    def next(self):
        if self.short_ma[0] > self.long_ma[0] and not self.position:
            self.buy()
        elif self.short_ma[0] < self.long_ma[0] and self.position:
            self.sell()

# Backtesting Engine
cerebro = bt.Cerebro()

# Load Data
from backtrader.feeds import GenericCSVData

data = GenericCSVData(
    dataname='BTCUSD.csv', 
    dtformat='%Y-%m-%d', 
    timeframe=bt.TimeFrame.Days, 
    compression=1, 
    openinterest=-1
)

cerebro.adddata(data)
cerebro.addstrategy(MovingAverageStrategy)
cerebro.run()
cerebro.plot()

# Execution (Live Trading)
# Binance API (Crypto Trading)
BINANCE_API_KEY = "your_binance_api_key"
BINANCE_SECRET_KEY = "your_binance_secret_key"
exchange = ccxt.binance({
    'apiKey': BINANCE_API_KEY,
    'secret': BINANCE_SECRET_KEY,
})

def execute_trade_binance(symbol, order_type, amount, stop_loss, take_profit):
    price = exchange.fetch_ticker(symbol)['last']
    stop_price = price * (1 - stop_loss) if order_type == 'buy' else price * (1 + stop_loss)
    take_profit_price = price * (1 + take_profit) if order_type == 'buy' else price * (1 - take_profit)
    
    order = exchange.create_order(symbol, 'market', order_type, amount)
    print("Binance Market Order Executed:", order)
    
    exchange.create_order(symbol, 'stop_loss_limit', order_type, amount, stop_price)
    exchange.create_order(symbol, 'take_profit_limit', order_type, amount, take_profit_price)
    print(f"Stop Loss set at {stop_price}, Take Profit set at {take_profit_price}")

# OANDA API (Forex Trading)
OANDA_API_KEY = "your_oanda_api_key"
OANDA_ACCOUNT_ID = "your_oanda_account_id"
client = oandapyV20.API(access_token=OANDA_API_KEY)

def execute_trade_oanda(instrument, units, side, stop_loss, take_profit):
    price = float(client.request(oandapyV20.endpoints.pricing.PricingInfo(OANDA_ACCOUNT_ID, params={"instruments": instrument}))['prices'][0]['bids'][0]['price'])
    stop_price = price * (1 - stop_loss) if side == 'buy' else price * (1 + stop_loss)
    take_profit_price = price * (1 + take_profit) if side == 'buy' else price * (1 - take_profit)
    
    order = orders.OrderCreate(OANDA_ACCOUNT_ID, data={
        "order": {
            "instrument": instrument,
            "units": str(units),
            "side": side,
            "type": "MARKET",
            "stopLossOnFill": {"price": str(stop_price)},
            "takeProfitOnFill": {"price": str(take_profit_price)}
        }
    })
    client.request(order)
    print(f"OANDA Order Executed: Stop Loss at {stop_price}, Take Profit at {take_profit_price}")
