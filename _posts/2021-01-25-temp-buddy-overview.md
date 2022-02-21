---
id: 33
title: 'Temp buddy &#8211; a remote temperature system'
date: '2021-01-25T10:03:52+05:00'
author: 'Adam Leone'
layout: post
guid: 'http://www.repo-me-this.com/?p=33'
permalink: /temp-buddy-overview/remotetempsensors/
image: 'http://www.repo-me-this.com/wp-content/uploads/2021/01/DSCF1364-1200x800.jpg'
categories:
    - RemoteTempSensors
tags:
    - RemoteTempSensor
---

My house has significant temperature differences in the winter between my office in the front, sun-facing side of the house and my bedroom in the back. Walking between the two rooms, I can experience more than a 5 degree temperature change! After some reflection, my cooler bedroom is probably attributable to a single HVAC vent and its shared (and likely uninsulated) wall with my unheated garage. That said, let’s not let logic and reason stop us from pursuing an interesting project!

### Project Overview

My starting vision is to measure temperatures from different rooms using remote sensors units, collect them using a central server, save them to a database for historical analysis, and display the results in a webpage so I can review current and trending temperatures on my phone while I poop. That’s right – I feel lost in the bathroom without my phone. Send help. When I asked my wife what I should call this system that’s been taking me away from her, she declared without hesitation, “*Temp Buddy*“. Temp Buddy it is.

### Why This?

I learn best when passion intersects with practical effects. Although my passion might flare out later, this project is practical and can be used in many situations. It’s also a fresh opportunity to make a “professional” project as opposed to my old hack-jobs and can incorporate a broad spread of concepts, techniques, and systems I want to learn:

- Version Control
- Configuration files
- Logging to files
- API development
- Multithreading
- Error handling – graceful degredation
- Graphing libraries
- Website development and hosting
- Unit testing

### Development Philosophy

I spent a few hours a day for two weeks getting the bones of this system set up and will be dedicating a few hours a weekend to it moving forward. This is a learning project and, as such, may never reach completion as originally envisioned. I’m also in the process of moving to start a new job (negating the need for a temperature sensing system), which will slow me down. Expect periodic posts under the tag **\#RemoteTempSensors** as I complete an aspect of the system, discover a new technique, or find a poorly documented corner of internet knowledge that I can fill. These posts will be written in an instructuctional “you” voice as if you, the reader, planned to follow in my footsteps.

### Source Code

My code is publically available on my [TempBuddy GitHub repository](https://github.com/ildrummer/TempBuddy) and will be periodically updated as I complete new aspects of this project.