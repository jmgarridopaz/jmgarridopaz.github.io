---
title: The right boundary - Hexagonal Architecture
custom_article_title: Hexagonal Architecture
custom_article_subtitle: The "right" boundary
custom_chapter_title:
custom_published_date: November 27, 2020
custom_author_url: https://github.com/jmgarridopaz
custom_author_name: Juan Manuel Garrido de Paz
layout: default
---

<div class="published">Updated on May 19, 2022 by <a href="{{ page.custom_author_url }}">{{ page.custom_author_name }}</a></div>
<div class="published">[Added URLs with video and document by Eric Evans]</div>

#### TABLE OF CONTENTS

1. [The Mistake](#tc1)
2. [How did I realize of](#tc2)
3. [Domain Driven Design & Hexagonal Architecture](#tc3)
4. [Driven Port (Hexagonal Architecture)](#tc4)
5. [Anti Corruption Layer (Domain Driven Design)](#tc5)
6. [Fitting "Domain Driven Design" into "Hexagonal Architecture" at Driven Side](#tc6)
7. [Example application](#tc7)  
   7.1. [DDD Context Map](#tc7-1)  
   7.2. [Implementation using Hexagonal Architecture](#tc7-2)  
   7.3. [About testing](#tc7-3)
8. [Links](#tc8)

<div id="tc1"></div>
### 1.- THE MISTAKE.

This article tries to clear up a misconception I had regarding Hexagonal Architecture I've recently realized of. It has to do with the "right" (driven) side of the hexagon:

___Where do we put the boundary (port interfaces) between the inside and the outside of the hexagon in the driven side?___

When I had a domain concept that depended on an external system, I used to put an interface to abstract the process of building its instances. I gave that interface the "driven port role", and I gave that process the "driven adapter role", no matter how complex the process was, or if it had to translate the model of the external system into the domain model of my application. This is a "Domain Driven Design" ( DDD ) influence. That adapter is called "Anti Corruption Layer" ( ACL ) in DDD lingo.

But Hexagonal Architecture has nothing to do with DDD, Hexagonal Architecture knows nothing of DDD, Hexagonal Architecture is just a pattern that says: Put a driven port interface for any "real world thing" (driven actor) that the hexagon needs to talk to.

That was my mistake: I put a driven port for the domain concept, instead of putting a driven port for the external system.

<div id="tc2"></div>
### 2.- HOW DID I REALIZE OF.

I was aware of my mistake about the location of driven ports after <a target="_blank" href="https://twitter.com/JuanMGarridoPaz/status/1316276051866157056">asking Alistair Cockburn about it on Twitter</a>. He answered "yes" to all of these:

> 
> So, Alistair, if a task doesn't need to touch any technology (for example) translate A into B (once we got A from a repo), my mistake is that I shouldn't put that task in an adapter, but inside the hexagon. The adapter is just fetching A from the repository. Did I get you right?
> 
> I mean if we need to translate concepts locally it would be like having a sublayer of local adapters inside the hexagon, but that's our decision and hexagonal architecture doesn't say anything about it.
> 
> Similar to split the hexagon into application layer and domain. It's a DDD thing that you can do or not, but it isn't a hexagonal architecture issue.
> 

And also after reading again this <a target="_blank" href="https://web.archive.org/web/20170925184018/http://alistair.cockburn.us/Hexagonal+Architecture+FAQ">"Hexagonal Architecture FAQ"</a>, where Alistair Cockburn defines how to create an hexagonal application. Here he says that a port is an interface that groups interactions with a repository/recipient:

> 
> step 1. Draw a circle (or a hexagon). __Put everything that touches the real world on the outside__. That includes humans, servers, databases, ...
> 
> step 2. __Organize all the interactions across the boundary__ of that hexagon... things that provide information to the application (I’ll call these __"repositories"__), things that get notified by the application (I’ll call these __"recipients"__). Each of those capture a __set of verbs or function calls or interactions, and makes a "port" or "interface"__...
> 
> step 3. Label those clusters with a verb ending in "-ing" (present participle) saying what they are for. For example: ... This __repository port__ is for "obtaining information about (whatever)". This __recipient port__ is for "notifying recipients about (whatever)”
> 
> step 4. Allow any desired technology to operate to those interfaces using some adapter code... __on the recipient or repository side, adapters allow different technologies to respond to requests and notification__: mock databases, flat files, real databases, and so on.
> 
> ...
> 

By reading again all of these, and of course the original article with the pattern too, I realized that a driven port is just for talking to a driven actor (repository/recipient), abstracting the technology this actor might use, i.e. for sending/retrieving information to/from a driven actor regardless of the actor's technology. Then an adapter will implement that communication using the specific technology of the concrete actor. But nothing else, no aditional logic for translating or processing the sent/retrieved information into another one.

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

If then we need to process that information to obtain objects belonging to our domain, we will have to add logic inside the hexagon to do it. But that's not a Hexagonal Architecture issue.

<div id="tc5"></div>
### 5.- ANTI CORRUPTION LAYER (DOMAIN DRIVEN DESIGN).

An Anti Corruption Layer is an integration pattern between two bounded contexts ( Upstream ---> Downstream ). Upstream context is the "server" application, and downstream context is the "client".

Vaughn Vernon, in his book "Implementing Domain Driven Design" says the following about ACLs.

On page 93 (ACL definition):

> 
> As a downstream client, create an isolation layer to provide your system with functionality of the upstream system in terms of your own domain model. This layer talks to the other system through its existing interface, requiring little or no modification to the other system. Internally, the layer translates in one or both directions as necesary between the two models.
> 

On page 101 (Technical characteristics of an ACL):

> 
> A domain service can be defined in the downstream context... A client domain service implementation accesses a remote service. Server returns representations in a shared common language (for example XML or JSON). The downstream ACL translates representations into domain objects of its local context, not of the upstream model.
> 

Also, in his book "Domain-Driven Design Distilled", Vaughn Vernon defines ACL this way:

> 
> An Anticorruption Layer is the most defensive Context Mapping relationship, where the downstream team creates a translation layer between its Ubiquitous Language (model) and the Ubiquitous Language (model) that is upstream to it. The layer isolates the downstream model from the upstream model and translates between the two. Thus, this is also an approach to integration. Whenever possible, you should try to create an Anticorruption Layer between your downstream model and an upstream integration model, so that you can produce model concepts on your side of the integration that specifically fit your business needs and that keep you completely isolated from foreign concepts. Yet, just like hiring a translator to act between two teams speaking different languages, the cost could be too high in various ways for some cases.
> 

Eric Evans, says the following about the ACL, in contrast to the Classic Repository Pattern (see instant 40:50 of the next video):

<a target="_blank" href="https://www.infoq.com/presentations/Strategy-Messy-Legacy-Systems/">https://www.infoq.com/presentations/Strategy-Messy-Legacy-Systems/</a>

If you prefer the paper version, here it is (page 6):

<a target="_blank" href="https://www.domainlanguage.com/wp-content/uploads/2016/04/GettingStartedWithDDDWhenSurroundedByLegacySystemsV1.pdf">https://www.domainlanguage.com/wp-content/uploads/2016/04/GettingStartedWithDDDWhenSurroundedByLegacySystemsV1.pdf</a>

<div id="tc6"></div>
### 6.- FITTING "DOMAIN DRIVEN DESIGN" INTO "HEXAGONAL ARCHITECTURE" AT DRIVEN SIDE.

So my mistake is to give the "driven port role" to a "Domain Service Interface" (D.S.I.) when the implementation of such an interface does much more things than just sending/retrieving the information to/from a driven actor.

The ACL concept from DDD is wider than the driven adapter concept from Hexagonal Architecture.

What an ACL does besides talking to a repository (translating objects the driven port deals with into objects of our domain) is not a Hexagonal Architecture issue. It is logic that belongs to the inside of the hexagon, and Hexagonal Architecture says nothing about it, it's a DDD issue.

We could add a sublayer inside the hexagon for that "ACL logic", just like splitting the hexagon at the driver side into application layer and domain, which is also a DDD issue.

![Figure 1: Hexagonal Architecture "right" side with ACL](/assets/images/therightboundary/figure1.png)
<p class="figure">Figure 1: Hexagonal Architecture "right" side with ACL</p>

Hexagon = Domain + ACL logic + HA driven port

ACL = ACL logic + HA driven port + HA driven adapter

We want to fit DDD into Hexagonal Architecture, not the oher way. Hexagonal Architecture is already there and it defines what a driven port is. If we want to create an interface inside the hexagon (a domain service interface) to protect the domain, we shouldn't say it is a Hexagonal Architecture driven port, because it is not. Hexagonal Architecture says: "Put a driven port for the repository". It doesn't say: "Put a driven port for an internal domain concept to build it from others".

The following picture summarizes how DDD would fit into Hexagonal Architecture, at both driver and driven sides.

![Figure 2: DDD into Hexagonal Architecture](/assets/images/therightboundary/figure2.png)
<p class="figure">Figure 2: DDD into Hexagonal Architecture</p>

<div id="tc7"></div>
### 7.- EXAMPLE APPLICATION.

I've coded a simple example application, called Task Management, that you can see in this Github repository:

<a target="_blank" href="https://github.com/jmgarridopaz/task-management">https://github.com/jmgarridopaz/task-management</a>

___Task Management___ is an application, a collaborative tool, used by employees of a company for assigning tasks to each other. The domain model has concepts like "assignee", that is the employee who a task is assigned to, and has to perform it. An assignee has an id, a unique short name kindof an alias, and an email.

<div id="tc7-1"></div>
#### 7.1.- DDD CONTEXT MAP.

We assume two already existing systems:

- _Company_, with information about employees, which have properties like id, first name, last name, email, job.
- _Identity and Access_, with information about the users of our application. Users have an id, a login name, and a password. A user is an employee, the user id is the employee id.

Using "Domain Driven Design" ( DDD ) strategic patterns, we have the following context map showing how our application integrates with these external systems:

![Figure 3: Context Map](/assets/images/therightboundary/figure3.png)
<p class="figure">Figure 3: Context Map</p>

Both "upstream" (U) systems offer an "Open Host Service / Published Language" (OHS/PL), which is a HTTP REST API as the OHS, using JSON as the PL.

Our _Task Management_ application is "downstream" (D), with an "Anti Corruption Layer" (ACL) for translating concepts from U models into our D model. So we build an assignee from an employee (id and email) and a user (login name).

<div id="tc7-2"></div>
#### 7.2.- IMPLEMENTATION USING HEXAGONAL ARCHITECTURE.

This drawing shows interfaces, classes and packages from source code:

![Figure 4: Hexagonal Architecture implementation](/assets/images/therightboundary/figure4.png)
<p class="figure">Figure 4: Hexagonal Architecture implementation</p>

Ports are APIs isolating the application from the real world. In our case, both external systems are real world items (http repositories). So there will be driven ports for getting information (employees and users) from them.

When our application needs to get an assignee from an id, it uses a domain service interface ("AssigneeService"). The ACL implements this interface in two steps:

1. Accesses the repositories using ports and adapters, in order to get the objects (employe and user) from the external systems. This operation deals with techonology.
2. Translates employee and user objects into an "assignee" object. This is a semantic operation, no technologies are involved.

We put the ACL translation logic inside the hexagon but outside the domain, because it doesn't deal with technology (it isn't infraestructure code), but it deals with concepts that don't belong to our domain (employee and user).

Does this mean or suggest, specially to junior developers, that they could use technology into this ACL because it is outside the domain? Absolutely no, because it is inside the hexagon, it isn't infrastructure code... And rule number one in hexagonal architecture is to isolate the hexagon from real world using APIs (ports). Moreover, it is an architect responsibility to avoid using technology inside the hexagon, so that an inexpert developer couldn't write infrastructure code there. With Maven this is easy to achieve: Just don't put any dependency item in the pom file of the hexagon module.

This is analog to "Application Layer" in the driver side. It is inside the hexagon, but outside the domain. This layer is technology agnostic too, it isn't infrastructure code, but it isn't the domain. It has concepts that don't belong to the domain, like DTOs and DPOs, that we expose to the real world, protecting our domain from external access.

<div id="tc7-3"></div>
#### 7.3.- ABOUT TESTING.

Configurable Dependency pattern allows us to swap external real world items (web repositories) for test doubles (stubs), so that we can test the hexagon in isolation from techonologies.

ACL translation logic is covered by these tests too, since it is not technological stuff and so it belongs to the hexagon.

Does this mean that we cannot just test the domain itself without the ACL? Absolutely no, you can test it by mocking the ACL. But it would be just like testing any other component else inside the hexagon. And hexagonal architecture doesn't say anything about it.

Testing the hexagon in isolation from real world items, driven by test cases, is one of the main goals of hexagonal architecture. Putting ACL logic outside the hexagon would break this.

Here is a pretty cool Cucumber html test report, for the test case that I've implemented in the example:

![Figure 5: Cucumber HTML report](/assets/images/therightboundary/figure5.png)
<p class="figure">Figure 5: Cucumber HTML report</p>

As we can see, employee and user concepts appear in the test case but they don't belong to our domain, they belong to _Company_ and _Identity & Access_ external systems. Why is this? Because we are testing the whole hexagon including the ACL logic, not just the domain. So the data that feed our test case in the "given" clause are the data from the repositories (employees and users). And the driver actor of the real system is also an employee, who is the final real world user of the application, that's why it appears in the "AS a..." clause of the feature.

<div id="tc8"></div>
### 8.- LINKS

- Hexagonal Architecture resources:

<a target="_blank" href="https://jmgarridopaz.github.io/content/resources.html">https://jmgarridopaz.github.io/content/resources.html</a>

- "Hexagonal Architecture: Don't get lost on your right-side", by Thomas Pierrain:

<a target="_blank" href="http://tpierrain.blogspot.com/2020/03/hexagonal-architecture-dont-get-lost-on.html">http://tpierrain.blogspot.com/2020/03/hexagonal-architecture-dont-get-lost-on.html</a>

- "Adapters a true heralds of DDD", by Thomas Pierrain:

<a target="_blank" href="http://tpierrain.blogspot.com/2020/04/adapters-are-true-heralds-of-ddd.html">http://tpierrain.blogspot.com/2020/04/adapters-are-true-heralds-of-ddd.html</a>

<br/>

<div class="commentbox"></div>
