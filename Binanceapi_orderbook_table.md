<img src="images/Cover_BinanceAPI_OrderBook.png">

# The Binance API

# Order book data

## Introduction

Hello, data miners! ⛏

The second episode of this chapter regarding the Binance API will explore just one, always useful, piece of information you can retrieve: the Order Book. If you read these articles, you are probably very familiar with the concept of an Order Book so I will not bother you with all the usual boiler-plate introduction on what is an Order Book? How Orders are matched in Finance? and bla and bla and bla.

I do not have to rank on Google so I will keep my posts as I think it's better: concise. Very few concepts per article, some small examples, and a max of 5 minutes read.

By the way, my [the last article](https://gcgrossi.github.io/NoMore_Excel_has_stopped_working/Binanceapi_intro/) on the Binance API came just before the collapse of the Terra ecosystem, with the de-peg of its stable coin UST and the annihilation of its token LUNA, which value went from 120$ to zero in record time! 

Since then, a recovery plan has been rolled out, and a new version of the coin (LUNA-2) has been released and it's trading at almost 6$ by the time I'm writing. Since the near future of this token will probably be very volatile, I think a nice, and funny aspect to cover is live monitoring.

Live monitoring comes into play in any aspect of data and is of crucial importance in many fields where the live performance of any kind of system should be evaluated on the fly, to make snap decisions (I think about Formula1 telemetry here). When I was working at CERN as a physicist of the Large Hadron Collider, I designed a (small) part of the live dashboarding system, that was able to stream the live performance of the particle detectors in the experiment, while the machine was running and taking data.

In Finance almost everything is live and stock data can be obtained with the frequency of less than minutes so it makes a perfect use case for practicing with live dashboarding. This is what we're going to attack today! 

In the next episodes, we will build a simple dashboard to stream live data from cryptocurrencies. We will start by designing the very first one: a table with the order book that is constantly updated. What stack we'll be using? You may think about something very complicated but listen to this:

**WE WILL ONLY USE PYTHON** 🐍

We will leverage the power of Pandas and Jupiter Notebook widgets, so let's just create a blank Jupiter Notebook and start drawing on it like an artist with an empty canvas! FYI: this article is completely written with Jupyter Notebook!

# Retrieveing the first data

retrieving the order book for the couple LUNA/BUSD is done via the endpoint 'depth'. In the previous article, we learnt how to perform the request to get the data, so the next cell should be very familiar to you.


```python
import json
import requests

r = requests.get("https://api.binance.com/api/v3/depth",params=dict(symbol="LUNABUSD"))
response = r.json()
response
```




    {'lastUpdateId': 1514402319,
     'bids': [['6.62410000', '3.18000000'],
      ['6.62380000', '196.56000000'],
      ['6.62370000', '3.08000000'],
      ['6.62260000', '18.48000000'],
      ['6.62170000', '1.52000000'],
      ['6.62020000', '64.16000000'],
      ['6.62010000', '60.81000000'],
      ['6.62000000', '716.02000000'],
      ['6.61980000', '2.18000000'],
      ['6.61970000', '2.81000000'],
      ['6.61960000', '27.08000000'],
      ['6.61880000', '439.93000000'],
      ['6.61810000', '33.00000000'],
      ['6.61800000', '8.00000000'],
      ['6.61760000', '247.00000000'],
      ['6.61740000', '389.37000000'],
      ['6.61730000', '3.62000000'],
      ['6.61510000', '4.38000000'],
      ['6.61500000', '8.86000000'],
      ['6.61490000', '165.47000000'],
      ['6.61420000', '1.59000000'],
      ['6.61410000', '75.46000000'],
      ['6.61400000', '5.00000000'],
      ['6.61310000', '2.17000000'],
      ['6.61250000', '7.27000000'],
      ['6.61180000', '382.89000000'],
      ['6.61150000', '319.44000000'],
      ['6.61050000', '388.94000000'],
      ['6.61010000', '490.91000000'],
      ['6.61000000', '228.99000000'],
      ['6.60980000', '302.13000000'],
      ['6.60950000', '676.12000000'],
      ['6.60940000', '56.15000000'],
      ['6.60900000', '2.92000000'],
      ['6.60760000', '2.80000000'],
      ['6.60730000', '4.00000000'],
      ['6.60690000', '3.09000000'],
      ['6.60620000', '1.72000000'],
      ['6.60600000', '295.53000000'],
      ['6.60580000', '3.11000000'],
      ['6.60500000', '98.72000000'],
      ['6.60480000', '148.14000000'],
      ['6.60470000', '18.75000000'],
      ['6.60430000', '612.33000000'],
      ['6.60380000', '57.92000000'],
      ['6.60320000', '750.00000000'],
      ['6.60130000', '3.09000000'],
      ['6.60000000', '896.84000000'],
      ['6.59920000', '25.56000000'],
      ['6.59800000', '5.00000000'],
      ['6.59650000', '505.24000000'],
      ['6.59600000', '187.62000000'],
      ['6.59570000', '3.09000000'],
      ['6.59510000', '601.86000000'],
      ['6.59470000', '4.06000000'],
      ['6.59270000', '181.35000000'],
      ['6.59050000', '301.89000000'],
      ['6.59040000', '233.98000000'],
      ['6.59020000', '3.09000000'],
      ['6.59010000', '162.56000000'],
      ['6.59000000', '338.83000000'],
      ['6.58920000', '35.76000000'],
      ['6.58900000', '6.64000000'],
      ['6.58880000', '30.35000000'],
      ['6.58870000', '45.53000000'],
      ['6.58830000', '4.02000000'],
      ['6.58800000', '4.02000000'],
      ['6.58750000', '19.00000000'],
      ['6.58720000', '472.18000000'],
      ['6.58500000', '64.59000000'],
      ['6.58460000', '3.10000000'],
      ['6.58420000', '2.17000000'],
      ['6.58370000', '13.70000000'],
      ['6.58330000', '30.07000000'],
      ['6.58260000', '3.64000000'],
      ['6.58200000', '5.00000000'],
      ['6.58170000', '34.21000000'],
      ['6.58070000', '104.35000000'],
      ['6.58060000', '37.61000000'],
      ['6.58020000', '8.37000000'],
      ['6.58000000', '325.51000000'],
      ['6.57960000', '759.92000000'],
      ['6.57920000', '7.44000000'],
      ['6.57900000', '3.10000000'],
      ['6.57880000', '25.64000000'],
      ['6.57800000', '6.55000000'],
      ['6.57740000', '196.13000000'],
      ['6.57730000', '76.01000000'],
      ['6.57640000', '2071.32000000'],
      ['6.57630000', '4.92000000'],
      ['6.57500000', '81.37000000'],
      ['6.57490000', '32.09000000'],
      ['6.57420000', '1.65000000'],
      ['6.57370000', '3.13000000'],
      ['6.57350000', '3.10000000'],
      ['6.57340000', '143.04000000'],
      ['6.57290000', '15.06000000'],
      ['6.57180000', '19.78000000'],
      ['6.57170000', '7.50000000'],
      ['6.57120000', '30.43000000']],
     'asks': [['6.62900000', '21.10000000'],
      ['6.62960000', '13.47000000'],
      ['6.63010000', '15.07000000'],
      ['6.63070000', '3.02000000'],
      ['6.63180000', '3.02000000'],
      ['6.63230000', '3.02000000'],
      ['6.63390000', '3.02000000'],
      ['6.63550000', '51.02000000'],
      ['6.63670000', '35.25000000'],
      ['6.63790000', '510.78000000'],
      ['6.63800000', '29.92000000'],
      ['6.64050000', '722.95000000'],
      ['6.64090000', '181.52000000'],
      ['6.64100000', '453.76000000'],
      ['6.64110000', '30.11000000'],
      ['6.64120000', '17.32000000'],
      ['6.64150000', '100.00000000'],
      ['6.64160000', '226.21000000'],
      ['6.64200000', '393.33000000'],
      ['6.64210000', '382.89000000'],
      ['6.64370000', '56.64000000'],
      ['6.64470000', '29.92000000'],
      ['6.64500000', '6.99000000'],
      ['6.64580000', '77.55000000'],
      ['6.64600000', '5.00000000'],
      ['6.64610000', '710.36000000'],
      ['6.64630000', '123.02000000'],
      ['6.64770000', '6.19000000'],
      ['6.64860000', '126.20000000'],
      ['6.65000000', '123.20000000'],
      ['6.65110000', '35.76000000'],
      ['6.65210000', '357.12000000'],
      ['6.65280000', '750.00000000'],
      ['6.65370000', '3.07000000'],
      ['6.65400000', '81.95000000'],
      ['6.65710000', '50.00000000'],
      ['6.65820000', '302.13000000'],
      ['6.65920000', '2.15000000'],
      ['6.65930000', '3.07000000'],
      ['6.66000000', '134.73000000'],
      ['6.66060000', '4.19000000'],
      ['6.66100000', '11.87000000'],
      ['6.66200000', '5.00000000'],
      ['6.66340000', '22.20000000'],
      ['6.66360000', '82.89000000'],
      ['6.66500000', '5.29000000'],
      ['6.66660000', '7.82000000'],
      ['6.66700000', '2.16000000'],
      ['6.66880000', '33.49000000'],
      ['6.66900000', '3.17000000'],
      ['6.66920000', '2.89000000'],
      ['6.67000000', '19.48000000'],
      ['6.67060000', '3.07000000'],
      ['6.67190000', '145.14000000'],
      ['6.67220000', '12.06000000'],
      ['6.67250000', '3.08000000'],
      ['6.67340000', '78.95000000'],
      ['6.67360000', '73.16000000'],
      ['6.67390000', '29.43000000'],
      ['6.67420000', '46.08000000'],
      ['6.67480000', '3.03000000'],
      ['6.67520000', '16.85000000'],
      ['6.67600000', '1.67000000'],
      ['6.67620000', '3.06000000'],
      ['6.67700000', '2.00000000'],
      ['6.67800000', '5.00000000'],
      ['6.67860000', '7.21000000'],
      ['6.67880000', '3.98000000'],
      ['6.68000000', '407.63000000'],
      ['6.68020000', '3.79000000'],
      ['6.68060000', '7.50000000'],
      ['6.68180000', '2.55000000'],
      ['6.68190000', '3.06000000'],
      ['6.68260000', '13.08000000'],
      ['6.68400000', '499.22000000'],
      ['6.68430000', '2.17000000'],
      ['6.68450000', '3.10000000'],
      ['6.68550000', '3.16000000'],
      ['6.68650000', '3.00000000'],
      ['6.68690000', '3.59000000'],
      ['6.68750000', '54.32000000'],
      ['6.68890000', '1.86000000'],
      ['6.68900000', '59.88000000'],
      ['6.68970000', '1.60000000'],
      ['6.69000000', '92.88000000'],
      ['6.69160000', '3.08000000'],
      ['6.69250000', '3.09000000'],
      ['6.69270000', '19.53000000'],
      ['6.69280000', '1.59000000'],
      ['6.69310000', '3.06000000'],
      ['6.69340000', '78.96000000'],
      ['6.69350000', '6.75000000'],
      ['6.69360000', '4.11000000'],
      ['6.69390000', '1.53000000'],
      ['6.69400000', '5.00000000'],
      ['6.69450000', '29.49000000'],
      ['6.69650000', '39.05000000'],
      ['6.69800000', '1.70000000'],
      ['6.69830000', '3.97000000'],
      ['6.69890000', '3.05000000']]}



The response is always a dictionary. In this case, the "bids/asks" key is a list of lists with the holding price, and quantity values.

Let's just construct the bid and ask DataFrames by reading the "bid"/"ask" key of the response. Using the pandas method ```from_records``` the corresponding list can be transformed into a DataFrame with a snap! 

As the name suggests, with the method ```concat``` we can concatenate the two DataFrames.


```python
import pandas as pd

# define the bid/ask datframes by reading the bid/ask keys of the response
# and the pandas constructor method from_records. Add also a column "side".
df_bid = pd.DataFrame.from_records(response['bids'], columns=['price', 'quantity']).astype('float')
df_bid['side'] = 'bid'

df_ask = pd.DataFrame.from_records(response['asks'], columns=['price', 'quantity']).astype('float')
df_ask['side'] = 'ask'

# concatenate the dataframes
# for the bid we first sort the dataframe in ascending order
# then we select the last 5 rows of bid and the first five of ask
df = pd.concat([df_bid.sort_values(by=['price'])[-5:], df_ask[:5]], ignore_index=True)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>price</th>
      <th>quantity</th>
      <th>side</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6.6217</td>
      <td>1.52</td>
      <td>bid</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6.6226</td>
      <td>18.48</td>
      <td>bid</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6.6237</td>
      <td>3.08</td>
      <td>bid</td>
    </tr>
    <tr>
      <th>3</th>
      <td>6.6238</td>
      <td>196.56</td>
      <td>bid</td>
    </tr>
    <tr>
      <th>4</th>
      <td>6.6241</td>
      <td>3.18</td>
      <td>bid</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6.6290</td>
      <td>21.10</td>
      <td>ask</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6.6296</td>
      <td>13.47</td>
      <td>ask</td>
    </tr>
    <tr>
      <th>7</th>
      <td>6.6301</td>
      <td>15.07</td>
      <td>ask</td>
    </tr>
    <tr>
      <th>8</th>
      <td>6.6307</td>
      <td>3.02</td>
      <td>ask</td>
    </tr>
    <tr>
      <th>9</th>
      <td>6.6318</td>
      <td>3.02</td>
      <td>ask</td>
    </tr>
  </tbody>
</table>
</div>



As you can see we limited the number of entries to 5 for each table, and we sorted the bid prices in ascending order to have the effect of bid-ask matching that you often see in the order book representations.

## Representing the table in ipython widgets

We will now start working with [ipython widgets](https://ipywidgets.readthedocs.io/en/latest/examples/Widget%20List.html) library. The library has a widget that can hold HTML values, and that is really what we are looking for in this project. Why?

Not a lot of people know that pandas DataFrames have an API (meaning an entire set of functions) to style the DataFrame and export it to HTML.
There is a nice step-by-step guide on the [official website](https://pandas.pydata.org/docs/user_guide/style.html).
As a first step let's see how a DataFrame is rendered in HTML by simply creating an HTML widget and dumping the DataFrame with the ```to_html()``` method.


```python
import ipywidgets as widgets

# create a jupyter widget with the table html
widgets.HTML(value=df.to_html())
```


    HTML(value='<table border="1" class="dataframe">\n  <thead>\n    <tr style="text-align: right;">\n      <th></…


Well, it's rendered like ... a DataFrame. We can play a little bit more with the API and eliminate the index, alongside selected columns with the methods ```hide_index()``` and ```hide_columns()```. The methods return a 'style' object (basically an HTML table with some CSS) that can be rendered in Jupyter Notebooks or other engines.


```python
# hiding the index and the column 'side'
# st is a 'style' object. 
st=df.style.hide_index()
st = st.hide_columns(['side'])
widgets.HTML(value=st.to_html())
```


    HTML(value='<style type="text/css">\n</style>\n<table id="T_3a8af_">\n  <thead>\n    <tr>\n      <th class="co…


## Draw the table in black green and red colors

Now it's time to make some cool styling. In the following cell, we are going to add colors to the table. Since the styler object is an HTML table with CSS for styling, we need to modify the CSS of the table. For those who are not familiar with CSS, I will just tell you that is a language that can select HTML elements based on some rules, and add style attributes to them. In example, I may select all the ```<p>``` tags in an HTML file and apply the style ```color:rgb(0,0,0)```. 

But how can we do that in a Pandas DataFrame? The styler object can add CSS! Using the ```set_table_styles``` method. You will need to pass a list of CSS selectors and style properties to the method to apply the desired changes. But let's see how this is translated into code:


```python
# here we create a mask for selecting bid and ask
# portions of the dataframe. We will color 
# the two parts in different ways
df_mask = df.copy(deep=True)
df_mask[df['side'] == 'bid'] = 'bid'
df_mask[df['side'] == 'ask'] = 'ask'

# here we apply the style
# pass a list of dictionaries with keys 
# 'css selector' and 'properties'
s = df.style.set_table_styles([
    {'selector': 'th:not(.index_name)','props':'text-align:center; color:grey;background-color:#373737'},  # select all the headers 
    {'selector': 'td','props':'text-align:center; background-color:#2d2d2d;font-weight:bold'},  # select all table cells
    {'selector':'.bid','props':'color:#086623'}, # select all elements with class name 'bid'
    {'selector':'.ask','props':'color:red'}, # select all elements with class 'ask'
],overwrite=False)

# we set the classes of the cells to
# 'bid' or 'ask' using the mask we created
s.set_td_classes(df_mask)
```




<style type="text/css">
#T_8809c_ th:not(.index_name) {
  text-align: center;
  color: grey;
  background-color: #373737;
}
#T_8809c_ td {
  text-align: center;
  background-color: #2d2d2d;
  font-weight: bold;
}
#T_8809c_ .bid {
  color: #086623;
}
#T_8809c_ .ask {
  color: red;
}
</style>
<table id="T_8809c_">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >price</th>
      <th class="col_heading level0 col1" >quantity</th>
      <th class="col_heading level0 col2" >side</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_8809c_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_8809c_row0_col0" class="data row0 col0 bid" >29945.010000</td>
      <td id="T_8809c_row0_col1" class="data row0 col1 bid" >0.166740</td>
      <td id="T_8809c_row0_col2" class="data row0 col2 bid" >bid</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_8809c_row1_col0" class="data row1 col0 bid" >29945.300000</td>
      <td id="T_8809c_row1_col1" class="data row1 col1 bid" >0.015490</td>
      <td id="T_8809c_row1_col2" class="data row1 col2 bid" >bid</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_8809c_row2_col0" class="data row2 col0 bid" >29946.610000</td>
      <td id="T_8809c_row2_col1" class="data row2 col1 bid" >0.000400</td>
      <td id="T_8809c_row2_col2" class="data row2 col2 bid" >bid</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_8809c_row3_col0" class="data row3 col0 bid" >29946.620000</td>
      <td id="T_8809c_row3_col1" class="data row3 col1 bid" >0.030000</td>
      <td id="T_8809c_row3_col2" class="data row3 col2 bid" >bid</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row4" class="row_heading level0 row4" >4</th>
      <td id="T_8809c_row4_col0" class="data row4 col0 bid" >29947.540000</td>
      <td id="T_8809c_row4_col1" class="data row4 col1 bid" >0.026630</td>
      <td id="T_8809c_row4_col2" class="data row4 col2 bid" >bid</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row5" class="row_heading level0 row5" >5</th>
      <td id="T_8809c_row5_col0" class="data row5 col0 ask" >29950.820000</td>
      <td id="T_8809c_row5_col1" class="data row5 col1 ask" >0.675350</td>
      <td id="T_8809c_row5_col2" class="data row5 col2 ask" >ask</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row6" class="row_heading level0 row6" >6</th>
      <td id="T_8809c_row6_col0" class="data row6 col0 ask" >29952.600000</td>
      <td id="T_8809c_row6_col1" class="data row6 col1 ask" >0.050000</td>
      <td id="T_8809c_row6_col2" class="data row6 col2 ask" >ask</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row7" class="row_heading level0 row7" >7</th>
      <td id="T_8809c_row7_col0" class="data row7 col0 ask" >29953.180000</td>
      <td id="T_8809c_row7_col1" class="data row7 col1 ask" >0.050000</td>
      <td id="T_8809c_row7_col2" class="data row7 col2 ask" >ask</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row8" class="row_heading level0 row8" >8</th>
      <td id="T_8809c_row8_col0" class="data row8 col0 ask" >29953.940000</td>
      <td id="T_8809c_row8_col1" class="data row8 col1 ask" >0.166230</td>
      <td id="T_8809c_row8_col2" class="data row8 col2 ask" >ask</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row9" class="row_heading level0 row9" >9</th>
      <td id="T_8809c_row9_col0" class="data row9 col0 ask" >29954.610000</td>
      <td id="T_8809c_row9_col1" class="data row9 col1 ask" >0.039820</td>
      <td id="T_8809c_row9_col2" class="data row9 col2 ask" >ask</td>
    </tr>
  </tbody>
</table>




In the previous cell, we created a mask DataFrame with strings 'bid' or 'ask' depending on if the cell should be colored with green (bid) or red (ask). We applied the desired style to the DataFrame. We first selected the headers (```th:not(.index_name)```) and applied some light grey and text centering. After we select all the cells (```td```) and apply a dark grey background. After we select all the cells with class names 'bid' and 'ask' (```.bid, .ask```) and color the font in green and red.

In the end, the ```set_td_classes``` sets the class of cells in the styler equal to the strings in the mask we previously created (we need it to select when in the CSS selector).

The result is pretty stylish! 😎

We can push the limit and add a final touch, by adding bars in the volume columns. Let's do that!

## We add the bars

We now add bars in the 'quantity' column, using the ```.bar``` method of the styler object. This will draw a bar in each cell, with height proportional to the value of the cell. We need to pass to the method the portion of the DataFrame we want to add bars. 

To do that we will use the pandas ```IndexSlice```, the object that is responsible for the .loc magic in pandas. Let's jump to the next cell to see the code:


```python
# we initialize a pandas IndexSlice
idx = pd.IndexSlice

# we retrieve the indexes of the 'bid' portion of the
# DataFrame as a list
idxs = df.index[df['side'] == 'bid'].tolist()

# Here we use the .bar method. We provide the method
# with the subset of the Datframe to apply bars in the same
# way we would do in .loc, but using the IndexSlice.
# we also provide the color of the bar as 2nd argument
s.bar(subset=idx[:idxs[-1],'quantity'], color='#dbeac5')
s.bar(subset=idx[idxs[-1]+1:,'quantity'], color='#ff9090')
```




<style type="text/css">
#T_8809c_ th:not(.index_name) {
  text-align: center;
  color: grey;
  background-color: #373737;
}
#T_8809c_ td {
  text-align: center;
  background-color: #2d2d2d;
  font-weight: bold;
}
#T_8809c_ .bid {
  color: #086623;
}
#T_8809c_ .ask {
  color: red;
}
#T_8809c_row0_col1 {
  width: 10em;
  height: 80%;
  background: linear-gradient(90deg,#dbeac5 100.0%, transparent 100.0%);
}
#T_8809c_row1_col1 {
  width: 10em;
  height: 80%;
  background: linear-gradient(90deg,#dbeac5 9.1%, transparent 9.1%);
}
#T_8809c_row2_col1, #T_8809c_row9_col1 {
  width: 10em;
  height: 80%;
}
#T_8809c_row3_col1 {
  width: 10em;
  height: 80%;
  background: linear-gradient(90deg,#dbeac5 17.8%, transparent 17.8%);
}
#T_8809c_row4_col1 {
  width: 10em;
  height: 80%;
  background: linear-gradient(90deg,#dbeac5 15.8%, transparent 15.8%);
}
#T_8809c_row5_col1 {
  width: 10em;
  height: 80%;
  background: linear-gradient(90deg,#ff9090 100.0%, transparent 100.0%);
}
#T_8809c_row6_col1, #T_8809c_row7_col1 {
  width: 10em;
  height: 80%;
  background: linear-gradient(90deg,#ff9090 1.6%, transparent 1.6%);
}
#T_8809c_row8_col1 {
  width: 10em;
  height: 80%;
  background: linear-gradient(90deg,#ff9090 19.9%, transparent 19.9%);
}
</style>
<table id="T_8809c_">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th class="col_heading level0 col0" >price</th>
      <th class="col_heading level0 col1" >quantity</th>
      <th class="col_heading level0 col2" >side</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_8809c_level0_row0" class="row_heading level0 row0" >0</th>
      <td id="T_8809c_row0_col0" class="data row0 col0 bid" >29945.010000</td>
      <td id="T_8809c_row0_col1" class="data row0 col1 bid" >0.166740</td>
      <td id="T_8809c_row0_col2" class="data row0 col2 bid" >bid</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row1" class="row_heading level0 row1" >1</th>
      <td id="T_8809c_row1_col0" class="data row1 col0 bid" >29945.300000</td>
      <td id="T_8809c_row1_col1" class="data row1 col1 bid" >0.015490</td>
      <td id="T_8809c_row1_col2" class="data row1 col2 bid" >bid</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row2" class="row_heading level0 row2" >2</th>
      <td id="T_8809c_row2_col0" class="data row2 col0 bid" >29946.610000</td>
      <td id="T_8809c_row2_col1" class="data row2 col1 bid" >0.000400</td>
      <td id="T_8809c_row2_col2" class="data row2 col2 bid" >bid</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row3" class="row_heading level0 row3" >3</th>
      <td id="T_8809c_row3_col0" class="data row3 col0 bid" >29946.620000</td>
      <td id="T_8809c_row3_col1" class="data row3 col1 bid" >0.030000</td>
      <td id="T_8809c_row3_col2" class="data row3 col2 bid" >bid</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row4" class="row_heading level0 row4" >4</th>
      <td id="T_8809c_row4_col0" class="data row4 col0 bid" >29947.540000</td>
      <td id="T_8809c_row4_col1" class="data row4 col1 bid" >0.026630</td>
      <td id="T_8809c_row4_col2" class="data row4 col2 bid" >bid</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row5" class="row_heading level0 row5" >5</th>
      <td id="T_8809c_row5_col0" class="data row5 col0 ask" >29950.820000</td>
      <td id="T_8809c_row5_col1" class="data row5 col1 ask" >0.675350</td>
      <td id="T_8809c_row5_col2" class="data row5 col2 ask" >ask</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row6" class="row_heading level0 row6" >6</th>
      <td id="T_8809c_row6_col0" class="data row6 col0 ask" >29952.600000</td>
      <td id="T_8809c_row6_col1" class="data row6 col1 ask" >0.050000</td>
      <td id="T_8809c_row6_col2" class="data row6 col2 ask" >ask</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row7" class="row_heading level0 row7" >7</th>
      <td id="T_8809c_row7_col0" class="data row7 col0 ask" >29953.180000</td>
      <td id="T_8809c_row7_col1" class="data row7 col1 ask" >0.050000</td>
      <td id="T_8809c_row7_col2" class="data row7 col2 ask" >ask</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row8" class="row_heading level0 row8" >8</th>
      <td id="T_8809c_row8_col0" class="data row8 col0 ask" >29953.940000</td>
      <td id="T_8809c_row8_col1" class="data row8 col1 ask" >0.166230</td>
      <td id="T_8809c_row8_col2" class="data row8 col2 ask" >ask</td>
    </tr>
    <tr>
      <th id="T_8809c_level0_row9" class="row_heading level0 row9" >9</th>
      <td id="T_8809c_row9_col0" class="data row9 col0 ask" >29954.610000</td>
      <td id="T_8809c_row9_col1" class="data row9 col1 ask" >0.039820</td>
      <td id="T_8809c_row9_col2" class="data row9 col2 ask" >ask</td>
    </tr>
  </tbody>
</table>




Easy peasy. In a few lines of code, we Initialize a pandas IndexSlice and we use it in the ```.bar``` method to select the portion of the DataFrame that should have bars. We also pass a custom color. The result is nice I think!

We will use the HTML of this table in the next step to fill an ipython HTML widget, but the job is pretty much done!

## Drawing the Jupyter Widget

Before drawing the widget, I add some CSS with Jupyter magic, just to have a uniform background for the widget. 

I then create a horizontal box widget with the style I created and some additional Layout and set his children to ```tab```, the HTML widget that is holding the table. 


```python
%%html
<style>
    .box_style{
        width:100%;
        border : 0px solid red;
        height: auto;
        background-color:#373737;
    }
</style>
```


<style>
    .box_style{
        width:100%;
        border : 0px solid red;
        height: auto;
        background-color:#373737;
    }
</style>




```python
from ipywidgets import Layout

# this is the widget holding the HTML of the table
tab = widgets.HTML(value=s.to_html())

# this is an horizontal box holding the table widget
# with some Layout an the CSS style we defined before
hBox = widgets.HBox([tab],layout=Layout(justify_content= 'center'))
hBox.add_class("box_style")

# draw the horizontal Box
hBox
```


    HBox(children=(HTML(value='<style type="text/css">\n#T_8809c_ th:not(.index_name) {\n  text-align: center;\n  …


Et voilà! Let's know put some magic on this table and animate it. 🧙‍♂️

## 🧙‍♂️ Let the magic begin: updating the table with live data

Nothing more than wrapping up all the previous cells in a function called ```style_pipe```. The function takes a DataFrame in input and returns the styler object with all the properties we built before.


```python
def style_pipe(df):
    # this is just a copy paste of the previous cells
    # the functions returns a styler objet 

    # hide index and columns
    s= df.style.hide_index().hide_columns(['side'])

    # color the cells in red, gree and apply style
    # to index and headers
    df_mask = df.copy(deep=True)
    df_mask[df['side'] == 'bid'] = 'bid'
    df_mask[df['side'] == 'ask'] = 'ask'

    s.set_table_styles([
        {'selector': 'th:not(.index_name)','props':'text-align:center; color:grey;background-color:#373737'},
        {'selector': 'td','props':'text-align:center; background-color:#2d2d2d;font-weight:bold'},
        {'selector':'.bid','props':'color:green'},
        {'selector':'.ask','props':'color:red'},
    ],overwrite=False)

    s.set_td_classes(df_mask)   

    # add the bars in the bid and ask portion
    # of the dataframe
    idxs = df.index[df['side'] == 'bid'].tolist()
    idx = pd.IndexSlice
    s.bar(subset=idx[:idxs[-1],'quantity'], color='#dbeac5')
    s.bar(subset=idx[idxs[-1]+1:,'quantity'], color='#ff9090')

    return s 
```

Then we launch an 'infinite' loop that retrieves the data from Binance, constructs the Order Book DataFrame, and applies the style pipeline to the HTML in the ```tab``` widget we just defined. Pretty much all we did in this notebook is wrapped up!


```python
# here we just repeat the process of retrieving data
# applying the style
# and overwrite the html in the tab widget

# start a long loop (not using while True 
# for obvious reasons)
count=0
while count<100:
    
    # retrieve data from Binance
    r = requests.get("https://api.binance.com/api/v3/depth",params=dict(symbol="LUNABUSD"))
    response = r.json()

    # construct the order book Dataframe
    df_bid = pd.DataFrame.from_records(response['bids'], columns=['price', 'quantity']).astype('float')
    df_bid['side'] = 'bid'

    df_ask = pd.DataFrame.from_records(response['asks'], columns=['price', 'quantity']).astype('float')
    df_ask['side'] = 'ask'

    df = pd.concat([df_bid.sort_values(by=['price'])[-5:], df_ask[:5]], ignore_index=True)
    count=count+1
    
    # updating the widget HTML after
    # having applied the style the DataFrame
    tab.value=style_pipe(df).to_html()
```

## Conclusion

If everything went fine, while the last cell was running, your widget got updated in real-time, by the time the loop was still processing. I think this makes a lot of fun because from here we can construct a complete and even complex dashboard! And get this:

we did everything in a Jupyter Notebook, with just some basic Python and the help of HTML and CSS. I don't know you but I am sincerely impressed by how far technology has pushed itself! 

Pure Python magic! 🧙‍♂️

I think next time I will add some plots to this widget! I want to see how far I can go. This always happens to me when playing with data and tech: I take a road, and I cannot see the end ... 

I want to see where it goes ... 

I want to see if I can make it to the next hill or turn.

and there I go for my adventure! 

I may come back? I don't know. 

I will be the same when back home? Never!

As Bilbo sang one time:

*The Road goes ever on and on,* <br>
*Down from the door where it began.* <br>
*Now far ahead the Road has gone,* <br>
*And I must follow, if I can,* <br>
*Pursuing it with eager feet,* <br>
*Until it joins some larger way* <br>
*Where many paths and errands meet.* <br>
*And whither then? I cannot say.*
