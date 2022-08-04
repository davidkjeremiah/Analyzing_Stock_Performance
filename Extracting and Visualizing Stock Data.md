# **Extracting and Visualizing Stock Data**

Created on Thur Aug 4 12:56:02 2022

@author: David K. Jeremiah

## **Objective**
Extract the profit data for `Tesla` and `GameStop` and build a dashboard to compare the `price of the stock` vs the `profit for the hedge fund`. 

## **Table of Contents**
* Define a Function that Makes a Graph
* Use yfinance to Extract Tesla Stock Data
* Use Webscraping to Extract Tesla Revenue Data
* Use yfinance to Extract GameStop Stock Data
* Use Webscraping to Extract GME Revenue Data
* Plot Tesla Stock Graph
* Plot GameStop Stock Graph

```py
# import necessary libraries
import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
import plotly.graph_objects as go
from plotly.subplots import make_subplots
```

## **Define Graphing Function**
In this section, we define the function `make_graph`. It takes a dataframe with stock data (dataframe must contain `Date` and `Close` columns), a dataframe with revenue data (dataframe must contain `Date` and `Revenue` columns), and the `name of the stock`

```py
def make_graph(stock_data, revenue_data, stock):
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=("Historical Share Price", "Historical Revenue"), vertical_spacing = .3)
    stock_data_specific = stock_data[stock_data.Date <= '2021--06-14']
    revenue_data_specific = revenue_data[revenue_data.Date <= '2021-04-30']
    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data_specific.Date, infer_datetime_format=True), y=stock_data_specific.Close.astype("float"), name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data_specific.Date, infer_datetime_format=True), y=revenue_data_specific.Revenue.astype("float"), name="Revenue"), row=2, col=1)
    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    fig.update_layout(showlegend=False,
    height=900,
    title=stock,
    xaxis_rangeslider_visible=True)
    fig.show()
```

## **Use yfinance to Extract Tesla Stock Data**
Using the `Ticker` function, we enter the ticker symbol of the stock we want to extract data on to create a ticker object. The stock is Tesla and its ticker symbol is `TSLA`.

```py
tesla = yf.Ticker('TSLA')
```

Using the ticker object and the function `history`, we extract stock information and save it in a dataframe named `tesla_data`. We set the `period` parameter to `max` so we get information for the maximum amount of time.

```py
tesla_data = tesla.history(period='max')
```

Next, we reset the index using the `reset_index(inplace=True)` function on the tesla_data DataFrame and display the first five rows of the tesla_data dataframe using the head function.

```py
# reseting index of data frame
tesla_data.reset_index(inplace=True)

# printing the first five rows
tesla_data.head()
```

## **Use Webscraping to Extract Tesla Revenue Data**
Now, we use the `requests` library to download the webpage [https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue](https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0220ENSkillsNetwork23455606-2022-01-01). 

And we save the text of the response as a variable named `html_data`

```py
html_data= requests.get("https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0220ENSkillsNetwork23455606-2022-01-01").text
```

Parse the html data using `beautiful_soup`.

```py
soup = BeautifulSoup(html_data, 'html.parser')
```

Using `BeautifulSoup` or the `read_html` function, we extract the table with `Tesla Quarterly Revenue` and store it into a dataframe named `tesla_revenue`. 

Also, we rename dataframe columns to `Date` and `Revenue` respectively.

```py
# Extracting the table with Tesla Quarterly Revenue and storing it into tesla_revenue
tesla_revenue = pd.read_html(str(soup))[1]

# renaming dataframe
tesla_revenue.rename(columns = {'Tesla Quarterly Revenue(Millions of US $)':'Date', 'Tesla Quarterly Revenue(Millions of US $).1':'Revenue'}, inplace = True)

# confirm columns have been renamed
tesla_revenue.head()
```

Next, we remove the `comma` and `dollar sign` from the `Revenue` column, for ploting purposes.

```py
tesla_revenue['Revenue'] = tesla_revenue['Revenue'].str.replace(',|\$',"", regex=True)
```

Next, we remove all null or empty strings in the Revenue column.

```py
# drop all NA'set in the Revenue column
tesla_revenue.dropna(inplace=True)

# remove all empty strings in the Revenue column
tesla_revenue = tesla_revenue[tesla_revenue['Revenue'] != ""]

# inspect dataframe
tesla_revenue.tail()
```

## **Use yfinance to Extract GameStop Stock Data**
Now, using the `Ticker` function, we enter the ticker symbol of the stock we want to extract data on to create a ticker object. The stock in this case is `GameStop` and its ticker symbol is `GME`.

```py
gamestop = yf.Ticker('GME')
```

Using the ticker object and the `history` function, we extract stock information and save it in a dataframe named `gme_data`. We set the `period` parameter to `max` so we get information for the maximum amount of time.

```py
gme_data = gamestop.history(period='max')
```

Next, we reset the index using the `reset_index(inplace=True)` function on the gme_data DataFrame and display the first five rows of the gme_data dataframe using the head function.

```py
# reseting index of data frame
gme_data.reset_index(inplace=True)

# printing the first five rows
gme_data.head() 
```

## **Use Webscraping to Extract GME Revenue Data**
Next, we use the `requests` library to download the webpage <https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html>. And save the text of the response as a variable named `html_data`.

```py
html_data = requests.get("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html").text
```

Parse the html data using `beautiful_soup`.

```py
soup = BeautifulSoup(html_data, 'html.parser')
```

Using `BeautifulSoup` or the `read_html` function, we extract the table with `GameStop Quarterly Revenue` and store it into a dataframe named `gme_revenue`. 

Also, we rename dataframe columns to `Date` and `Revenue` respectively.

```py
# extract the table with `GameStop Quarterly Revenue` and store it into a dataframe named `gme_revenue`
gme_revenue = pd.read_html(str(soup))[1]

# rename dataframe
gme_revenue.rename(columns = {'GameStop Quarterly Revenue(Millions of US $)':'Date', 'GameStop Quarterly Revenue(Millions of US $).1':'Revenue'}, inplace = True)

# removing the comma and dollar sign from the Revenue column
gme_revenue['Revenue'] = gme_revenue['Revenue'].str.replace(",|\$", "", regex=True)
```

```py
# display the last five rows of the gme_revenie dataframe
gme_revenue.tail()
```

## **Plot Tesla Stock Graph**
Now, we use the `make_graph` function to graph the Tesla Stock Data, also provide a title for the graph. The structure to call the make_graph function is `make_graph(tesla_data, tesla_revenue, 'Tesla')`. 

***Note*** the graph will only show data up to June 2021.

```py
make_graph(tesla_data, tesla_revenue, 'Tesla')
```

## **Plot GameStop Stock Graph**
Next, we do same for GameStop Stock Data. We use the `make_graph` function to graph the GameStop Stock Data, also provide a title for the graph. The structure to call the make_graph function is `make_graph(gme_data, gme_revenue, 'GameStop')`. 

Also ***Note:*** the graph will only show data upto June 2021.

```py
make_graph(gme_data, gme_revenue, 'GameStop')
```