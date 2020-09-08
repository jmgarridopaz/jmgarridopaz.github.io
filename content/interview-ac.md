---
layout: default
---

<div id="title">
<h1>Interview with Alistair Cockburn</h1>
<p>About Hexagonal Architecture... and more</p>
<hr>
<span class="credits right">Published on September 8, 2020 by <a href="{{ site.github.owner_url }}">{{ site.github.owner_name }}</a></span>
</div>

<p class="intro">Here I ask some questions to Dr. Alistair Cockburn, the man who wrote the Hexagonal Architecture pattern, also known as Ports And Adapters pattern. They are just some questions that I considered interesting, so I hope that "hexagonal readers" enjoy this post. I would like to thank Alistair Cockburn for letting me ask him these questions, and for spending his time answering them in such a comprehensive way.</p>

<br>

__QUESTION #1 (JG): I'm not going to ask you "why a hexagon", but "what is the story of the two names for the pattern"? (two articles at "wiki.c2.com" before you wrote the pattern) and "which name do you prefer"? (_Hexagonal Architecure_ or _Ports and Adapters_).__

__ANSWER #1 (AC):__

a) The hexagon: Everyone was drawing architectural pictures with rectangles, user on the top and database on the bottom, or user on the left and database on the right, I wanted to avoid that reflex, so I couldn't use a rectangle. (Related: I didn't know about the left-right asymmetry at the time.) Pentagons and heptagons are impossible to draw, so hexagon was an unused shape. That's all.

b) Ports and Adapters: I first drew this picture with the hexagon in 1994 (found my course notes with it!), next drew it on the board of a friend around 1998. It wasn't until sometime around 2002 or so (I don't recall when), when I was doing some research on the pattern to write it up formally, and I was looking for similar patterns to refer to (found some similar ones, but none covered the main idea), that I saw something about "ports" in a pattern somewhere, and possibly the "adapter" pattern in the Design Patterns book, that I realized the sides of the hexagon represented port in some formal sense. Hence, "Ports and Adapters" to make a clearer name for the architectural pattern. I then had an answer to "what constitutes one facet of the hexagon?"

c) However, "hexagonal architecture" is catchier, the hexagon shape is memorable, and they had already been in use for a while by that time (I published it first without the "ports and adapters" name), so #HexagonalArchitecture stuck and is still the easiest handle for the pattern.


__QUESTION #2 (JG): When did you first write anything about hexagonal architecture, and why? (what was the motivation of writing it?)__

__ANSWER #2 (AC):__

Interesting trying to find the history of it.

a) It first showed up in a drawing for my object-oriented programming class in 1994.

![](/assets/images/interviewac/firsthexarchdiag.png)

At the time, I was looking to have interfaces on all sides, borrowing from the Model-View-Controller discipline, and also trying to get a loopback capability in our architecture for our databases (I failed in that, and our project suffered for weeks, very expensive). I had no idea what ports were, I just knew it had to be about like that.

=====

b) Probably the first time I got to test this idea in the wild would have been maybe around 1998 or so, when I was visiting my friend Mho Salim and he was describing a problem he had that the hexagonal architecture solved.

I described that in the video in Paris, take a look here: https://youtu.be/th4AgBcrEHA?t=858

=====

c) Evidently I had been talking about it more in 1998, because Kevin Rutherford wrote in his blog "Ever since I heard of it (around 1998 or so) I’ve been trying to persuade people to think in terms of Alistair Cockburn’s hexagonal architecture model."

Reference: https://silkandspinach.net/2004/07/13/hexagonal-architecture/

=====

d) I wrote it up in Ward Cunningham's wikwiki web by 2004 (sadly, one cannot see the creation dates of pages on Ward's wiki). You can still read that discussion here: http://wiki.c2.com/?HexagonalArchitecture. Kevin Rutherford refers to it in 2004/07/13: (https://silkandspinach.net/2004/07/13/hexagonal-architecture/).

=====

e) Kevin Rutherford seems to have provided the final "whack on the side of the head" with his March 22 2005 blog post about it (https://silkandspinach.net/2005/03/22/the-middle-hexagon-should-be-independent-of-the-adapters/) and I created the page http://wiki.c2.com/?PortsAndAdaptersArchitecture in June 2005, with the sentences:

<<"I finally had this 'aha!' moment (June 2005) in which I saw that the facets of the hexagon are 'ports' (well, this is the part I sort of knew) and the objects between the two hexagons are 'adapters' (oddly, this was the aha!, and thanks to RebeccaWirfsBrock for her new book ObjectDesign and KevinRutherford for his blog entry http://silkandspinach.net/blog/2005/03/the_middle_hexa.html for providing me with the necessary WhackOnTheSideOfTheHead to see the ports and the adapters (adaptors?)), and so the architectural pattern is PortsAndAdaptersArchitecture. It admits of a better explanation than what I could come up with in HexagonalArchitecture. The facets of the hexagon represent different conversations that the application is having with the outside world... ">>

Kevin was right on it: On July 25, 2005, he wrote a new blog post saying: "Last month Alistair began using a new name for the Hexagonal Architecture pattern – he now calls it the Ports and Adapters Architecture." (reference: https://silkandspinach.net/2005/07/25/ports-and-adapters-architecture/)

=====

f) It seems it was only around September 2005 that I finally had all the pieces: code, meaning of adapters, side references, and all. I finally wrote the article on my site: Reference: https://web.archive.org/web/20060318201137/http://alistair.cockburn.us/rss.rdf

(and in those days, I hand-wrote my RSS feed :) hah.

Here is what I wrote on my RSS feed about it: 

"

<item><pubdate>
2005 07 15
13:01 MST
</pubdate><title>
Ports and Adapters Architecture (formerly"Hexagonal" architecture)
</title> <link>
http://c2.com/cgi/wiki?PortsAndAdaptersArchitecture
</link><description>
Somewhere in the mid-90s I started drawing a symmetric architecture in which the database is considered not at the "bottom of the stack", but fully outside the application, just as we recommend doing with the user interface. To break up perceptions about top and bottom and left and right, I drew it with a hexagonal shape, and came up with the rather lame name HexagonalArchitecture --- simply because I could not identify think of what the "hexagon" meant, but knew it had to have facets, and no number smaller than 5 made visual sense (and pentagons are harder to draw than hexagons). Finally just worked out what the drawing meant and realized this picture or architecture should be called Ports and Adapters (think operating system or hi-fi ports, and Design Pattern "adapters"). I'll get around to writing a full article on it when I have more examples and project implications to relate, but for now I'm just posting to Ward's wikiwiki site. If you know of prior or related architectural patterns similar to this, write me. 
</description>
</item>

"

and 

"

<item><pubdate>
2005 09 05
23:01 MST
</pubdate><title>
Hexagonal (Ports and Adapters) Architecture
</title> <link>
http://alistair.cockburn.us/crystal/articles/hpaaa/hexagonalportsandadaptersarchitecture.htm
</link><description>
Back in 1998 I named the Hexagonal architecture pattern, and some people have used it, but it wasn't until 2005 that I understood what the sides of the hexagon mean. Here's my best current description, in GoF pattern form. 
This is version 0.9, waiting for some additional reader feedback.
</description>
</item>

"

The code came from a job in 2004, when I was visiting a project at the hospitals in Utah, and they wanted to get better automated testing, so I worked with one programmer to create sample hexagonal architecture code. I had realized by this time that there wasn't another pattern quite like it, and it needed a proper writeup. Up until that time I didn't have any sample code to show, so I couldn't write it. With the sample code, I could.

By the way, it is kind of fun to use webarchive.org to look at the different states of the site over the years, and in particular how I published all of the content in table or RSS. Take a look:

https://web.archive.org/web/20060110220907/http://alistair.cockburn.us/sitehistory.htm

https://web.archive.org/web/20060318201137/http://alistair.cockburn.us/rss.rdf

and earlier: https://web.archive.org/web/20040605235858/http://alistair.cockburn.us/sitehistory.html

that was pretty nice, as I now look at it.

You can look at a lot of the histories by going to : https://web.archive.org/web/20040701000000*/http://alistair.cockburn.us/

(at this point, I wish I had a web site operational, so I could load all this history up there.)


#### Q3 (Juan Garrido): Regarding the origin of the pattern, I discovered few time ago the paralelism between "Hexagonal Architecture" and "Use Cases". So I wrote about it in a section of one of my articles ( https://jmgarridopaz.github.io/content/hexagonalarchitecture-ig/chapter1.html#tc4 ). What can you tell us about this relationship with use cases? Am I right about it? or should I change anything I wrote?

The driver/driven = primary/secondary distinction is correct. The goal levels less so. A sea-level or user-task-level use case roughly speaking represents one business atomic transaction, quite possible a database "transaction" in database terms (meaning fully committed or fully rolled back). However, every function call on a port is a use case, and probably a fish-level or even clam-level use case from a human's point of view. A new function call might only add a small piece of information or answer a small question needed to reach the full business transaction.

It might be that a port aggregates one-person summary level use case, but it's possible that they summary level use case involves multiple people hitting multiple ports. The purpose of a summary level use case is to show the macro flow, so there is no reason to expect them to stay on one port. Something needs to show how all the hits to the system over time add up to something interesting from the human or business perspective.


#### Q4 (Juan Garrido): You said in your "Alistair in the Hexagone" talk how popular the pattern is (lot of clicks on your website link), and I would add lot of people talking about it in Twitter, etc. In fact, "Use Cases" apart, I know you for "Hexagonal Architecture", not for the "Agile Manifesto", but this is my fault :). What do you think is the reason of this? (why is it that popular?) Did you ever imagine this when you wrote the pattern 15 years ago?

No idea, quite frankly. Imagine that I started teaching this in 1994, talking about it in 1998, trying unsuccessfully to do anything about it in 2004. Then Kevin Rutherford picks it up in 2005 and starts writing about it (now 10 years later). Even later, maybel 2012 or so, the Domain Driven Design people picked it up, as I imagine it, to put a wall around the domain model and get the technology out of their way. An act of self-defense, as I imagine it. I have still never been able to get anyone to implement it on any projects I visit. One of my senior architect friends still was making fun of me for this funny pattern in 2015.

So, I had basically forgotten it by 2010. Was using it on a baby project of my own in 2013, but that was all.

Imagine my surprise, then, when it turned up in a book on Domain Driven Design ("Growing Object-Oriented Software, Guided by Tests, by Steve Freeman and Nat Pryce) and started seeing it quoted and explained!

In 2017, there were a number of people giving tutorials on this pattern, and nobody had ever asked me why I created it and what it meant! And some of those explanations were quite wrong (the main error is usually using only one technology per port, or port per technology, when the whole point of a port is to allow technology substitutions). So I asked Thomas Pierrain if we could do a dual-presentation, me giving the history and him doing the programming. That is the only time I've explained the history and motivation in public so far. You can see it here: https://www.youtube.com/watch?v=th4AgBcrEHA.


#### Q5 (Juan Garrido): Would you like to say something about some topic, or explain something that I didn't ask to you?

Maybe ... One thing about use cases and one thing about my other work.

a) Originally, I was looking to make this pattern completely symmetric, that was very much in my mind, to get people to stop thinking about the database as "something different" that they then didn't have to pay attention to in the same way they did the user interface separation. A colleague of mine once said, "Give me API or give me death", which pretty much sums up this pattern from my perspective.

I was actually shocked, when I went to implement it one time for myself, that the driver and the driven adapters couldn't be the same. Gerard Mezaros, absolutely the best patterns writer I know, was visiting me in 2011, and we discussed why they are different. He came up with the name "Configurable Dependency" for the master pattern that describes changing technologies at an interface (a term I use now), and showed me multiple implementations of Configurable Dependency. It turns out that based on "who knows what", different implementations are used for driving than for driven actors.

This ruined my quest for total symmetry, and frankly, I was sad about that. The only saving grace is that the Hexagonal Architecture pattern is described using Configurable Dependency, which is independent of the driver-driven distinction.

It is nice that there is a clean map to use cases, so we have a tidy model that helps us think about these things from a macro perspective, not just a coding perspective.

b) The relationship between my various works.

I am known for my methodology work, my use case work, my object-oriented design work, my hexagonal architecture work, my patterns work, my project management work, my work in the agile world, and my work on "collaboration cards". Also, a little bit for my poetry.

Obviously, my methodology work links straight to my agile work and anything that has to do with the theory of software development, culminating in my current initiative the Heart of Agile.

I got interested in patterns in 1994, and started writing them in 1995. My macro interest is the relation between people's psychology and software development, so my first pattern language captured the relationship between social issues and software architecture.

I was consultant to the project manager on a large project in 1994, which got me interested in project management strategies, tactics, and patterns. I researched and wrote up project management patterns for about 5 years, looking particular at two issues: how do you handle hundreds of pattern? and What do you do when they start to contradict each other?  (sadly that book never got written)

The hexagonal architecture was pretty random for me. We got burned by the absence of it on the 1994 project, and as a programmer, I knew it was possible. Basically, I just chasing that one idea until i wrote it up, and then basically forgot about it, because no one was interested at the time.

Use cases were part of my methodology research, as was object-oriented design. I became expert on those two topics. No one had described use cases clearly, and in 1999 there were many terrible books coming out about them, so I hurried and wrote mine pretty much in self defense. I wrote guide booklets on object-oriented modeling and programming in the 1990s, and taught that, but there were many other writers at the time, including Rebecca Wirfs-Brock, so there was no point in publishing them. I might publish some of that now a bit out of historical curiosity, but also because the line of thinking is still different than other people's.

Poetry, strangely enough, feels to me the same as when I described techniques that I observe other people using. In both cases, I try to climb into someone's head and describe what I or they see in a way that a different person can see that also. This has been an interesting experience.

When I ever get my own publishing channel, I guess I'll publish a lot of this stuff. It's been an interesting journey.


#### Q6 (Juan Garrido): Do you have any question for me?

Yes.

What was it that triggered your first interest in Hexagonal Architecture, and what made you finally so passionate and energetic about explaining it?

=====

Well, at the beginning of 2016, I was developing a web application, a personal project, for my nephew Emilio García (), SEO consultant. I used the classical 3 layered architecture and MVC pattern. One day, looking at my laptop screen, I realized that the source code was full of framework annotations, a real mess, that didn't allow me to understand what the code was for, regarding to business. I began to search on the Internet and I discovered architectures that promote business domain decoupling. The most I liked was Hexagonal Architecture. Why? Probably because of the ports mechanism, which helps to define better the interactions with the outside world.

Am I passionate and energetic explaining Hexagonal Architecture? Well I don't know, but my motivation for writing an article explaining it, was to share my knowledgement and to clarify concepts, since I saw many misconceptions over the Internet. So I did a lot of research on your material (the pattern, your website, the "Alistair in the Hexagone" talk) in order to write a thorough article.
