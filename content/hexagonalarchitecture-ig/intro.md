---
layout: default
showcomments: true
---

<div id="title">
<h1>Hexagonal Architecture</h1>
<p>An implementation guide</p>
<hr>
<span class="credits right">Published on March 29, 2020 by <a href="{{ site.github.owner_url }}">{{ site.github.owner_name }}</a></span>
</div>

<p class="intro">When I wrote a conceptual article about Hexagonal Architecture, aka Ports and Adapters pattern, my intention was to show an implementation example as well. It’s been a long time, but never is too late. So here it is, a pragmatic article for sharing my vision of how to implement an application structuring it according to Hexagonal Architecture pattern. As I always do, I try to be faithful to the original pattern and other material from its author, Dr. Alistair Cockburn</p>

#### TABLE OF CONTENTS

1. [Introduction](#tc1)
2. [The Overall Idea](#tc2)
3. [Chapters](#tc3)
4. [Links](#tc4)

<div id="tc1"></div>
### 1.- INTRODUCTION

First of all, I think that it’s good to remember the main goal of Hexagonal Architecture (Ports and Adapters pattern):

Allow “users” (a human being, another application, a batch script, ...) to drive the application “in isolation” from real world devices (databases, files, servers, other applications, ...). In particular, test cases are also drivers of the application. Therefore, Hexagonal Architecture allows business logic to be tested in isolation.

This is important to be reminded, because as we will see later during the development process, the application will be already done when we run the test cases against the isolated hexagon and they pass, using “test doubles” for the real world devices that the hexagon depends on.

At that point the business logic will be done. What will be left is adding real adapters for the UI, database, etc. And from that moment on, those acceptance tests will become regression tests, so that we should pass them after every refactoring to prevent and detect business logic leaking from the hexagon.

Regarding the terminology for defining the different kinds of tests, I've used the term "acceptance" for the hexagon tests, i.e. the business logic, the application in isolation without the adapters. For other people, acceptance tests are end-to-end tests including adapters. Disqussing the right vocabulary falls out of the scope of this article. Regardless how you name it, what you have to know is which components are you testing.

The goal we’ve just seen is achieved by decoupling business logic from technology, so that we will have a modular structure, with the hexagon at the center, rounded by adapters attached to the hexagon ports. But decoupling isn’t the goal per se, it is just a mechanism to achieve the goal.

<div id="tc2"></div>
### 2.- THE OVERALL IDEA

In this article I want to show an implementation guide for describing the structure, the development process, and the startup of a hexagonal architecture application, using some kind of design techniques, mainly custom diagrams, and illustrating it with the development of an example application using Java 9.

However, my intention isn’t just to show the source code of an example in a particular programming language, but to show also the dependencies between the components of the architecture (who knows of whom), and how to configure those dependencies to build and run the whole system, since they are important things to know for a programmer who wants to implement a hexagonal application, regardless of the programming language we use.

Does this mean that the programming language we use for the implementation doesn’t matter? Not at all. It is important, in the sense that you will have to manage to implement the concepts and rules of the application architecture, the better your programming language mechanisms allow you.

When I read about some Java 9 features, they made me think of giving it a try. And I realized that it fits Hexagonal Architecture like a glove. Some reasons why I chose Java 9 are:

- It has a modular system that allows the development of fully modular and decoupled applications.

- You can add modules at runtime. This is useful for adding new adapters to ports.

- It has the so called "services". A "Java 9 service" is an interface that other modules can implement. These implementations are the "service providers". Then at runtime Java detects all the available providers, and the "service client" can choose one of them. This is perfect for swapping adapters for a driven port. The port would be a "Java 9 service", and the available adapters for the port would be "service providers".

- Visibility scopes enhancement. Before Java 9, a public class was visible to the rest of the world. We just had private, package and public scopes. Now with modules, a public class is just visible inside its module. For other modules to see the public class, you have to export the package the class belongs to. To export a package would be like to "publish" its public classes to the outside world. This is great for defining APIs better. For example, a port would be a package of the hexagon module, containing the port interface and other classes that the interface uses. All the other packages wouldn't be visible to the outside.

- It allows to apply the "Configurable Dependency Pattern" without using any framework like Spring to inject dependencies. "Java 9 services" are not exactly the same as dependency injection, because Java 9 does a lookup of implementations instead of dependencies being injected, but using this approach solves the same problem. And it is useful if you want to get rid of dependency injection frameworks.

Along the implementation guide, there will be some points where several alternatives are available. For implementing the example application I choose one of them, but I will always try to explain the others, so that the reader can compare them and choose another one.

<div id="tc3"></div>
### 3.- CHAPTERS

Since this article would be very large, I will write and publish it (and the source code as well) in a series of chapters, one by one. We could say that it is going to be a living article, where feedback and opinion from readers are wellcome.

I’ve structured the implementation guide into the following chapters, and each one will be an article on its own. So if you are interested, stay tunned... I will enable these chapters incrementally, as I write and publish them:

1. Application design.
2. Project structure.
3. The hexagon in isolation.
4. Real adapters.
5. Advanced execution.

These are my intentions, but then life will decide whether or not I will be able to finish it all. I hope I will though, but who knows?... This is life, and live :)

Last, but not least, I would like to thank Alistair Cockburn for being always there, for always answering to my questions. Thank you very much Alistair.

<div id="tc4"></div>
### 4.- LINKS

* Article by Alistair Cockburn defining Ports and Adapters Pattern:

[https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture](https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture)

* My conceptual article about Ports and Adapters Pattern (aka Hexagonal Architecture):

[https://jmgarridopaz.github.io/content/hexagonalarchitecture.html](https://jmgarridopaz.github.io/content/hexagonalarchitecture.html)

* Alistair in the Hexagone: a talk by Alistair Cockburn about Hexagonal Architecture:

[https://www.youtube.com/watch?v=th4AgBcrEHA&list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL](https://www.youtube.com/watch?v=th4AgBcrEHA&list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL)

* Interview with Alistair Cockburn about Hexagonal Architecture:

[https://web.archive.org/web/20170925184018/http://alistair.cockburn.us/Hexagonal+Architecture+FAQ](https://web.archive.org/web/20170925184018/http://alistair.cockburn.us/Hexagonal+Architecture+FAQ)

* My translation of Alistair Cockburn article into spanish:

[https://jmgarridopaz.github.io/content/hexagonalarchitecturespanish.html](https://jmgarridopaz.github.io/content/hexagonalarchitecturespanish.html)

* An article I like about Hexagonal Architecture:

[https://beyondxscratch.wordpress.com/2017/08/19/decoupling-your-technical-code-from-your-business-logic-with-the-hexagonal-architecture-hexarch](https://beyondxscratch.wordpress.com/2017/08/19/decoupling-your-technical-code-from-your-business-logic-with-the-hexagonal-architecture-hexarch)

<div class="commentbox"></div>
