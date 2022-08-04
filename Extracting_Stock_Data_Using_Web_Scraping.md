# **Extracting Stock Data Using a Web Scraping**

Created on Wed Aug 3 02:56:02 2022

@author: David K. Jeremiah

## **Objectives**

Not all stock data is available via API, some stock info or financial data can only be obtained using web-scraping.

Using beautiful soup we will extract historical share data from a web-page.

## **Table of Contents**
* Downloading the Webpage Using Requests Library
* Parsing Webpage HTML Using BeautifulSoup
* Extracting Data and Building DataFrame

```py
# import the necessary libraries
import pandas as pd
import requests
from bs4 import BeautifulSoup
```

## **Using Webscraping to Extract Stock Data for Netflix**
First we use the `request` library to downlaod the webpage, and extract the text. We will extract `Netflix` [stock data](https://finance.yahoo.com/quote/NFLX/history?p=NFLX)

```py
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/netflix_data_webpage.html"

response = requests.get(url).text
```

Next we must parse the text into `html` using beautiful_soup

```py
soup = BeautifulSoup(response, 'html.parser')
```
```py
print(soup.prettify())
```

Now we can turn the html table into a pandas dataframe

```py
netflix_data = pd.DataFrame(columns=["Date", "Open", "High", "Low", "Close", "Adj Close", "Volume"])

# First we isolate the body of the table which contains all the information
# Then we loop through each row and find all the column values for each row
for row in soup.find("tbody").find_all('tr'):
    col = row.find_all("td")
    date = col[0].text
    Open = col[1].text
    high = col[2].text
    low = col[3].text
    close = col[4].text
    adj_close = col[5].text
    volume = col[6].text
    
    # Finally we append the data of each row to the table
    netflix_data = netflix_data.append({"Date":date, "Open":Open, "High":high, "Low":low, "Close":close, "Adj Close":adj_close, "Volume":volume}, ignore_index=True)
```

We can now print out the dataframe

```py
print(netflix_data.head())
```

We can also use the pandas `read_html` function using the url

```py
read_html_pandas_data = pd.read_html(url)

print(read_html_pandas_data)
```

Or we can convert the BeautifulSoup object to a string

```py
read_html_pandas_data = pd.read_html(str(soup))

print(read_html_pandas_data)
```

Beacause there is only one table on the page, we just take the first table in the list returned

```py
netflix_data = read_html_pandas_data[0]

print(netflix_data.head())
```

## **Using Webscraping to Extract Stock Data for Amazon**
First, we use the `requests` library to download the [webpage](https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/amazon_data_webpage.html)

```py
url_ = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/amazon_data_webpage.html"

html_data = requests.get(url_).text
```

Parse the html data using beautiful_soup.

```py
soup_ = BeautifulSoup(html_data, 'html.parser')

print(soup_.prettify())
```

Let's be sure we are considering the correct stock's (i.e. Amazon) Historical Prices & Data 

```py
title = soup_.title.text

# print content of the title attribute
print(title)
```

Next, using beautiful soup, we extract the table with historical share prices and store it into a dataframe named `amazon_data`. 

The dataframe would have columns `Date`, `Open`, `High`, `Low`, `Close`, `Adj Close`, and `Volume`.

```py
# create a pandas for Amazon stock
amazon_data = pd.DataFrame(columns=['Date', 'Open', 'High', 'Low', 'Close', 'Adj close', 'Volumne'])

# parsing html
# first isolate the table body
# Then we loop through each row and find all the column values for each row
for row in soup_.find('tbody').find_all('tr'):
    col = row.find_all('td')
    date = col[0].text
    open = col[1].text
    high = col[2].text
    low = col[3].text
    close = col[4].text
    adj_close = col[5].text
    volume = col[6].text
    
    # Finally we append the data of each row to the table
    amazon_data = amazon_data.append({'Date':date, 'Open':open, 'High':high, 'Low':low, 'Close':close, 'Adj close':adj_close, 'Volumne':volume}, ignore_index=True)
```
```py
# Print out the first five rows of the amazon_data dataframe you created.
print(amazon_data.head())
```

## **Using Webscraping to Extract Stock Data for Meta - a shortcut method**
First, we get the url of [Meta's](https://about.facebook.com/) (META) Stock Historical Prices Data from [Yahoo Finance](https://query1.finance.yahoo.com/v7/finance/download/META?period1=1628004714&period2=1659540714&interval=1d&events=history&includeAdjustedClose=true). In this case, we are considering the historical data within the following `Time Period:` Aug 03, 2021 - Aug 03, 2022.

```py
url = "https://query1.finance.yahoo.com/v7/finance/download/META?period1=1628004714&period2=1659540714&interval=1d&events=history&includeAdjustedClose=true"
```

Then using pandas DataFrame method, we read the url, which is in `CSV` format

```py
meta_data = pd.read_csv(url)

# print the first five rows of the data
print(meta_data.head())
```

Let's see the current stock price of META as at `2022-08-03` for the entire day

print(meta_data[meta_data['Date'] == '2022-08-03'])

**Result:** The [Bull market](https://www.investopedia.com/insights/digging-deeper-bull-and-bear-markets/#:~:text=Bull%20Market%20vs.-,Bear%20Market,stocks%20are%20declining%20in%20value) triumphed over the [Bear market](https://www.investopedia.com/insights/digging-deeper-bull-and-bear-markets/#:~:text=Bull%20Market%20vs.-,Bear%20Market,stocks%20are%20declining%20in%20value), given that the `close price` an the end of the day was higher (very close to the high price) than the `opening price`. This indicates that thus far, Meta stock isn't undervalued.