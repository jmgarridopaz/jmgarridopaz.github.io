---
layout: default
---

<div id="title">
<h1>Hexagonal Architecture: An implementation guide</h1>
<p>Chapter 1: Application Design</p>
<hr>
<span class="credits right">Published on March 29, 2020 by <a href="{{ site.github.owner_url }}">{{ site.github.owner_name }}</a></span>
</div>

<p class="introquote">To Thomas Pierrain. You are not alone, we are all fighting with you</p>

#### TABLE OF CONTENTS

1. [Introduction](#tc1)
2. [The example application](#tc2)
3. [Designing process](#tc3)  
3.1. [Identify actors](#tc3-1)  
3.2. [Identify ports](#tc3-2)  
3.3. [Add adapters](#tc3-3)  
3.4. [The whole picture](#tc3-4)  
3.5. [Driver ports](#tc3-5)
4. [Analogy with use cases](#tc4)
5. [Links](#tc5)

<div id="tc1"></div>
### 1.- INTRODUCTION

This article is the first one of a series showing how to implement an application conforming to Ports and Adapters pattern, also known as Hexagonal Architecture.

This series has a practical sense. It is supposed that the reader already has theorical knowledge about concepts regarding hexagonal architecture, like actors, ports, adapters, etc. Otherwise, in the Links section you can find resources to read about it.

In this chapter, we will see a process for designing the application, explaining how to find out the different elements of the architecture for the application we want to build.

First of all, I describe the application that will be used as an example along this series. And finally, as a bonus track, I will show an outline of the paralelism between hexagonal architecture and use cases.

<div id="tc2"></div>
### 2.- THE EXAMPLE APPLICATION

We will develop an example application called BlueZone, that allows car drivers to pay remotely for parking cars at regulated parking areas in a city, instead of paying with coins using parking meters.

When car drivers want to park their car, they have to get a parking permit, providing the ending datetime of the permit period. The starting datetime is the one at which the permit is requested. The application calculates the price of the permit, according to the number of minutes of the period, applying the rate of the area where the car is parked. Payments are done with cards, using a remote system.

Once car drivers have paid, the application issues the parking permit, that allows them to park the car during the period of time they have paid for. Permits are stored in a database.

The application won't allow to get a permit for a rate if there already exists an active permit for the same rate. The car driver will have to wait for the active permit to expire in order to get another one.

Car drivers will access the application using a Web UI (User Interface).

At any time, parking inspectors can check the plate of any car parked at a regulated area. If the car doesn't have any active permit for the rate of the area, they will issue a parking fine warning.

Parking inspectors will access the application using a terminal with a CLI (Command Line Interface).

About rates:

A regulated parking area has a rate, and there maybe different rates in a city. Rates are provided by external data files. A rate includes the following information:

* Name of the rate.

* Amount of money to pay per hour.

* Minimum and maximum number of minutes allowed to pay for.

* Timetable that defines time intervals when the rate applies, depending on the day of the week. Out of those intervals, parking is for free.

For the sake of simplicity, we'll do the following assumptions:

* When requesting a permit, the car driver won't be warned about how much it will cost before issuing it. The application will charge the price to the payment card and will issue the permit.

* Payments are performed calling the remote system synchronously. It could be possible that a payment was succesful, but then the permit issuing failed. In real life, an scheduled process would be launched to refund these transactions. In the example application, this refund process won't be implemented.

* Regarding parking inspectors, the example application will just implement the car checking. Parking fine warnings won't be managed by the application.

<div id="tc3"></div>
### 3.- DESIGNING PROCESS

In this section we will find out the elements of the architecture for the application that we are going to implement, and we will define in detail the driver ports, i.e. the API of the application.

The steps of this designing process are:

1. Identify actors
2. Identify ports
3. Add adapters
4. The whole picture
5. Driver ports

So that finally we should have the following information, that could be considered the output of this process and the starting point for the next chapters:

* A diagram showing the components of the system: Hexagon (with ports), actors and adapters.
* Driver ports in detail: Input, output and description of driver ports operations.

We will not define driven ports in detail yet, since we will find out their operations later on, as we develop the business logic.

Now let's see these design steps...

<div id="tc3-1"></div>
### 3.1.- IDENTIFY ACTORS

1. Draw an hexagon. It represents the application, the business logic.

2. Put driver actors outside the hexagon, on the left side. Drivers are those who needs the application for achieving their goals. They trigger the communication with the application. [As Alistair Cockburn says in his talk Alistair in the Hexagone, think of the application as a thing resting in a quiet state, doing nothing. When the driver needs the application to perform an action, the driver would kick it and wake it up saying "Hey you, do this"](https://youtu.be/th4AgBcrEHA?list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL&t=1409).

3. Put driven actors outside the hexagon, on the right side. Driven actors are those who are needed by the application for achieving application goals. The application triggers the communication with the driven actors.

In next chapters, we will see that the triggering of the conversation has to do with dependency knowledge, in the sense that the one who triggers the interaction has to know the dependency.

___In the example application___, BlueZone, the real world things we can identify are:

* Human users: Car driver, Parking inspector.
* Rate files.
* Permit database.
* Remote payment system.

At this early stage of development we don't care if actors are humans, files, databases, remote systems, etc. We just abstract the actor as a thing outside the application, it doesn't matter the technology it uses.

So we have the actors shown in Figure 1. Here I borrow a [picture for representing actors, drawn by Alistair Cockburn in his talk Alistair in the Hexagone, meaning that an actor can be a human being, a computer, or whatever... it doesn't matter](https://youtu.be/th4AgBcrEHA?list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL&t=1365).

Figure 1: Hexagon and Actors

<div id="tc3-2"></div>
### 3.2.- IDENTIFY PORTS
