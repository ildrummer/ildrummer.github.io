---
id: 94
title: 'Collecting &#038; Decoding Temperature JSON'
date: '2021-02-02T09:44:03+05:00'
author: 'Adam Leone'
layout: post
guid: 'http://www.repo-me-this.com/?p=94'
permalink: /collecting-decoding-temperature-json/remotetempsensors/
categories:
    - RemoteTempSensors
tags:
    - Python
    - RaspberryPi
    - Server-side
---

Now that our remote sensors are functioning and “on air”, it’s time to tackle the most essential function of the central server – polling remote sensor stations and extracting temperatures. Let’s begin with a very simple proof-of-concept and elaborate it a little later.

### The Outline

This won’t be news to most of you, but Python doesn’t start executing from a declared main function. I never got deep enough into the language before to realize that. Maybe it’s a result of its scripting alter-ego? Regardless, we need to make this *object-oriented* program start from a defined main method. To do that, we’ll call *main()* if the current [*\_\_name\_\_* variable](https://www.geeksforgeeks.org/__name__-special-variable-python/) is “\_\_main\_\_”.

```
<pre class="wp-block-code">```
def getTemp(address):
        # Ask a sensor at <em>address</em> for a temperature

def main():
	# Run the system


if __name__ == "__main__":
	main()
```
```

Because this file doesn’t contain a class (yet?), it runs like a script – sequentially. That means we need to declare and define our functions and procedures before calling them, forcing *main()* to be above the conditional that calls it. I also threw in a shell for the *getTemp(address)* method that reaches out to a sensor.

### HTTP Up In Here

To access our API, the system needs to understand how to request data and interpret the JSON result. Let’s import the building blocks and also add our list of sensor addresses.

```
<pre class="wp-block-code">```
import requests
import json

tempHosts = ["192.168.1.10", "192.168.1.11", "192.168.1.12"]
```
```

Right now I’m using a simple array of IP addresses but an array of name-address **tuples** might be easier to read and work with later on.

The people that wrote the [requests ](https://requests.readthedocs.io/en/latest/user/quickstart/)and [JSON ](https://docs.python.org/3/library/json.html)libraries are amazing. They make HTTP communication so easy! Watch this:

```
<pre class="wp-block-code">```
def getTemp(address):

	remoteTemp = -400

	response = requests.get("http://" + address + "/temperature", timeout=10)

	if response.status_code == 200:
		remoteTemp = float(response.json()['value'])

	return remoteTemp


def main():

        for x in tempHosts:
                tempStr = "Temperature for " + tempHosts[x] + " is "
                tempStr += str(getTemp(tempHosts[x]))
                print(tempStr)
```
```

In *main()*, we iterate over each sensor (tempHost), ask it for data, and print an output string. *getTemp(address)* is where the magic happens. The **request** library *get* function takes the HTTP address of our host’s API function and a timeout parameter so we aren’t waiting forever and returns a response object. We can dig into that response to see if the call was successful (server code 200) and, if so, pull out the temperature value. The **JSON** library decodes the stringified JSON response from the ESP32 by searching for the key ‘value’ and returning the value, or temperature. *getTemp* then returns either the float value from our remote sensor or the implausible temperature *remoteTemp* was initialized to.

Our output from running this should be

```
<pre class="wp-block-code">```
Temperature for 192.168.1.10 is 68.80
Temperature for 192.168.1.11 is 69.03
Temperature for 192.168.1.12 is 68.79
```
```

Be prepared to wait for this output, though. Our ESP32 sensor stations don’t hold the current temperature in a buffer and blast it back – they only sniff the temperature when requested. It might take 6-10 seconds to run through these three sensors in sequential order. We’ll fix that later with multi-threading.

### Add A Little Bit Of … Exception Handling

Because **requests.status\_code** might not be 200 (successful), we need to handle the alternative scenarios with some exception handling and will add in logging later so we can analyze system failures.

```
<pre class="wp-block-code">```
def getTemp(address):

	remoteTemp = -400

	try:
		response = requests.get("http://" + address + "/temperature", timeout=10)

	except requests.Timeout as err:
		# log timeout error

	except requests.RequestException as err:
		# log other request library exceptions
	except:
		# log any other exception that might occur

	else:
   # Request came back with a response
		if response.status_code == 200:
			remoteTemp = float(response.json()['value'])
			
		else:  
			# log unsuccessful response

	return remoteTemp
```
```

I’m deliberately separating Timeout errors from [every other **requests** error](https://requests.readthedocs.io/en/master/_modules/requests/exceptions/) or exception because I want to know explicitly when the ESP32s fail to response, indicating they aren’t on the network or have an internal error. Connection errors, invalid URL errors, and the like can be logged in one statement with their error message and a note that it came from the **requests**. Otherwise, we’ll log any-old exception that happens with just its message and hope to figure it out later.

If no exception is thrown, we have either a valid temperature or a valid HTTP response with an unsuccessful payload. Both are easy to handle.

I’ll discuss logging in a future post – it’s easy but detailed enough to deserve the limelight on its own.

- - - - - -

My code is publically available on my [TempBuddy GitHub repository](https://github.com/ildrummer/TempBuddy).