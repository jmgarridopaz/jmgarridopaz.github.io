---
title: Configurable Dependency
description:
custom_article_title: Configurable Dependency
custom_article_subtitle:
custom_chapter_title:
custom_published_date: January 8, 2023
custom_author_url: https://github.com/jmgarridopaz
custom_author_name: Juan Manuel Garrido de Paz
layout: default
---

<p class="intro">This is a copy of a blog post written by Alistair Cockburn, who published it on March 18, 2012, at his old web site, which is offline nowadays.
  
  , where he talks about "Configurable Dependency"  published on March 18, 2012, a blog post about "Configurable Dependency" in his old web site, which is offline nowadays. This is a copy of the snapshot http://web.archive.org/web/20170624023207/http://alistair.cockburn.us/Configurable+Dependency, </p>

### Configurable Dependency

__Pattern name: Configurable Dependency__

to replace Dependency Injection and Inversion of Control, the former a specific implementation, the latter a double negative.

(This pattern needs to be fleshed out).

My dissatisfaction with Inversion of Control and Dependency Injection goes back to their first naming - both names are wrong in type. The term Inversion of Control says: "You did it wrong, now go back and invert it." But what would you teach someone so that they never get it wrong? The term Dependency Injection says what to do: pass the dependency information in. But that's an action.

A good pattern names a common property of good examples of something (software design in this case). The property version of Dependency Injection would be "Injected Dependency", but that still doesn't name what it is that we are after - the property we want present in the design.

conf_dep_illustrated_800px.jpeg

Gerard Meszaros today pointed out that the property we want is that the dependency is configurable, "configurable dependencies", he said. Gerard clarified further that Dependency Injection is only one way to get this property (which we still had not named at this point in the discussion, I was busy angling for "Parameterized Dependency"). Dependency Lookup would be another. That's when I decided that Configurable Dependency is the best (current) name for the property and the pattern, with Dependency Injection as one implementation pattern and Dependency Lookup as another.

The good news about this renaming is that the pattern becomes something that is phrased in the positive, so I can put in my undergrad text on Software Engineering, and teach it to beginners so they just "get things right" from the beginning, not having to do it wrong first and right second. It also names the property we are after, so we can argue over when we want that property present, and when not (it introduces complexity, so one wouldn't want it everywhere).

And finally, as with any pattern, there is an upside and a downside. The upside is configurability, the downside is complexity. So there should always be a discussion - do we want this benefit for this cost? That's the way a pattern should work.

The pattern still needs to be written in detail. Anyone care to help with that?

How do we choose a name?

The ideal name is a noun that names a property that is present in well-constructed implementations. (The proper term for Patterns should be a phrase with a dangling referent: Common Properties of Good Examples of X. Thus you have X Patterns, as you should). That's why I don't like either Dependency Injection or Inversion of Control.

In an ideal world, let's pretend for a minute that Configurable or Swappable passed our tests, I would be able to teach new students design according to this list:

- What is a dependency? What isn't?
- When do we want our dependency to be swappable/configurable?
- What are ways to make it swappable/configurable? (Dependency Injection and Dependency Lookup being two alternative implementation routes)

Other names we looked at were:

- Parameterized Dependency
- Swappable Dependency
- Externalized Dependency

Needing an icon or image, but for now:

yin_yang.gif

Discussion

I would not mind helping write it up

-by Chris on 3/18/2012 at 11:06 PM

email me? thx AC

===

Is this the same or only releated to Kevlin Henney’s Parameterise from Above (PfA)?
Kevlin Henney. The PfA Papers: From the Top,
ACCU Overload Journal #80 – Aug 2007,
accu.org/index.php/journals/1411

Kevlin Henney. The PfA Papers: The Clean Dozen,
ACCU Overload Journal #81 – Oct 2007
accu.org/index.php/journals/1420

Kevlin Henney. The PfA Papers: Context Matters,
ACCU Overload Journal #82 – Dec 2007
accu.org/index.php/journals/1432

Kevlin Henney. The PfA Papers: Deglobalisation,
ACCU Overload Journal #83 – Feb 2008
accu.org/index.php/journals/1470

Cheers, Martin

-by Martin Moene on 3/19/2012 at 4:24 AM


Thanks, Martin – I was unaware of that – will research and write Kevlin…. could be I just came up with the same thing. I notice that he chose an imperative-verb name. thx.

-by Alistair on 3/19/2012 at 10:33 AM


Your remark reminds me of Allan Kelly’s “Encapsulated Context”, formerly known as “Encapsulate Context” ;)
allankelly.net/patterns/index_assets/encapsulatecontext.pdf

-by Martin Moene on 3/19/2012 at 1:54 PM


I would call this pattern “Managed Dependencies” in opposite to its anti-pattern “Unmanaged Dependencies”. Word “configurable” confuses me a little.
-by Paul on 3/20/2012 at 5:11 PM

Paul: Chris Collins has the same sort of problem: “configurable dependency sounds like it is geared towards wiring up or configuring which concrete dependency I want utilized in the object at runtime. Not sure it covers the purpose of DI as removing the responsibility of the creation of the dependency from inside the object to outside the object.”

I agree, so I’m open for suggestion …

Swappable Dependency is a name that could work for me – I’ll give my tests for names in a sec. There’s some text on the web under that name, quite similar in intent. I like some of the text at http://troygilbert.com/2010/04/invalidation-pattern/ and there’s something at http://stackoverflow.com/questions/8599678/openrasta-swap-instance-in-dependency-resolver

The ideal name is a noun that names a property that is present in well-constructed implementations. (The proper term for Patterns should be a phrase with a dangling referent : Common Properties of Good Examples of x). That’s why I don’t like either Dependency Injection or Inversion of Control. “Managed” doesn’t say yet what the property is.

In an ideal world, let’s pretend for a minute that Configurable or Swappable passed our tests, I would be able to teach new students design according to this list:

What is a dependency? What isn’t?
When do we want our dependency to be swappable/configurable?
What are ways to make it swappable/configurable? (Dependency Injection and Dependency Lookup being two alternative implementation routes)
Configurable Dependencies and Swappable Dependencies both have those properties, Configurable has the unhappy associations you mention. What else could pass those tests?

Thanks – Alistair

(p.s. if you want to put URLs in here, leave off the http:// or the security system in here won’t let you post. I’ll add the http:// later to make it a live link.)


“removing the responsibility of the creation of the dependency from inside the object to outside the object” => Externalized Dependency.
-by Paul on 3/21/2012 at 12:28 PM


nice
-by Alistair on 3/21/2012 at 1:48 PM


Hi Alistair,
you said: “Configurable Dependency is the best (current) name for the property and the pattern, with Dependency Injection as one implementation pattern and Dependency Lookup as another.”

Have you seen Mark Seemann’s answer to the Stackoverflow question “Dependency injection: should I inject everything or use a service locator for some objects?”:

“When it comes to the concern about cluttering the constructors, it’s most likely a code smell that the classes are violating the Single Responsibility Principle. Constructor Injection is very beneficial here because it make this much more obvious.

Some people also worry about injecting dependencies which are only rarely used, but that’s not a problem either. When it comes to creating object graphs, performance is rarely a problem, and even if it is, the Virtual Proxy pattern can fix it.

In short, there’s no reason to ever use a Service Locator. There’s always a better alternative that involves proper inversion of control.”

Philip

-by Philip Schwarz on 3/21/2012 at 7:30 PM


Thx, Philip, i’ll pass that along to Gerard, who wrote up Dependency Locator. He uses it for test suites, I’m curious how he feels about that final assertion.

Gerard writes, “Service Locators are most often needed when retrofitting stubability into legacy code.”

-by Alistair on 3/21/2012 at 11:16 PM


I’ll tell you all what I’d really like to do : collect a few thousand lines of code from different systems and categorize every dependency as
method parameter
constructor parameteer
hard-coded
or whatever else,
and publish the results and take a look at what people think is good or bad about those stats.
Starting from what is presumably pretty decent code to start with.

I’m getting curious as to what counts as a dependency and what counts as a decent way to manage dependencies in different places.
Alistair

-by Alistair on 3/21/2012 at 11:21 PM


Alistair,
I like your idea at looking at existing code and analyzing dependencies. I wonder if there would be some value to looking at existing code that was not written with Configurable Dependencies(CD) in mind and then refactor the code to use CD or a DI container and compare and contrast the two styles and try to qualitatively and/or quantitatively measure the benefits of using CD and/or a DI container?

-by Chris Collins on 3/22/2012 at 3:53 PM


Isn’t this already called Dependency Inversion, or is that also an unsatisfactory name? Wikipedia/Dependency_inversion_principle
-by Steve Wedig on 3/22/2012 at 6:46 PM


that name is what started it all … Invert from what? Answer: invert from the wrong way to the right way. OK: what’s the right way? Answer: we don’t know, that’s why we’re here. OK, so when you know what the right way is, why don’t you just people how to write it correctly in the first place, then you don’t have to invert anything. Oh. ok, what’s the right way? That’s why we’re here

-by Alistair on 3/23/2012 at 1:34 AM


It does beg that question. I’ve started using Dependency Pyramid as the opposite of Dependency Inversion. In the physical world, the most direct way to construct something is to place things on top of each other. From this starting point, inverting the dependencies means introducing interchangeable parts, meaning components that depend on interfaces, not implementations.
This also fits with Alan Kay’s quote:
“Most software today is very much like an Egyptian pyramid with millions of bricks piled on top of each other, with no structural integrity, but just done by brute force and thousands of slaves.”

I guess I could believe there is a clearer terminology though. I might prefer “interchangeable” rather than “configurable”, because that emphasizes dependence on interfaces, rather than emphasizing the benefit of configurability. That being said, I find interchangeable too hard to type.

-by Steve Wedig on 3/23/2012 at 1:04 PM
Thx Steve – consider Interchangeable in the list. the naming will always be problematic. Kevlin Henney commented that the dominant concern in his mind is being able to set it, not being able to change it afterward. So he preferred Configurable to Swappable. Whatever name should highlight the dominant property first; the other lesser incorrect implications may have in the writeup.

-by Alistair on 3/23/2012 at 1:34 AM


I agree, however for me the dominant concern is broader than configurability: Libraries should depend on interfaces, not implementations.
That principle leads to several benefits (one of which is of course configurability):
- Client can choose the dependency (configurable).
- Library doesn’t need to know where/how to get/build the dependency (creation/usage separation).
- Enables the library to depend on a small interface, rather than an entire implementation (interface segregation).
- Library can be tested using doubles before a real dependency implementation even exists.
- I’m sure there are more benefits following from the increased modularity…

So configurable seems to focus on just one (important) benefit. Perhaps these names would be broader:
- Provided Dependency: Dependency Inversion is when a dependency is provided to the library.
- Retrieved Dependency: Dependency Pyramid is when a library retrieves it itself.

-by Steve Wedig on 3/23/2012 at 2:25 PM


I think we are trying to find one name for two distinct features of this “still to be named pattern”:
1. Names like Configurable/Swappable/Settable Dependency describes ability to set/change dependency.

2. Names like Externalized/Provided/Supplied Dependecy emphasis the place of the definition of dependency (outside the object).

I think it is impossible to find one name which address both of these features. For me, more important is feature #2 and feature #1 is secondary. How often do you change once established dependency?

Additional question:
Doesn’t externalizing dependency implies configurability in some way?

-by Paul on 3/26/2012 at 4:38 AM


Teedy Deigh. A Position on Running Interference in Languages.
There is much debate about the merits of different approaches to
type systems. Teedy Deigh considers how to make the most of them.

accu.org/var/uploads/journals/overload108.pdf#page=37

;)

-by Martin Moene on 4/13/2012 at 7:22 AM
