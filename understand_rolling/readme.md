# 1 understand rollling object and the machanism
~~~
import numpy as np
import pandas as pd

df = pd.DataFrame(np.random.randn(6,3))
output1 = df.rolling(5).mean()
~~~

~~~
def print_df(x):
    print(x)
    print(type(x),x.shape)
    print("--------------------------")
    return x.mean()
    
output = df.rolling(5).apply(lambda x: print_df(x))
~~~

# 2 Efficient Python Pandas Stock Beta Calculation on Many Dataframes
[code](https://stackoverflow.com/questions/39501277/efficient-python-pandas-stock-beta-calculation-on-many-dataframes)

~~~

def beta(df, market=None):
    # If the market values are not passed,
    # I'll assume they are located in a column
    # named 'Market'.  If not, this will fail.
    if market is None:
        market = df['Market']
        df = df.drop('Market', axis=1)
    X = market.values.reshape(-1, 1)
    X = np.concatenate([np.ones_like(X), X], axis=1)
    b = np.linalg.pinv(X.T.dot(X)).dot(X.T).dot(df.values)
    return pd.Series(b[1], df.columns, name=df.index[-1])

def calc_beta(df):
    np_array = df.values
    m = np_array[:,0] # market returns are column zero from numpy array
    s = np_array[:,1] # stock returns are column one from numpy array
    covariance = np.cov(s,m) # Calculate covariance between stock and market
    beta = covariance[0,1]/covariance[1,1]
    return beta


def roll(df, w):
    for i in range(df.shape[0] - w + 1):
        yield pd.DataFrame(df.values[i:i+w, :], df.index[i:i+w], df.columns)


#betas = pd.concat([beta(sdf) for sdf in roll(df.pct_change().dropna(), 12)], axis=1).T


m, n = 500, 3000
dates = pd.date_range('1995-12-31', periods=m, freq='M', name='Date')

cols = ['Open', 'High', 'Low', 'Close']
dfs = {'s{:04d}'.format(i): pd.DataFrame(np.random.rand(m, 4), dates, cols) for i in range(n)}

market = pd.Series(np.random.rand(m), dates, name='Market')

df = pd.concat([market] + [dfs[k].Close.rename(k) for k in dfs.keys()], axis=1).sort_index(1)


start_time = time.time()

betas = pd.concat([beta(sdf) for sdf in roll(df.pct_change().dropna(), 12)], axis=1).T
#
for c, col in betas.iteritems():
    dfs[c]['Beta'] = col
print("total time:{}".format(time.time() - start_time))

~~~
