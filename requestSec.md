
#request.security

Requests data from another symbol and/or timeframe.

```
request.security(symbol, timeframe, expression, gaps, lookahead, ignore_invalid_symbol, currency) → series <type>
```

EXAMPLE
```
//@version=5
indicator("Simple `request.security()` calls")
// Returns 1D close of the current symbol.
dailyClose = request.security(syminfo.tickerid, "1D", close)
plot(dailyClose)

// Returns the close of "AAPL" from the same timeframe as currently open on the chart.
aaplClose = request.security("AAPL", timeframe.period, close)
plot(aaplClose)
```

EXAMPLE
```
//@version=5
indicator("Advanced `request.security()` calls")
// This calculates a 10-period moving average on the active chart.
sma = ta.sma(close, 10)
// This sends the `sma` calculation for execution in the context of the "AAPL" symbol at a "240" (4 hours) timeframe.
aaplSma = request.security("AAPL", "240", sma)
plot(aaplSma)

// To avoid differences on historical and realtime bars, you can use this technique, which only returns a value from the higher timeframe on the bar after it completes:
indexHighTF = barstate.isrealtime ? 1 : 0
indexCurrTF = barstate.isrealtime ? 0 : 1
nonRepaintingClose = request.security(syminfo.tickerid, "1D", close[indexHighTF])[indexCurrTF]
plot(nonRepaintingClose, "Non-repainting close")

// Returns the 1H close of "AAPL", extended session included. The value is dividend-adjusted.
extendedTicker = ticker.modify("NASDAQ:AAPL", session = session.extended, adjustment = adjustment.dividends)
aaplExtAdj = request.security(extendedTicker, "60", close)
plot(aaplExtAdj)

// Returns the result of a user-defined function.
// The `max` variable is mutable, but we can pass it to `request.security()` because it is wrapped in a function.
allTimeHigh(source) =>
    var max = source
    max := math.max(max, source)
allTimeHigh1D = request.security(syminfo.tickerid, "1D", allTimeHigh(high))

// By using a tuple `expression`, we obtain several values with only one `request.security()` call.
[open1D, high1D, low1D, close1D, ema1D] = request.security(syminfo.tickerid, "1D", [open, high, low, close, ta.ema(close, 10)])
plotcandle(open1D, high1D, low1D, close1D)
plot(ema1D)

// Returns an array containing the OHLC values of the chart's symbol from the 1D timeframe.
ohlcArray = request.security(syminfo.tickerid, "1D", array.from(open, high, low, close))
plotcandle(array.get(ohlcArray, 0), array.get(ohlcArray, 1), array.get(ohlcArray, 2), array.get(ohlcArray, 3))

```
RETURNS
A result determined by `expression`.

ARGUMENTS

**symbol (simple string)** Symbol to request the data from. Use [syminfo.tickerid](https://www.tradingview.com/pine-script-reference/v5/#var_syminfo.tickerid) to request data from the chart's symbol. To request data with additional parameters (extended sessions, dividend adjustments, or a non-standard chart type like Heikin Ashi or Renko), a custom ticker identifier must first be created using functions in the `ticker.*` namespace.

**timeframe (simple string)** Timeframe of the requested data. To use the chart's timeframe, use an empty string or the [timeframe.period](https://www.tradingview.com/pine-script-reference/v5/#var_timeframe.period) variable. Valid timeframe strings are documented in the User Manual's [Timeframes](https://www.tradingview.com/pine-script-docs/en/v5/concepts/Timeframes.html#timeframe-string-specifications) page.

**expression (variable, function, array or matrix of series int/float/bool/string/color, or a tuple of these)** An expression to be calculated and returned from the [request.security](https://www.tradingview.com/pine-script-reference/v5/#fun_request.security) call's context. It can be a built-in variable like [close](https://www.tradingview.com/pine-script-reference/v5/#var_close), an expression such as `ta.sma(close, 100)`, a non-mutable user-defined variable previously calculated in the script, a function call that does not use PineScript™ drawings, an array, a matrix, or a tuple. Mutable variables are not allowed, unless they are enclosed in the body of a function used in the expression.

**gaps (input barmerge_gaps)** Specifies how the returned values are merged on chart bars. Possible values: [barmerge.gaps_on](https://www.tradingview.com/pine-script-reference/v5/#var_barmerge.gaps_on), [barmerge.gaps_off](https://www.tradingview.com/pine-script-reference/v5/#var_barmerge.gaps_off). With [barmerge.gaps_on](https://www.tradingview.com/pine-script-reference/v5/#var_barmerge.gaps_on) a value only appears on the current chart bar when it first becomes available from the function's context, otherwise [na](https://www.tradingview.com/pine-script-reference/v5/#var_na) is returned (thus a "gap" occurs). With [barmerge.gaps_off](https://www.tradingview.com/pine-script-reference/v5/#var_barmerge.gaps_off) what would otherwise be gaps are filled with the latest known value returned, avoiding [na](https://www.tradingview.com/pine-script-reference/v5/#var_na) values. Optional. The default is [barmerge.gaps_off](https://www.tradingview.com/pine-script-reference/v5/#var_barmerge.gaps_off).

**lookahead (input barmerge_lookahead)** On historical bars only, returns data from the timeframe before it elapses. Possible values: [barmerge.lookahead_on](https://www.tradingview.com/pine-script-reference/v5/#var_barmerge.lookahead_on), [barmerge.lookahead_off](https://www.tradingview.com/pine-script-reference/v5/#var_barmerge.lookahead_off). Has no effect on realtime values. Optional. The default is [barmerge.lookahead_off](https://www.tradingview.com/pine-script-reference/v5/#var_barmerge.lookahead_off) starting from Pine Script™ v3. The default is [barmerge.lookahead_on](https://www.tradingview.com/pine-script-reference/v5/#var_barmerge.lookahead_on) in v1 and v2. WARNING: Using [barmerge.lookahead_on](https://www.tradingview.com/pine-script-reference/v5/#var_barmerge.lookahead_on) at timeframes higher than the chart's without offsetting the `expression` argument like in `close[1]` will introduce future leak in scripts, as the function will then return the `close` price before it is actually known in the current context. As is explained in the User Manual's page on [Repainting](https://www.tradingview.com/pine-script-docs/en/v5/concepts/Repainting.html#future-leak-with-request-security) this will produce misleading results.

**ignore_invalid_symbol (input bool)** Determines the behavior of the function if the specified symbol is not found: if [false](https://www.tradingview.com/pine-script-reference/v5/#op_false), the script will halt and throw a runtime error; if [true](https://www.tradingview.com/pine-script-reference/v5/#op_true), the function will return [na](https://www.tradingview.com/pine-script-reference/v5/#var_na) and execution will continue. Optional. The default is [false](https://www.tradingview.com/pine-script-reference/v5/#op_false).

**currency (simple string)** Currency into which values expressed in currency units ([open](https://www.tradingview.com/pine-script-reference/v5/#var_open), [high](https://www.tradingview.com/pine-script-reference/v5/#var_high), [low](https://www.tradingview.com/pine-script-reference/v5/#var_low), [close](https://www.tradingview.com/pine-script-reference/v5/#var_close), etc.) or expressions using such values are to be converted. The conversion rates used are based on the FX_IDC pairs' daily rates of the previous day (relative to the bar where the calculation is done). Possible values: a three-letter string with the [currency code in the ISO 4217 format](https://en.wikipedia.org/wiki/ISO_4217#Active_codes) (e.g. "USD") or one of the constants in the currency.* namespace, e.g. [currency.USD](https://www.tradingview.com/pine-script-reference/v5/#var_currency.USD). Note that literal values such as `200` are not converted. Optional. The default is [syminfo.currency](https://www.tradingview.com/pine-script-reference/v5/#var_syminfo.currency).

REMARKS

Pine Script™ code using this function may calculate differently on historical and realtime bars, leading to  [repainting](https://www.tradingview.com/pine-script-docs/en/v5/concepts/Repainting.html).

A single script can have no more than 40 calls to `request.*()` functions.

### request.security_lower_tf

Requests data from a specified symbol from a lower timeframe than the chart's. The function returns an array containing one element for each closed lower timeframe intrabar inside the current chart's bar. On a 5-minute chart using a `timeframe` argument of "1", the size of the array will usually be 5, with each array element representing the value of `expression` on a 1-minute intrabar, ordered sequentially in time.

```
request.security_lower_tf(symbol, timeframe, expression, ignore_invalid_symbol, currency) → array<type>
```

Example
```
//@version=5
indicator("`request.security_lower_tf()` Example", overlay = true)

// If the current chart timeframe is set to 120 minutes, then the `arrayClose` array will contain two 'close' values from the 60 minute timeframe for each bar.
arrClose = request.security_lower_tf(syminfo.tickerid, "60", close)

if bar_index == last_bar_index - 1
    label.new(bar_index, high, str.tostring(arrClose))
```

RETURNS

An array of a type determined by `expression`, or a tuple of these.

ARGUMENTS

symbol (simple string) Symbol to request the data from. Use  [syminfo.tickerid](https://www.tradingview.com/pine-script-reference/v5/#var_syminfo.tickerid)  to request data from the chart's symbol. To request data with additional parameters (extended sessions, dividend adjustments, or a non-standard chart type like Heikin Ashi or Renko), a custom ticker identifier must first be created using functions in the `ticker.*` namespace.

timeframe (simple string) Timeframe of the requested data. To use the chart's timeframe, use an empty string or the  [timeframe.period](https://www.tradingview.com/pine-script-reference/v5/#var_timeframe.period)  variable. Valid timeframe strings are documented in the User Manual's  [Timeframes](https://www.tradingview.com/pine-script-docs/en/v5/concepts/Timeframes.html#timeframe-string-specifications)  page.

expression (variable or function of series int/float/bool/string/color, or a tuple of these) An expression to be calculated and returned from the function call's context. It can be a built-in variable like  [close](https://www.tradingview.com/pine-script-reference/v5/#var_close), an expression such as `ta.sma(close, 100)`, a non-mutable user-defined variable previously calculated in the script, a function call that does not use PineScript™ drawings, arrays or matrices, or a tuple. Mutable variables are not allowed, unless they are enclosed in the body of a function used in the expression.

ignore_invalid_symbol (const bool) Determines the behavior of the function if the specified symbol is not found: if  [false](https://www.tradingview.com/pine-script-reference/v5/#op_false), the script will halt and throw a runtime error; if  [true](https://www.tradingview.com/pine-script-reference/v5/#op_true), the function will return  [na](https://www.tradingview.com/pine-script-reference/v5/#var_na)  and execution will continue. Optional. The default is  [false](https://www.tradingview.com/pine-script-reference/v5/#op_false).

currency (simple string) Currency into which values expressed in currency units ([open](https://www.tradingview.com/pine-script-reference/v5/#var_open),  [high](https://www.tradingview.com/pine-script-reference/v5/#var_high),  [low](https://www.tradingview.com/pine-script-reference/v5/#var_low),  [close](https://www.tradingview.com/pine-script-reference/v5/#var_close), etc.) or expressions using such values are to be converted. The conversion rates used are based on the FX_IDC pairs' daily rates of the previous day (relative to the bar where the calculation is done). Possible values: a three-letter string with the  [currency code in the ISO 4217 format](https://en.wikipedia.org/wiki/ISO_4217#Active_codes)  (e.g. "USD") or one of the constants in the currency.* namespace, e.g.  [currency.USD](https://www.tradingview.com/pine-script-reference/v5/#var_currency.USD). Note that literal values such as `200` are not converted. Optional. The default is  [syminfo.currency](https://www.tradingview.com/pine-script-reference/v5/#var_syminfo.currency).

REMARKS

Pine Script™ code using this function may calculate differently on historical and real-time bars, leading to  [repainting](https://www.tradingview.com/pine-script-docs/en/v5/concepts/Repainting.html).

Please note that spreads (e.g., “AAPL+MSFT*TSLA”) will not always return reliable data with this function.

A single script can have no more than 40 calls to `request.*()` functions.

A maximum of 100,000 lower timeframe bars can be accessed by this function. The number of chart bars for which lower timeframe data is available will thus vary with the requested lower timeframe.
