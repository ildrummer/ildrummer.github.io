---
id: 63
title: 'Serving Up Some Temps'
date: '2021-01-28T09:49:21+05:00'
author: 'Adam Leone'
layout: post
guid: 'http://www.repo-me-this.com/?p=63'
permalink: /serving-up-some-temps/remotetempsensors/
image: 'http://www.repo-me-this.com/wp-content/uploads/2021/01/you-get-an-api.jpg'
categories:
    - RemoteTempSensors
tags:
    - API
    - ESP32
    - RemoteTempSensor
---

Now we have [temperatures](https://www.repo-me-this.com/querying-sensors/remotetempsensors/) but they’re only locally accessible. Let’s fix that.

### API Development

You know how every job posts asks for API development experience? Here’s your chance!

Serving data through an API means two things – our ESP32 needs to be a web server and also needs to become fluent in JSON in a hurry.

```
<pre class="wp-block-code">```
#include <WebServer.h>
#include <ArduinoJson.h>
```
```

You guessed it – someone else did the heavy lifting. *WebServer* is included with the Arduino IDE and [*ArduinoJSON* ](https://arduinojson.org/)was contributed by Benoit Blanchon.

<figure class="wp-block-image size-large">![](https://www.repo-me-this.com/wp-content/uploads/2021/01/ArduinoJSON-library-1024x282.png)</figure>The server library makes API hosting easy! We need one global variable for the server.

```
<pre class="wp-block-code">```
WebServer server(80);

```
```

Next, we describe what “page” the server should host and how it gets the data to respond. We’re starting small with the temperature data served up by a *getTempF* method that’s just a placeholder for now.

```
<pre class="wp-block-code">```
void setupServer() {
  server.on("/temperature", getTempF);
  server.begin();
}

```
```

Finally, setup the server when the ESP32 turns on and continuously handle incoming requests.

```
<pre class="wp-block-code">```
void setup() {
  setupServer();
}

void loop() {
  server.handleClient();
}

```
```

Now that the server is ready, let’s talk about *getTempF*.

```
<pre class="wp-block-code">```
void getTempF() {
  create_json("Temperature", getTempValueF());
  server.send(200, "application/json", buffer);
}
```
```

Whenever the server is hit with an HTTP GET request for *http://\[address\]/temperature*, it needs to respond with a successful message (200) and the JSON-formatted temperature. Because I plan to expand this API in the future, I chose to create a generic JSON-formatting method and pass it temperature-specific values. First, a few variables.

```
<pre class="wp-block-code">```
StaticJsonDocument<250> jsonDoc;
char buffer[250];

```
```

Next, the method to clear the JSON document (so we don’t have to instantiate it every time) and build the reponse from the values passed as parameters. For temperature responses, *tag* is “Temperature” and *value* is a float provided by the *getTempValueF* function.

```
<pre class="wp-block-code">```
void create_json(char *tag, float value){
  jsonDoc.clear();
  jsonDoc["type"] = tag;
  jsonDoc["value"] = value;
  serializeJson(jsonDoc, buffer);
}

```
```

Once built, the JSON response returned is `{'type': 'Temperature', 'value': 'XX.XXX'}`. We’ll parse the temperature value out on the TempBuddy hub-side.  
  
Full disclosure – this was my first foray into JSON and I am not smart enough to come up with this on my own. A little Google-fu, though, and we have a winner. Our remote temperature stations now provide data upon request.

### Why Bother?

Why APIs? Why JSON? Why any of this?

Computers need to talk to each other. Our modern world is underpinned by servers that use many different protocols to share data to facilitate real-world impacts. APIs (Application Programming Interfaces) define how computers can access information by specifying how to request data and the format of data returned. APIs support encapsulation (hiding the internal complexity of a system) by providing a ‘simple’ public interface and support interoperability regardless of requestor type. Once these temperature sensors are active on my network, any device can query the sensors if they adhere to the API format.

[JSON ](https://www.json.org/json-en.html)– JavaScript Object Notation – is a “lightweight data-interchange format”. More importantly, it’s the defacto, agreed-upon method of passing data between servers and services. Like everything, there’s a bit of a learning curve but most languages have libraries that make JSON formatting easier. The advantage of JSON is that it encodes key-value pairs in objects and makes it easy to convert objects to text for transmission and back into objects on the other end.

- - - - - -

My code is publically available on my [TempBuddy GitHub repository](https://github.com/ildrummer/TempBuddy).