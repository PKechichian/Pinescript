### Simple long only scalping strategy

---

**Entry conditions** : Price > EMA, Price > Donchian middle line, MA crossover

**Stop Loss** : Donchian middle line crossunder

**TP** : RSI level

---

```pinecript
//@version=5
strategy("MA EMA DC RSI Strategy", overlay=true, initial_capital = 1000, commission_value = 0.02, default_qty_value = 50, default_qty_type = strategy.percent_of_equity)

// Input parameters
maLength = input.int(20, "MA Length")
emaLength = input.int(50, "EMA Length")
dcLength = input.int(20, "Donchian Channel Length")
rsiLength = input.int(14, "RSI Length")
rsiExitLevel = input.float(70, "RSI Exit Level")
emaHtf = input.timeframe("D", "EMA Higher Timeframe")
dcHtf = input.timeframe("W", "Donchian Channel Higher Timeframe")

// Current timeframe MA
ma = ta.sma(close, maLength)

// Higher timeframe EMA
emaHigher = request.security(syminfo.tickerid, emaHtf, ta.ema(close, emaLength))

// Higher timeframe Donchian Channel middle line
dcMiddleHigher = request.security(syminfo.tickerid, dcHtf, (ta.highest(high, dcLength) + ta.lowest(low, dcLength)) / 2)

// RSI
rsi = ta.rsi(close, rsiLength)

// Conditions
maUpCross = ta.crossover(close, ma)
aboveDC = close > dcMiddleHigher
aboveEMA = close > emaHigher
rsiExit = ta.crossover(rsi, rsiExitLevel)

// Plot indicators
plot(ma, color=color.blue, title="MA")
plot(emaHigher, color=color.red, title="HTF EMA")
plot(dcMiddleHigher, color=color.green, title="HTF DC Middle")

// Strategy logic
if (maUpCross and aboveDC and aboveEMA)
    strategy.entry("Long", strategy.long)

if (rsiExit)
    strategy.close("Long")

// Set stop loss
strategy.exit("SL", "Long", stop=dcMiddleHigher)
```
