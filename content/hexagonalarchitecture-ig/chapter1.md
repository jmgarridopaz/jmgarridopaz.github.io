---
layout: default
---

<div id="title">
<h1>Hexagonal Architecture: An implementation guide</h1>
<p>Chapter 1: Application Design</p>
<hr>
<span class="credits right">Published on March 30, 2020 by <a href="{{ site.github.owner_url }}">{{ site.github.owner_name }}</a></span>
</div>

<p class="introquote">To Thomas Pierrain. You are not alone, all together we will win</p>

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

This article is the first one of a series showing a way of implementing an application conforming to Ports and Adapters pattern, also known as Hexagonal Architecture.

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
#### 3.1.- IDENTIFY ACTORS

1. Draw an hexagon. It represents the application, the business logic.

2. Put driver actors outside the hexagon, on the left side. Drivers are those who needs the application for achieving their goals. They trigger the communication with the application. As Alistair Cockburn says in his talk [Alistair in the Hexagone, think of the application as a thing resting in a quiet state](https://youtu.be/th4AgBcrEHA?list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL&t=1409), doing nothing. When the driver needs the application to perform an action, the driver would kick it and wake it up saying "Hey you, do this".

3. Put driven actors outside the hexagon, on the right side. Driven actors are those who are needed by the application for achieving application goals. The application triggers the communication with the driven actors.

In next chapters, we will see that the triggering of the conversation has to do with dependency knowledge, in the sense that the one who triggers the interaction has to know the dependency.

In the example application, BlueZone, the real world things we can identify are:

* Human users: Car driver, Parking inspector.
* Rate files.
* Permit database.
* Remote payment system.

At this early stage of development we don't care if actors are humans, files, databases, remote systems, etc. We just abstract the actor as a thing outside the application, it doesn't matter the technology it uses.

So we have the actors shown in Figure 1. Here I borrow a [picture for representing actors, drawn by Alistair Cockburn in his talk Alistair in the Hexagone](https://youtu.be/th4AgBcrEHA?list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL&t=1365), meaning that an actor can be a human being, a computer, or whatever... it doesn't matter.

![Figure 1: Hexagon and Actors](/assets/images/hexagonalarchitecture-ig/figure1-1.png)

<p class="intro">Figure 1: Hexagon and Actors</p>

<div id="tc3-2"></div>
#### 3.2.- IDENTIFY PORTS

Actors interacts with the hexagon through ports. A port groups the allowed interactions of the hexagon with a set of possible actors according to the purpose of the communication, using an declared program interface (API) which will be independent from the technologies any of the actors might use. ___An application port has a purpose, it is "for doing something", and we should name ports that way, even in source code___.

* __Driver ports__: For each driver actor, ask to yourself: What purpose does the driver actor want the application for? The answer will be the name of the port.  
In the example application:
  - Car drivers want the application for parking their cars. So there will be a driver port named ___"for parking cars"___.
  - Parking inspectors want the application for checking cars. So there will be a driver port named ___"for checking cars"___.

* __Driven ports__: For each driven actor, ask to yourself: What purpose does the application want the driven actor for? The answer will be the name of the port. Do this considering the driven actors as abstract repositories/recipients, regardless of their techonology.  
In the example application:
  - The application wants the "Rate Provider" for obtaining rates, in order to calculate prices of parking permits. So there will be a driven port named ___"for obtaining rates"___. The application will just say to the driven port: "Hey you! Gimme the rate of name ...", but it doesn't care if it comes from a file, or from another application, or from whatever device else.
  - The application wants the "Permit Storage" for storing the parking permits that car drivers request, and for querying them when parking inspectors want to check a car. So there will be a driven port named ___"for storing permits"___.
  - The application wants the "Payment System" for letting car drivers pay. So there will be a driven port named "___for paying___".

Maybe at this early stage of development, you still don’t know all driven actors needed by the application. But don’t worry about it, they will appear when you implement the business logic. You will realize that the application has to deal with some technologic device, or it has to use a functionality which is not under its responsability. In such a case you will have to abstract the purpose of the communication, and create a driven port for it, which should be named according to the "for doing something" pattern.

Figure 2 shows the identified ports added to the drawing. I write the name of the ports inside the hexagon to remark that they are part of it, they belong to the hexagon.

![Figure 2: Hexagon with Ports](/assets/images/hexagonalarchitecture-ig/figure1-2.png)

<p class="intro">Figure 2: Hexagon with Ports</p>

<div id="tc3-3"></div>
#### 3.3.- ADD ADAPTERS

Do you remember when I said forget about technologies? Think of an actor as an abstract thing outside the hexagon? Well now it's time for technologies, forget about abstractions.

By now we have the hexagon with its ports, and actors outside. Actors are real world devices using a technology. Ports don't know about technologies. So we have to add adapters for the __different technologies that actors use to interact with a port__.

For every port, we will have __at least two adapters__:

* What I call the _"Default adapter"_: It is the one that will be used when we want to test the hexagon in isolation.
  - In the driver side, the default adapter for a port is a _testing automation tool_ that will run test cases against the port.
  - In the driven side, the default adapter for a port is a _test double_. There are many kinds of test doubles: mocks, fakes, stubs, etc. A resource I recommend about this topic is ["XUnit Test Patterns" by Gerard Meszaros](http://xunitpatterns.com).
* What I call the _"Real adapter"_: It is the one that will be used when running the application in production environment.

In the example application we have the adapters you can see in Figure 3.

Then, more adapters could be added if we needed to. For example, if requirements change and users were allowed to access the application from a mobile app, we could add REST API adapters.

___Design alternatives for the driver side:___

I've chosen the design of Figure 4 for showing how to implement an application with multiple driver ports, adapters and actors, but of course there are other ways to design it. I mention here two of them if someone want to try:

1. Imagine that the requirements say that both driver actors (car drivers and parking inspectors) use just one web UI to access the application. Here the trick is to consider those actors as roles that a person can play. We would just have one actor (the person) playing two roles (car driver and parking inspector). There would be two web UI adapters, one for each port. A middleware component would route the web UI request to one of the adapters, depending on the requested operation. In this case ports would have to check the role of the user who did the request.

2. Having just one port instead of two. The port would be kind of a "use case bus". The hexagon would have use case handlers. This would be analog to a command bus, but instead of commands you would have use cases with a request and a response. This almost deserves an article on its own. Maybe the next one after this series.

<div id="tc3-4"></div>
#### 3.4.- THE WHOLE PICTURE

Putting it all together, we have the following design diagram that shows all the elements of the system: hexagon (with ports), actors and adapters. It will be a reference for next chapter, where we will see the project structure and dependencies between elements.

![Figure 3: Hexagon (with Ports), Adapters and Actors](/assets/images/hexagonalarchitecture-ig/figure1-3.png)

<p class="intro">Figure 3: Hexagon (with Ports), Adapters and Actors</p>

<div id="tc3-5"></div>
#### 3.5.- DRIVER PORTS

Finally, to implement the business logic in the next chapters, we need to define now the API of the hexagon, i.e. the operations offered by driver ports to the outside world.

A port groups a set of operations related to the purpose of the port. For every driver port, do the following:

1. Write down the sequence of interactions that take place when a driver actor uses the port. These interactions will help you to find out the operations that the port should offer.
2. Identify and write down the names of the operations you found.
3. For every operation, describe the input it receives, the output it returns, and what it does.

Once you've done this, you will have defined the API of the hexagon.

Here I will put the API definition in Java, but you can do it in natural language, since this is just the contract design.

In the example application:

___"for parking cars" port:___

1. Interactions of the actor (A) with the port (P):  
A: Requests a parking permit  
P: Returns all the rates  
A: Enters the car plate, the rate name, the ending datetime of the period, and the payment card  
P: Issues the parking permit  

2. Operations of the port:  
getAllRates, issuePermit  

3. Description of the operations:

~~~
public interface ForParkingCars {

	/**
	 * Returns the information of all the available rates in the city.
	 * 
	 * @return	a list of RateInfo objects
	 */
	public List<RateInfo> getAllRates();

	
	/**
	 * Issues a permit for a car parked at a regulated area, valid until a datetime, paying it with a card.
	 * Returns a ticket with the permit information.
	 * 
	 * First the permit price is calculated, depending on the number of minutes of the permit period,
	 * according to the rate of the area where the car is parked.
	 * Then, permit price is charged to the payment card.
	 * And finally the permit is stored.
	 * 
	 * @param	clock			Clock to get current datetime from, since it will be the starting datetime of the permit period
	 * @param	carPlate		Car plate of the car we want to get the permit for
	 * @param	rateName		Rate name of the regulated area where we want to park the car
	 * @param	endingDateTime	A datetime in the future, that will be the expiration date of the permit period
	 * @param	paymentCardInfo	Information about the card to charge the permit price to.
	 * @return					an PermitTicket object
	 * 
	 */
	public PermitTicket issuePermit ( Clock clock, String carPlate, String rateName, LocalDateTime endingDateTime, PaymentCardInfo paymentCardInfo );

}
~~~

Definition of the data that port operations manage:
~~~
public interface RateInfo {
	
	public String name();
	
	public BigDecimal amountPerHour();
	
	public String currencySymbol();
	
	public int minNumberOfMinutes();
	
	public int maxNumberOfMinutes();
	
	public Map < DayOfWeek, List<LocalTime> > timeTable();

}

public interface PaymentCardInfo {
	
	public String number();
	
	public String cvv();
	
	public YearMonth expirationDate();

}

public interface PermitTicket {

	public String code();
	
	public String carPlate();
	
	public String rateName();
	
	public LocalDateTime startingDateTime();
	
	public LocalDateTime endingDateTime();
	
	public BigDecimal priceAmount();
	
	public String priceCurrencySymbol();
	
}
~~~

___"for checking cars" port:___

1. Interactions of the actor (A) with the port (P):  
A: Enters the car plate and the rate name  
P: Checks whether the car is parked correctly  

2. Operations of the port:  
isParkedCorrectly  

3. Description of the operations:

~~~
public interface ForCheckingCars {
		
	/**
	 * Checks whether a car parked in a regulated area has an active permit.
	 * A permit is active if the current datetime is before the ending datetime of the permit period.
	 * 
	 * @param	clock		Clock to get current datetime from
	 * @param	carPlate	Car plate of the car that we want to check
	 * @param	rateName	Rate name of the regulated area where the car to check is parked
	 * @return				true if there exists an active permit, false otherwise
	 */
	public boolean isParkedCorrectly ( Clock clock, String carPlate, String rateName );
	
}
~~~

I want to remark here that the clock as a parameter is useful for mocking the current datetime when testing. But if you have many methods that need the clock, it has the drawback that you should pass it to all of them. To avoid this, it would be better to create a driven port for the clock.

<div id="tc4"></div>
### 4.- ANALOGY WITH USE CASES

It is worth to mention here the paralelism between hexagonal architecture and use cases world.

Reading the article ["Structuring use cases with goals" by Alistair Cockburn](https://web.archive.org/web/20170620145208/http://alistair.cockburn.us/Structuring+use+cases+with+goals), I realized that hexagonal architecture is tightly related to use cases. I describe here some analogies I've found:

* In use cases there are different levels of abstraction for goals: summary goals, user goals (sea level) and subfunctions. At high level (summary goals) you have multiple primary actors. This is for helping you to find out better the functionality that the system might offer to the users. Then at lower levels (user goals and subfunctions) you have just one actor.  
In hexagonal architecture, the hexagon (the application) as a whole is needed to achieve summary goals, and it is used by multiple driver actors. Then driver ports are at a lower level, for achieving user goals. And subfunctions are operations of a driver port.  
A topic related to this is "How primary actors can be important and unimportant at the same time", that Alistair Cockburn explains in his book "Writing effective use cases".

* In use cases we have this figure, that shows how actors communicate with a "goal - interaction - responsability" chain:

![Figure 4: Actor-to-actor communication model](/assets/images/hexagonalarchitecture-ig/figure1-4.png)

<p class="intro">Figure 4: Actor-to-actor communication model</p>

Hexagonal architecture fit this model: Driver actors are primary actors, the hexagon (the application) is the "system under design", and driven actors are secondary actors. Driver ports group use cases.

Actors have responsabilities, which are performed by fixing goals. To achieve a goal, the actor has to do some actions. An action triggers the interaction with another actor. This interaction is a call to the other actor responsability. And so on.

In hexagonal architecture, interactions link driver actor goals with application responsabilities. The operations that the application offer at a driver port are application responsabilities related to the purpose of the port.

So the application is structured grouping responsabilities in ports acording to a purpose, which is the actor goal.

In the right side of the hexagon... To perform its responsabilities, the hexagon will have to achieve goals. For achieving these goals it might need to interact with external systems (driven actors) through driven ports. These interactions link application goals with driven actor responsabilies.

The hexagon would be like any other actor. It has goals and to achieve them it might need to interact with another actor.

<div id="tc5"></div>
### 5.- LINKS

* Ports and Adapters Pattern (aka Hexagonal Architecture), by Alistair Cockburn:

[https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture](https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture)

* My conceptual article about Ports and Adapters Pattern (aka Hexagonal Architecture):

[https://jmgarridopaz.github.io/content/hexagonalarchitecture.html](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html)

* "XUnit Test Patterns", by Gerard Meszaros:

[http://xunitpatterns.com](http://xunitpatterns.com)

* "Structuring use cases with goals" article, by Alistair Cockburn:

[https://web.archive.org/web/20170620145208/http://alistair.cockburn.us/Structuring+use+cases+with+goals](https://web.archive.org/web/20170620145208/http://alistair.cockburn.us/Structuring+use+cases+with+goals)

<br/>

<hr/>

<div class="commentbox"></div>
