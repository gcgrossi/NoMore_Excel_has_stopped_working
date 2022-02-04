---
layout: default
title: 👹 Introduction to the nightmare
nav_order: 2
---

# 👹 Introduction to the nightmare

The world of Finance is completely overwhelmed by the usage of Excel. From small businesses to big organizations Excel is the go-to tool when speaking about almost everything regarding data. 

We cannot deny that Excel is a very powerful tool with a lot of interesting and nice features:

- **Easy organizations of data in tabular form**: The classical Excel grid layout with cells makes going through data (manually and visually) very comfortable.
- **Direct Type-in formula cells**: Choosing a cell in Excel is one click away, as well as writing a formula and performing a calculation. It is very easy to fire up an Excel spreadsheet and start your analysis.
- **Easy and user-friendly interface**: There is a button for almost everything. 
- **A powerful calculation engine**: I am always sincerely surprised about how Excel efficiently calculates a very big amount of formulas and it manages to keep its interface still quite smooth.
- **Excel can be used to store data**: It has a big amount of cells available in a single workbook so that you can store sufficient information for almost any project you will face. If it's used in Finance where there are a lot of long-time series to store, then it is suitable for many other applications. It supports Charts and other visualizations.
- **It is responsive**: you can use it to create dashboards, GUIs, and user interfaces.
- **It is programmable**: with Visual Basics, any manual action you can make in Excel has a programmable counterpart, so that you can ideally automatize everything. 
- **It has a very big package of integrations**: Many Excel libraries are super well integrated with the environment and add powerful features to an already good basic product.

There are many other interesting features that I didn't put on the list, but the point is clear: Excel is a very nice tool for data analysis. But let's look at it from another point of view. 

- **Tabular data is not enough**: Software Development and Data Science have told us that we need a different types of data structures to deploy efficient solutions to complicated problems. We need tables but also dictionaries, lists, sets, (multi-dimensional) arrays.
- **Typing can be very dangerous**: Excel involves a lot of manual typing and inserting data into cells. This is a big source of human error and inefficiency. Circular references are always around the corner, #VALUE, #REF, fat-fingers, and other subtle bugs.
- **Powerful calculations are at the cost of very little flexibility**: have you ever tried matrix multiplication in Excel? It's doable if you try, but how many Xanax do you have to take before succeeding? Sincerely I never tried because I refuse to approach the problem with Excel.
- **Excel is not a database**: it does not have a server to connect to, cannot make index queries, it's not relational (does not have primary and foreign keys).
- **Dashboards with Excel are ugly**: I think that Excel dashboards are very ugly, even if you try to make it the fanciest possible. It is just not meant for that. HTML and CSS are far better friends when it comes to deploying a feasible solution. There are known Dashboarding frameworks like Plotly Dash and Streamlit, as well as Jupyter Notebooks that make dashboarding easy and fancy.
- **Programming with Excel is a nightmare**: Visual Basics is a dead programming language. It throws unknown and undecryptable errors. There are no libraries for enhancing the programming experience.
- **Integrations are nice but still Excel-like**: The only fact that those libraries are integrated with Excel makes them as inefficient as the main tool.

In the end, even if Excel is extensively used by many big and small companies and supports mission-critical tasks, this does not mean that it's the best or the correct tool for everything. Let's be frank: Excel was built for unskilled personnel so that they can enhance their productivity with all the good features we have talked about. It was meant for data analysis? dashboarding? data-storage? I don't know.

I'm not Bill Gates and I'm not in the mind of who first conceived this tool. Maybe it's like everything that happened with humankind: it was meant for a good purpose but then it was given to humanity, that turned Excel applications into giant monsters that every X hours randomly say: "Excel has stopped working". You cry and want to run away from this apocalyptic scenario but you cannot, you are bound to the spreadsheet and its caprices. Unpredictable rules you cannot escape. 

The finance world is the guiltiest industry, where Excel usage is pushed to the very limit. I guess is very natural that Excel took place so deeply in the financial world. Data is tabular by nature and the calculations are not very expensive in general, from both a computational and a mathematical point of view. But again with a small clarification. This reasoning can be true for accounting, credit, back office, and maybe some portfolio/wealth managers. 

This is not true for Quant and Asset Management. It's now more than three years since I've quit research and joined this industry, and I already can tell that I saw Excel used in every way. Surely I can tell that 80% of the time it was not the correct usage and that there was a better tool for each of the times. I will give you an example. I just think about something very easy like a moving average. Now I want that you think about how to do that in Excel. It surely involves something like writing a formula, selecting the range, propagating the formula on the spreadsheet. Everything manually? dragging something with the mouse or maybe with a shortcut? 

Well in python it goes like that:

```python
df['ma20'] = df['price'].rolling(20)
```

just one single line of code. No hustle, no fat-fingers, no fingers slipping on the mouse. Just a fantastic python library. I don't want to say that Python will solve every problem, or that it should be always used in any situation. Python is useful for certain tasks, while for others some languages and tools will better serve your cause. Coming from a C++ base I know that Python has certain drawbacks. 

What I want to say is that the world is evolving, as well as technology. We can automatize much more tasks than before, perform better analysis, run better optimizations. This is not only true for the most complicated asset allocation or option trading strategies but also many office tasks. Organizations should profit from this and take the occasion to cut costs. If it's not too late for them. 

Because some of these costs come as technical debt, in part related to the over usage of Excel applications. At some point is not only a question of how much money an organization is currently spending to keep alive some business functions that can be more efficiently automatized, but also how much money they need to spend and invest to revert the current situation. Some organizations are so much intertwined with these inefficient frameworks that coming back will cost them much more money than what they would gain. Crazy! &#129327; 

Not speaking about how much money they will lose because their systems do not scale. With a correct system if tomorrow I onboard 1000 new clients it will process them smoothly. With an inefficient system, I maybe need to hire one or more people to manage these new clients. 

This is where modern application development comes into play! What we need is to use tools that match certain criteria:

1. They should be able to handle more than one request at a time.
2. They should be able to handle the requests synchronously or asynchronously.
3. They should be able to run in parallel.
4. They should be able to run in the background.
5. They should be able to send notifications. 
6. They should be able to process a big amount of data in a relatively small time.

Of course, Excel does not match almost all the requirements. This is why I'm coming to the main point of this article. I am starting a series of articles (blog posts if you prefer) on how to move from an Excel-based framework to a Python one. 

I will limit everything to Finance applications for several reasons. Besides what I already wrote I am convinced that Finance needs such digital transformation, and many managers are facing this problem now or will face it in the future. I think this series can be useful to all of them because it is aimed to solve(answer) a real business problem. 

It is not meant to be an extensive course on Python, for that, you can go to any web education provider. I think I will structure it to be more of a guide to concepts, ideas, and methodologies on how to translate certain Financial applications from Excel to Python. But how should I structure this guide? I still don't know. I think there is a certain logic to approach the problem so I will try to write here the main points:

- **Retrieving Data:** Financial Data is usually retrieved from providers like Bloomberg or Reuters but most of the time spreadsheets need to be opened manually to refresh data. Sometimes a 'Refresh' button needs to be pushed. The same data providers have developed python libraries and API endpoints to serve applications.
- **Manipulating Data:** A crucial part, where data is handled and transformed to be served to more or less complicated models. Most of the data manipulations that require long formulas and multiple columns can be simplified using just two python libraries: pandas and NumPy.
- **Storing Data:** Excel used as a database is a classic. If lucky, the data is stored in a separate workbook. If not, the data, the back-end, and the front-end are on the same workbook. Good applications require that data are stored in a database, accessible from a server connection, and separated from the other application components. Many python libraries can help in managing interactions with databases. A very famous and used one is SQL Alchemy.
- **Presenting Data:** Consuming the data and the information that is extracted from it. Usually in form of charts. Interaction with data is commonly accomplished via reactive dashboards. Excel has a built-in reaction since all the workbook is recalculated when a cell value is modified. It is therefore very easy to make a dashboard in Excel. Usually, dashboards are created using a front-end web page in HTML, CSS, and Javascript. There are also several already-made dashboarding tools Like Tableau, Power-BI, Dash, Streamlit, Gradio.

In the following episodes, I will take something I am familiar with Excel, and try to translate it into Python. If you feel interested in the topic and you want to follow I will be glad to share everything I know about the subject. I am not the best teacher nor the best Python developer but one thing I am sure of: I cannot stand working with Excel anymore. If you feel the same, let's walk through this new adventure together! &#128512;




