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

* When requesting a permit, the car driver won't be warned about how much will it costs before issuing it. The application will charge the price to the payment card and will issue the permit.

* Payments are performed calling the remote system synchronously. It could be possible that a payment was succesful, but then the permit issuing failed. In real life, an scheduled process would be launched to refund these transactions. In the example application, this refund process won't be implemented.

* Regarding parking inspectors, the example application will just implement the car checking. Parking fine warnings won't be managed by the application.


(3) DESIGNING PROCESS.
