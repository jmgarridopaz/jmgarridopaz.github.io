---
layout: default
---

<div id="title">
<h1>Interview with Alistair Cockburn</h1>
<p>About Hexagonal Architecture... and more</p>
<hr>
<span class="credits right">Published on September 7, 2020 by <a href="{{ site.github.owner_url }}">{{ site.github.owner_name }}</a></span>
</div>

<p class="intro">For talking about a thing, nobody's better than the creator of that thing. Here I will post an interview with Dr. Alistair Cockburn, the man who wrote the Hexagonal Architecture pattern. They are just some questions that I considered interesting and curious. I hope that hexagonal readers enjoy this post. I would like to thank Alistair Cockburn for letting me ask him these questions, and for spending his time answering them the way he does.</p>


### Q1 (Juan Garrido): I'm not going to ask you "why a hexagon", but "what is the story of the two names for the pattern"? (two articles at "wiki.c2.com" before you wrote the pattern) and "which name do you prefer"? (___Hexagonal Architecure___ or ___Ports and Adapters___).

a) The hexagon: Everyone was drawing architectural pictures with rectangles, user on the top and database on the bottom, or user on the left and database on the right, I wanted to avoid that reflex, so I couldn't use a rectangle. (Related: I didn't know about the left-right asymmetry at the time.) Pentagons and heptagons are impossible to draw, so hexagon was an unused shape. That's all.

b) Ports and Adapters: I first drew this picture with the hexagon in 1994 (found my course notes with it!), next drew it on the board of a friend around 1998. It wasn't until sometime around 2002 or so (I don't recall when), when I was doing some research on the pattern to write it up formally, and I was looking for similar patterns to refer to (found some similar ones, but none covered the main idea), that I saw something about "ports" in a pattern somewhere, and possibly the "adapter" pattern in the Design Patterns book, that I realized the sides of the hexagon represented port in some formal sense. Hence, "Ports and Adapters" to make a clearer name for the architectural pattern. I then had an answer to "what constitutes one facet of the hexagon?"

c) However, "hexagonal architecture" is catchier, the hexagon shape is memorable, and they had already been in use for a while by that time (I published it first without the "ports and adapters" name), so #HexagonalArchitecture stuck and is still the easiest handle for the pattern.


### Q2 (Juan Garrido): When did you first write anything about hexagonal architecture, and why? (what was the motivation of writing it?)

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
