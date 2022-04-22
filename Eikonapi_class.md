---
layout: default
title: Wrap Everything in a Python Class
parent: 🎏 Getting Data
nav_order: 6
---

# The Eikon API
## Create a Python Class to hold all the functionalities

It's time to get a little bit more technical! After 4 Episodes I guess we are ready to ramp up the level of our applications

Up to now, we introduced the building blocks of basic data retrieval with the Eikon Python API:

- [Live Data](https://gcgrossi.github.io/NoMore_Excel_has_stopped_working/Eikonapi_stream_data/)
- [Time Series](https://gcgrossi.github.io/NoMore_Excel_has_stopped_working/Eikonapi_timeseries_data/)
- [A tool to autostart the Desktop Terminal](https://gcgrossi.github.io/NoMore_Excel_has_stopped_working/Eikonapi_proxy/)

The examples proposed were just snippets of code that you can run in a python executable or a Jupyter Notebook cell. Sometimes we wrapped some code into a function for a clearer view of the process, but that's all.

What I want to communicate is that all of these elements we introduced are scattered around different lines of code and they do not belong to the same 'entity' (object, for pythonist terminology). We would like to create an object to hold all of these functionalities so that we can always have them everywhere when we initialize this object.

In this way, when writing a new script we could do:

```python
my_object = object()
my_object.connect()
```

and our new object will do all the leg-work of checking if our Terminal is opened and connecting to it. Our object will also be able to query the data and, if we wish to, manipulate the retrieved data to be presented to our application the way we need it with a more simple:

```python
my_object.get_data()
```

If you didn't get it yet, it's obvious I'm referring to python Classes. We will going to leverage this beatufil kind of object to have our Swiss Army knife of data retrieval.

## Python Classes

Python Classes are 'portable' objects that can store different kind of variables/data. Those Class variables can be accessed everywhere in the Class, and can be modified by specific Class functions, called 'methods'.

I will not go into the technical details of how a python Class works, it's not the scope of the guide. But I leave here a list of curated resources where you can find everything you need, written by far more competent people than me.

- [Official Python Docs](https://docs.python.org/3/tutorial/classes.html) for all the theoretical basis on classes
- [Real Python Tutorial](https://docs.python.org/3/tutorial/classes.html). A comprehensive tutorial for a pragmatic approach.

If you are new to Python Classes, I suggest you to read at least one of the two to get the general idea of how they work, because from now on we're going to make a deep dive into building our very own.

## Our Custom Class

Let's start by creating a new python file in our current directory. We will call it ```dataclass.py```. There are two basic ways to use our class. 

1. We write the Class in ```dataclass.py``` and our main script in the same file.
2. We write the Class in ```dataclass.py``` and our main script in a separate file and import ```dataclass.py``` as a module.

Of course we will use the second methodology for the principle of 'separating corncerns'. In a new file named ```dataclass.py``` let's initialize our Class:

```python
class EikonStreamer():
    def __init__(self,app_key,connection=True):
        # connect to the eikon terminal
        if connection: self.connect(app_key)
        return
```

You will recognize the typical ```__init__``` function of the Python classes constructor. Here we pass (apart from the necessary ```self``` argument):

1. The app key string to connect to the Eikon terminal
2. a boolean telling if we want to connect to the eikon terminal

The default behavior would be to connect to the Eikon terminal on the first Class initialization. This is why the default value for ```connection``` is set to ```True```. 
The below function ```connect``` will be called in the constructor.

```python
    def connect(self,app_key, check_proxy = True, attempts = 2, sleep = 1):
        '''
        check if the Eikon application is opened.
        connects to Eikon via app key.
        '''
        # check if eikon is running
        # shuts down the session after n trials
        if check_proxy: self.check_eikon_proxy(total_attempts = attempts, minutes_sleep = sleep)
        # setup connection with Eikon
        # raise ConnectionError if encounter a problem.
        print('connecting to Eikon ...')
        try:
            eikon.set_app_key(app_key)
        except:
            raise ConnectionError('An error was produced when connecting to Eikon.')
        return
```

If we want to call the class we can do it in our ```main``` or a cell in the Jupiter Notebook. You should be running the script in the same directory of the ```dataclass.py``` file:

```python
# import class
from dataclass import EikonStreamer

# connect to Eikon
ek = EikonStreamer(app_key='your app key here')
```

You may want to first initialize the Class and connect later. in this case, you can use the following code:

```python
# import class
from dataclass import EikonStreamer

# initialize the class without connecting
ek=EikonStreamer(app_key='your app key here',connect=False)
# connect to Eikon
ek.connect()
```

What is the importance of wrapping the connection in a Python class? 'You can directly connect to Eikon in your main' you may say, and it would be legitimate. But let's have a closer look at the ```connect``` function. The first thing is a call to yet another function ```check_eikon_proxy```. Only after that function, the actual connection is tried and eventually established.

```check_eikon_proxy``` does what the name says, and what we saw in the previous Episode: it checks if the terminal is up and running and eventually tries to open it.

```python
    def check_eikon_proxy(self,total_attempts = 2, minutes_sleep = 1):
        '''
        Check if the Eikon application is running. 
        Repeat the check each minutes_sleep.
        Send a warning if Eikon application not running.
        Throws an error if the application is closed after total_attempts,
        '''
        eikon_running = False
        attempt_number = 1

        # start an infinite loop
        while True:
            # ping eikon proxy -> return true if Eikon is runninig
            eikon_running = self.ping_eikon_proxy()
            # break loop if eikon running 
            # or if connection attempts reaches maximum
            if eikon_running or attempt_number > total_attempts: 
                break
            else:
                # send warning message if eikon not running
                self.eikon_not_running_message(minutes_sleep,total_attempts,attempt_number)
                # try starting the terminal
                self.start_eikon_terminal()
                # increase the attempts counter and 
                # wait some time before re-trying
                attempt_number+=1
                time.sleep(minutes_sleep*60) # minutes in seconds
        if eikon_running:
            return
        else:
            raise ConnectionError('Eikon is not Running!')

```

There are a couple of parameters we can pass to the function:

1. the number of connection attempts we want to try.
2. the minutes we need to wait between one attempt and another.

The code will start an infinite loop with the ```while``` statement, ping the eikon terminal to see if it's up and running, and store its value in a boolean variable ```eikon_running```. It will stop only if the terminal is running or we have exhausted the maximum number of attempts. If we exhaust the number of attempts and the Terminal is still not running an error will be produced. 

As you can see, during the procedure we call other Class methods. During the attempts we call firstly ```ping_eikon_proxy()```:

```python
    def ping_eikon_proxy(self):
        '''
        Tries to check if the Eikon application is opened
        by sending a request on the 9060 ports where
        the application should be running. 
        '''
        not_proxy = True
        # check if eikon application is opened
        try:
            # send a request to port 9060
            # returns a status 'ST_PROXY_READY' if eikon application is opened
            # throws a timeout error if the application is closed
            request = requests.get('http://localhost:9060/api/status')
            response = request.json()
            if response['statusCode'] == 'ST_PROXY_READY': return True
        except:
            not_proxy = True

        return False if not_proxy else True
```

This is the same function we saw in the [last Episode: How to autostart the Eikon terminal](https://gcgrossi.github.io/NoMore_Excel_has_stopped_working/Eikonapi_proxy/) to see if the proxy is up and running.

If we see the terminal is not running, we print a message and we try to start it using the functions ```eikon_not_running_meesage()``` and ```start_eikon_terminal()```

```python
    def eikon_not_running_message(self,sleep_time,total_attempts,attempt_number):
        '''
        prints a message saying that the Eikon application is not running.
        '''
        # prepare a long message
        print(
            'Warning:',
            'Attempt number: {}/{}'.format(attempt_number,total_attempts),
            'Eikon is not running. I will retry the connection in {} minutes'.format(sleep_time),
            'You still have: {} attempts to connect to Eikon.'.format(total_attempts-attempt_number),
            'After I will throw an error.',
            sep='\n')
        return
    
    def start_eikon_terminal(self):
        # try starting the terminal using 
        # os startfile
        try:
            os.startfile("C:\Program Files (x86)\Thomson Reuters\Eikon\Eikon.exe")
        except:
            print('Something went wrong. I could not start the Eikon Terminal',
                  f'here is the stacktrace: \n {traceback.format_exc()}',sep = '\n')
        return
    
```

What is nice here is that we took all the ingredients that are needed for a well functioning application and we cooked them together in one tool:

- we have sanity checks at startup to control if all the components needed for the application to run are there.
- we log or notify the user if there is a problem.
- we make attempts to fix the problem if we find it.
- we wrap the code in try-except statements to handle possible errors we can find on the way.

We just have a small piece missing: the actual data retrieval.

## Adding Data Retrieval

Now we can add the functions to perform the data retrieval via the Eikon API. I will just add the following function as an example.

```python
    def live_quote(self,rics,fields):
        # here we can perform instance check
        # if we want 
                # ------------

                # ------------
        # I try to get data from a quote 
        # and print the error if I fail in some ways
        try:
            df = eikon.get_data(instruments = rics, fields = fields)
            return df
        except:
            # or better -> dump the error on a file using the logging module
            print('Error in retrieving your data. Here is the stacktrace',
                traceback.format_exc(),sep='\n')
            return None
```

This is a very simple one: it just gets the actual data from the Eikon terminal. This is exactly what we saw in the Episode [Getting Started with Python Eikon](https://gcgrossi.github.io/NoMore_Excel_has_stopped_working/Eikonapi_getting_started/). We have just enclosed the request in a try/except statement to handle eventual errors.

We can now have a look how all this class may look like and how can we use it in the code below:


```python
import eikon
import requests
import time
import os
import traceback

class EikonStreamer():
    def __init__(self,app_key,connection=True):
        # connect to the eikon terminal
        if connection: self.connect(app_key)
        return
    
    def connect(self,app_key, check_proxy = True, attempts = 2, sleep = 1):
        '''
        check if Eikon application is opened.
        connects to Eikon via app key.
        '''
        # check if eikon is running
        # shuts down the session after n trials
        if check_proxy: self.check_eikon_proxy(total_attempts = attempts, minutes_sleep = sleep)
        # setup connection with Eikon
        # raise ConnectionError if encounter a problem.
        print('connecting to Eikon ...')
        try:
            eikon.set_app_key(app_key)
        except:
            raise ConnectionError('An error was produced when connecting to Eikon.')
        return

    def ping_eikon_proxy(self):
        '''
        Tries to check if the Eikon application is opened
        by sending a request on the 9060 ports where
        the application should be running. 
        '''
        not_proxy = True
        # check if eikon application is opened
        try:
            # send a request to port 9060
            # returns a status 'ST_PROXY_READY' if eikon application is opened
            # throws a timeout error if the application is closed
            request = requests.get('http://localhost:9060/api/status')
            response = request.json()
            if response['statusCode'] == 'ST_PROXY_READY': return True
        except:
            not_proxy = True

        return False if not_proxy else True
      
    def eikon_not_running_message(self,sleep_time,total_attempts,attempt_number):
        '''
        prints a message saying that the Eikon application is not running.
        '''
        # prepare a long message
        print(
            'Warning:',
            'Attempt number: {}/{}'.format(attempt_number,total_attempts),
            'Eikon is not running. I will retry the connection in {} minutes'.format(sleep_time),
            'You still have: {} attempts to connect to Eikon.'.format(total_attempts-attempt_number),
            'After I will throw an error.',
            sep='\n')
        return
    
    def start_eikon_terminal(self):
        # try starting the terminal using 
        # os startfile
        try:
            os.startfile("C:\Program Files (x86)\Thomson Reuters\Eikon\Eikon.exe")
        except:
            print('Something went wrong. I could not start the Eikon Terminal',
                  f'here is the stacktrace: \n {traceback.format_exc()}',sep = '\n')
        return
    
    def check_eikon_proxy(self,total_attempts = 2, minutes_sleep = 1):
        '''
        Check if the Eikon application is running. 
        Repeat the check each minutes_sleep.
        Send a warning if Eikon application not running.
        Throws an error if the application is closed after total_attempts,
        '''
        eikon_running = False
        attempt_number = 1

        # start an infinite loop
        while True:
            # ping eikon proxy -> return true if Eikon is runninig
            eikon_running = self.ping_eikon_proxy()
            # break loop if eikon running 
            # or if connection attempts reaches maximum
            if eikon_running or attempt_number > total_attempts: 
                break
            else:
                # send warning message if eikon not running
                self.eikon_not_running_message(minutes_sleep,total_attempts,attempt_number)
                # try starting the terminal
                self.start_eikon_terminal()
                # increase the attempts counter and 
                # wait some time before re-trying
                attempt_number+=1
                time.sleep(minutes_sleep*60) # minutes in seconds
        if eikon_running:
            return
        else:
            raise ConnectionError('Eikon is not Running!')

    def live_quote(self,rics,fields):
        # here we can perform instance check
        # if we want 
				# ------------

				# ------------
        # I try to get data from a quote 
        # and print the error if I fail in some ways
        try:
            df = eikon.get_data(instruments = rics, fields = fields)
            return df
        except:
            # or better -> dump the error on a file using the logging module
            print('Error in retrieving your data. Here is the stacktrace',
                traceback.format_exc(),sep='\n')
            return None

# connect to Eikon
ek = EikonStreamer(app_key='your app key here')
df = ek.live_quote('.SPX',['BID','ASK'])
df
```

    connecting to Eikon ...
    




    (  Instrument      BID      ASK
     0       .SPX  4414.41  4416.64,
     None)



Here I made a very simple example, just to show the functionality of the class. In the future, we will take again this class and modify it to handle other tasks. For example, one can introduce a pre-processing step, where data are digested and transformed into the form that is most suitable for the application we are building. But as Aragorn used to say in front of the Black Gates of Mordor:

<img src="images/Aragorn.jpg">

Against the inefficiency of manual typing Evil, we stand, men of the Python Planet! 
