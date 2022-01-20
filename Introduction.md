---
layout: default
title: Introduction
nav_order: 2
---

# Introduction

The world of Finance is completely overwhelmed by the usage of Excel. From small businesses to big organizations Excel is the go to tool when speaking about almost everything regarding data. 

We cannot deny that Excel is a very powerful tool with a lot of interesting and nice features:

- **Easy organisations of data in tabular form**: The classical Excel grid layout with cells makes going through data (manually and visually) very comfortable.
- **Direct Type-in formula cells**: Chosing a cell in Excel is one click away, as well as writing a formula and perform calculation. It is very easy to fire up an Excel spreadsheet and starting your analysis.
- **Easy and user-friendly interface**: There is a button for almost everything. 
- **A powerful calculation engine**: I am always sincerely surprised about how Excel efficiently calculates a very big amount of formulas and it manages to keep its interface still quite smooth.
- **Excel can be used to store data**: It has a big amount of cells available in a single workbook so that you can store sufficient information for almost any project you will face. If it's used in Finance where there are a lot of long timeseries to store, then it is suitable for many other applications. It support Charts and other visualisations.
- **It is responsive**: you can use it to create dashboards, GUIs and user interfaces.
- **It is programmable**: with Visual Basics any manual action you can make in Excel has a programmable counterpart, so that you can ideally automatize everything. 
- **It has a very big package of integrations**: There are many Excel libraries that are super well integrated with the environment and add powerful features to an already good basic product.

There are many other interesting features that I didn't put on the list, but the point is clear: Excel is a very nice tool for data analysis. But let's look it from another point of view. 

- **Tabular data is not enough**: Software Development and Data Science have told us that we need different type of data structures to deploy efficient solutions to complicated problems. We need tables but also dictionaries, lists, sets, (multi-dimensional) arrays.
- **Typing can be vary dangerous**: Excel involves a lot of manual typing and inserting data into cells. This is a big source of human error and inefficiency. Circular references are always around the corner, #VALUE, #REF, fat-fingers and other subtle bugs.
- **Powerful calculations are at the cost of a very few flexibility**: have you ever tried a matrix multiplication in Excel? It's doable if you try, but how many Xanax you have to take before succeding? Sincerely I never tried because I refuse to approach the problem with Excel.
- **Excel is not a database**: it does not have a server to connect to, cannot make index query, it's not relational (does not have primary and foreign keys).
- **Dashboards with Excel are ugly**: I personally think that Excel dashboards are very ugly, even if you try to make it the fanciest possible. It is just not meant for that. HTML and CSS are far better friends when it comes to deploy a fruible solution. There are know Dashboarding frameworks like plotly Dash and Streamlit, as well as Jupyter Notebooks that make dashboarding easy and fancy.
- **Programming with Excel is a nightmare**: Visual Basics is a dead programming language. It throws unknown and undecryptable errors. There are no libraries for enhancing programming experience.
- **Integrations are nice but still Excel like**: The only fact that those libraries are integrated with Excel makes them as inefficient as the main tool.

In the end, even if Excel is extensively used by many big and small companies and supports mission critical tasks, this does not mean that it's the best or the correct tool for everything. Let's be frank: Excel was built for unskilled personnel, so that they can enhance their productivity with all the good features we have talked about. It was meant for data analyis? dashboarding? data-storage? I don't know.

I'm not Bill Gates and I'm not in the mind of who first conceived this tool. Maybe it's like everything that happened with human kind: it was meant for a good purporse but then it was given to humanity, that turned Excel applications in giant monsters that every X hours randomly say: "Excel has stopped working". You cry you and want to run away from this apocaliptic scenario but you cannot, your are bound to the spreadsheet and to its caprices. Unpredicable rules you cannot escape. 

Finance world is the guiltiest industries, where Excel usage is pushed to the very limit. I guess is very natural that Excel took place so deeply in the financial world. Data is tabular by nature and the calculations are not very expensive in general, from both a computational and a mathematical point of view. But again with a small clarification. This reasoning can be true for accounting, credit, backoffice and maybe some portfolio/wealth managers. 

Definetly is not true for Quant and Asset Management. It's know more than three years I've quit research and joined this industry, and I already can tell that I saw Excel used in every way. Surely I can tell that 80% of the time it was not the correct usage, and that there was a better tool for each of the times. I will give you and example. I just think at something very easy like a moving average. Now I want that you think on how to do that in Excel. It surely involves something like writing a formula, selecting the range, propagating the formula on the spreadsheet. Everything manually? dragging something with the mouse or maybe with a shortcut? 

Well in python it goes like that:

```python
df['ma20'] = df['price'].rolling(20)
```

just one single line of code. No hustle, no fat-fingers, no fingers slipping on the mouse. Just a fantastic python library. I don't want to say that Python will solve every problem, or that it should be always used in any situation. Python is useful for certain tasks, while for others there are languages and tools that will better serve your cause. Coming from a C++ base I know that Python has certain drawbacks. 

What I want to say is that the world is evolving, as well as technology. We can automathize much more tasks than before, perform better analysis, run better optimisations. This is not only true for the most complicated asset allocation or option trading strategies, but also for many office tasks. Organisations should profit from this and take the occasion to cut costs. If it's not too late for them. 

Because some of these costs come as a technical debt, in part related to the overusage of Excel applications. At some point is not only a question of how much money an organisation is currently spending to keep alive some business functions that can be more efficiently automatized, but also how much money they need to spend and invest to revert the current situation. Some organisations are so much intertwined with these ineffiencient frameworks that coming back will cost them much more money than what they would actually gain. Crazy! &#129327; 

Not speaking about how much money they will lose because their systems do not scale. With a correct system if tomorrow I onboard 1000 new clients it will process them smoothly. With an inefficient system I maybe need to hire one or more people to manage these new clients. 

This is where modern application development comes into play! What we need is to use tools that match certain criteria:

1. They should be able to handle more than one request at a time.
2. They should be able to handle the requests synchronously or asynchronously.
3. They should be able to run in parallel.
4. They should be able to run in the background.
5. They should be able to send notifications. 
6. They should be able to process a big amount of data in a relatively small time.

Of course Excel does not match almost all the requirements. This is why I'm coming to the main point of this article. I am starting a series of articles (blog posts if you prefer) on how to move from an Excel based framework to a Python one. 

I will limit everything to Finance applications for several reasons. Besides what I already wrote I am convinced that Finance needs such digital transformation, and many managers are facing this problem now or will face it in the future. I think this series can be useful to all of them because it is aimed to solve(answer) a real business problem. 

It is not meant to be an extensive course on Python, for that you can go to any web education provider. I think I will structure it to be more a guide to concepts, ideas and methodologies on how to translate certain Financial applications from Excel to Python. But how should I structure this guide? I still don't know. I think there is a certain logic to approach the problem so I will try to write here the main points:

- **Retrieving Data:** Financial Data is usually retrevied from providers like Bloomberg or Reuters but most of the time spreadsheets need to be opened manually to refresh data. Sometimes a 'Refresh' button needs to be pushed. The same data providers have developed python libraries and api endpoints to serve applications.
- **Manipulating Data:** A crucial part, were data is handled and trasformed to be served to more or less complicated models. Most of the data manipulations that require long formulas and multiple columns can be simplified using just two python libraries: pandas and numpy.
- **Storing Data:** Excel used as a datbase is a classic. If lucky, the data is stored in a separate workbook. If not, the data, the back-end and the front-end is on the same workbook. Good applications require that data are stored in a database, accessible from a server connection and separated from the other application components. Many python libraries can help in managing interactions with databases. A very famous and used one is SQL Alchemy.
- **Presenting Data:** Consuming the data and the information that is extracted from it. Usually in form of charts. Interaction with data is commonly accomplished via reactive dashboards. Excel has a built-in reaction, since all the workbook is recalculated when a cell value is modified. It is therefore very easy to make a dashboard in Excel. Usually dashboards are created using a front-end web page in HTML,CSS and Javascript. There are also several already-made dashboarding tools Like Tableau, Power-BI, Dash, Streamlit, Gradio.

In the following Episosed I will take something I am familiar with Excel and try to translate in Python. If you feel interested about the topic and you want to follow I will be glad to share everything I know about the subject. I am not the best teacher nor the best Python developer but one thing I am sure: I cannot stand working with Excel anymore. If you feel the same, let's walk through this new adventure together! &#128512;


