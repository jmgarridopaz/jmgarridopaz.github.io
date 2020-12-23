---
layout: default
page.title: Hexagonal Architecture
---

<div id="title">
{{ page.title }}
<hr>
<span class="credits right">Published on August 29, 2018 by <a href="{{ site.github.owner_url }}">{{ site.github.owner_name }}</a></span>
</div>

<p class="intro">This article is for sharing my knowledge about Ports & Adapters pattern (also known as Hexagonal Architecture), hoping it can be helpful to someone interested in this subject. Here I expose my understanding of this architecture pattern, after being reading articles, watching talks and learning about it, since I first heard of it two years ago. This is a conceptual article, rather than a practical one. I hope to publish a pragmatic article soon, explaining my implementation of this architecture using Java 9 modules; and I hope to upload a proof of concept to github as well</p>

#### TABLE OF CONTENTS

1. [Introduction](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc1)
2. [The Architecture](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2)  
2.1. [The Hexagon](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2-1)  
2.2. [Actors](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2-2)  
2.3. [Ports](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2-3)  
2.4. [Adapters](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2-4)  
2.5. [Summary](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2-5)  
2.6. [Example](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2-6)
3. [Configurable Dependency Pattern](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc3)
4. [From "Symmetrical Asymmetry" to "Asymmetrical Symmetry"](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc4)
5. [Misconceptions](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc5)  
5.1. [Layered Architecture](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc5-1)  
5.2. [Why a Hexagon?](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc5-2)  
5.3. [Ports Outside Adapters](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc5-3)
6. [Pros and Cons](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6)  
6.1. [Pros](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-1)  
6.1.1. [Testability improvement](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-1-1)  
6.1.2. [Maintainability improvement](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-1-2)  
6.1.3. [Flexibility](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-1-3)  
6.1.4. [Application immune to technology evolution](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-1-4)  
6.1.5. [Delay technological decisions](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-1-5)  
6.2. [Cons](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-2)  
6.2.1. [Complexity](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-2-1)  
6.2.2. [Build process performance](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-2-2)  
6.2.3. [Indirection and Mappings](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-2-3)  
7. [When to "Hexagonal this!"](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc7)
8. [Implementation steps](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc8)  
8.1. [Test driver adapters / Mock driven adapters](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc8-1)  
8.2. [Real driver adapters / Mock driven adapters](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc8-2)  
8.3. [Test driver adapters / Real driven adapters](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc8-3)  
8.4. [Real driver adapters / Real driven adapters](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc8-4)
9. [Links](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc9)

<div id="tc1"></div>
### 1.- INTRODUCTION

Ports and Adapters is an Object Structural Pattern coined by Dr. Alistair Cockburn, in an <a href="https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture" target="_blank">article</a> he wrote in 2005.
  
If you are thinking... "Isn't the article too old? How is it that it is still worth it nowadays, being software development a discipline in continous evolution where new technologies and frameworks arise every day and kill the one we used yesterday?" Well the answer is in the question. Ports & Adapters is a pattern that promotes __decoupling from technology__ and frameworks. So no, it isn’t too old. Good things are timeless. They are like wine, they get better as time passes by.

The __main idea of Ports & Adapters__ is to define the structure of an application so that it could be run by different kinds of clients (humans, tests cases, other applications, ...), and it could be __tested in isolation from external devices__ of the real world that the application depends on (databases, servers, other applications, ...).

Let’s see how to achieve this.

<div id="tc2"></div>
### 2.- THE ARCHITECTURE

In this section we will see the elements of Ports & Adapters pattern and the relationships between them.

<div id="tc2-1"></div>
#### 2.1.- THE HEXAGON

Ports & Adapters pattern depicts the __application as a closed area__.

The closed area chosen by Alistair Cockburn for drawing the application was a hexagon, that's why this pattern is also called __Hexagonal Architecture__.

Personally I prefer the name __Ports and Adapters__, because it refers to the key elements of the architecture, as we will see soon. On the other hand the figure you use for drawing the application isn’t important. However, it seems that the name Hexagonal Architecture is more popular.

The hexagon is the application itself. Saying "hexagon" and saying "application" is the same thing, from now on these terms will be used indistinctly.

__Inside the hexagon__ we just have the things that are important for the __business__ problem that the application is trying to solve.

The hexagon contains the business logic, with no references to any technology, framework or real world device. So the application is __technology agnostic__.

Ports & Adapters pattern says nothing about the structure of the inside of the hexagon. You can have layers... you can have components by feature... you can have spagheti code... you can have a Big Ball of Mud... you can apply DDD tactical patterns... you can have a single CRUD... it’s up to you.

<div id="tc2-2"></div>
#### 2.2- ACTORS

__Outside the hexagon__ we have any real world thing that the application interacts with. These things include __humans, other applications, or any hardware/software device__. They are the actors. We could say that actors are the __environment of the application__.

Actors are arranged around the hexagon depending on who triggers the interaction between the application and the actor:

- Actors on the left/top side are __Drivers, or Primary Actors__. The interaction is triggered by the actor. A driver is an actor that interacts with the application to achieve a goal. Drivers are the users (either humans or devices) of the application.

- Actors on the right/bottom side are __Driven Actors, or Secondary Actors__. The interaction is triggered by the application. A driven actor provides some functionality needed by the application for implementing the business logic.

There are two kinds of driven actors:

- _Repository_: The application can also obtain information from it, besides sending. For example, a database or any other storage device.

- _Recipient_: The application just sends information to it and forgets about it. For example, a SMTP server for sending emails.

The following picture shows some examples of actors in both driver and driven sides:

![Figure 1: Actors](/assets/images/hexagonalarchitecture/figure1.png)

Figure 1: Actors

These concepts of primary (drivers) and secondary (driven) actors evoke use cases.

So, for knowing which kind is the actor in an __application-actor interaction__, ask yourself __"who" triggers the conversation__. If the answer is "the actor" then it is a driver. If the answer is "the application" then the actor is a driven actor.

<div id="tc2-3"></div>
#### 2.3.- PORTS

The interactions between actors and the application are organized at the hexagon boundary by the reason why they are interacting with the application. Each __group of interactions with a given purpose/intention__ is a port.

Ports should be named according to what they are for, not according to any technology. So, in order to name a port, we should use a verb ending with "ing" and we should say __"this port is for ...ing something"__. For example:

- This driver port is for "adding products to the shopping cart".
- This driven port (repository) is "for obtaining information about orders".
- This driven port (recipient) is for "sending notifications".

Ports are the application boundary, in the picture a port is an __edge of the hexagon__. From the outside world, actors can only interact with the hexagon ports, they shouldn’t be able to access the inside of the hexagon. Ports are __interfaces__ that the application offers to the outside world for allowing actors interact with the application. So the application should follow the [Information Hiding Principle](https://en.wikipedia.org/wiki/Information_hiding). An important thing to remark is that __ports belong to the application__.

__Driver Ports__ offer the application functionality to drivers of the outside world. Thus, driver ports are said to be the __use case boundary__ of the application. They are __the API__ of the application.

Depending on the granularity we apply when grouping functionality, we can have a port interface with many use cases or with just a few. If we want to follow the [Single Responsibility Principle](https://en.wikipedia.org/wiki/Single_responsibility_principle), then we would have a lot of ports, each one for a use case. In this case a better option is applying the [command bus](https://matthiasnoback.nl/2015/01/a-wave-of-command-buses/) design pattern to the port, with a command handler for each use case. Same idea could be applied to queries, so that we would satisfy the [CQRS pattern](https://kalele.io/blog-posts/really-simple-cqrs/) as well. We would have a port for executing commands and another port for executing queries.

A __driven port__ is an interface for a functionality, needed by the application for implementing the business logic. Such functionality is provided by a driven actor. So driven ports are the __SPI (Service Provider Interface) required by the application__. A driven port would be like a [Required Interface](https://martinfowler.com/bliki/RequiredInterface.html).

<div id="tc2-4"></div>
#### 2.4.- ADAPTERS

Actors interact with hexagon ports through adapters using a specific technology. An __adapter__ is a software component that allows a __technology__ to interact with a port of the hexagon. Given a port, there may be an adapter for each desired technology that we want to use. Adapters are __outside the application__.

A __driver adapter uses a driver port interface__, converting a specific technology request into a technology agnostic request to a driver port.

Figure 2 shows some examples of driver adapters:

![Figure 2: Driver Adapters](/assets/images/hexagonalarchitecture/figure2.png)

Figure 2: Driver Adapters

- An _automated test framework_: Converts test cases into requests to a driver port.
- A _CLI (Command Line Interface)_: Converts text entered in a console.
- A _GUI of a desktop application_: Converts events triggered by graphical components.
- An _MVC web application_: The Controller receives from the View the action requested by the user, and converts it into a request to a driver port.
- A _REST API controller_: Converts REST API requests.
- An _event subscriber_: Converts messages (events) from a message queue to which the application is subscribed.

For each driver port, there should be at least two adapters: one for the real driver that is going to run it, and another one for testing the behaviour of the port.

A __driven adapter implements a driven port interface__, converting the technology agnostic methods of the port into specific technology methods.

Some examples of driven adapters are shown in the picture:

![Figure 3: Driven Adapters](/assets/images/hexagonalarchitecture/figure3.png)

Figure 3: Driven Adapters

- A _mock adapter_: It mimics the behaviour of a real secondary actor, for example an inmemory database.
- A _SQL adapter_: Implements a driven port for persisting data by accessing a SQL database.
- An _email adapter_: Implements a driven port for notifying people by sending an email to them.
- An _App-To-App adapter_: Implements a driven port for getting some data by requesting them to a remote application.
- An _event publisher_: Implements a driven port for publishing events by sending them to a message queue, so that they are available for subscribers.

For each driven port we should write at least two adapters: one for the real world device, and another one a [mock](https://en.wikipedia.org/wiki/Mock_object) that mimics the real behavior.

What an adapter does in the end is to convert an interface into another, so we could use the [Adapter Design Pattern](http://w3sdesign.com/?gr=s01&ugr=intent) to implement it.

Which adapter to use for each port is something that is configured at application startup. This is what gives flexibility to this pattern, so that we can __switch from a technology to another__ everytime we run the application. If we choose a test driver and mocks adapters for the driven ports, it allows the application to be tested in isolation.

<div id="tc2-5"></div>
#### 2.5.- SUMMARY

As we have seen, the elements of the architecture are:

- __The Hexagon__ ==> the application
  * Driver Ports ==> API offered by the application
  * Driven Ports ==> SPI required by the application
- __Actors__ ==> environment devices that interact with the application
  * Drivers ==> application users (either humans or hardware/software devices)
  * Driven Actors ==> provide services required by the application
- __Adapters__ ==> adapt specific technology to the application
  * Driver Adapters ==> use the drivers ports
  * Driven Adapters ==> implement the driven ports

Besides these elements, there will be a [Composition Root](http://blog.ploeh.dk/2011/07/28/CompositionRoot/) (also called __Main Component__ by Robert C. Martin, in his book [Clean Architecture: A Craftsman’s Guide to Software Structure and Design](https://www.amazon.com/Clean-Architecture-Craftsmans-Software-Structure/dp/0134494164)). This component will run at startup and it builds the whole system doing the following:

- It initializes and configures the environment (databases, servers, ...)
- For each driven port, it chooses a driven adapter implementing the port, and creates an instance of the adapter.
- It creates an instance of the application injecting the driven adapters instances into the application constructor.
- For each driver port:
  * It chooses a driver adapter that uses the port, and creates an instance of the adapter, injecting the application instance into the adapter constructor.
  * It runs the driver adapter instance.

<div id="tc2-6"></div>
#### 2.6- EXAMPLE

Simple application with a web interface, used by the employees of a company for assigning tasks to each other. When an employee is assigned a task, the application sends an email to him/her.

![Figure 4: An example - Task Assignement Application](/assets/images/hexagonalarchitecture/figure4.png)

Figure 4: An example (Task Assignement Application)

<div id="tc3"></div>
### 3.- CONFIGURABLE DEPENDENCY PATTERN

[Configurable Dependency](https://web.archive.org/web/20170624023207/http://alistair.cockburn.us/Configurable+Dependency) is a generalization of [Dependency Injection](https://en.wikipedia.org/wiki/Dependency_injection), also called [Inversion of Control](https://martinfowler.com/bliki/InversionOfControl.html).

It’s a new name coined by Gerard Meszaros. Alistair Cockburn considers it is a __better name for the pattern__, because it is a property, a characteristic. “Dependency Injection” is not a property, it is an action, it is the thing you do to achieve a configurable dependency. “Inversion of Control” is a double negative, like doing things wrong first and then inverting them.

Configurable Dependency fits the definition of what a pattern is:

__"A pattern for something are common characteristics of good examples of something"__

We could say that a configurable dependency is a dependency of an object on an interface. This interface would be an argument of the object constructor. Then, at runtime, a specific implementation of the interface is passed to the constructor when instantiating the object.

Hexagonal Architecture applies __Configurable Dependency Pattern to both driver and driven side__. In an actor-application interaction, the one that starts the conversation must know of the other, in order to know who to talk to, i.e. it must have a Configurable Dependency on an interface implemented by the other:

- _Driver side_: The conversation is started by the driver (primary actor), so __the driver adapter has a configurable dependency on the driver port__, which is an interface implemented by the application.

- _Driven side_: The conversation is started by the application, so __the application has a configurable dependency on the driven port__, which is an interface implemented by the driven adapter of the secondary actor.

So dependencies are as follows:

![Figure 5: Dependencies](/assets/images/hexagonalarchitecture/figure5.png)

Figure 5: Dependencies

- A driver adapter depends on the hexagon (it uses a driver port interface).
- A driven adapter depends on the hexagon (it implements a driven port interface)
- Hexagon depends on nothing, maybe just on utilities of the programming lenguage.

Configurable Dependency is the most important pattern that Ports & Adapters Architecture is based on, as it allows the __hexagon__ to be __decoupled from any techonology__. And this __decoupling is what make possible the main goal of the architecture__, that is, to have an application that can be run by several drivers and tested in isolation from recipients/repositories.

<div id="tc4"></div>
### 4.- FROM “SYMMETRICAL ASYMMETRY” TO “ASYMMETRICAL SYMMETRY”

Sounds quite confusing? If so, it’s ok, that’s what I wanted. Let’s explain it.

When the pattern was written in 2005, what the author wanted to show is that the asymmetry of the traditional layered architecture (user side vs data side), was in fact symmetrical. He did it by drawing an hexagon and putting both UI and database outside. Database is the same as UI, just technology, the application doesn’t know about it, like it doesn’t know about the UI.

Decoupling the application from the database is done by creating a driven port for it, and applying the Configurable Dependency Pattern by creating a driven adapter that implements the port. This way database depends on the application, and not the other way.

Then, when implementing examples of hexagonal architecture, the author realized that the symmetry was in fact asymmetrical, in the sense that the Configurable Dependency Pattern doesn’t apply the same way in the driven side than in the driver side.

In the driven side, the application must know about the driven adapter, because it’s the application who starts the dialog. It must know who to talk to.

On the other hand, in the driver side, it’s the driver adapter who must know about the application to start the dialog. The application doesn’t know which driver is driving it.

So what’s the conclusion? Is Ports & Adapters symmetrical or asymmetrical?

The corollary is that both symmetry and asymmetry coexists:

- _Symmetry_: All the adapters, both driver and driven, depends on the hexagon. __The application is technology agnostic in both sides__.

- _Asymmetry_: __Configurable Dependency implementation is different for each side__. In the driver side, the application doesn’t know about which adapter is driving it. But in the driven side, the application must know which driven adapter it must talk to.

<div id="tc5"></div>
### 5.- MISCONCEPTIONS

<div id="tc5-1"></div>
#### 5.1.- LAYERED ARCHITECTURE

Many articles I’ve read about this architecture say that it is a layered one. __They talk about three layers: domain, ports, adapters__. I don’t know why they say such thing, __the pattern says nothing about layers__. The pattern just says that we have the application (the hexagon), with a given number of ports, and that for each port we can have different adapters, each one using a technology. There’s no layers nowhere.

<div id="tc5-2"></div>
#### 5.2.- WHY A HEXAGON?

Some people wonder why a hexagon, perhaps __thinking that the number of edges is important__. Well the answer is no, __it doesn’t matter at all__. Number six isn’t important per se. Anyway, if you are curious, here you have some reasons by Alistair Cockburn on why he chose a hexagon:

- You have enough space for drawing ports and adapters as you need.
- The shape should evoke the inside/outside asymmetry rather than top/down or let/right. Then Squeare is not suitable. Pentagon, Heptagon, Octogon, ... too hard to draw. So Hexagon is the winner.

<div id="tc5-3"></div>
#### 5.3.- PORTS OUTSIDE ADAPTERS

I’ve seen many drawings where __they put ports outside adapters, so that an actor interacts directly with the port__, and then the adapter is the middleware between the port and the hexagon. This way:

Actor ===> Port ===> Adapter ===> Hexagon

__This is not correct at all__.

Ports are the hexagon boundary. In fact, they belong to the hexagon, they are part of it, they are interfaces of the hexagon. The actor interacts with the hexagon (the port) through an adapter. __The adapter is the middleware between the actor and the port__. The correct drawing is:

Actor ===> Adapter ===> (Port) Hexagon

And it is this way for both driver and driven sides. It is the symmetry of Port & Adapters pattern.

<div id="tc6"></div>
### 6.- PROS AND CONS

Modularity and application decoupling from technology are two important characteristics of Hexagonal Architecture. Such properties are the reason for both the advantages and the drawbacks. Here are some pros and cons I’ve found regarding of Hexagonal Architecture.

<div id="tc6-1"></div>
#### 6.1.- PROS

<div id="tc6-1-1"></div>
##### 6.1.1.- TESTABILITY IMPROVEMENT

The main benefit this architecture provides is the ability of __testing the application in isolation__ from external devices it depends on. This is achieved by doing two things:

- For each driver port, develop a test adapter that will run test cases against the port.
- For each driven port, develop a mock adapter.

Testing the hexagon in isolation can be useful for:

- Running __regression tests__. When source code changes for whatever reason (a new feature is added, a bug is fixed, ...), these tests are run to ensure that those changes don’t have side effects on any already existing functionality. To run these tests, the driver adapter uses an automated test framework.

- Doing [BDD (Behaviour Driven Development)](https://dannorth.net/introducing-bdd). For each driver port functionality, a set of acceptance criteria is defined by the user. The functionality will be considered “done” when all the acceptance criteria are met. These acceptance criteria are called scenarios, which will be the test cases run by the test adapter. For running these acceptance tests the adapter can use tools like [Cucumber](https://cucumber.io/).

Nat Pryce (co-author of the book [Growing Object-Oriented Software, Guided by Tests](https://www.amazon.com/gp/product/0321503627)) defines in his article [Visualising Test Terminology](http://www.natpryce.com/articles/000772.html) different kinds of tests related to hexagonal architecture:

- _Unit Tests_: For testing single objects inside the hexagon.

- _Integration Tests_: For testing adapters. They ensure that translation between ports and the outside world is done by the adapters correctly.

- _Acceptance Tests_: For testing driver ports, i.e. the hexagon in isolation. They check that application behaves as the user expects, meeting the acceptance criteria he/she previously defined for the use cases.

- _System Tests_: For testing the whole system, adapters and the hexagon together. They also test system deployment and startup.

<div id="tc6-1-2"></div>
##### 6.1.2.- MAINTAINABILITY IMPROVEMENT

Maintainable systems are those who are easy to modify. Hexagonal Architecture increases maintainability, because it provides separation of concerns and business logic decoupling, which makes it __easier to locate the code we want to modify__.

Application maintainability is a long term concept related to [technical debt](https://martinfowler.com/bliki/TechnicalDebt.html). The more maintainability the less technical debt. So, Hexagonal Architecture reduces the techical debt.

<div id="tc6-1-3"></div>
##### 6.1.3.- FLEXIBILITY

__Swapping between different technologies__ is easy. For a given port, you can have multiple adapters, each one using a specific technology. For choosing one of them, you just have to configure which adapter to use for that port. This configuration can be as easy as modifying an external configuration properties file. No source code modified, no re-compiling, no re-building.

Likewise, __adding a new specific technology adapter__ to a port can be done without touching the existing source code. The adapter is developed and compiled on his own. At runtime, it will be detected and plugged into the port.

<div id="tc6-1-4"></div>
##### 6.1.4.- APPLICATION IMMUNE TO TECHNOLOGY EVOLUTION

Technology evolves more frequently than business logic does. In applications where the business logic is tied to technology, you can’t do technology changes without touching business logic. This is no good, beacuse business should not change.

With hexagonal architecture, the technolgy you want to upgrade is located at an adapter outside the application. __You just have to change the adapter__. The __application itself remains immutable__ because it doesn’t depend on adapters.

<div id="tc6-1-5"></div>
##### 6.1.5.- DELAY TECHNOLOGICAL DECISIONS

When you start developing and coding, you can __focus just on business logic__, deferring decisions about which framework and technology you are going to use. You can __choose a technology later__, and code an adapter for it.

<div id="tc6-2"></div>
#### 6.2.- CONS

<div id="tc6-2-1"></div>
##### 6.2.1.- COMPLEXITY

A software project implementing Hexagonal Architecture has a complex structure, with __lot of modules and explicit dependencies__ defined between them. By modules I mean source code subprojects (e.g. Maven modules) to physically separate the different elements of the architecture.

At least, there will be one module for the hexagon, one module for each adapter, and one module for starting up the whole project. You would also have to define the dependencies between modules: hexagon depends on nothing, adapters depend on hexagon, and the starting-up depends on all of them.

If the programming language doesn’t allow the hexagon to expose just the ports, then there would be even more modules. You would have to split the hexagon into ports and implementation. Hexagon implementation and adapters would depend on ports, and ports would depend on nothing.

<div id="tc6-2-2"></div>
##### 6.2.2.- BUILD PROCESS PERFORMANCE

Due to the complexity we have just seen, if the project were too big, with lot of adapters, then the process of compiling, running tests, building all the modules together, and starting up the whole project would take a a lot of time.

<div id="tc6-2-3"></div>
##### 6.2.3.- INDIRECTION AND MAPPINGS

Decoupling application from technology through ports and adapters adds indirection, i.e. extra calls to methods when an adapter converts between port and specific technology interfaces. Besides that, a mapping between application and ouside world objects, may be needed.

<div id="tc7"></div>
### 7.- WHEN TO “HEXAGONAL THIS!”

Or you could say it this way if you want: “When should Hexagonal Architecture be applied to a project?”. Well, the answer may be the most hated answer by silver bullet hunters: “It depends”.

- For __small projects__, maybe “the cure is worse than the disease”, so that solving trivial problems doesn’t deserve the extra complexity added by the architecture.

- For __medium/large projects__, which are supposed to have a long life cycle, and are supposed to be modified many times during their lifetime, using Hexagonal Architecture will be worth it in the long-term.

Some might say that they don’t need Hexagonal Architecture if they know for sure that the __technology or frameworks used in the project are not going to change__ (e.g. because they are tied to a specific technology for some reason). Well, even in this case Ports and Adapters Pattern is useful, because you could add mock adapters to be used when devices/services the application depends on are not available, or you could add adapters for different runtime enviroments (development, test, production).

<div id="tc8"></div>
### 8.- IMPLEMENTATION STEPS

The starting point is the __application as a black box, with ports interfaces defined__ around it, at both driver and driven sides, for interaction with the ouside world.

At the beginning maybe you __still can’t define completely every driven port__, as you still don’t know exactly all the needings the application will have regarding the purpose of the port. Or maybe you missed some driven port. But these __needings will arise when developing the inside of the hexagon__, i.e. the implementation of the driver ports.

So, for developing a hexagonal application from scratch, here is the __order in which adapters are constructed and added__ at both driver and driven sides, until getting all done:

<div id="tc8-1"></div>
#### 8.1.- TEST DRIVER ADAPTERS / MOCK DRIVEN ADAPTERS

- _Driver side_: For each driver port, construct a test adapter, and implement the driver port driven by the tests. BDD can be used here for implementing driver ports, and the test cases would be [GWT scenarios](https://martinfowler.com/bliki/GivenWhenThen.html).

- _Driven side_: When implementing a driver port, you may need to use driven ports. In such a case, construct mock adapters for them.

Once you have implemented all the driver ports and mocked the driven ports, you are done.

At this point the __hexagon is complete__, with tests at driver side and mocks at driven side. The application __can be tested in isolation__.

__Next steps are for adding “real” driver and driven adapters__ for each port as you need, depending on the communication requirements with the outside world. For example, you may need Web UI and REST API adapters for the driver side; and SQL database and app-to-app adapters for the driven side. 

<div id="tc8-2"></div>
#### 8.2.- REAL DRIVER ADAPTERS / MOCK DRIVEN ADAPTERS

- _Driver side_: For each driver port, construct and add the “real” driver adapters you need. For example a Web UI, a REST API, ...

- _Driven side_: Keep the mock adapters you constructed in step (1).

This way you can test the new driver adapters.

<div id="tc8-3"></div>
#### 8.3.- TEST DRIVER ADAPTERS / REAL DRIVEN ADAPTERS

- _Driver side_: Configure each driver port with the test driver adapter constructed in step (1).

- _Driven side_: For each driven port, construct and add the "real" driven adapters you need. For example a database adapter, an email notification adapter, ...

This way you can test the new driven adapters.

<div id="tc8-4"></div>
#### 8.4.- REAL DRIVER ADAPTERS / REAL DRIVEN ADAPTERS

- _Driver side_: Configure each driver port with a “real” driver adapter constructed in step (2).

- _Driven side_: Configure each driven port with a “real” driven adapter constructed in step (3).

This way you can test the application end to end, including “real” adapters at both driver and driven sides.

At this point you are done. You can __configure every port with the adapter you wish__, and __run the application with any combination__ of ports and adapters configuration.

<div id="tc9"></div>
### 9.- LINKS

* Article by Alistair Cockburn defining Ports and Adapters Pattern:

<a target="_blank" href="https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture">https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture</a>

* My translation of Alistair Cockburn article into spanish:

<a target="_blank" href="https://jmgarridopaz.github.io/content/hexagonalarchitecturespanish.html">https://jmgarridopaz.github.io/content/hexagonalarchitecturespanish.html</a>

* Alistair in the Hexagone: a talk by Alistair Cockburn about Hexagonal Architecture:

<a target="_blank" href="https://www.youtube.com/watch?v=th4AgBcrEHA&amp;list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL">https://www.youtube.com/watch?v=th4AgBcrEHA&amp;list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL</a>

* Interview with Alistair Cockburn about Hexagonal Architecture:

<a target="_blank" href="https://web.archive.org/web/20170925184018/http://alistair.cockburn.us/Hexagonal+Architecture+FAQ">https://web.archive.org/web/20170925184018/http://alistair.cockburn.us/Hexagonal+Architecture+FAQ</a>

* An article I like about Hexagonal Architecture:

<a target="_blank" href="https://beyondxscratch.wordpress.com/2017/08/19/decoupling-your-technical-code-from-your-business-logic-with-the-hexagonal-architecture-hexarch">https://beyondxscratch.wordpress.com/2017/08/19/decoupling-your-technical-code-from-your-business-logic-with-the-hexagonal-architecture-hexarch</a>

<br/>

<div class="commentbox"></div>
