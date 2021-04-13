---
title: Development Sequence - An Implementation Guide - Hexagonal Architecture
custom_article_title: Hexagonal Architecture
custom_article_subtitle: An implementation guide
custom_chapter_title: 'Chapter 3: DEVELOPMENT SEQUENCE'
custom_chapter_prev_url: https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter2.html
custom_chapter_next_url:
custom_published_date: April 13, 2021
custom_author_url: https://github.com/jmgarridopaz
custom_author_name: Juan Manuel Garrido de Paz
layout: default
---

### TABLE OF CONTENTS

1. [Introduction](#tc1)
2. [Hardcoded hexagon](#tc2)
3. [The hexagon in isolation](#tc3)
4. [Adding real adapters](#tc4)
5. [Links](#tc5)

<div id="tc1"></div>
### 1.- INTRODUCTION

This is the third article of a series showing how to implement an application according to Hexagonal Architecture, also known as Ports and Adapters pattern, whose author is Dr. Alistair Cockburn.

So far we have seen the application design (<a target="_blank" href="https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter1.html">Chapter 1</a>) and structure (<a target="_blank" href="https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter2.html">Chapter 2</a>). As a result of the design, we got two things:

- A diagram showing the hexagon with the adapters we want to develop.

![Figure 1: The big picture](/assets/images/hexagonalarchitecture-ig/figure3-1.png)
<p class="figure">Figure 1: The big picture</p>

- The API definition, i.e. the interfaces (driver ports) with methods (use cases) that the application offers to users (driver actors).

  - <a target="_blank" href="https://github.com/jmgarridopaz/bluezone/blob/stage1/src/bluezone-hexagon/src/main/bluezone.hexagon/io/github/jmgarridopaz/bluezone/hexagon/driver/forparkingcars/ForParkingCars.java">ForParkingCars interface</a> (`getAllRatesByName` and `issuePermit` methods).
  - <a target="_blank" href="https://github.com/jmgarridopaz/bluezone/blob/stage1/src/bluezone-hexagon/src/main/bluezone.hexagon/io/github/jmgarridopaz/bluezone/hexagon/driver/forcheckingcars/ForCheckingCars.java">ForCheckingCars interface</a> (`illegallyParkedCar` method).

From these two things, we will begin to implement the different modules of the architecture, following a development sequence.

According to Alistair Cockburn (see for example his <a target="_blank" href="https://www.youtube.com/watch?v=Sc_B6dQ6di0&t=813">talk about Hexagonal Architecture at Agiles2020 conference</a>), this development sequence would have some stages:

|STAGE|DRIVER SIDE|HEXAGON<br />(APPLICATION)|DRIVEN SIDE|
|:--:|:--:|:--:|:--:|
|(S1)|Test cases|Hardcoded|-|
|(S2)|Test cases|Real|Test doubles|
|(S3)|Real|Real|Test doubles|
|(S4)|Test cases|Real|Real|
|(S5)|Real|Real|Real |

We can see that we start with test harness at both sides: first on the left side (S1) and then on the right side (S2).

From that moment on, we add real adapters at both sides: first we change left side technology (S3) and then we change right side technology (S4).

So that finally we get the application running in a production environment with real adapters at both sides (S5).

Some important things to remark:

- A test case is also a user of the application. And not just a user like any other else, but the first user.
- After S1, the left side is done. We will have the driver adapter (test harness) with a configurable dependency on the hexagon.
- After S2, the hexagon is done. At this point we have our application (business logic) done, since it can be tested in isolation from real world items. This is the main goal of Hexagonal Architecture.
- The inner development strategy and structure of each module (hexagon and adapters), are orthogonal to hexagonal architecture, it's your business. So, for example, it's up to you to use either an outside-in or an inside-out approach to develop the business logic, to structure it using layers or not, etc.
- It falls out of the scope of these article series to fix a terminology for the different existing test categories (unit, functional, acceptance, end-to-end, system, etc). The most important thing is that hexagonal architecture lets you test the business logic in isolation from real world devices (technologies). Call this test whatever you want, the important thing is what it means.
- The hardcoded version of the hexagon implemented in S1, is also useful when developing real adapters for driver ports in S3, since we can "mock" the hexagon to test the driver adapter.

<div id="tc2"></div>
### 2.- HARDCODED HEXAGON

In this section we will see the first stage in the development sequence: "Test Cases + Hardcoded Hexagon".

Source code for this stage is available at <a target="_blank" href="https://github.com/jmgarridopaz/bluezone/tree/stage1">stage1 tag in BlueZone GitHub Repository</a>.

![Figure 2: First development stage: Test Cases + Hardcoded Hexagon](/assets/images/hexagonalarchitecture-ig/figure3-2.png)
<p class="figure">Figure 2: First development stage: Test Cases + Hardcoded Hexagon</p>

We have these modules:

- <a target="_blank" href="https://github.com/jmgarridopaz/bluezone/tree/stage1/src/bluezone-adapter-forparkingcars-test">bluezone.adapter.forparkingcars.test</a>: This adapter launches Cucumber tool from the command line, in order to run test cases against `ForParkingCars` interface methods. Cucumber is a test automation tool built to support BDD (Behavior-Driven Development). If you are interested in BDD and Cucumber, see the Links section at the end of this article.
- <a target="_blank" href="https://github.com/jmgarridopaz/bluezone/tree/stage1/src/bluezone-adapter-forcheckingcars-test">bluezone.adapter.forcheckingcars.test</a>: This adapter tests `ForCheckingCars` interface methods. To show different testing tools, this time we use TestNG framework, instead of Cucumber.
- <a target="_blank" href="https://github.com/jmgarridopaz/bluezone/tree/stage1/src/bluezone-hexagon">bluezone.hexagon</a>: This module contains the API, i.e. the driver ports (`ForParkingCars` and `ForCheckingCars` interfaces), and the classes implementing them using hardcoded values (<a target="_blank" href="https://github.com/jmgarridopaz/bluezone/blob/stage1/src/bluezone-hexagon/src/main/bluezone.hexagon/io/github/jmgarridopaz/bluezone/hexagon/driver/forparkingcars/implementation/hardcoded/HardCodedCarParker.java">HardCodedCarParker</a> and <a target="_blank" href="https://github.com/jmgarridopaz/bluezone/blob/stage1/src/bluezone-hexagon/src/main/bluezone.hexagon/io/github/jmgarridopaz/bluezone/hexagon/driver/forcheckingcars/implementation/hardcoded/HardCodedCarChecker.java">HardCodedCarChecker</a>). In this first development stage, driven ports aren't used yet.
- <a target="_blank" href="https://github.com/jmgarridopaz/bluezone/tree/stage1/src/bluezone-startup">bluezone.startup</a>: This module instantiates the different components, builds the whole system, and runs the driver adapters. It deserves a chapter on its own, explaining different startup modes. So we will see it at the end of these article series.

<a target="_blank" href="https://github.com/jmgarridopaz/bluezone/blob/master/README.md">README.md file at BlueZone GitHub repository</a> explains how to run the application.

Here you can see screen-shots of HTML reports, generated after running test cases:

![Figure 3: "for parking cars" test HTML report, generated by Cucumber](/assets/images/hexagonalarchitecture-ig/figure3-3.png)
<p class="figure">Figure 3: "for parking cars" test HTML report, generated by Cucumber</p>

![Figure 4: "for checking cars" test HTML report, generated by TestNG](/assets/images/hexagonalarchitecture-ig/figure3-4.png)
<p class="figure">Figure 4: "for checking cars" test HTML report, generated by TestNG</p>

In the next stage we implement test doubles as adapters on the right side.

Let's see.

<div id="tc3"></div>
### 3.- THE HEXAGON IN ISOLATION

***TODO ASAP***

<div id="tc4"></div>
### 4.- ADDING REAL ADAPTERS

***TODO***

<div id="tc5"></div>
### 5.- LINKS

- Hexagonal Architecture resources:

<a target="_blank" href="https://jmgarridopaz.github.io/content/resources.html">https://jmgarridopaz.github.io/content/resources.html</a>

- Cucumber Website:

<a target="_blank" href="https://cucumber.io/">https://cucumber.io/</a>

- "Behavior-Driven Development" (BDD) at Wikipedia:

<a target="_blank" href="https://en.wikipedia.org/wiki/Behavior-driven_development">https://en.wikipedia.org/wiki/Behavior-driven_development</a>

- "Behavior-Driven Development from scratch", an article by Julien Topçu:

<a target="_blank" href="https://beyondxscratch.com/2019/05/21/behavior-driven-development-from-scratch/">https://beyondxscratch.com/2019/05/21/behavior-driven-development-from-scratch/</a>

- "Implementing Functional Tests in Domain-Driven Design & Hexagonal Architecture using Cucumber", an article by Julien Topçu:

<a target="_blank" href="https://beyondxscratch.com/2019/08/26/implementing-functional-tests-in-domain-driven-design-hexagonal-architecture-using-cucumber-domain-driven-testing/">https://beyondxscratch.com/2019/08/26/implementing-functional-tests-in-domain-driven-design-hexagonal-architecture-using-cucumber-domain-driven-testing/</a>

- TestNG, a testing framework for Java:

<a target="_blank" href="https://testng.org/doc/">https://testng.org/doc/</a>

<br/>

<div class="commentbox"></div>
