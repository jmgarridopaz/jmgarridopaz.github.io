---
layout: default
---
<div id="title">
<div class="title_text"><h1>Hexagonal Architecture</h1></div>
<div class="title_text"><h2>THE "RIGHT" BOUNDARY</h2></div>
<div class="title_up_line"><hr></div>
<div class="published">Published on November 27, 2020 by <a href="{{ site.github.owner_url }}">{{ site.github.owner_name }}</a></div>
</div>

#### TABLE OF CONTENTS

1. [The Mistake](#tc1)
2. [How did I realize of](#tc2)
3. [Domain Driven Design & Hexagonal Architecture](#tc3)
4. [Driven Port (Hexagonal Architecture)](#tc4)
5. [Anti Corruption Layer (Domain Driven Design)](#tc5)
6. [Fitting "Domain Driven Design" into "Hexagonal Architecture" at Driven Side)](#tc6)
7. [Links](#tc7)

<div id="tc1"></div>
### 1.- THE MISTAKE.

This article tries to clear up a misconception I had regarding Hexagonal Architecture I've recently realized of. It has to do with the "right" (driven) side of the hexagon:

Where do we put the boundary (port interfaces) between the inside and the outside of the hexagon in the driven side?

When I had a domain concept that depended on external systems, I used to put an interface to abstract the process of building its instances. I gave that interface the "driven port role", and I gave that process the "driven adapter role", no matter how complex the process was, or how many external systems it had to access. This is a "Domain Driven Design" ( DDD ) influence. That adapter is called "Anti Corruption Layer" ( ACL ) in DDD lingo.

But Hexagonal Architecture has nothing to do with DDD, Hexagonal Architecture knows nothing of DDD, Hexagonal Architecture is just a pattern that says: Put a driven port interface for any "real world thing" (driven actor) that the hexagon needs to talk to.

That was my mistake: I put a driven port for the domain concept, instead of putting a driven port for each external system.

<div id="tc2"></div>
### 2.- HOW I DID REALIZE OF.

I was aware of my mistake about the location of driven ports after <a target="_blank" href="https://twitter.com/JuanMGarridoPaz/status/1316276051866157056">asking Alistair Cockburn about it on Twitter</a>. He answered "yes" to all of these:

=====

So, Alistair, if a task doesn't need to touch any technology (for example) translate A into B (once we got A from a repo), my mistake is that I shouldn't put that task in an adapter, but inside the hexagon. The adapter is just fetching A from the repository. Did I get you right?

I mean if we need to translate concepts locally it would be like having a sublayer of local adapters inside the hexagon, but that's our decision and hexagonal architecture doesn't say anything about it.

Similar to split the hexagon into application layer and domain. It's a DDD thing that you can do or not, but it isn't a hexagonal architecture issue.

=====

And also after reading again this <a target="_blank" href="https://web.archive.org/web/20170925184018/http://alistair.cockburn.us/Hexagonal+Architecture+FAQ">"Hexagonal Architecture FAQ"</a>, where Alistair Cockburn defines how to create an hexagonal application. Here he says that a port is an interface that groups interactions with a repository/recipient:

=====

step 1. Draw a circle (or a hexagon). Put everything that touches the real world on the outside. That includes humans, servers, databases, ...

step 2. Organize all the interactions across the boundary of that hexagon... things that provide information to the application (I’ll call these "repositories"), things that get notified by the application (I’ll call these "recipients"). Each of those capture a set of verbs or function calls or interactions, and makes a "port" or "interface"...

step 3. Label those clusters with a verb ending in "-ing" (present participle) saying what they are for. For example: ... This repository port is for "obtaining information about (whatever)". This recipient port is for "notifying recipients about (whatever)”

step 4. Allow any desired technology to operate to those interfaces using some adapter code... on the recipient or repository side, adapters allow different technologies to respond to requests and notification: mock databases, flat files, real databases, and so on.

...

=====

By reading again all of these, and of course the original article with the pattern too, I realized that a driven port is just for talking to a driven actor (repository/recipient) using a technology, i.e. for sending/retrieving information to/from a driven actor according to a technology. But nothing else, no aditional logic for translating or processig that information into another one.

<div id="tc3"></div>
### 3.- DOMAIN DRIVEN DESIGN & HEXAGONAL ARCHITECTURE.

Domain Driven Design is not Hexagonal Architecture, is not an architecture, is not just a set of tactical patterns, is not a methodology for developing software...

DDD is a philosophy, is defining an ubiquitous language, is talking to domain experts using the ubiquitous language for distilling the core domain, is splitting the whole domain context into smaller bounded contexts according to the ubiquitous language, is defining the relationships between bounded contexts...

So which is the relationship between DDD and Hexagonal Architecture?

Well, each bounded context (software application for a subdomain) is autonomous and can be implemented however we want to. One of the options is to use Hexagonal Architecture for structuring the application.

But Hexagonal Architecture knows nothing about DDD, right? It is just a pattern that says how to structure your application.

It's very popular how DDD fits into Hexagonal Architecture at driver side:

Hexagonal Architecture driver ports are the use cases boundary, and so are DDD application services. So application services and domain model would be inside the hexagon. User Interface would be outside (driver adapter).

But what about Hexagonal Architecture driven ports and DDD?

Let's see...

<div id="tc4"></div>
### 4.- DRIVEN PORT (HEXAGONAL ARCHITECTURE).

Hexagonal Architecture just says that the hexagon is not allowed to touch real world technologies (files, databases, network, servers, ...)

And how does Hexagonal Architecture solve this?

It puts an interface (driven port) at the boundary of the hexagon, for talking to an external secondary actor (repository/recipient). And it also puts a driven adapter outside the hexagon, for each technology the actor might use.

This adapter just sends/retrieves the information to/from the driven actor using a technology. For example, an adapter for a relational database could use SQL sentences to write/read the database, an adapter for a remote repository could use web calls to a REST API with JSON requests/responses, etc.

If the secondary actor is a repository, it's a "round trip" information, a "boomerang".

If the secondary actor is a recipient, the information gets just a "one way ticket", the hexagon "shoot and forget".

The port deals with objects representing the information sent/retrieved to/from the driven actor.

That's the hexagon boundary.

If then we need to process (translate, merge with other, whatever) that information to obtain objects belonging to our domain, we will have to add logic inside the hexagon to do it. But that's not a Hexagonal Architecture issue.

<div id="tc5"></div>
### 5.- ANTI CORRUPTION LAYER (DOMAIN DRIVEN DESIGN).

An Anti Corruption Laye is an integration pattern between two bounded contexts ( Upstream ---> Downstream ). Upstream context is the "server" application, and downstream context is the "client".

Vaughn Vernon, in his book "Implementing Domain Driven Design" says the following about ACLs:

On page 93 (ACL definition):

=====

As a downstream client, create an isolation layer to provide your system with functionality of the upstream system in terms of your own domain model. This layer talks to the other system through its existing interface, requiring little or no modification to the other system. Internally, the layer translates in one or both directions as necesary between the two models.

=====

On page 101 (Technical characteristics of an ACL):

=====

A domain service can be defined in the downstream context... A client domain service implementation accesses a remote service. Server returns representations in a shared common language (for example XML or JSON). The downstream ACL translates representations into domain objects of its local context, not of the upstream model.

=====

<div id="tc6"></div>
### 6.- FITTING "DOMAIN DRIVEN DESIGN" INTO "HEXAGONAL ARCHITECTURE" AT DRIVEN SIDE.

So the mistake is to give the "driven port role" to a domain service interface when the implementation of such an interface does much more things than just sending/retrieving the information to/from a driven actor.

The ACL concept from DDD is wider than the driven adapter concept from Hexagonal Architecture.

The things that an ACL does besides talking to repositories (mixing their information into another, translating concepts, processing that information, whatever) are not a Hexagonal Architecture issue. Those things are logic that belongs to the inside of the hexagon, and Hexagonal Architecture says nothing about that, it's a DDD issue.

We could add a sublayer inside the hexagon for that "ACL logic", just like splitting the hexagon at the driver side into application layer and domain, which is also a DDD issue.

![Figure 1: ...](/assets/images/figure.png)
<p class="figure">Figure 1: ...</p>

Hexagon = Domain + ACL logic

ACL = ACL logic + HA ports + HA adapters

We want to fit DDD into Hexagonal Architecture, not the oher way. Hexagonal Architecture is already there and it defines what a driven port is. If we want to create an interface inside the hexagon (a domain service interface) to protect the domain, we shouldn't say it is a Hexagonal Architecture driven port, because it is not. Hexagonal Architecture says: "Put a driven port for the repository". It doesn't say: "Put a driven port for an internal domain concept to build it from others".

Thomas Pierrain has written some articles about this subject, the driven side of the hexagon, which I refer to in the links section.

<div id="tc7"></div>
### 7.- LINKS

* Ports and Adapters Pattern (aka Hexagonal Architecture), by Alistair Cockburn:

<a target="_blank" href="https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture">https://web.archive.org/web/20180822100852/http://alistair.cockburn.us/Hexagonal+architecture</a>

* My conceptual article about Ports and Adapters Pattern (aka Hexagonal Architecture):

<a target="_blank" href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html">https://jmgarridopaz.github.io/content/hexagonalarchitecture.html</a>

* Alistair in the Hexagone: a talk by Alistair Cockburn about Hexagonal Architecture:

<a target="_blank" href="https://www.youtube.com/watch?v=th4AgBcrEHA&list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL">https://www.youtube.com/watch?v=th4AgBcrEHA&list=PLGl1Jc8ErU1w27y8-7Gdcloy1tHO7NriL</a>

* "Hexagonal Architecture FAQ", questions by Stefane Wojewoda to Alistair Cockburn, at InfoQ in France:

<a target="_blank" href="https://web.archive.org/web/20170925184018/http://alistair.cockburn.us/Hexagonal+Architecture+FAQ">https://web.archive.org/web/20170925184018/http://alistair.cockburn.us/Hexagonal+Architecture+FAQ</a>

* "Hexagonal Architecture: Don't get lost on your right-side", by Thomas Pierrain:

<a target="_blank" href="http://tpierrain.blogspot.com/2020/03/hexagonal-architecture-dont-get-lost-on.html">http://tpierrain.blogspot.com/2020/03/hexagonal-architecture-dont-get-lost-on.html</a>

* "Adapters a true heralds of DDD", by Thomas Pierrain:

<a target="_blank" href="http://tpierrain.blogspot.com/2020/04/adapters-are-true-heralds-of-ddd.html">http://tpierrain.blogspot.com/2020/04/adapters-are-true-heralds-of-ddd.html</a>

<br/>

<div class="commentbox"></div>
