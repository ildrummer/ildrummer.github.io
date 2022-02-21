---
id: 44
title: 'Getting Started with ESP32'
date: '2021-01-27T10:27:03+05:00'
author: 'Adam Leone'
layout: post
guid: 'http://www.repo-me-this.com/?p=44'
permalink: /getting-started-with-esp32/remotetempsensors/
image: 'http://www.repo-me-this.com/wp-content/uploads/2021/01/arduino-ide-splash.png'
categories:
    - RemoteTempSensors
tags:
    - ESP32
    - RemoteTempSensor
---

Right. We have our hardware. The wires are hooked up to power and program the ESP32. What now?

### Arduino IDE

ESP32s run on Arduino C and use the [Arduino IDE](https://www.arduino.cc/en/Main.Software). It’s actually better than it sounds – the IDE is simple, only text editing functions for the code, but compiles and deploys your program to your microcontrollers and has a great library manager so we can utilize the hard work of smarter people. Arduino C is a stripped-down version of the C language and lacks many of the niceties of higher level languages but serves well enough for programs running close to the metal.

### Priority #1: WiFi

This entire project hinges on achieving sensor “remoteness” though my home network. We can validate ESP32 connectivity in two ways – feedback from the serial connection and seeing the ESP32 show up on the router’s client list.

To connect:

1. Comment! Lay the groundwork for future-me to understand this file.

```
<pre class="wp-block-code">```
/*
 * ildrummer
 * Oct 2020
 * 
 * Use an ESP32-CAM with a DS18B20 waterproof temperature probe to collect
 * ambient room temperature.
 * */
```
```

2\. Import a smarter person’s hard work. Use the Arduino IDE’s library manager (Tools -&gt; Library Manager) to search for and download the WiFi module. Add it to your code.

```
<pre class="wp-block-code">```
#include <WiFi.h>
```
```

3\. Declare some variables for the network. I’m settting up a static IP address so my server can reliably poll each remote temperature station for data.

```
<pre class="wp-block-code">```
// WiFi parameters
const char *wifiName = "network_name";
const char *password = "******";
IPAddress local_IP(192, 168, 1, 12);
IPAddress gateway(192, 168, 1, 1);
IPAddress subnet(255, 255, 0, 0); 

```
```

4\. Break out the connection details into a function. Provide feedback through the serial connection for debugging purposes.

```
<pre class="wp-block-code">```
void connectToWifi(){
  Serial.print("Connecting to ");
  Serial.println(wifiName);
  
  if (!WiFi.config(local_IP, gateway, subnet)){
    Serial.println("Static IP failed to configure");
  }
  
  WiFi.begin(wifiName, password);
  while (WiFi.status() != WL_CONNECTED){
    delay (500);
    Serial.print(".");
  }
  Serial.println();
  Serial.print("WiFi ready. Use 'http://");
  Serial.print(WiFi.localIP());
  Serial.println("' to connect");
}

```
```

5\. Call *connectToWifi* from the standard *setup()* method that each Arduino/ESP32 program includes.

```
<pre class="wp-block-code">```
void setup() {
  // Start the Serial Monitor
  Serial.begin(115200);
  Serial.println("I'm back online");

  connectToWifi();
}

```
```

6\. Periodically check to make sure the EPS32 is still connected and correct an unconnected state. Optional: print a message indicating that the microcontroller is working.

```
<pre class="wp-block-code">```
void loop() {
  if (WiFi.status() != WL_CONNECTED){
    connectToWifi();
  }
 
  Serial.print("Looping...");
  delay(2000);
}

```
```

### Test

Once this program is compiled and uploaded to the ESP32, remove the grounding wire that allows for programming, reset the board, and you should see the Arduino IDE serial monitor come to life! If your ESP32 connects successfully, use this program to any other ESP32s you’re using to ensure they all function.

### Troubleshooting

- The WiFi hardware draws a lot of power. If you’re powering your ESP32 with 3.3 volts and see the serial output die or your microcontroller reset, try powering it with 5 volts. That solved a brown-out issue for me.

- Make sure the static IP address you’re trying to assign is free (check your router’s client list).

- - - - - -

This code is publically available on my [TempBuddy GitHub repository](https://github.com/ildrummer/TempBuddy).