---
id: 73
title: 'ESP32 Clean-up Items'
date: '2021-01-29T10:06:41+05:00'
author: 'Adam Leone'
layout: post
guid: 'http://www.repo-me-this.com/?p=73'
permalink: /esp32-clean-up-items/remotetempsensors/
categories:
    - RemoteTempSensors
tags:
    - ESP32
    - RemoteTempSensor
---

We all know computers can encounter errors. Let’s preempt that.

### Daily Reboot

I’m a fan of periodic reboots in autonomous systems because it prevents errors from piling up and impacts me very little. For this system, we’ll have the remote temperature sensors reboot once per day. They take about 20 seconds to restart and connect to WiFi. I plan to havce the central server ask for temperature data once per minute, so we have a 33% chance of missing one minute of data per 24 hours. That’s acceptable.

The best way I could find to do this was to simply define a global variable containing the number of seconds in a day.

```
<pre class="wp-block-code">```
#include <time.h>

// 86400000 for 24 hours
unsigned long resetInterval = 86400000;
```
```

*millis()* is a *time* library function that returns the number of milliseconds a system has been turned on.

```
<pre class="wp-block-code">```

  // Reset once per interval (default to once/day)
  if (millis() > resetInterval){
    restart();
  }
```
```

Finally, tell the serial output we’re restarting (in case someone’s listening) and reboot.

```
<pre class="wp-block-code">```
void restart (){
  Serial.println("Restarting");
  ESP.restart();
}
```
```

I tested this with a reboot every ten seconds and recommend you do to to validate that it works and your ESP32 connects to WiFi reliably.

- - - - - -

More content to follow as I tweak this remote temperature system.

- - - - - -

My code is publically available on my [TempBuddy GitHub repository](https://github.com/ildrummer/TempBuddy).