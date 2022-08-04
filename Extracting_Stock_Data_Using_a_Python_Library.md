# **Extracting Stock Data Using a Python Library**
Created on Wed Aug 3 08:00:12 2022

@author: David K. Jeremiah

## **Introduction**
A company's [stock](https://www.investopedia.com/terms/s/stock.asp) share is a piece of the company, more precisely.

A stock (also known as [equity](https://www.investopedia.com/terms/e/equity.asp#:~:text=Equity%20represents%20the%20value%20that,debts%20associated%20with%20that%20asset) is a security that represents the ownership of a fraction of a corporation. This entitles the owner of the stock to a proportion of the corporation's assets and profits equal to how much stock they own. Units of stock are called "shares."

An investor can buy a stock and sell it later. If the stock price increases, the investor profits, If it decreases,the investor with incur a loss. Determining the stock price is complex; it depends on the number of outstanding shares, the size of the company's future profits, and much more.

## **Objective**
In this lab we will extract stock data using a Python library. 

We will use the `yfinance` library. It allows us to extract information about the stocks - returning data in a pandas dataframe.

## **Table of Contents**
* Using yfinance to Extract Stock Info
* Using yfinance to Extract Historical Share Price Data
* Using yfinance to Extract Historical Dividends Data

```py
# install the yfinance library
!pip3 install yfinance==0.1.67
```

```py
# import the necessary libraries
import yfinance as yf
import pandas as pd
```

## **Using the yfinance Library to Extract Stock Data**
***Note:*** We have what we call as the [stock ticker](https://www.investopedia.com/ask/answers/12/what-is-a-stock-ticker.asp#:~:text=A%20stock%20ticker%20is%20a,movement%20is%20up%20or%20down), which is a report of the price of a certain stock, updated continuously throughout the trading session by the various stock market exchanges. The y-finance API is used to obtain the stock ticker and extract information about the stock. 

Using the `Ticker` module from the yfinance library, we can create an object that will allow us to access functions to extract data. To do this we need to provide the ticker symbol for the stock, here the company is `Apple` and the ticker symbol is `AAPL`.

```py
# For Apple stock
apple = yf.Ticker("AAPL")

print(apple)
```

Now we can access functions and variables to extract the type of data we need.

### **Stock Info**
Using the attribute `info` we can extract information about the stock as a Python dictionary.

We can get to know more about the company using the `'longBusinessSummary'` key

```py
# summary about the company
print(apple_info['longBusinessSummary'])
```

### **Extracting Share Price**
A [share](https://www.investopedia.com/terms/s/shares.asp) is the single smallest part of a company's stock that you can buy. The prices of these shares fluctuate over time. 

Using the `history()` method we can get the share price of the stock over a certain period of time. 

Using the period parameter we can set how far back from the present to get data. The options for period are 1 day (1d), 5d, 1 month (1mo) , 3mo, 6mo, 1 year (1y), 2y, 5y, 10y, ytd, and max.

```py
apple_share_price_data = apple.history(period="max")
```

The format that the data is returned in, is a Pandas DataFrame. With the Date as the index, and the share `Open`, `High`, `Low`, `Close`, `Volume`, and `Stock Splits` are given for each day, as columns.

```py
# print the first 5 rows
print(apple_share_price_data.head())
```

We can reset the index of the DataFrame with the `reset_index` function. We also set the `inplace` parameter to `True` so the change takes place to the DataFrame itself.

```py
apple_share_price_data.reset_index(inplace=True)

print(apple_share_price_data.head())
```

We can plot the `Open` price against the `Date`:

```py
apple_share_price_data.plot(x="Date", y="Open")
```

### **Extracting Dividends**
`Dividends` are the distribution of a company's profits to shareholders. In this case they are defined as an amount of money returned per share an investor owns. Using the variable dividends we can get a dataframe of the data. The period of the data is given by the period defined in the `history` function.

```py
apple.dividends
```

We can plot the dividends overtime:

```py
apple.dividends.plot()
```

Now using the Ticker module, let's create an object for AMD (Advanced Micro Devices) with the ticker symbol is `AMD` called; name the object `amd`.

```py
# For Advanced Micro Devices
amd = yf.Ticker('AMD')
```

Using the key `country`, let's find the country the stock belongs to:

```py
amd_country = amd.info['country']
print(amd_country)
```

Using the key `sector` lets find the sector the stock belongs to:

```py
amd_sector = amd.info['sector']
print(amd_sector)
```

Now, let's obtain stock data for AMD using the `history` function - setting the `period` to `max`. And we'll consider the Volume traded on the first day (first row).

```py
amd_share_price_data = amd.history(period='max')

# reset index
amd_share_price_data.reset_index(inplace=True)

# print stock data
print(amd_share_price_data)

# print the volume traded the first day (first row)
print(amd_share_price_data.loc[[1,]])
```