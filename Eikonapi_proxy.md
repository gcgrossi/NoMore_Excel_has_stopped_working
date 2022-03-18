---
layout: default
title: How to autostart the Eikon Terminal with Python
parent: 🎏 Getting Data
nav_order: 5
---

# The Eikon API

## How to autostart the Eikon Terminal

[![GitHub](https://img.shields.io/static/v1?logo=Jupyter&label=jupyter&message=notebook&color=orange)](https://github.com/gcgrossi/NoMore_Excel_has_stopped_working/blob/main/Eikonapi_proxy.ipynb)

In the previous episodes, we saw how could we obtain data from Eikon using the Python API to connect to its Terminal. We saw it was very easy and straightforward to obtain a constant stream of information or historic data for a particular financial instrument, using an app key and the API methods.

I spent a lot of nice words praising the ease of connection and the simplicity of the API’s design. The time has come to see the flip of the coin. I will introduce it by telling you a story.

## How does the Python Eikon API get data and how I've discovered it

When I started developing applications for my job I thought that an API works always in the same way: you request a remote server via some authentication keys and you get back the response from the server with the information you need. That is what I thought also when connecting to Eikon via my app key. I thought it was my key to authenticate to the Eikon server and get data. 

I was excited the first time I tried my connection so I quickly fired up a Jupyter Notebook to tried out my fresh app key, only to discover with my horror that the eikon proxy was throwing an error:

> ```[MainThread 31936] Eikon Proxy not running or cannot be reached. Please read the documentation on troubleshooting.```

I was a little bit puzzled about the error, and I was thinking that a sort of firewall was preventing me to connect. I tried to see if I had proxies on... but no, everything was ok. So I did the number one thing in the list of the debugging cookbook: Googling the error. 
I found out that there is a developers forum where you can pose questions of any kind regarding developing applications with Eikon and you can find answers to recurrent problems. You can find it [here](https://community.developers.refinitiv.com/tags/eikon.html). 

I also realized that the connection between your Python application and Eikon via the app key serves only to connect to your desktop terminal, which should be up and running and you should be already logged in. 😱

The actual data will be requested by your Terminal and not by the Python application itself. The API is only a bridge between your python script and the Eikon desktop. What happens technically is that the Eikon desktop is running on a port on your Laptop (usually 9000 or 9060), and the Python API will make a request to that port to obtain the data. 

So, in the end, there is no real interaction between you (your script) and a remote server. I was shocked! 'What is the problem!' you will say. As long as you have a connection to a server you will always get your data and you will be able to write your applications. Yes this is true and I can still do it... but I have a developer's mentality and the deployment of your solution is a fundamental step in the process. 

## How to deploy my application that uses Python Eikon API? 

My first question, after having discovered the issue was: "How can I deploy my application? can I do it on the cloud?". Based on what we said, we always need a machine with an Eikon Desktop Terminal up and running. So, deploying the solution on a Linux virtual machine on the cloud is impossible, we need a Windows machine. Moreover, we need a machine with the Eikon Desktop installed and a valid account on it. Disappointing! 😡

I had the confirmation searching the developers' community forum and I found out that I was not alone. Someone before me asked the same (or similar) question:

<img width="517" alt="Eikon_Proxy_Question" src="https://user-images.githubusercontent.com/14998207/157275409-944a3d8f-80bb-43e5-b377-4846de344e35.png">

and one of the answers:

<img width="523" alt="Eikon_Proxy_Answer" src="https://user-images.githubusercontent.com/14998207/157275459-67081a64-2d1b-4ca4-b25c-65c0d077d4df.png">

you can follow the thread [here](https://community.developers.refinitiv.com/questions/48321/how-can-i-use-the-eikon-data-api-without-signing-i.html). Later on, I started thinking about what would be the best solution to deploy my applications, and the only two that came to my mind were: 
1. A physical server you can connect via VNC or similar. 
2. A virtual Desktop in Microsoft Azure Client.

If you have a better solution please let me know!

After 3 years of work in Finance, I finally realize why this diabolic connection method works in this way. Data providers firms like Bloomberg or Reuters try to exploit revenues not only from the single user but for the single machine accessing the service. Every machine should pay a license fee so that you cannot access the API from wherever you want, i.e. external hosts like cloud servers. 

This is particularly true with providers like Bloomberg, where the level of control over the IPs reaches a paranoic level. The Client usually kicks you out if the desktop goes asleep and obliges you to re-connect, just to be sure to check your location every day. Even with a particular subscription called 'everywhere' you receive a device for fingerprint authentication as the next level of security. Always be sure that is you, and that you didn't lend your terminal to some other guy.

Eventually, also your Eikon Desktop will kick you out. In my case, it happens during weekends and unfortunately, I have to deal with it and find a workaround for that. 

## How to check if the Eikon Desktop is up and running

To deploy a robust application we should be able to check if the Desktop app is up and running. We may need this check, in particular, if there is a scheduled job that runs every X time and, for example, feeds a database with some instrument past data. We don't want to trigger a job that crashes already in the first lines if the Application is not running.

To do this we will use some interesting python libraries that every developer should be familiar with: os, subprocess, and requests and also a simple method from the API:

## ```get_port_number()```

Returns the port number used to communicate with the Eikon Data API Proxy.

### Structure

```python
get_port_number()
```

The method returns ```None``` if the Eikon terminal is not running. 

The port is either 9060 or 9000. If we want to control if the proxy is running we have two possible methods: use ```get_port_number``` and check if the result is not ```None``` or send an HTTP request to the 9060 port. 

I prefer using the method from the API, because it will avoid the hard-coding of the port number in the request, but I will still show you the two methods:

### #1 Use the API method

```python

import eikon

# connect to the eikon api
eikon.set_app_key('your app key here')

# get port number to communicate
# with the Eikon Terminal
port = eikon.get_port_number()

if port: 
    print(f'The Eikon proxy is up and running on port {port}')
else:
    print('It seems your Eikon Terminal is not running')
    # take action here
```

### #2 making an http request 

We will use the python library ```requests``` and send an HTTP request to localhost 9060 and route api/status and look at the response.


```python
import requests

# check if eikon application is opened
try:
    # send a request to port 9060
    # returns a status 'ST_PROXY_READY' if eikon application is opened
    # throws a timeout error if the application is closed
    request = requests.get('http://localhost:9060/api/status')
    response = request.json()

    if response['statusCode'] == 'ST_PROXY_READY': 
        print('The Eikon proxy is up and running')
except:
    print('It seems your Eikon Terminal is not running')
    # take action here
```

    The Eikon proxy is up and running
    

In this case, the request throws a timeout error if the application is not running and we need to wrap our request into a try-except statement.

## What to do if the Terminal is not running

Notice that I left a #take action here comment if the Terminal is not running. In this case, we want to do something meaningful. This can be a simple notification telling the user to switch on the application, manually. But we are python samurais ⛩, our motto is: 

> _no mercy for manual entry_ 

and we are here to try to automatize the most our application, so we will make our machine open the terminal for us. The executable to launch the terminal is usually in ```C:\Program Files (x86)\Thomson Reuters\Eikon\Eikon.exe```. Python can launch windows shell commands via the ```subprocess``` module or launch windows executable using the module ```os```. Either of the two following commands will launch the Terminal


```python
# using os startfile method to launch the Eikon Terminal
import os
os.startfile("C:\Program Files (x86)\Thomson Reuters\Eikon\Eikon.exe")

# using the subprocess check_call method to launch the Eikon Terminal
import subprocess
subprocess.check_call([r"C:\Program Files (86)\Thomson Reuters\Eikon\Eikon.exe"])
```

## Wrap up everything in an auto-start tool

With all the information we have, we are now able to create a small tool that will try to open the terminal if it’s closed. In this way we are ‘triggerable’, meaning that we can launch our application when some event happens.

One example could be launching a script every morning, to fetch the closing prices of a security and feed a database.

Our application will still be robust enough to check if the terminal is opened without crashing. Moreover, we will not need to intervene manually to fix the problem, since our machine will solve it for us.

We can wrap what we’ve done in the following code:


```python
import eikon
import requests
import time
import os

# connect to the eikon api
eikon.set_app_key('your app key here')

def ping_eikon_proxy(max_attempts = 4):
    # counter for the number of attempts made
    attempts = 1
    # we start a loop until the attemps made are
    # > than the maximum number of attempts
    while attempts < max_attempts:
        try:
            # send a request to port 9060
            # returns a status 'ST_PROXY_READY' if eikon application is opened
            # throws a timeout error if the application is closed
            request = requests.get('http://localhost:9060/api/status')
            response = request.json()
            if response['statusCode'] == 'ST_PROXY_READY': 
                print(f'The Eikon proxy is up and running on port 9060')
                return True
        except:
            print(f'It seems your Eikon Terminal is not running. Attempt {attempts}/{max_attempts} to launch it.')
            # try to open the terminal using the executable
            os.startfile("C:\Program Files (x86)\Thomson Reuters\Eikon\Eikon.exe")
            # increase the attempt counter
            attempts +=1
            # wait 60 seconds before trying again. It takes some time to
            # open the terminal
            time.sleep(60)
    return False


ping_eikon_proxy()
```

let’s review the code above:

- we define a function called ```ping_eikon_proxy```, with input an integer representing the number of attempts to launch the terminal we want to make.

- We start a while loop until the number of attempts exceeds the maximum attempts.

- We try to get a response with the method we saw earlier:

- If we succeed we exit with a ```True``` status.

- If not we try to open the terminal with ```os.startfile```, we increase the number of attempts and we wait 60 seconds before trying again the procedure.

- If after 4 attempts the terminal is not opened there is a major problem, and the while loop finishes, exiting the function with a ```False``` status.

Running the code above will produce the following output if the terminal is closed:

```
It seems your Eikon Terminal is not running. Attempt 1/4 to launch it.
The Eikon proxy is up and running on port 9060
True
```

And here we are! You can now try to play a bit and write a cron/task job to retrieve some data each day, with the first check being the ```ping_eikon_proxy``` function. It will be a new tool in your Swiss Knife 🇨🇭 of development. So far we saw:

- How to retrieve live data
- How to retrieve time series data
- How to check if the terminal is up and running

These are all the blades composing your knife, but they are all separated. In the next Episodes, we will create a nice case to hold them all and to draw only the tools we need, when we need them. Something portable that we can take everywhere and that will always behave the same way: a Python Class 🔧.
