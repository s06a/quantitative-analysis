# Table of Contents

<!-- vim-markdown-toc GFM -->

* [Prepare data](#prepare-data)
* [Risk and Return](#risk-and-return)
	* [return of assets](#return-of-assets)
		* [simple return](#simple-return)
		* [multi period return](#multi-period-return)
		* [annual return](#annual-return)
	* [risk](#risk)
		* [volatility](#volatility)
			* [variance](#variance)
			* [standard deviation](#standard-deviation)
			* [annualizing volatility](#annualizing-volatility)
			* [sharpe ratio](#sharpe-ratio)
			* [max drawdown](#max-drawdown)
				* [wealth index](#wealth-index)
				* [previous peaks](#previous-peaks)
				* [drawdown](#drawdown)
				* [drawdown function](#drawdown-function)
		* [calmar ratio](#calmar-ratio)

<!-- vim-markdown-toc -->

# Prepare data

```python
# better to have dates as index
returns.index = pd.datetime(returns.index, format="%Y$m")

# we can also change the period from daily to monthly (if we have one row for each month)
returns.index = returns.index.to_period('M')
# > 1991-01-01 --> 1991-01

# to see all datas in a specific year
returns["1991"]
# > brings all rows from 1991
```

# Risk and Return

average return is misleading, having the same average return doesn't mean that you're gonna end up with the same amount of money

## return of assets

### simple return
```math
R_{t, t+1} = \frac{P_{t+1} + D_{t,t+1}}{P_{t} - 1}
```

```python
# in numpy
prices = np.array([4, 5, 8.56, 9.41])
prices[1:]/prices[:-1] - 1 # will return the returns

# in pandas wih dataframes
prices = pd.DataFrame({"stock_a": [1, 2, 3],
		       "stock_b": [1.5, 2.2, 2.6]
		      })
prices.iloc[1:].values/prices.iloc[:-1] - 1 # one should be with no index to return dataframe correctly

# better way in pandas
prices/prices.shift(1) - 1

# using pure pandas
returns = prices.pct_change()

# to plot prices
prices.plot()

# to plot returns using bar plot
prices.plot.bar()

# to see standard deviation of returns for each stock
returns.std()

# mean of returns for each stock
returns.mean()

```

### multi period return
```math
R_{t,t+2} = (1 + R_{t,t+1})(1 + R_{t+1, t+2}) - 1
```

```python
# multi period return using numpy
np.prod(returns + 1) - 1

# multi period return using pandas
(returns + 1).prod() - 1
```

### annual return
we have 252 financial days, 12 months, and 4 quarters in a year

```math
R_{a} = ((1 + R_{d})^{252} - 1)
```

```math
R_{a} = ((1 + R_{m})^{12} - 1)
```

```math
R_{a} = ((1 + R_{q})^{4} - 1)
```

## risk

### volatility

#### variance
```math
\sigma_{R}^2 = \frac{1}{N}\Sigma_{i=1}^{N}(R_{i}-\bar{R})^2
```

#### standard deviation
```math
\sigma_{R} = \sqrt{\frac{1}{N}\Sigma_{i=1}^{N}(R_{i}-\bar{R})^2}
```

```python

# we have return per each month

prices = pd.read_csv(path_to_a_dataframe_saved_as_csv,
		     header=0, index_col=0, parse_dates=True,
		     na_values=-99.99)

returns = prices.pct_change()

returns.dropna(inplace=True) # to drop first row

returns.std()
```

#### annualizing volatility

A year has 252 days and 12 months
```math
\sigma_{ann} = \sigma_{p}\sqrt{p}
```

```python
annualized_volatility = returns.sdt() * np.sqrt(12) # no matter how many months we have in data
```

#### sharpe ratio

sharp ratio (risk adjusted return) of small cap stocks is larger than large cap stocks, eventhough their return per volatility is almost the same.
 
```math
sharp \space ratio (p) = \frac{R_{p} - R_{risk \space free}}{\sigma_{p}}
```

```python
risk_free_rate = 0.03
number_of_months = returns.shape[0] # first number is length of each column
monthly_return = (returns + 1).prod()**(1 / number_of_months) - 1
annualized_return = (monthly_return + 1)**12 - 1
excess_return = annualized_return - risk_free_return
sharpe_ratio = excess_return/annualized_volatility

```

#### max drawdown

it is the worst possible return (going from the peak to bottom)

##### wealth index
hypothetical buy and hold investment in an asset or portfolio (1$ or 1000$) to see how the wealth fluctuates during the investment period and how much money you'll have

```python
# if we invest 100$ in an asset
wealth_index = 100 * (1 + returns['whatever_asset_you_want']).cumprod()
# cumprod() computes the comulative product in a moving forward form and returns a column of wealth (each row's value shows the wealth at that month)
```

##### previous peaks
```python
previous_peaks = wealth_index.cummax()
# cummax() returns the max value till every time step (moves forward)
```

##### drawdown
```python
drawdown = (wealth_index - previous_peaks)/previous_peaks # returns percentage

#to see the maximum drawdown in a period

from = "1991"
drawdown[period:].min()

to get index (date) of the maximum drawdown
drawdown[period:].idxmin()
# idxmin() return the index of the row

```

##### drawdown function
it's better to wrap all these calculations in a function
```python
def drawdown(investment_budget, returns_series: pd.Series):
	"""
	takes a series of asset returns
	returns a dataframe of below values:
		the wealth index
		the previous peaks
		percent of drawdown
	"""
	wealth_index = investment_budget * (1 + returns_series).cumprod()
	previous_peaks = wealth_index.cummax()
	drawdowns = (wealth_index - previous_peaks)/previous_peaks
	return pd.DataFrame({
		"wealth": wealth_index,
		"peaks": previous_peaks,
		"drawdown": drawdowns
	})
```

```python
# to plot wealth index and previous peaks from period till now
drawdown(returns_series[period:])[['wealth', 'peaks']].plot()
```

### calmar ratio
ratio of the annualized return over the trailing 36 months to the maximum drawdown over those trailing 36 months


