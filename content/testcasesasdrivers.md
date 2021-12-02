---
title: Test Cases as Drivers - Hexagonal Architecture
custom_article_title: Hexagonal Architecture
custom_article_subtitle: Test Cases as Drivers
custom_chapter_title:
custom_published_date: December 2, 2021
custom_author_url: https://github.com/jmgarridopaz
custom_author_name: Juan Manuel Garrido de Paz
layout: default
---

#### TABLE OF CONTENTS

1. [Introduction](#tc1)
2. [Drivers](#tc2)
3. [Test Cases](#tc3)
4. [The Forbidden Path](#tc4)
5. [Alternative Paths](#tc5)  
   5.1. [Path Via Hexagon](#tc5-1)  
   5.2. [Path Via "Dependency Configurator"](#tc5-2)
6. [Parallelism with Test Patterns](#tc6)  
   6.1. [Round Trip Test](#tc6-1)  
   6.2. [Back Door Manipulation](#tc6-2)
7. [Conclusion](#tc7)
8. [Links](#tc8)

<div id="tc1"></div>
### 1.- INTRODUCTION.

<a target="_blank" href="https://youtu.be/th4AgBcrEHA?list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL&t=1748">"The test case is the first user of the system"</a> (Alistair Cockburn quoting Ron Jeffries at "Alistair in The Hexagone" talk, Paris, june 2017).

The "Intent" section of Hexagonal Architecture pattern says:
"Allow an application ... to be ... tested in isolation from its eventual run-time devices and databases".

So, tests are important, aren't they? Testability is one of the main advantages of an hexagonal application.

Test cases are also a driver of the application, like any other one (for example, a human user). But there are some additional permissions that test cases need to have. Do you want to know which ones? I invite you to continue reading this article to discover it.

<div id="tc2"></div>
### 2.- DRIVERS.

Regarding Hexagonal Architecture, a driver (or primary actor) is an actor that triggers an interaction with the application.

An adapter in between the driver and the application, translates the technology dependant request done by the driver into a technology neutral call to a driver port of the application.

So a driver adapter "uses" a driver port, which is an interface belonging to the API of the application.

An example of driver is a human user, with a GUI as driver adapter for using the application at a port.

The driver adapter "depends on" (uses) the hexagon port. It receives the port of the already built hexagon for using it, but it doesn't know how to build the hexagon, and it doesn't know of maybe driven actors needed by the hexagon to perform the functionality requested by the driver.

Initializing driven actors, building the hexagon from them, and passing the hexagon to the driver adapter, are responsibilities of an additional component of the architecture: the Dependency Configurator (also known as startup component, main component or composition root).

![Figure 1: "Dependency Configurator" component](/assets/images/testcasesasdrivers/figure1.png)
<p class="figure">Figure 1: "Dependency Configurator" component</p>

<div id="tc3"></div>
### 3.- TEST CASES.

Usually a test case is run by a test harness (also known as automated test framework) in 3 steps:

1) Given: Configure test fixture (context), to ensure that test pre-conditions are met.

2) When: Exercise SUT (System Under Test), calling to a method of the API.

3) Then: Verify outcome, to ensure that test post-conditions are met.

In hexagonal architecture, the SUT would be the application (hexagon), the test fixture would be composed by driven actors (for example, a database), and the test harness would be the driver adapter (it takes the test cases and run them).

![Figure 2: Test terminology applied to Hexagonal Architecture](/assets/images/testcasesasdrivers/figure2.png)
<p class="figure">Figure 2: Test terminology applied to Hexagonal Architecture</p>

<div id="tc4"></div>
### 4.- THE FORBIDDEN PATH.

So, which additional permissions does a "test harness" need to have, comparing to a "usual" driver adapter?

- It needs access to driven actors to configure them ("Given" step), and maybe to verify the test outcome ("Then" step).

- It needs to instantiate the hexagon, in order to exercise it.

- And even more: It needs to do this more than once. Not just at the startup, but for every use case, since each one may configure the test fixture in a different way to test different behaviours of the SUT in the same test case suite.

As you can see, the test harness needs to do what the "Dependency Configurator" component does.

But wait a minute... this does break dependency rules of Hexagonal Architecture, doesn't it?

A driver adapter shouldn't know about the driven side, and shouldn't know about building the system.

So, how can we deal with this then? Is everything lost? :) Fortunately not, let's see...

<div id="tc5"></div>
### 5.- ALTERNATIVE PATHS.

I found the following options to avoid the test harness having a dependency on the driven side (see Figure 3):

<div id="tc5-1"></div>
#### 5.1.- PATH VIA HEXAGON.

Let the test harness access the driven side through the driver port of the hexagon.

This way the application itself offers methods for setting-up and reading the configuration of driven actors.

In this case, the test harness doesn't have to build the hexagon, it just calls the available methods defined by the API of the hexagon, like any other driver adapter else.

<div id="tc5-2"></div>
#### 5.2.- PATH VIA "DEPENDENCY CONFIGURATOR".

Let the test harness access the driven side through the "Dependency Configurator" component.

To achieve this, we have to change the driver side dependency. So that now the driver adapter (the test harness) will depend on a driver port builder, instead of depending on the driven port itself. This builder would belong to the "Dependency Configurator".

In practice, this can be done in different ways, for example:

- Defining a builder interface at the driver adapter, and the interface implementation at the "Dependency Configurator".

- Defining the builder as a function at the "Dependency Configurator", and passing it as a callback to the driver adapter.

But the essence is that at runtime the driver adapter will call a driver port builder located at the "Dependency Configurator" component.

You can find a source code example in my <a target="_blank" href="https://github.com/jmgarridopaz/bluezone">BlueZone GitHub repository</a>.

![Figure 3: Alternatives to forbidden path](/assets/images/testcasesasdrivers/figure3.png)
<p class="figure">Figure 3: Alternatives to forbidden path</p>

<div id="tc6"></div>
### 6.- PARALLELISM WITH TEST PATTERNS.

When talking about testing, I always recommend the resource <a target="_blank" href="http://xunitpatterns.com">"xUnit Test Patterns" by Gerard Meszaros</a> as a reference on the subject.

I've found there two patterns related to the topic of this article:

<div id="tc6-1"></div>
#### 6.1.- ROUND TRIP TEST.

The test configures and reads the fixture through the API of the SUT, doing simple method calls.

![Figure 4: Round Trip Test](/assets/images/testcasesasdrivers/figure4.png)
<p class="figure">Figure 4: Round Trip Test</p>

- Pros: Encapsulation (the test only knows the public interface of the SUT, it doesn't know how to build the SUT).

- Cons: Slow.

<div id="tc6-2"></div>
#### 6.2.- BACK DOOR MANIPULATION.

The test configures and reads the fixture directly, bypassing the SUT.

This pattern is used in these situations:

- When using a round trip test is either not possible or not desirable.

- When testing basic CRUD operations: If we used round trip tests, it would be difficult to test a "Read" without using a "Create", or to test a "Create" without using a "Read". However, if we access the database directly, we can easily test a "Read" (doing an "insert" for setting-up the fixture), and test a "Create" (doing a "select" for verifying the outcome).

![Figure 5: Back Door Manipulation](/assets/images/testcasesasdrivers/figure5.png)
<p class="figure">Figure 5: Back Door Manipulation</p>

- Pros: Fast.

- Cons: Test coupled to fixture technology.

<div id="tc7"></div>
### 7.- CONCLUSION.

In test automation there exists a principle called "Front Door First", which says that we should test the SUT using tests that only interact with the "front door" (public interface) of the SUT.

It would be the normal approach: use round trip tests.

Then, if this has not been sufficient for testing the SUT behavior accurately, we could use back door manipulation tests to verify the calls the SUT makes to other components it depends on.

So, we can conclude that in Hexagonal Architecture, if possible, the prefered alternative to avoid the "forbidden path" (driver adapter depending directly on the driven side), would be the "path via hexagon" alternative, described in (5.1) section.

<div id="tc8"></div>
### 8.- LINKS

- Hexagonal Architecture resources:

<a target="_blank" href="https://jmgarridopaz.github.io/content/resources.html">https://jmgarridopaz.github.io/content/resources.html</a>

- "XUnit Test Patterns", by Gerard Meszaros:

<a target="_blank" href="http://xunitpatterns.com">http://xunitpatterns.com</a>

<br/>

<div class="commentbox"></div>
