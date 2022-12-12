# Table of Contents

<!-- vim-markdown-toc GFM -->

* [Risk and Return](#risk-and-return)
	* [return of assets](#return-of-assets)
		* [simple return](#simple-return)
		* [multi period return](#multi-period-return)
		* [annual return](#annual-return)
	* [volatility](#volatility)
		* [variance](#variance)
		* [standard deviation](#standard-deviation)
		* [annualizing volatility](#annualizing-volatility)
		* [sharp ratio](#sharp-ratio)

<!-- vim-markdown-toc -->

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

## volatility

### variance
```math
\sigma_{R}^2 = \frac{1}{N}\Sigma_{i=1}^{N}(R_{i}-\bar{R})^2
```

### standard deviation
```math
\sigma_{R} = \sqrt{\frac{1}{N}\Sigma_{i=1}^{N}(R_{i}-\bar{R})^2}
```

### annualizing volatility

A year has 252 days and 12 months
```math
\sigma_{ann} = \sigma_{p}\sqrt{p}
```

### sharp ratio
```math
sharp \space ratio (p) = \frac{R_{p} - R_{risk \space free}}{\sigma_{p}}
```

```python
prices = pd.read_csv(path_to_a_dataframe_saved_as_csv,
		     header=0, index_col=0, parse_dates=True,
		     na_values=-99.99)

returns = prices.pct_change()

returns.dropna(inplace=True) # to drop first row

returns.std()

```
