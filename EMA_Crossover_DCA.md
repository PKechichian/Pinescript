### EMA Crossover DCA

---

**Entry conditions** : Fast EMA > Slow EMA

**Stop Loss** : No SL (pyramiding orders)

**TP** : % of gains

---

```
//@version=5
strategy("EMA Crossover DCA Tester", overlay=true, initial_capital = 1000, commission_value = 0.04)

// Input parameters for EMA periods and crossover direction
fast_ema_period = input.int(9, title="Fast EMA Period")
slow_ema_period = input.int(21, title="Slow EMA Period")
cross_up = input.bool(true, title="Cross Upwards")
cross_down = input.bool(false, title="Cross Downwards")

// Input for TP
take_profit = input.float(10, title="Take Profit (%)")

// Calculate EMAs
fast_ema = ta.ema(close, fast_ema_period)
slow_ema = ta.ema(close, slow_ema_period)

// Check for crossover
cross_long = cross_up and ta.crossover(fast_ema, slow_ema)
cross_short = cross_down and ta.crossunder(fast_ema, slow_ema)

// Entry based on equity percentage
equity_percent = input.float(10, title="Equity Percentage for Entry (%)") / 100 
entry_amount = strategy.equity * equity_percent

// Calculate take profit price
long_tp_price = strategy.position_avg_price * (1 + take_profit / 100)

// Enter and exit conditions
if cross_long
    strategy.entry("Long", strategy.long, qty=entry_amount / close)

if strategy.position_size > 0 and close >= long_tp_price
    strategy.close("Long")

// Plot EMAs
plot(fast_ema, color=color.green, title="Fast EMA")
plot(slow_ema, color=color.red, title="Slow EMA")
```
