---
layout: default
---

<div id="title">
<h1>Hexagonal Architecture: An implementation guide</h1>
<p>Chapter 2: PROJECT STRUCTURE</p>
<hr>
<span class="credits right">Published on Month dd, yyyy by <a href="{{ site.github.owner_url }}">{{ site.github.owner_name }}</a></span>
</div>

[Previous Chapter](https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter1.html)
[Intro Chapter](https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/intro.html)
[Next Chapter](https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter3.html)

#### TABLE OF CONTENTS

1. [Introduction](#tc1)
2. [Modules vs Layers](#tc2)
3. [Source code organization](#tc3)
4. [Dependencies](#tc4)  
4.1. [Knowledge](#tc4-1)  
4.2. [Configuration](#tc4-2)
5. [Links](#tc5)

<div id="tc1"></div>
### 1.- INTRODUCTION

This is the second article of a series showing how to implement an application according to Hexagonal Architecture, also known as Ports and Adapters pattern.

The example application is called BlueZone, and it is described and designed in <a target="_blank" href="https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter1.html">Chapter 1</a>.

The source code is available at <a target="_blank" href="https://github.com/jmgarridopaz/bluezone">this GitHub repository</a>.

In this chapter we will see how to organize the source code, and the dependencies between the different components, analyzing the knowledge ("who knows of whom") and the configuration of those dependencies.

<div id="tc2"></div>
### 2.- MODULES vs LAYERS

The pattern definition by Alistair Cockburn doesn't say anything anywhere about how to implement the architecture.

However, from the reading of the <a target="_blank" href="https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture">pattern</a>, the watching of the <a target="_blank" href="https://www.youtube.com/watch?v=th4AgBcrEHA&list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL">videos explaining it</a>, and the different drawings in them, I see two choices:

[DIAGRAMS OF UML COMPONENTS OR CIRCLE, AND FLATTENNED LAYERS]

* Modular:  
  - One module for the hexagon.  
  - One module for each adapter.
* Two layers:  
  - Inner layer: the hexagon.  
  - Outer layer: all the adapters (drawn in an outer hexagon).

Whatever option you choose, I consider 3 key concepts here, that makes this architecture different from others like Onion:

1. Ports are not a layer themselves. A driver port is part of the hexagon API, and a driven port is part of the hexagon SPI. But both are a part of the hexagon, they are interfaces that belong to the business logic of the application, i.e. to the hexagon. In Java 9, ports are interfaces into packages that the hexagon module publishes to other modules. The other packages are hidden to the outside world.

2. An adapter depends on the hexagon at one of its ports. So it has to say: "Hey! I depend on you hexagon at X port". An adapter is a piece of software playing a role (driver/driven) against a port. A driver adapter will be a Java class that "uses" a driver port interface, and a driven adapter will be a class that "implements" a driven port interface. In Java, this is achieved by using generic types. For example, a driver adapter is a generic class with one type parameter, which is the driver port interface that it uses. Whether an adapter plays different roles: it drives more than one port; or it is driven by more than one port; or it is driven by a hexagon port and it drives a port of another hexagon... I's up to you, but then you should know that you are not following the <a target="_blank" href="https://en.wikipedia.org/wiki/Single_responsibility_principle">SRP (Single Responsability Principle)</a>.

3. Adapters are independent from each other. An adapter doesn't know of the others, it just depends on the hexagon, and on the actor whose technology it is adapting (for example a database). But an adapter shouldn't be able to access another adapters. This is why if we go for the two layers implementation, we should care about protecting visibility between adapters, since a layered architecture allows a component to access other components in the same layer. In Java, we have the default access modifier (package private), which makes a type visible to just the members in the same package. So if you have a package for the adapters layer, you should have a sub-package for each adapter, and make types in each sub-package not accesible from others, i.e. make them package protected.

That said, the vision I've always had of Hexagonal Architecture is modular, with the hexagon at the center, and adapters around it, belonging to no layer.

So let’s se how source code is structured in the following section.

<div id="tc3"></div>
### 3.- SOURCE CODE ORGANIZATION

The code base of the example application, BlueZone, is at the GitHub repository https://github.com/jmgarridopaz/bluezone

The whole project is structured in modules.

I've put all the modules in just one repository, so that you can see and build them all toghether, but Java 9 allows you to have them in different repositories, compile them apart, and then provide their locations at runtime to choose them dinamically. We will see this later on, at the end of this article series, as an advanced way of execution.

These are the modules and the convention I follow to name them and their packages (a Java 9 module is a set of packages):

* One module for the hexagon, with name: <app_name>.hexagon

There will be one package for each port:
  <reverse_domain_name>.<app_name>.hexagon.driverports.<driver_port_name>
	<reverse_domain_name>.<app_name>.hexagon.drivenports.<driven_port_name>

Ports are named following the forDoingSomething pattern, saying what they are for, in a technology agnostic way from the application point of view, i.e. no matter the technology of the actor that is behind the port.

A port package contains the interface and the data types it manages, that the hexagon will publish to the outside world.

bluezone.hexagon module has 5 ports (2 driver and 3 driven):
	package io.github.jmgarridopaz.bluezone.hexagon.driverports.forparkingcars;
	package io.github.jmgarridopaz.bluezone.hexagon.driverports.forcheckingcars;
	package io.github.jmgarridopaz.bluezone.hexagon.drivenports.forobtainingrates;
	package io.github.jmgarridopaz.bluezone.hexagon.drivenports.forstoringpermits;
	package io.github.jmgarridopaz.bluezone.hexagon.drivenports.forpaying;

The other packages of the hexagon module, and how you organize them, fall out of the scope of Hexagonal Architecture pattern.

An usual way of structuring the hexagon is to split it into two layers: application and domain, following DDD rules, but this has nothing to do with Hexagonal Architecture.

The business logic (hexagon source code) will have to implement driver ports interfaces, and to use driven ports interfaces, but you can structure it however you want to. You just have to take care of publishing the ports to the outside world.

* One module for each adapter, with name <app_name>.adapter.<port_name>.<technology>

The root package for the adapter source code is:

package <reverse_domain_name>.<app_name>.adapters.<port_name>.<technology>

The <technology> part could be as specific and long as you want. For example:

<app_name>.adapter.<port_name>.webui
<app_name>.adapter.<port_name>.webui.spring
<app_name>.adapter.<port_name>.test.cucumber
<app_name>.adapter.<port_name>.database.sql.orm.jpa
<app_name>.adapter.<port_name>.database.mongo
<app_name>.adapter.<port_name>.file
<app_name>.adapter.<port_name>.stub

In our example we are going to develop 2 adapters for each port:

Driver adapters:

bluezone.adapter.forparkingcars.test
bluezone.adapter.forparkingcars.webui

bluezone.adapter.forcheckingcars.test
bluezone.adapter.forcheckingcars.cli

Driven adapters:

bluezone.adapter.forobtainingrates.stub
bluezone.adapter.forobtainingrates.file

bluezone.adapter.forstoringpermits.fake.imdb.map
bluezone.adapter.forstoringpermits.database.jpa

bluezone.adapter.forpaying.mock
bluezone.adapter.forpaying.remotesystem

* One module for building and running the application, with nane: <app_name>.startup

<div id="tc4"></div>
### 4.- DEPENDENCIES

text

<div id="tc4-1"></div>
#### 4.1.- KNOWLEDGE

text

<div id="tc4-2"></div>
#### 4.2.- CONFIGURATION

text

<div id="tc5"></div>
### 5.- LINKS

* Ports and Adapters Pattern (aka Hexagonal Architecture), by Alistair Cockburn:

[https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture](https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture)

* My conceptual article about Ports and Adapters Pattern (aka Hexagonal Architecture):

[https://jmgarridopaz.github.io/content/hexagonalarchitecture.html](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html)

* Alistair in the Hexagone: a talk by Alistair Cockburn about Hexagonal Architecture:

[https://www.youtube.com/watch?v=th4AgBcrEHA&list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL](https://www.youtube.com/watch?v=th4AgBcrEHA&list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL)

* "Hexagonal != Layers", by Thomas Pierrain:

[https://tpierrain.blogspot.com/2016/04/hexagonal-layers.html](https://tpierrain.blogspot.com/2016/04/hexagonal-layers.html)

<br/>

<div class="commentbox"></div>
