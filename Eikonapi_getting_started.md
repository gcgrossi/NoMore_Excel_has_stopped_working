---
layout: default
title: Getting Started with Python Eikon
parent: 🎏 Getting Data
nav_order: 2
---

# The Eikon API

# Getting Started with Python Eikon

## Introduction

The first logical step of each application is always directly linked to the question: 'Where do I get my data?'. 
Financial Data is very heterogeneous and comes from different sources, may it be a financial statement of a company or the price of a particular security. It also has different formats, from CSV files to live streams of data.

Taking into consideration every source of data is prohibitive for a guide. I will therefore focus on some key methodologies and a couple of different data providers and I hope I will cover a good range of situations so that everybody can apply and adapt these concepts to the particular case at hand without big traumas.

The two most famous data providers on the market are Reuters and Bloomberg. I will not comment on their characteristics and which one has better quality service or better price. I imagine that each institution makes the decision based on different aspects: from a purely budgetary point of view to the availability of certain data figures or to the frequency of requests. Is up to you to decide which provider better fits your need and act accordingly.

I mainly use the Eikon terminal, which is the application for Reuter's data streaming. I also use Bloomberg for a certain type of data we need, but less often. This is the reason why I will start speaking about the Eikon terminal, its integration in Excel, and how to retrieve the same data using Python. 

## Two types of data

Before jumping on the
subject it is maybe useful to make a distinction between the main two types of data you can retrieve from these providers:

1. **Live data:** this type of data always refers to the latest data available for a particular instrument. There is a timestamp attached to the quote. It is used mainly for live monitoring of the financial market and live/Algo trading. 
2. **Historical data:** this type of data refers to all the records for a certain interval of time. There are different frequencies of sampling available, i.e. daily, hourly, 10 minutes, etc. It is used mainly for backtesting, model training, and the calculation of metrics/indicators.

## The Eikon Excel interface

Connection with Eikon in Excel is made via a dynamic (.dll) library that is installed on the system together with the desktop application. The integration is therefore very easy and the functionalities for Excel come shipped with the whole package. Very handy!

Once Excel is opened, the add-in buttons are accessible via the ribbon.

<img width="959" alt="Eikon_Ribbon" src="https://user-images.githubusercontent.com/14998207/152521060-efb42198-3a0f-4a87-a2ca-1e9d291242a7.png">

I'm not going to explain the functionalities of all the buttons but there are a couple of takeaways and features to underline:

1. Connection is made under the hood via the Connect button.
2. There is a button to start/stop the data stream.
3. There is a button to refresh all the formulas.

Historical data can be obtained by using a service called 'Datastream'. There is another panel on the Ribbon to access it.

<img width="959" alt="Datastream_Ribbon" src="https://user-images.githubusercontent.com/14998207/152521324-0c4f2b38-7dca-4fae-8f4f-ec14fba800b7.png">

Also in this case there are different buttons. The buttons on the left will open a GUI with different features. Some takeaways:

1. You can browse the GUI to find what you're looking for
2. You can build the requests via the interface.
3. You can manage your requests, change the symbols and time frame of the data you are pulling.

Once the request is constructed, Datastream will dump the time-series in a spreadsheet using a custom formula with codes.

If you know the codes and the formulas, you can directly type them in a blank cell and the result will be the same. Live data work in the same way: There is a code for every financial instrument called 'RIC', and the Live data can be accessed by typing a custom formula.

Here is an example of an Excel Formula for Live data:

<img width="236" alt="RtGet" src="https://user-images.githubusercontent.com/14998207/152521352-9f862e7c-6e06-4140-8463-9025035c811c.png">

while here is an example of the Datastream formula:

<img width="710" alt="DS_Formula" src="https://user-images.githubusercontent.com/14998207/152521383-27abf7dd-45ef-4c79-81eb-b8ee8df364cd.png">

These features help in building a nice framework to work with Excel. A lot of Excel applications in Finance are constructed on top of Eikon. There are some drawbacks in my opinion by the way:
1. both of the interfaces (for live and historical data) are counter-intuitive and hard to navigate. 
2. Formula for the datastream request is somewhat cryptic and hard to construct.
3. The instrument codes for live data and Datastream are different! 😡

Let's see how can we retrieve the same information using Python, instead of the Excel add-in. 

## Setting up an App key

There is a code you can tap in the Eikon bar search bar: 'APPKEY'

<img width="358" alt="appkey" src="https://user-images.githubusercontent.com/14998207/152521413-46d0e079-d967-4fe6-8615-b9cab0a7c26e.png">

And register a new app with a custom name

![register_app](https://user-images.githubusercontent.com/14998207/152521489-2e819c2f-592f-4f5d-8192-e8c8ac6fa269.png)

you will be given a long string of numbers and alphas that you will need after, connecting from Python. As you can see I have already done that for my development projects.

## Install the python library

Another easy step to accomplish. From a terminal run the command:
```
pip install eikon
```
The python package is now ready to use. We can move on to the fun part! &#127881; &#127881; &#127881;

# Retrieve Live Data for a specific Instrument

It's now time to get the actual data from the API. We start by trying to obtain the last price for the most famous of the market indexes, The S&P500. First, we need to import the eikon API and pass the string we generated in the previous section using the method ```set_app_key```. 


```python
import eikon
eikon.set_app_key('your api key here')
```

Secondly, we obtain the last price using the method ```get_data```. The method accepts two inputs:

1. a list of strings with the codes of the instruments we need to retrieve (S&P500, APPL, etc.).
2. the names of the fields we need (i.e. last, bid, ask, etc.).

it returns a 2-tuple with a pandas DataFrame and an error message (if any). 


```python
df,e = eikon.get_data(['.SPX','AAPL.O'],['CF_LAST','CF_BID'])
msg = f'the following error has been produced {e}' if e else 'No errors have been produced' 
print(msg)
```

    No errors have been produced
    

We print the error if any, otherwise we can have a look at the DataFrame.


```python
df
```



<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Instrument</th>
      <th>CF_LAST</th>
      <th>CF_BID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>.SPX</td>
      <td>4326.51</td>
      <td>4285.59</td>
    </tr>
    <tr>
      <th>1</th>
      <td>AAPL.O</td>
      <td>159.22</td>
      <td>166.3</td>
    </tr>
  </tbody>
</table>
</div>



Fantastic! &#128170;
This is the very first step to reaching your Python independence! Setting up your environment and getting your data is already a very big step forward. I am tempted to make a quote here:

> A small step for a developer, a big step for humanity.

Every step we take we get closer to freeing ourselves from the agony of Eikon Excel Add-In randomly disappearing from the toolbar, crashing, or taking minutes to load.

Next Episode we will see how to receive a stream of data using the same principle and a new method of the Python Eikon API. See you next time.