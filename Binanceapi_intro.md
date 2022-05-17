---
layout: default
title: The Binance API - Getting Started
parent: 🎏 Getting Data
nav_order: 1
---

# The Binance API

# Getting Started

## Introduction

Crypto is the new thing. Well, not new since it’s been sometime now that is more than this. Crypto is a reality, established and accepted by many. The more the time passes, the more cryptocurrencies are present in the portfolios of several investors, from retail to institutional. Many banks and other financial institutions have opened new crypto services, providing solutions to hold and exchange these assets. The first ETFs are popping out, and regulators are faced with the reality of incorporating cryptocurrencies into the present financial rules.

In summary, I think it’s nice to dedicate a part of this guide to explaining how to get data for cryptocurrencies since most of the time the APIs are free and the Defi Exchanges providing data are well designed and extremely easy to use. 

I hold some crypto too, via the most famous and big exchange in the world, Binance. I have an account that you can set up for free. I also ‘discovered’ that Binance has a super well-documented API that, with relatively simple HTTP requests, lets you interact with the exchange in many ways: you can simply consult the market data or get historical market prices. You can also interact with your wallet, consult the balance, and place market orders.

In other words, you can do all the things you do with your smartphone application but with the power of coding your actions and with all the benefits that come with it: automation, control, and calculation power.

So here I am, trying to explain a bit how to connect this service with our favorite programming language, Python! 🐍

## Basic Concepts

The full documentation can be found [here](https://binance-docs.github.io/apidocs/spot/en/) and I will try to distill the main concepts for you, as well as guide you to its usage in Python.

You will need my help because here comes the first thing we have to notice:

**There is no official python code for the API** 😱

No simple ```.get_quote()``` method to obtain your data in pandas DataFrames! This is not so tragic in the end, on the contrary! It’s a fantastic occasion to practice basic python libraries, which are the everyday food of the developer.

The API must be interrogated with an HTTP request, and the response is always in JSON format, so you have the chance to practice with the requests and JSON library, as well as manipulating python dictionaries. There are a couple of things before performing our very first (and super-easy) request:

- the basic URL to perform the requests is: https://api.binance.com, but there are also other fallback URLs if there are performance issues:
    1. https://api1.binance.com
    2. https://api2.binance.com
    3. https://api3.binance.com
- All endpoints return either a JSON object or array.
- Data is returned in ascending order. Oldest first, newest last.
- All-time and timestamp-related fields are in milliseconds.

### Type of requests

There are 3 different security levels, requiring authentication depending on the type of endpoint we are querying. 

1. **Free Endpoints**: marked as Security Type ```NONE``` they do not require any kind of authentication.
2. **API key Endpoints**: marked as Security type ```USER_STREAM``` or ```MARKET_DATA``` they require the creation of an API key from your Binance account. So yes, you need to open a Binance account to retrieve the data!
3. **API key + Signed Endpoints**: marked as Security type ```TRADE``` or ```MARGIN``` or ```USER_DATA``` they require both an API key and a cryptographic signature to be sent with the request. As you can see, they allow to consult personal information and doing actual trading so they need the highest possible security. 

All of this said we can start playing a little bit with the API using the free endpoints before moving to more advanced features, just to have a feeling of how to work with this API.

## Ping the Server

we can ping the server to see if it's up or to test the connectivity speed.


```python
import requests
from datetime import datetime
import time

# let's see how much time we spend $
# to get data from server
t1=time.time()

# ping the api
# response is an empy dictionary
response = requests.get("https://api.binance.com/api/v3/ping")

# let's print the time 
# difference together with the response
tdelta=time.time()-t1
print(f'this is the response {response.json()} in {tdelta} seconds')
```

    this is the response {} in 0.29949188232421875 seconds
    

As you can see the whole snippet run in 0.29 seconds. The ```ping``` endpoint returns an empty dictionary.

## Get the server time

In a very similar fashion, using the ```time``` endpoint we can obtain the time of the server. 


```python
# get the time of the server
response = requests.get("https://api.binance.com/api/v3/time")
print(response.json())

# server time is in milliseconds, fromtimestamps expects
# seconds: divide it by 1000 to get seconds.
server_timestamp = response.json().get('serverTime')
server_time = datetime.fromtimestamp(int(server_timestamp/1000))
print(f"The server time is: {server_time}")
```

    {'serverTime': 1651239589198}
    The server time is: 2022-04-29 15:39:49
    

Here we have a non-empty response with key 'serverTime'. We need a bit of manipulation to convert the time from milliseconds -> seconds and from timestamp -> datetime, but everything is really straightforward. 

## Requests that require one or more symbols

There are some kind of requests that requires the specification of one or more symbols, for example, the couple "ETHBTC" if you want to know the information regarding the price of ETH expressed in BTC. In this case, the request must be formulated by adding the symbol/symbols option in the request body. With the request library, you can do this in two ways. Let's see an example with the 24h price change:

### 24h price change with only one symbol

you can pass the symbol directly in the request string adding ?symbol=<symbol_name> string at the end, or you can pass to requests a parameter dictionary with all the options you want to specify and requests will construct the string for you. In the example below I retrieve the information for the couple BNBUSDT and print the information obtained.


```python
import json

# get ticker of a particular symbol
# you can do either this
response = requests.get("https://api.binance.com/api/v3/ticker/24hr?symbol=BNBUSDT")
# or this (to avoid long strings)
params = dict(symbol="BNBUSDT")
response = requests.get("https://api.binance.com/api/v3/ticker/24hr",params=params)

print(json.dumps(response.json(), indent=2))

```

    {
      "symbol": "BNBUSDT",
      "priceChange": "-4.50000000",
      "priceChangePercent": "-1.118",
      "weightedAvgPrice": "402.88287732",
      "prevClosePrice": "402.40000000",
      "lastPrice": "397.90000000",
      "lastQty": "0.02500000",
      "bidPrice": "397.80000000",
      "bidQty": "85.29500000",
      "askPrice": "397.90000000",
      "askQty": "361.25800000",
      "openPrice": "402.40000000",
      "highPrice": "410.70000000",
      "lowPrice": "393.20000000",
      "volume": "841410.78900000",
      "quoteVolume": "338989999.68090000",
      "openTime": 1651153189790,
      "closeTime": 1651239589790,
      "firstId": 541760795,
      "lastId": 542155887,
      "count": 395093
    }
    

### 24h price change with more than one symbol

Similarly you can retrieve the price change for more the one symbol. You only have to pass a list of symbols to the request. The example below will clarify the point.


```python
# get the info of a list of symbols
response = requests.get('https://api.binance.com/api/v3/ticker/24hr?symbols=["BNBBTC","BTCUSDT"]')

# or this (to avoid long strings)
params = dict(symbols='["BNBBTC","BTCUSDT"]')
response = requests.get("https://api.binance.com/api/v3/ticker/24hr",params=params)

print(json.dumps(response.json(), indent=2))

```

    [
      {
        "symbol": "BNBBTC",
        "priceChange": "-0.00005000",
        "priceChangePercent": "-0.489",
        "weightedAvgPrice": "0.01019562",
        "prevClosePrice": "0.01021600",
        "lastPrice": "0.01016600",
        "lastQty": "0.06800000",
        "bidPrice": "0.01016600",
        "bidQty": "41.93000000",
        "askPrice": "0.01016700",
        "askQty": "5.80200000",
        "openPrice": "0.01021600",
        "highPrice": "0.01029300",
        "lowPrice": "0.01008000",
        "volume": "108508.44100000",
        "quoteVolume": "1106.31076373",
        "openTime": 1651153190018,
        "closeTime": 1651239590018,
        "firstId": 183934968,
        "lastId": 184010070,
        "count": 75103
      },
      {
        "symbol": "BTCUSDT",
        "priceChange": "-259.96000000",
        "priceChangePercent": "-0.660",
        "weightedAvgPrice": "39536.34690122",
        "prevClosePrice": "39388.97000000",
        "lastPrice": "39129.01000000",
        "lastQty": "0.47204000",
        "bidPrice": "39129.01000000",
        "bidQty": "5.23357000",
        "askPrice": "39129.02000000",
        "askQty": "1.72000000",
        "openPrice": "39388.97000000",
        "highPrice": "40372.63000000",
        "lowPrice": "38616.46000000",
        "volume": "59080.46958000",
        "quoteVolume": "2335825940.40213200",
        "openTime": 1651153190435,
        "closeTime": 1651239590435,
        "firstId": 1339594404,
        "lastId": 1340872364,
        "count": 1277961
      }
    ]
    

There is a lot of output but, as you can see, you retrieve a list of dictionaries, one dictionary for each symbol you specified.

## Conclusion

I like this API. Easy to use, minimalist, rich in information. I still have to practice a bit with it to form a well defined idea but for the moment I passed good times trying to retrieve data from this crypto exchange. 

Let's see if we can do more in the next episodes. But for the moment stay anchored to this block of posts! Cheers.
