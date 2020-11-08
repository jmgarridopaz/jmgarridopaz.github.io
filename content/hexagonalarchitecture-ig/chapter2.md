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
<div class="left"><a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter1.html">Previous Chapter</a></div>
<div class="center"><a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/intro.html">Intro Chapter</a></div>
<div class="right"><a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter3.html">Next Chapter</a></div>

#### TABLE OF CONTENTS

1. [Introduction](#tc1)
2. [Source code organization](#tc2)  
   2.1. [One module for the hexagon](#tc2-1)  
   2.2. [One module for each adapter](#tc2-2)  
   2.3. [One module for the application startup](#tc2-3)
3. [Dependencies](#tc3)
4. [Modules vs Layers](#tc4)
5. [Links](#tc5)

<div id="tc1"></div>
### 1.- INTRODUCTION

This is the second article of a series showing how to implement an application according to Hexagonal Architecture, also known as Ports and Adapters pattern, whose author is Dr. Alistair Cockburn.

The example application is called BlueZone, and it is described and designed in <a target="_blank" href="https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter1.html">Chapter 1</a>.

The <a target="_blank" href="https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture">pattern definition</a> doesn't say anything about how to implement the architecture. My **vision of Hexagonal Architecture is modular**, with the hexagon at the center, and adapters around it, belonging to no layer, each one attached to a port of the hexagon. The different adapters depending on a port stand for different swappable technologies that the actor communicating with the hexagon might use.

In this chapter we will see how to organize the source code in modules using Java 9, dependencies between modules ("who knows of whom"), and how to configure those dependencies.

<div id="tc2"></div>
### 2.- SOURCE CODE ORGANIZATION

The code base of the example application is at <a target="_blank" href="https://github.com/jmgarridopaz/bluezone">BlueZone GitHub repository</a>.

The whole project is structured in modules. In Java 9, a module is a set of packages, that you can compile apart generating a jar file. Modules depend on each other.

I've put all the modules in just one GitHub repository, so that you can see and build them all together, but Java 9 allows you to have them in different repositories, compile them apart, and then provide their locations at runtime to choose them dynamically. We will see this later on, at the end of this article series, as an advanced way of execution.

Now let's see the modules and the convention I follow for naming them and their packages.

The important thing with this way of naming modules, is that when we see a module name at first glance, we know whether it is business logic or it deals with real world technology, since the first word after the application name is either "hexagon" or "adapter".

![Figure 1: Modules naming convention](/assets/images/hexagonalarchitecture-ig/figure2-1.png)

<p class="figure">Figure 1: Modules naming convention (*)</p>
<p class="figure">(*) bluezone-parent is a maven configuration module, nothing to do with a Java 9 source code module</p>

<div id="tc2-1"></div>
#### 2.1.- ONE MODULE FOR THE HEXAGON, with name:  `<app_name>.hexagon`

Hexagonal Architecture pattern says nothing about how to structure the source code of the hexagon. The pattern just says that the hexagon offers "ports" to the outside world, for allowing external actors to interact with the hexagon.

So how do we "publish" an interface (the port) of a module (the hexagon)?

Before Java 9, a public type was visible to the rest of the world. Now, with modules, a public type is just visible inside its module. For other modules to see the public type, you have to export the package the type belongs to. Exporting a package would be like "publishing" its public types to the outside world.

So we will have **a package for each port of the hexagon**. A package for a port contains a public interface defining the port operations, and public data types that those operations manage.

![Figure 2: Ports packages in hexagon module](/assets/images/hexagonalarchitecture-ig/figure2-2.png)

<p class="figure">Figure 2: Ports packages in hexagon module</p>

The name of a package for a port will be:

~~~
<reverse_domain_name>.<app_name>.hexagon.<side>.<port_name>
~~~

This way ports are grouped by their side (driver/driven), so that we see at a first glance whether the port is driver or driven. This is important, since driver ports operations are called by an adapter, while driven port operations are implemented by an adapter. It's the left/right asymmetry .

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

This stands for a module containing an adapter for an actor using the `<technology>` in the interaction with the hexagon at `<port_name>` port. The `<technology>` part could be as specific and long as you want.

For example:

~~~
<app_name>.adapter.<port_name>.webui
~~~

is an adapter for an actor that interacts with `<port_name>` port using a web UI. We could be more specific and say more about the technology. For example, if we are using the Spring Framework, we could have named the adapter:

~~~
<app_name>.adapter.<port_name>.webui.spring
~~~

If then we develop another web adapter using the Tapestry Framework:

~~~
<app_name>.adapter.<port_name>.webui.tapestry
~~~

Other examples of technology naming could be:

~~~
<app_name>.adapter.<port_name>.test.cucumber
<app_name>.adapter.<port_name>.database.sql.orm.jpa
<app_name>.adapter.<port_name>.database.mongo
<app_name>.adapter.<port_name>.file
<app_name>.adapter.<port_name>.stub
~~~

You can observe that with this naming convention we don't say anywhere whether the adapter is either driver or driven. We don't need to, since that's something that comes with the port. The port of the hexagon is either driver or driven, and so will be the adapter.

In our example application, we are going to develop 2 adapters for each port:

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

The root package for the source code of an adapter would be:

~~~
package <reverse_domain_name>.<app_name>.adapter.<port_name>.<technology>
~~~

<div id="tc2-3"></div>
#### 2.3.- ONE MODULE FOR THE APPLICATION STARTUP, with name: `<app_name>.startup`

This module is the entry point to the application.

You can run the application by running any of the driver adapters, so there will be a package for each driver adapter, containing the source code needed for instantiating the different components of the architecture, wiring them up, and running the driver adapter. 

~~~java
package io.github.jmgarridopaz.bluezone.startup.forparkingcars.test;
package io.github.jmgarridopaz.bluezone.startup.forparkingcars.webui;

package io.github.jmgarridopaz.bluezone.startup.forcheckingcars.test;
package io.github.jmgarridopaz.bluezone.startup.forcheckingcars.cli;
~~~

During my researching on hexagonal architecture, I've studied several techniques for implementing the application startup, and I will show them in detail along the next articles in these series.

<div id="tc3"></div>
### 3.- DEPENDENCIES.

Rules about dependencies between modules are easy:

- The **hexagon module** depends on nothing.

- An **adapter module** for an actor communicating with the hexagon at a port, depends on:
  - The hexagon.
  - The technology used by the actor.
  
- The **startup module** depends on the hexagon and all the adapters.

Regarding to dependencies, in Java 9 every module has a definition file (`module-info.java`) where it declares:
- The modules it "depends on" (requires).
- The packages it "publishes" (exports) to other modules.

So let's see the `module-info.java` file of the modules:

**The hexagon module.**
It contains the business logic, including ports. The hexagon is decoupled from any technology, i.e. from the adapters. If we modify the adapters, the hexagon shouldn't care, so *it doesn't require (depend on) any module*, maybe just some library module with utilities of the programming language if you will.

~~~java
module bluezone.hexagon {
    // driver ports
	exports io.github.jmgarridopaz.bluezone.hexagon.driver.forparkingcars;
	exports io.github.jmgarridopaz.bluezone.hexagon.driver.forcheckingcars;
	// driven ports
	exports io.github.jmgarridopaz.bluezone.hexagon.driven.forobtainingrates;
	exports io.github.jmgarridopaz.bluezone.hexagon.driven.forstoringpermits;
	exports io.github.jmgarridopaz.bluezone.hexagon.driven.forpaying;
    
	...
	...
}
~~~
*It publishes (exports) the packages with the ports*. Each port package just publishes the interface defining port operations, and the data types that those operations manage. Any logic implementing the driver ports is hidden to other modules.
It is also possible to declare the modules you want to export a package to, so that you could export a port just to its adapters. But we would have to change this file and recompile the hexagon, every time we add a new adapter.

**The adapters modules.**
An adapter module for a port, requires the hexagon module, and other modules related to libraries and frameworks, according to the technology that the actor behind the port is using.
For example, the definition file for `bluezone.adapter.forparkingcars.test` module is:

~~~java
module bluezone.adapter.forparkingcars.test {
   /*
    * DEPENDENCIES: Hexagon, libs, test framework
    */
	requires bluezone.hexagon;	
	
	requires io.github.jmgarridopaz.lib.portsadapters;
	
	requires io.cucumber.java;
	requires junit;
	requires io.cucumber.datatable;
	requires io.cucumber.core;

    ...
}
~~~
We can see that it requires the hexagon module, since it uses `forparkingcars`  port, and it also requires modules about "cucumber", which is the specific technology used by the actor (test cases).
`io.github.jmgarridopaz.lib.portsadapters` module is a library of my own, where I define types regarding hexagonal architecture.

**The startup module.**
It builds the application and runs it, so it needs access to all modules: hexagon and adapters.

~~~java
module bluezone.startup {
	/*
	 * Depends on all the other modules: hexagon and adapters
	 */
	requires bluezone.hexagon;
	requires bluezone.adapter.forparkingcars.test;
	requires bluezone.adapter.forcheckingcars.test;
	...
}
~~~
This module configures the dependencies between the hexagon and the adapters, applying the Configurable Dependency Pattern at both driven and driver side, following these steps:
1. For each driven port, instantiates a driven adapter, which implements the driven port interface.
2. Apply Configurable Dependency Pattern on driven side: Instantiate the hexagon class, which knows of driven ports, and implements a driver port interface.
3. Apply Configurable Dependency Pattern on driver side: Instantiate a driver adapter, which knows of the driver port of the hexagon.
4. Run the driver adapter.

Here is a hand-drawn diagram I made once about this topic:

![Figure 3: Dependency Configurator in Startup Module](/assets/images/hexagonalarchitecture-ig/figure2-3.png)

<p class="figure">Figure 3: Dependency Configurator in Startup Module</p>

I offered this picture to Alistair Cockburn, who published it on Twitter: <a target="_blank" href="https://twitter.com/TotherAlistair/status/1122918489558278145">https://twitter.com/TotherAlistair/status/1122918489558278145</a>

At either the driver or driven side, the Configurable Dependency Pattern applies as follows:

> 
>
> *The one who triggers the interaction has to know the dependency.*
>
> 

So at driver side, a driver adapter depends on an abstraction of the hexagon (a driver port):

~~~java
public abstract class DriverAdapter<T> {

	private final T driverPort;
	
	protected DriverAdapter ( T driverPort ) {
		this.driverPort = driverPort;
	}
	
	protected T driverPort() {
		return this.driverPort;
	}

	public abstract void run ( String[] args );

}
~~~

And at driven side, the hexagon depends on an abstraction of the driven adapter (a driven port):

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
    
    // ...
~~~

<div id="tc4"></div>
### 4.- MODULES vs LAYERS.

Hexagonal Architecture is an ***object structural pattern***, it is not an architecture style.

So it is not layered "per se", it is not component-based (modular) "per se", etc. It is whatever you want it to be, in the sense that the architecture style you use is an implementation detail that the pattern doesn't fix. You can implement it using any architecture style, as long as it fits the structure and relationship rules of the pattern.

For example, I see two architecture styles we can implement the pattern with:

* **Modular**: This is the approach I follow in these article series, as we have just seen in the previous sections.
  - One module for the hexagon.  
  - One module for each adapter.

* **Layered**: It splits source code into two layers (inside vs outside), with just a dependency rule: "outside" depends on "inside".
  - Inside layer: the hexagon.  
  - Outside layer: all the adapters (drawn in an outer hexagon).

I see the modular approach more flexible, since we can add adapters dynamically, without having to recompile the other adapters in the layer.

Whatever approach you choose, there are some key concepts to consider for fitting Hexagonal Architecture pattern rules:

1. **Ports are not a layer themselves.** A driver port is part of the hexagon API, and a driven port is part of the hexagon SPI. But both are a part of the hexagon, they are interfaces that belong to the business logic of the application, i.e. to the hexagon. In Java 9, ports are interfaces into packages that the hexagon module publishes to other modules. The other packages are hidden to the outside world.

2. **An adapter depends on the hexagon at one of its ports.** An adapter is a piece of software playing a role (driver/driven) against a port. A driver adapter will be a Java class that "uses" a driver port interface, and a driven adapter will be a class that "implements" a driven port interface. In Java, this is achieved by using generic types. For example, a driver adapter is a generic class with one type parameter, which is the driver port interface that it uses. Whether an adapter plays different roles: it drives more than one port; or it is driven by more than one port; or it is driven by a hexagon port and it drives a port of another hexagon... it's up to you, but then you should know that you are not following the <a target="_blank" href="https://en.wikipedia.org/wiki/Single_responsibility_principle">SRP (Single Responsability Principle)</a>.

3. **Adapters are independent from each other.** An adapter doesn't know of the others, it just depends on the hexagon, and on the actor whose technology it is adapting (for example a database). But an adapter shouldn't be able to access another adapters. This is why if we go for the two layers implementation, we should care about protecting visibility between adapters, since a layered architecture allows a component to access other components in the same layer. In Java, we have the default access modifier (package-private), which makes a type visible to just the members in the same package. So you should have a package for each adapter, and make types in each package not accessible from others, i.e. make them package-private.

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
