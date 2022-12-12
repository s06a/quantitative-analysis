# Table of Contents

<!-- vim-markdown-toc GFM -->

* [Risk and Return](#risk-and-return)
	* [return of an asset](#return-of-an-asset)

<!-- vim-markdown-toc -->

# Risk and Return

average return is misleading, having the same average return doesn't mean that you're gonna end up with the same amount of money

## return of an asset

Simple return
```math
R_{t, t+1} = \frac{P_{t+1} + D_{t,t+1}}{P_{t} - 1}
```

multi period return
```math
R_{t,t+2} = (1 + R_{t,t+1})(1 + R_{t+1, t+2}) - 1
```

annual return based on monthly return
```math
R_{a} = ((1 + R_{m})^{12} - 1)
```

example
```python
import numpy as np
import pandas as pd

# in numpy

prices = np.array([4, 5, 8.56, 9.41])

prices[1:]/prices[:-1] - 1 # will return the returns

# in pandas wih dataframes

prices = pd.DataFrame({"stock_a": [1, 2, 3],
		       "stock_b": [1.5, 2.2, 2.6]
		      })

prices.iloc[1:].values/prices.iloc[:-1] - 1 # one should be with no index to return dataframe corectly

# better way in pandas

prices/prices.shift(1) - 1

# better way in pandas

prices.pct_change()
```
