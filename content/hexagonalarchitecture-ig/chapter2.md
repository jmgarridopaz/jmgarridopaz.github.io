---
layout: default
---

<div id="title">
<h1>Hexagonal Architecture: An implementation guide</h1>
<hr>
<p>Chapter 2: PROJECT STRUCTURE</p>
<hr>
<span class="credits right">Published on Month dd, yyyy by <a href="{{ site.github.owner_url }}">{{ site.github.owner_name }}</a></span>
</div>
[Previous Chapter](https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter1.html)
[Intro Chapter](https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/intro.html)
[Next Chapter](https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter3.html)

#### TABLE OF CONTENTS

1. [Introduction](#tc1)

2. [Source code organization](#tc2)

   2.1. [One module for the hexagon](#tc2-1)  
   2.2. [One module for each adapter](#tc2-2)  
   2.3. [One module for starting up the application](#tc2-3)

3. [Dependencies](#tc3)

4. [Modules vs Layers](#tc4)

5. [Links](#tc5)

<div id="tc1"></div>
### 1.- INTRODUCTION

This is the second article of a series showing how to implement an application according to Hexagonal Architecture, also known as Ports and Adapters pattern, whose author is Dr. Alistair Cockburn.

The example application is called BlueZone, and it is described and designed in <a target="_blank" href="https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter1.html">Chapter 1</a>.

Neither the <a target="_blank" href="https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture">pattern definition</a> nor the  <a target="_blank" href="https://www.youtube.com/watch?v=th4AgBcrEHA&list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL">videos by Alistair Cockburn explaining the pattern</a>, don't say anything anywhere about how to implement the architecture. However, after reading and watching those resources, the explanation of the elements (hexagon, actors, ports, adapters) and the interactions between them, along with the drawings, make me have **a modular vision of Hexagonal Architecture**, with the hexagon at the center, and adapters around it, belonging to no layer, each one attached to a port of the hexagon. The different adapters depending on a port stand for different swappable technologies that the actor communicating with the hexagon might use.

In this chapter we will see how to organize the source code in modules using Java 9, and the dependencies between modules, analyzing the knowledge ("who knows of whom") and the configuration of those dependencies.

<div id="tc2"></div>
### 2.- SOURCE CODE ORGANIZATION

The code base of the example application is at <a target="_blank" href="https://github.com/jmgarridopaz/bluezone">BlueZone GitHub repository</a>.

The whole project is structured in modules. In Java 9, a module is a set of packages, that you can compile apart generating a jar file. Modules depend on each other.

I've put all the modules in just one GitHub repository, so that you can see and build them all together, but Java 9 allows you to have them in different repositories, compile them apart, and then provide their locations at runtime to choose them dynamically. We will see this later on, at the end of this article series, as an advanced way of execution.

Now let's see the modules and the convention I follow for naming them and their packages.

<div id="tc2-1"></div>

#### 2.1.- ONE MODULE FOR THE HEXAGON, with name:  `<app_name>.hexagon`

Hexagonal Architecture pattern says nothing about how to structure the source code of the hexagon. The pattern just says that the hexagon offers "ports" to the outside world, for allowing external actors to interact with the hexagon.

So how do we "publish" an interface (the port) of a module (the hexagon)?

Before Java 9, a public type was visible to the rest of the world. Now, with modules, a public type is just visible inside its module. For other modules to see the public type, you have to export the package the type belongs to. Exporting a package would be like "publishing" its public types to the outside world.

So we will have **a package for each port of the hexagon**. A package for a port contains a public interface defining the port operations, and public data types that those operations manage.

![Figure 1: Ports packages in hexagon module](/assets/images/hexagonalarchitecture-ig/figure2-1.png)

<p class="intro">Figure 1: Ports packages in hexagon module</p>

The name of a package for a port will be:

~~~
<reverse_domain_name>.<app_name>.hexagon.<side>.<port_name>
~~~

This way ports are grouped by their side (driver/driven), so that we see at a first glance whether the port is driver or driven. This is important, since driver ports operations are called by an adapter, while driven port operation are implemented by an adapter. It's the left/right asymmetry .

In the example application, the name of the hexagon module is

~~~java
bluezone.hexagon
~~~

which has 5 packages for its ports (2 driver and 3 driven):

~~~java
package io.github.jmgarridopaz.bluezone.hexagon.driver.forparkingcars;
package io.github.jmgarridopaz.bluezone.hexagon.driver.forcheckingcars;

package io.github.jmgarridopaz.bluezone.hexagon.driven.forobtainingrates;
package io.github.jmgarridopaz.bluezone.hexagon.driven.forstoringpermits;
package io.github.jmgarridopaz.bluezone.hexagon.driven.forpaying;
~~~

Ports are named following the ***forDoingSomething pattern***, saying what they are for, in a technology agnostic way from the application point of view, i.e. no matter the technology of the actor that is behind the port.

=====

*The **business logic (hexagon source code)** organization fall out of the scope of Hexagonal Architecture pattern. It will be a set of Java classes, types, etc implementing the operations defined at driver port interfaces, and calling operations on driven port interfaces. But **you can structure this source code however you want to, it is not a Hexagonal Architecture issue**.*

*As an example, the simplest way in Java to implement a hexagon would be just one class, **implementing driver port interfaces**, and **depending on driven port interfaces** (constructor arguments).*

~~~java
public class BusinessLogic implements ForParkingCars, ForCheckingCars {
	
    private final ForObtainingRates forObtainingRates;
    private final ForStoringPermits forStoringPermits;
    private final ForPaying forPaying;
    
    public BusinessLogic ( ForObtainingRates forObtainingRates, ForStoringPermits forStoringPermits, ForPaying forPaying ) {
        this.forObtainingRates = forObtainingRates;
        this.forStoringPermits = forStoringPermits;
        this.forPaying = forPaying;
    }
    
    @Override
    public Map<String,RateData> getAllRatesByName() {
        // Implement the driver port operation calling driven ports operations
    }
    
    // ...
~~~

*A usual way of structuring the hexagon is to split it into two layers: application and domain, following DDD (Domain Driven Design) rules, but this has nothing to do with Hexagonal Architecture.*

=====

<div id="tc2-2"></div>

#### 2.2.- ONE MODULE FOR EACH ADAPTER, with name `<app_name>.adapter.<port_name>.<technology>`

The "technology" part could be as specific and long as you want. For example:

~~~
<app_name>.adapter.<port_name>.webui
<app_name>.adapter.<port_name>.webui.spring
<app_name>.adapter.<port_name>.test.cucumber
<app_name>.adapter.<port_name>.database.sql.orm.jpa
<app_name>.adapter.<port_name>.database.mongo
<app_name>.adapter.<port_name>.file
<app_name>.adapter.<port_name>.stub
~~~

In our example we are going to develop 2 adapters for each port:

Driver adapters:

~~~java
bluezone.adapter.forparkingcars.test
bluezone.adapter.forparkingcars.webui
    
bluezone.adapter.forcheckingcars.test
bluezone.adapter.forcheckingcars.cli
~~~

Driven adapters:

~~~java
bluezone.adapter.forobtainingrates.stub
bluezone.adapter.forobtainingrates.file

bluezone.adapter.forstoringpermits.fake.imdb.map
bluezone.adapter.forstoringpermits.database.jpa.mariadb
    
bluezone.adapter.forpaying.mock
bluezone.adapter.forpaying.remotesystem
~~~

The root package for an adapter source code would be:

~~~java
package <reverse_domain_name>.<app_name>.adapter.<port_name>.<technology>
~~~



<div id="tc2-3"></div>

#### 2.3.- ONE MODULE FOR STARTING UP THE APPLICATION, with name: `<app_name>.startup`

This module is the entry point to the application.

It instantiates the different components of the architecture, wires them up, and runs the application. The application can be run by either one of the existing driver adapters, including tests.

So the source code will be structured into one package for each driver adapter:

~~~java
package io.github.jmgarridopaz.bluezone.startup.forparkingcars.test;
package io.github.jmgarridopaz.bluezone.startup.forparkingcars.webui;

package io.github.jmgarridopaz.bluezone.startup.forcheckingcars.test;
package io.github.jmgarridopaz.bluezone.startup.forcheckingcars.cli;
~~~

During my researching on hexagonal architecture, I've studied several techniques for implementing the application startup, and I will show them in detail along the next articles in these series.

<div id="tc3"></div>

### 3.- DEPENDENCIES.

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

A package will have  Java main class will build the application and run the application following these steps:

1. Initialize driven actors. For example a database.
2. Instantiate driven adapters with driven actors.
3. Apply Dependency Configurable Pattern on driven side, i.e. instantiate hexagon classes passing driven adapters as arguments.
4. Apply Dependency Configurable Pattern on driver side, i.e. instantiate a driver adapter with the hexagon class that implements the driver port.
5. Run the driver adapter.

<div id="tc3"></div>

### 

<div id="tc4"></div>

###4.- MODULES vs LAYERS

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
