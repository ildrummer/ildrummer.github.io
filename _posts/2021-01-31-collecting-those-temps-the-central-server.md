---
id: 79
title: 'Collecting those temps &#8211; The Central Server'
date: '2021-01-31T09:52:32+05:00'
author: 'Adam Leone'
layout: post
guid: 'http://www.repo-me-this.com/?p=79'
permalink: /collecting-those-temps-the-central-server/remotetempsensors/
image: 'http://www.repo-me-this.com/wp-content/uploads/2021/01/pexels-photo-1089438-1200x800.jpeg'
categories:
    - RemoteTempSensors
tags:
    - Python
    - RaspberryPi
    - Server-side
---

The temperature hardware is set and the data accessible through my local network. Now to build a central system to collect, store, and display those temperatures that is scalable and modular.

### Designing for Loose Coupling

A key principle of Object-Oriented Design (OOD) is that components should be loosely-coupled, or maximally independent. Coupling is encoded when one object knows about the inner workings of another or when classes depend on the state of shared variables. My goal is to build a loosely coupled system with clearly defined interfaces and boundaries such that I can replace any module/component without needing to revisit the rest of the system. Methods of achieving this is with polymorphism, abstract classes, and interfaces for more complicated problems. This temperature system is simple enought that a single class per logical object should suffice. If that changes, I’ll refactor.

### Components

This system collects temperatures, stores them, and displays them according to user preference. Therefore, the components to build are:

- Temperature Collector
- Database Handler
- Graphics Builder
- User Interface

The loose coupling comes from the clearly defined purpose of each and minimal interactions/overlap. The temperature collector module polls remote stations and uses the database handler to store the temperatures. The user interface handles user input and tells the graphics builder what to build/display. The graphics builder pulls data from the database handler and serves it to the user through the user interface.

### Test-Driven Development

This project is my first foray into test-driven development. So far, it hasn’t been smooth or as seamless as [Uncle Bob’s demonstration](https://www.youtube.com/watch?v=58jGpV2Cg50&t=2628s), but I’m accomplishing the lesser goal of unit-testing every function of a module in its positive and negative aspects (this true statement is true, this false statement isn’t true). Thus far, this practice (even in infancy) has been **enormously helpful** because I know immediately when I break something and can pinpoint the right place to begin troubleshooting.

###  Final Decisions

Based on these principles, the last decisions to make include programming language(s) and hardware. Notice that the programming language for the central node of the temperature system is almost an afterthought. The design process is ideally independent of implementation language or hardware in order to focus on best design practices without adding artificially constraints. Once the design is finished, a suitable language(s), appropriate supporting libraries, and/or an ideal framework may be chosen. Implementation decisions sometimes require prototyping – writing throwaway code to test aspects of a concept, language, or framework – to find the best solution for the design.

I am going to write my centeral server in **Python**. I’ve dabbled but haven’t dedicated enough time to learn this popular language and think it deserves more of my attention. Python comes with the **PyTest** module for unit testing and test-driven development. My host will be a **RaspberryPi 2B** I use for odd projects. Because that runs Raspbian, a stripped down flavor of Debian Linux, I’ll display the temperature data to the user through an **Apache server** and will likely write some **HTML** for the website and user controls. RPis don’t have a lot of computing power so I’ll implement data persistence using **SQLite**, a serverless, single file database.

In the coming posts. I’ll walk through each module, discussing implementation decisions, testing, and the pitfalls I encountered. I welcome feedback on this system, as it’s still incomplete and the learning never truly needs to end.

- - - - - -

My code is publically available on my [TempBuddy GitHub repository](https://github.com/ildrummer/TempBuddy).