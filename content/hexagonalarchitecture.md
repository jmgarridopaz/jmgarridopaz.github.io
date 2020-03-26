---
layout: default
---

<div id="title">
<h1>Ports and Adapters Pattern</h1>
<p>(Hexagonal Architecture)</p>
<hr>
<span class="credits right">Published on August 29, 2018 by <a href="{{ site.github.owner_url }}">@{{ site.github.owner_name }}</a></span>
</div>

<p><em>This article is for sharing my knowledge about Ports & Adapters pattern (also known as Hexagonal Architecture), hoping it can be helpful to someone interested in this subject. Here I expose my understanding of this architecture pattern, after being reading articles, watching talks and learning about it, since I first heard of it two years ago. This is a conceptual article, rather than a practical one. I hope to publish a pragmatic article soon, explaining my implementation of this architecture using Java 9 modules; and I hope to upload a proof of concept to github as well</em></p>

#### TABLE OF CONTENTS

<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc1">1.- Introduction.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2">2.- The Architecture.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2-1">2.1.- The Hexagon.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2-2">2.2.- Actors.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2-3">2.3.- Ports.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2-4">2.4.- Adapters.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2-5">2.5.- Summary.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc2-6">2.6.- Example.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc3">3.- Configurable Dependency Pattern.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc4">4.- From “Symmetrical Asymmetry” To “Asymmetrical Symmetry”.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc5">5.- Misconceptions.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc5-1">5.1.- Layered Architecture.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc5-2">5.2.- Why a hexagon?</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc5-3">5.3.- Ports outside Adapters.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6">6.- Pros and Cons.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-1">6.1.- Pros.</a><br>
    <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-1-1">6.1.1.- Testability improvement.</a><br>
    <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-1-2">6.1.2.- Maintainability improvement.</a><br>
    <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-1-3">6.1.3.- Flexibility.</a><br>
    <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html#tc6-1-4">6.1.4.- Application immune to technology evolution.</a><br>
    <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-1-5">6.1.5.- Delay technological decisions.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-2">6.2.- Cons.</a><br>
    <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-2-1">6.2.1.- Complexity.</a><br>
    <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-2-2">6.2.2.- Build process performance.</a><br>
    <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-2-3">6.2.3.- Indirection and Mappings.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc7">7.- When to <em>“Hexagonal this!”</em>.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc8">8.- Implementation steps.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc8-1">8.1.- Test driver adapters / Mock driven adapters.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc8-2">8.2.- Real driver adapters / Mock driven adapters.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc8-3">8.3.- Test driver adapters / Real driven adapters.</a><br>
  <a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc8-4">8.4.- Real driver adapters / Real driven adapters.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc9">9.- Links.</a>

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

Ports & Adapters pattern depicts the application as a closed area.

The closed area chosen by Alistair Cockburn for drawing the application was a hexagon, that’s why this pattern is also called Hexagonal Architecture.

Personally I prefer the name Ports and Adapters, because it refers to the key elements of the architecture, as we will see soon. On the other hand the figure you use for drawing the application isn’t important. However, it seems that the name Hexagonal Architecture is more popular.

The hexagon is the application itself. Saying “hexagon” and saying “application” is the same thing, from now on these terms will be used indistinctly.

Inside the hexagon we just have the things that are important for the business problem that the application is trying to solve.

The hexagon contains the business logic, with no references to any technology, framework or real world device. So the application is technology agnostic.

Ports & Adapters pattern says nothing about the structure of the inside of the hexagon. You can have layers… you can have components by feature… you can have spagheti code… you can have a Big Ball of Mud… you can apply DDD tactical patterns… you can have a single CRUD… it’s up to you.

<div id="tc2-2"></div>
#### 2.2- ACTORS

Outside the hexagon we have any real world thing that the application interacts with. These things include humans, other applications, or any hardware/software device. They are the actors. We could say that actors are the environment of the application.

Actors are arranged around the hexagon depending on who triggers the interaction between the application and the actor:

Actors on the left/top side are Drivers, or Primary Actors. The interaction is triggered by the actor. A driver is an actor that interacts with the application to achieve a goal. Drivers are the users (either humans or devices) of the application.
Actors on the right/bottom side are Driven Actors, or Secondary Actors. The interaction is triggered by the application. A driven actor provides some functionality needed by the application for implementing the business logic.
There are two kinds of driven actors:

Repository: The application can also obtain information from it, besides sending. For example, a database or any other storage device.
Recipient: The application just sends information to it and forgets about it. For example, a SMTP server for sending emails.
The following picture shows some examples of actors in both driver and driven sides:

<div style="background-image: url('/images/figure1.png');background-size:cover;">

Figure 1: Actors
