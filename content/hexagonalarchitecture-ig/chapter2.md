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
5. [Links](#tc5)

<div id="tc1"></div>
### 1.- INTRODUCTION

This is the second article of a series showing how to implement an application according to Hexagonal Architecture, also known as Ports and Adapters pattern.

The example application is called BlueZone, and it is described and designed in <a target="_blank" href="https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter1.html">Chapter 1</a>.

In this chapter we will see how to organize the source code, which is available at <a target="_blank" href="https://github.com/jmgarridopaz/bluezone">this GitHub repository</a>, and the dependencies between the different components, analyzing the knowledge ("who knows of whom") and the configuration of those dependencies.

<div id="tc2"></div>
### 2.- MODULES vs LAYERS

The pattern definition by Alistair Cockburn doesn't say anything anywhere about how to implement the architecture.

However, from the reading of the <a target="_blank" href="https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture">pattern</a>, the watching of the <a target="_blank" href="https://www.youtube.com/watch?v=th4AgBcrEHA&list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL">videos explaining it</a>, and the different drawings in them, I see two choices:

[DIAGRAMS OF UML COMPONENTS OR CIRCLE, AND FLATTENNED LAYERS]

* **Modular**:  
  - One module for the hexagon.  
  - One module for each adapter.
* **Two layers**:  
  - Inner layer: the hexagon.  
  - Outer layer: all the adapters (drawn in an outer hexagon).

Whatever option you choose, I consider 3 key concepts here, that makes this architecture different from others like Onion:

1. **Ports are not a layer themselves.** A driver port is part of the hexagon API, and a driven port is part of the hexagon SPI. But both are a part of the hexagon, they are interfaces that belong to the business logic of the application, i.e. to the hexagon. In Java 9, ports are interfaces into packages that the hexagon module publishes to other modules. The other packages are hidden to the outside world.

2. **An adapter depends on the hexagon at one of its ports.** An adapter is a piece of software playing a role (driver/driven) against a port. A driver adapter will be a Java class that "uses" a driver port interface, and a driven adapter will be a class that "implements" a driven port interface. In Java, this is achieved by using generic types. For example, a driver adapter is a generic class with one type parameter, which is the driver port interface that it uses. Whether an adapter plays different roles: it drives more than one port; or it is driven by more than one port; or it is driven by a hexagon port and it drives a port of another hexagon... it's up to you, but then you should know that you are not following the <a target="_blank" href="https://en.wikipedia.org/wiki/Single_responsibility_principle">SRP (Single Responsability Principle)</a>.

3. **Adapters are independent from each other.** An adapter doesn't know of the others, it just depends on the hexagon, and on the actor whose technology it is adapting (for example a database). But an adapter shouldn't be able to access another adapters. This is why if we go for the two layers implementation, we should care about protecting visibility between adapters, since a layered architecture allows a component to access other components in the same layer. In Java, we have the default access modifier (package private), which makes a type visible to just the members in the same package. So if you have a package for the adapters layer, you should have a sub-package for each adapter, and make types in each sub-package not accesible from others, i.e. make them package protected.

<div id="tc3"></div>
### 3.- SOURCE CODE ORGANIZATION

The vision I've always had of Hexagonal Architecture is modular, with the hexagon at the center, and adapters around it, belonging to no layer, each one attached to the hexagon port it uses / implements.

So, the whole project is structured in modules.

The code base of the example application is at <a target="_blank" href="https://github.com/jmgarridopaz/bluezone">BlueZone GitHub repository</a>.

I've put all the modules in just one GitHub repository, so that you can see and build them all together, but Java 9 allows you to have them in different repositories, compile them apart, and then provide their locations at runtime to choose them dynamically. We will see this later on, at the end of this article series, as an advanced way of execution.

These are the modules and the convention I follow to name them and their packages (a Java 9 module is a set of packages):

* **One module for the hexagon**, with name:  `<app_name>.hexagon`

  There will be one package for each port:

  `<reverse_domain_name>.<app_name>.hexagon.driverports.<driver_port_name>`

  `<reverse_domain_name>.<app_name>.hexagon.drivenports.<driven_port_name>`

Ports are named following the ***forDoingSomething pattern***, saying what they are for, in a technology agnostic way from the application point of view, i.e. no matter the technology of the actor that is behind the port.

A port package contains the interface and the data types it manages, that the hexagon will publish to the outside world.

`bluezone.hexagon` module has 5 ports (2 driver and 3 driven):

```java
package io.github.jmgarridopaz.bluezone.hexagon.driverports.forparkingcars;
package io.github.jmgarridopaz.bluezone.hexagon.driverports.forcheckingcars;

package io.github.jmgarridopaz.bluezone.hexagon.drivenports.forobtainingrates;
package io.github.jmgarridopaz.bluezone.hexagon.drivenports.forstoringpermits;
package io.github.jmgarridopaz.bluezone.hexagon.drivenports.forpaying;
```

The other packages of the hexagon module, and how you organize them, fall out of the scope of Hexagonal Architecture pattern.

An usual way of structuring the hexagon is to split it into two layers: application and domain, following DDD rules, but this has nothing to do with Hexagonal Architecture.

Somewhere, the business logic (hexagon source code) will have to implement driver ports interfaces, and to use driven ports interfaces, but you can structure it however you want to.



* **One module for each adapter**, with name `<app_name>.adapter.<port_name>.<technology>`

  The "technology" part could be as specific and long as you want. For example:

`<app_name>.adapter.<port_name>.webui`
`<app_name>.adapter.<port_name>.webui.spring`
`<app_name>.adapter.<port_name>.test.cucumber`
`<app_name>.adapter.<port_name>.database.sql.orm.jpa`
`<app_name>.adapter.<port_name>.database.mongo`
`<app_name>.adapter.<port_name>.file`
`<app_name>.adapter.<port_name>.stub`

In our example we are going to develop 2 adapters for each port:

Driver adapters:

`bluezone.adapter.forparkingcars.test`
`bluezone.adapter.forparkingcars.webui`

`bluezone.adapter.forcheckingcars.test`
`bluezone.adapter.forcheckingcars.cli`

Driven adapters:

`bluezone.adapter.forobtainingrates.stub`
`bluezone.adapter.forobtainingrates.file`

`bluezone.adapter.forstoringpermits.fake.imdb.map`
`bluezone.adapter.forstoringpermits.database.jpa`

`bluezone.adapter.forpaying.mock`
`bluezone.adapter.forpaying.remotesystem`

The root package for an adapter source code would be:

`package <reverse_domain_name>.<app_name>.adapters.<port_name>.<technology>`



* **One module for building and running the application**, with name: `<app_name>.startup`

  This module is the entry point to the application. It runs a driver adapter. So it has a package for each driver adapter, with a Java main class, that will build the application and run the driver adapter.

  ```java
  package io.github.jmgarridopaz.bluezone.startup.forparkingcars.test;
  package io.github.jmgarridopaz.bluezone.startup.forparkingcars.webui;
  package io.github.jmgarridopaz.bluezone.startup.forcheckingcars.test;
  package io.github.jmgarridopaz.bluezone.startup.forcheckingcars.cli;
  ```

<div id="tc4"></div>

### 4.- DEPENDENCIES.

Dependencies rules between modules are easy:

- The **hexagon module** depends on nothing.
- An **adapter module** depends on:
  - The hexagon module.
  - The actor whose technology is adapting.
- The **startup module** depends on everything.

In Java 9, every module has a `module-info.java` file where it declares the modules it depends on (requires), and the packages it publishes (exports) to other modules.

1. **The Hexagon Module.**

   It contains the business logic, including ports. So it doesn't do

   ~~~java
   module bluezone.hexagon {
       // driver ports
   	exports io.github.jmgarridopaz.bluezone.hexagon.driverports.forparkingcars;
   	exports io.github.jmgarridopaz.bluezone.hexagon.driverports.forcheckingcars;				
   	// driven ports
   	exports io.github.jmgarridopaz.bluezone.hexagon.drivenports.forobtainingrates;
   	exports io.github.jmgarridopaz.bluezone.hexagon.drivenports.forstoringpermits;
   	exports io.github.jmgarridopaz.bluezone.hexagon.drivenports.forpaying;
       
   	...
   	...
   }
   ~~~

   It publishes (exports) the packages with the ports. Each port package just contains the interface defining port operations, and the classes defining the data that those operations manage. Any logic implementing the driver ports is hidden to other modules.

   It is also possible to declare the modules you want to export a package to, so that you could export a port just to its adapters. But we would have to change this file and recompile the hexagon, every time we add a new adapter.

2. **The Adapters Modules.**

   An adapter module requires the hexagon module, and other modules related to libraries and frameworks it uses, according

3. **The Startup Module.**



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
