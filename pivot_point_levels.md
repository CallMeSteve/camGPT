
### ta.pivot_point_levels

Calculates the pivot point levels using the specified `type` and `anchor`.
`
ta.pivot_point_levels(type, anchor, developing) → float[]
`
EXAMPLE

```
//@version=5  
indicator("Weekly Pivots",  max_lines_count=500,  overlay=true)  
timeframe  =  "1W"  
typeInput  =  input.string("Traditional",  "Type",  options=["Traditional",  "Fibonacci",  "Woodie",  "Classic",  "DM",  "Camarilla"])  
weekChange  =  timeframe.change(timeframe)  
pivotPointsArray  =  ta.pivot_point_levels(typeInput,  weekChange)  
if  weekChange  
for  pivotLevel  in  pivotPointsArray  
line.new(time,  pivotLevel,  time  +  timeframe.in_seconds(timeframe)  *  1000,  pivotLevel,  xloc=xloc.bar_time)
```

RETURNS

A float[] array with numerical values representing 11 pivot point levels: [P, R1, S1, R2, S2, R3, S3, R4, S4, R5, S5]. Levels absent from the specified `type` return  [na](https://www.tradingview.com/pine-script-reference/v5/#var_na)  values (e.g., "DM" only calculates P, R1, and S1).

ARGUMENTS

type (series string) The type of pivot point levels. Possible values: "Traditional", "Fibonacci", "Woodie", "Classic", "DM", "Camarilla".

anchor (series bool) The condition that triggers the reset of the pivot point calculations. When  [true](https://www.tradingview.com/pine-script-reference/v5/#op_true), calculations reset; when  [false](https://www.tradingview.com/pine-script-reference/v5/#op_false), results calculated at the last reset persist.

developing (series bool) If  [false](https://www.tradingview.com/pine-script-reference/v5/#op_false), the values are those calculated the last time the anchor condition was  [true](https://www.tradingview.com/pine-script-reference/v5/#op_true). They remain constant until the anchor condition becomes  [true](https://www.tradingview.com/pine-script-reference/v5/#op_true)  again. If  [true](https://www.tradingview.com/pine-script-reference/v5/#op_true), the pivots are developing, i.e., they constantly recalculate on the data developing between the point of the last anchor (or bar zero if the anchor condition was never  [true](https://www.tradingview.com/pine-script-reference/v5/#op_true)) and the current bar. Optional. The default is  [false](https://www.tradingview.com/pine-script-reference/v5/#op_false).

REMARKS

The `developing` parameter cannot be `true` when `type` is set to "Woodie", because the Woodie calculation for a period depends on that period's open, which means that the pivot value is either available or unavailable, but never developing. If used together, the indicator will return a runtime error.

### ta.pivothigh

This function returns price of the pivot high point. It returns 'NaN', if there was no pivot high point.
`
ta.pivothigh(source, leftbars, rightbars) → series float
`
`
ta.pivothigh(leftbars, rightbars) → series float
`
EXAMPLE
```
//@version=5
indicator("PivotHigh", overlay=true)
leftBars = input(2)
rightBars=input(2)
ph = ta.pivothigh(leftBars, rightBars)
plot(ph, style=plot.style_cross, linewidth=3, color= color.red, offset=-rightBars)
```

RETURNS

Price of the point or 'NaN'.

ARGUMENTS

**source (series int/float)** An optional parameter. Data series to calculate the value. 'High' by default.

**leftbars (series int/float)** Left strength.

**rightbars (series int/float)** Right strength.

REMARKS

If parameters 'leftbars' or 'rightbars' are series you should use  [max_bars_back](https://www.tradingview.com/pine-script-reference/v5/#fun_max_bars_back)  function for the 'source' variable.
### ta.pivotlow

This function returns price of the pivot low point. It returns 'NaN', if there was no pivot low point.
`
ta.pivotlow(source, leftbars, rightbars) → series float
`
`
ta.pivotlow(leftbars, rightbars) → series float
`
```
//@version=5  
indicator("PivotLow",  overlay=true)  
leftBars  =  input(2)  
rightBars=input(2)  
pl  =  ta.pivotlow(close,  leftBars,  rightBars)  
plot(pl,  style=plot.style_cross,  linewidth=3,  color=  color.blue,  offset=-rightBars)
```
RETURNS

Price of the point or 'NaN'.

ARGUMENTS

**source (series int/float)** An optional parameter. Data series to calculate the value. 'Low' by default.

**leftbars (series int/float)** Left strength.

**rightbars (series int/float)** Right strength.

REMARKS

If parameters 'leftbars' or 'rightbars' are series you should use  [max_bars_back](https://www.tradingview.com/pine-script-reference/v5/#fun_max_bars_back)  function for the 'source' variable.

### ta.range

Returns the difference between the min and max values in a series.
`
ta.range(source, length) → series float
`
`
ta.range(source, length) → series int
`
RETURNS

The difference between the min and max values in the series.

ARGUMENTS

**source (series int/float)** Series of values to process.

**length (series int)** Number of bars (length).
