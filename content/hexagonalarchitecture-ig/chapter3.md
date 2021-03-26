---
title: Development Sequence - An Implementation Guide - Hexagonal Architecture
custom_article_title: Hexagonal Architecture
custom_article_subtitle: An implementation guide
custom_chapter_title: 'Chapter 3: DEVELOPMENT SEQUENCE'
custom_chapter_prev_url: https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter2.html
custom_chapter_next_url:
custom_published_date: March 26, 2021
custom_author_url: https://github.com/jmgarridopaz
custom_author_name: Juan Manuel Garrido de Paz
layout: default
---

### TABLE OF CONTENTS

1. [Introduction](#tc1)
2. [Hardcoded hexagon](#tc2)
3. [The hexagon in isolation](#tc3)
4. [We are done](#tc4)
5. [Adding real adapters](#tc5)
6. [Links](#tc6)

<div id="tc1"></div>
### 1.- INTRODUCTION

This is the third article of a series showing how to implement an application according to Hexagonal Architecture, also known as Ports and Adapters pattern, whose author is Dr. Alistair Cockburn.

So far we have seen the design (<a target="_blank" href="https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter1.html">Chapter 1</a>) and structure (<a target="_blank" href="https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter2.html">Chapter 2</a>) of a hexagonal application. As a result of the design, we got two things:

- A diagram showing the hexagon with the adapters we want to build.

![Figure 1: The big picture](/assets/images/hexagonalarchitecture-ig/figure3-1.png)
<p class="figure">Figure 1: The big picture</p>

- The driver ports definition, i.e. the specification of the API that the application exposes to users.

![Figure 2: Driver ports interfaces](/assets/images/hexagonalarchitecture-ig/figure3-2.png)
<p class="figure">Figure 2: Driver ports interfaces</p>

These two things will be the starting point for developing the system.

Now in this article we'll see the development sequence for coding the different components of the architecture.

According to Alistair Cockburn (see for example his <a target="_blank" href="https://www.youtube.com/watch?v=Sc_B6dQ6di0&t=813">talk about Hexagonal Architecture at Agiles2020 conference</a>), the development sequence should have these steps:

| STEP |DRIVER SIDE|HEXAGON<br />(APPLICATION)|DRIVEN SIDE|
|:-:|:--:|:--:|:--:|
|(S1)|Test cases|Hardcoded|-|
|(S2)|Test cases|Real|Test doubles|
|(S3)|Real|Real|Test doubles|
|(S4)|Test cases|Real|Real|
|(S5)|Real|Real|Real |

We can see that we start with test harness at both sides: first on the left side (S1) and then on the right side (S2).

From that moment on, we add real adapters at both sides: first we change left side technology (S3) and then we change right side technology (S4).

So that finally we get the application running in a production environment with real adapters at both sides (S5).

There are some very important things to remark:

- A test case is also a user of the application. And not just a user like any other else, but the first user.
- After S1, the left side is done. We will have the driver adapter (test harness) with a configurable dependency on the hexagon.
- After S2, the hexagon is done. At this point we have our application (business logic) done, since it can be tested in isolation from real world items. This is the main goal of Hexagonal Architecture.

In the next sections we will apply these steps for developing our Java 9 example application, BlueZone. Source code is available at <a target="_blank" href="https://github.com/jmgarridopaz/bluezone">BlueZone GitHub repository</a>.

<div id="tc2"></div>
### 2.- HARDCODED HEXAGON

In this section we will see the first step of the development sequence: Test Cases + Hardcoded Hexagon.

![Figure 3: First development step: Test Cases + Hardcoded Hexagon](/assets/images/hexagonalarchitecture-ig/figure3-3.png)
<p class="figure">Figure 3: First development step: Test Cases + Hardcoded Hexagon</p>

We develop the following modules:

- An adapter to test "for parking cars" port functionality (use cases), using Cucumber test automation tool. Cucumber was built to support a software development process called BDD (Behaviour Driven Development). In the Links section you can see a couple of good articles by Julien Topçu about this topic.

- An adapter using TestNG framework to test "for checking cars" port functionality.

- The hexagon, including both driver port interfaces and their implementation, using hardcoded values in order to pass the tests.

In the next video, I explain the source code for this first development step, which is available at <a target="_blank" href="https://github.com/jmgarridopaz/bluezone/tree/S1">S1 tag in BlueZone GitHub repository</a>.

INSERT video

At this moment, the left side of the hexagon is done, since we already have driver adapters for running test cases, and we also have the configurable dependency on the hexagon.

Next step is developing test doubles as adapters on the right side.

Let's see.

<div id="tc3"></div>
### 3.- THE HEXAGON IN ISOLATION

TODO ASAP (coming soon).

<div id="tc4"></div>
### 4.- WE ARE DONE

TODO

<div id="tc5"></div>
### 5.- ADDING REAL ADAPTERS

TODO

<div id="tc6"></div>
### 6.- LINKS

- "Ports and Adapters Pattern" (aka Hexagonal Architecture), the original article, by Alistair Cockburn:

<a target="_blank" href="https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture">https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture</a>

- "Entre puertos y adaptadores: La Arquitectura Hexagonal", a talk by Alistair Cockburn at "Ágiles 2020" (XIII Jornadas Latinoamericanas de Agilidad). English with spanish subtitles:

<a target="_blank" href="https://www.youtube.com/watch?v=Sc_B6dQ6di0">https://www.youtube.com/watch?v=Sc_B6dQ6di0</a>

- "Alistair in the Hexagone", a talk by Alistair Cockburn in Paris, June 2017:

<a target="_blank" href="https://www.youtube.com/watch?v=th4AgBcrEHA&list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL">https://www.youtube.com/watch?v=th4AgBcrEHA&list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL</a>

- "Behavior-Driven Development from scratch", by Julien Topçu:

<a target="_blank" href="https://beyondxscratch.com/2019/05/21/behavior-driven-development-from-scratch/">https://beyondxscratch.com/2019/05/21/behavior-driven-development-from-scratch/</a>

- "Implementing Functional Tests in Domain-Driven Design & Hexagonal Architecture using Cucumber", by Julien Topçu:

<a target="_blank" href="https://beyondxscratch.com/2019/08/26/implementing-functional-tests-in-domain-driven-design-hexagonal-architecture-using-cucumber-domain-driven-testing/">https://beyondxscratch.com/2019/08/26/implementing-functional-tests-in-domain-driven-design-hexagonal-architecture-using-cucumber-domain-driven-testing/</a>

<br/>

<div class="commentbox"></div>
