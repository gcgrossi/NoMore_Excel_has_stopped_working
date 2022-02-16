---
layout: default
title: Python Eikon. How to get a constant stream of Data
parent: 🎏 Getting Data
nav_order: 3
---

# The Eikon API

# How to get a constant stream of data

In the [previous episode](https://gcgrossi.github.io/NoMore_Excel_has_stopped_working/Eikonapi_getting_started/) we saw how to set up your environment and get started very quickly with Eikon API for python. In a few steps, we were able to generate an app key and retrieve the live quote for a couple of instruments.

We saw that to be able to get information from the API we must know the specific codes from Eikon. Unfortunately, this is one of the difficulties in programming your Python applications with Eikon, but once you experiment a little bit with the API you will quickly gain knowledge of the codes and start navigating very easily. As always happens in coding, when learning something new you should get your hands dirty first!

Most financial applications are aimed to monitor the current market data during a period. For example, a trading system would want to be able to compute some indicators, run prediction models and generate a trading signal in a 'live' manner. This means that there should be a calculation engine that:

- runs persistently in the background.
- fetches data.
- manipulates them.  
- generate a trading signal.
- act on the trading signal or send a notification.

This is something that can hardly be achieved in Excel. With Excel you can, for example, have a spreadsheet opened in the background that is constantly running, but the solution will end up being very inefficient, with a lot of errors and crashes. Moreover, a problem with parallelization and threading will arise. Meaning that running multiple, concurrent jobs will be almost impossible to realize. You can still try to find a workaround, by running a master job that handles opening different workbooks but I doubt you're going to be still in good mental health after building such a framework.

Truth is that Excel was not made for this purpose. Python is a way better tool to handle these kinds of jobs, and with the Eikon API, you can create a very powerful live data processing station. Let's see how to do that. 

## ```get_data```
We will use this method, which returns a ```pandas.DataFrame``` with fields in columns and instruments as row index. The input parameters are summarized in the table below:

### Structure

```get_data(instruments, fields, parameters=None, field_name=False, raw_output=False, debug=False)```

### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| instruments | string or list | Single instrument or list of instruments to request. |
|fields | string, dictionary or list of strings and/or dictionaries.|List of fields to request. Examples: ```'TR.PriceClose' {'TR.GrossProfit': { 'params':{ 'Scale': 6, 'Curn': 'EUR' }}{'TR.GrossProfit': { 'params':{ 'Scale': 6, 'Curn': 'EUR'},sort_dir:'desc'} ['TR.PriceClose','TR.PriceOpen'][{'TR.PriceClose':{'sort_dir':asc,sort_priority:1}},{'TR.PriceOpen':{'sort_dir':asc,sort_priority:0}}```. You can use the function ```TR_Field``` to build the fields: ``` >>> fields =[ek.TR_Field('tr.revenue'),ek.TR_Field('tr.open','asc',1),ek.TR_Field('TRGrossProfit',{'Scale': 6, 'Curn': 'EUR'},'asc',0)] >>> data, err = ek.get_data(["IBM","MSFT.O"],fields).```
 | parameters| string or dictionary, optional| Single global parameter key=value or dictionary of global parameters to request. The default is None.|
|field_name| boolean, optional|Define if column headers are filled with field name or display names. If ```True``` value, field names will be used as column headers. Otherwise, the full display name will be used. The default is ```False```.|
|raw_output| boolean, optional| By default, the output is a  ```pandas.DataFrame```. Set raw_output=True to get data in JSON format. The default is ```False```.|
|debug |bool| When set to ```True```, the JSON request and response are printed. The default value is ```False```.|

### Returns
**pandas.DataFrame**

Returns ```pandas.DataFrame``` with fields in columns and instruments as row index

**errors**

Returns a list of errors

### Raises
**Exception**

If http request fails or if server returns an error.

**ValueError**

If a parameter type or value is wrong.

## A first naive approach

We saw we can retrieve the quote for an instrument using the method ```get_data``` discussed previously. This will return the last quote in the memory cache of the system. Naively, if we repeat the call each x seconds we have a first way of fetching the information we need constantly.

This can be achieved very easily in Python with the code below. 


```python
import eikon
import time
from datetime import datetime

# connect to the eikon api
eikon.set_app_key('your app key here')

# start infinite loop get quote
# do something is there was no error
while True:
    # print current time
    print(f'{datetime.now().strftime("%d/%m/%Y %H:%M:%S")}: retrieving info')
    
    # get data from eikon
    df,e = eikon.get_data(['.SPX','AAPL.O'],['CF_LAST','CF_BID'])
    if e:
        print(f'the following error has been produced {e}')
        break
    else:
        # do what you want here 
        print(df)
    
    # wait ten seconds before querying again
    time.sleep(10) 
    break
    

```

    15/02/2022 17:33:43: retrieving info
      Instrument  CF_LAST  CF_BID
    0       .SPX  4463.69  4462.7
    1     AAPL.O   171.46  171.45
    

In the code above: 
- We first set up the connection to Eikon, as we saw in the previous episodes.
- We start an infinite loop using the ```while``` clause.
- We retrieve the information we need as usual.
- Using the module ```time``` we wait 10 seconds before repeating the process.

We can set a different value for the timeout to change the frequency as we wish. Here we put a ```break``` if the retrieval produces an error, but we can wrap any kind of logic that will stop our execution. This is the developer's choice!

One remark about this procedure: it is a static procedure. Meaning that there is a fixed dead-time between the start of the loop and the sleep interval. During this dead time, if the price of the instrument changes, we will not be able to catch the change. This is not ideal for all those applications that need quick in-time responses.

Ideally, we would like to receive an update each time new data arrives on the server and perform our calculations asynchronously (i.e. process the data in a different/parallel thread), before receiving new updates. Fortunately, this is achievable by using a built-in method of the Eikon API. Let's see how.

# A second, more efficient, approach

We will exploit a Class of the API called ```Streaming Prices```. The Class is similar to ```get_data```, it accepts the same inputs (a list of instrument codes and a list of fields to retrieve) but in addition, it supports event handling. This means that when a certain event happens (i.e.a new field is updated) the information contained in the event can be passed to a function and you can use it for your purposes. 

Let's see how this is translated in Python:



```python
# define a callback function to print simple info
def print_update(streaming_price, instrument_name, fields):
    print("Update received for {}: {}".format(instrument_name, fields))
    return

# define the streaming price object
# use the callback to print the information
streaming_prices = eikon.StreamingPrices(
    instruments = ['.SPX', 'AAPL.O'], 
    fields = ['DSPLY_NAME', 'BID', 'ASK'],
    on_update = lambda streaming_price, instrument_name, fields :  
        print_update(streaming_price, instrument_name, fields)
)

# open streming
# wait 1 second
# close streaming
streaming_prices.open()
time.sleep(1)
streaming_prices.close()
```

    Update received for AAPL.O: {'BID': 175.62, 'ASK': 175.64}
    




    <StreamState.Closed: 1>



    Update received for AAPL.O: {'BID': 175.64, 'ASK': 175.65}
    Update received for .SPX: {'BID': 4535.64, 'ASK': 4538.9}
    Update received for AAPL.O: {'BID': 175.63, 'ASK': 175.64}
    Update received for AAPL.O: {'BID': 175.63, 'ASK': 175.64}
    

In the code above:
- we define a function that will print the information contained in the streaming event.
- we define a streaming price with its inputs.
- we open the streaming, wait 1 second, and then close it.

As you can see the ```instruments``` and ```fields``` inputs are the same, but here we use the ```on_update``` event to define a callback to the ```print_update``` function, that will be called every time a new data arrives and will be passed the streaming information via the ```lambda``` function. If you want to know more about the functioning of ```lambda``` you can read this small tutorial [article](https://www.w3schools.com/python/python_lambda.asp). 

## 4 Types of event handle

In total there are four types of events that can trigger your code. 

- ```on_update```: Update events are received when fields of a requested instrument change. Only the fields that changed are displayed.
- ```on_refresh```: Refresh events happen when all fields of one of the requested instruments are received.
- ```on_status```: Status events are received when the status of one of the requested instruments changes.
- ```on_complete```: A Complete event is received once all the requested instruments received either a Refresh or a Status event.

You can add any of those events as an additional argument to the code we already wrote. In example an update and refresh event together will look like this


```python
# define a callback function to print simple info
def print_update(streaming_price, instrument_name, fields):
    print("Update received for {}: {}".format(instrument_name, fields))
    return

def print_refresh(streaming_price, instrument_name, fields):
    print("Refresh received for {}: {}".format(instrument_name, fields))
    return

# define the streaming price object
# use the callback to print the information
streaming_prices = eikon.StreamingPrices(
    instruments = ['.SPX', 'AAPL.O'], 
    fields = ['DSPLY_NAME', 'BID', 'ASK'],
    on_update = lambda streaming_price, instrument_name, fields :  
        print_update(streaming_price, instrument_name, fields),
    on_refresh = lambda streaming_price, instrument_name, fields :  
        print_refresh(streaming_price, instrument_name, fields)
)

# open streming
# wait 1 second
# close streaming
streaming_prices.open()
time.sleep(1)
streaming_prices.close()
```

    Refresh received for AAPL.O: {'DSPLY_NAME': 'APPLE INC', 'BID': 171.53, 'ASK': 171.54}
    Refresh received for .SPX: {'DSPLY_NAME': 'S&P 500 INDEX', 'BID': 4463.79, 'ASK': 4465.77}
    




    <StreamState.Closed: 1>



    Update received for .SPX: {'BID': 4463.8, 'ASK': 4465.78}
    Update received for AAPL.O: {'BID': 171.53, 'ASK': 171.54}
    Update received for AAPL.O: {'BID': 171.53, 'ASK': 171.54}
    Update received for AAPL.O: {'BID': 171.53, 'ASK': 171.54}
    Update received for AAPL.O: {'BID': 171.53, 'ASK': 171.54}
    

In the end, we can achieve what we wanted in the beginning, a constant stream of data that is sent to us from the server, as we like: a nice waiter that serves data ready to be consumed, while we wait at the table and brew some coffee! ☕
