---
id: 54
title: 'Querying Sensors'
date: '2021-01-28T07:54:50+05:00'
author: 'Adam Leone'
layout: post
guid: 'http://www.repo-me-this.com/?p=54'
permalink: /querying-sensors/remotetempsensors/
image: 'http://www.repo-me-this.com/wp-content/uploads/2021/01/pexels-photo-4429561-1200x800.jpeg'
categories:
    - RemoteTempSensors
tags:
    - ESP32
    - RemoteTempSensor
---

In previous posts, we [assembled ](https://www.repo-me-this.com/remote-temp-sensor-hardware/remotetempsensors/)remote temperature stations and validated that the microcontroller functioned and [connected to wifi](https://www.repo-me-this.com/getting-started-with-esp32/remotetempsensors/).

### Priority #2: Accessing Sensor Data

Each sensor you connect to a microcontroller will likely require slightly different access methods and code. Fear not, however. Unless you’re working on the electronic frontier in a lab or with a home-brewed sensor, someone has been there first and likely wrote a library for you to use. Our DS18B20 temperature sensors require two – [*OneWire* ](https://www.pjrc.com/teensy/td_libs_OneWire.html)by Jim Studt et. al. and [*DallasTemperature* ](https://github.com/milesburton/Arduino-Temperature-Control-Library)by Miles Burton and friends. *OneWire* abstracts accessing multiple sensors on one bus and *DallasTemperature* was specifically written for the DS18B20 temperature sensors and to smooth out some of *OneWire’s* rough edges.

<figure class="wp-block-image size-large">![](https://www.repo-me-this.com/wp-content/uploads/2021/01/OneWire-and-DallasTemperature-Libraries-1024x580.png)<figcaption>OneWire and DallasTemperature Libraries</figcaption></figure>```
<pre class="wp-block-code">```
#include <OneWire.h>
#include <DallasTemperature.h>

```
```

Next, we declare some instance variables and chain them together. The *OneWire* constructor needs to know which GPIO pin to access for temperature data and the *DallasTemperature* sensor constructor accepts the *OneWire* instance.

```
<pre class="wp-block-code">```
// GPIO where the DS18B20 is connected to
const int oneWireBus = 13;     

// Setup a oneWire instance to communicate with any OneWire devices
OneWire oneWire(oneWireBus);

// Pass our oneWire reference to Dallas Temperature sensor 
DallasTemperature sensors(&oneWire);

```
```

Next, the ESP32 needs to set GPIO pin 13 to the proper state. I found (though much hair-pulling frustration) that the ESP32-CAM wifi hardware shares some interest in pin 13. To deconflict its use, we set it to *OUTPUT* initially. Kind of like *NULL* (take that, hardware!). After the wifi is configured, we set pin 13 to *INPUT* so *DallasTemperature* can read the temperature data. Finally, tell the *DallasTemperature* instance

```
<pre class="wp-block-code">```
void setup() {

  pinMode(oneWireBus, OUTPUT); 
  
  connectToWifi();

  pinMode(oneWireBus, INPUT);
  
  // Start the DS18B20 sensor
  sensors.begin();

}

```
```

“Get to the part where we measure temperatures, already!” Right. All you do is … request the temperature from *DallasTemperature*. Well, first you tell the instance to communicate with the sensor. Once that’s complete, pull the current temperature in your desired format (not sure if Kelvin is an option…).

```
<pre class="wp-block-code">```
float getTempValueF(){ 
  sensors.requestTemperatures(); 
  return sensors.getTempFByIndex(0);
}

float getTempValueC(){
  sensors.requestTemperatures(); 
  return sensors.getTempFByIndex(0);
}
```
```

Now we’re rolling in the temperatures, baby!

- - - - - -

My code is publically available on my [TempBuddy GitHub repository](https://github.com/ildrummer/TempBuddy).