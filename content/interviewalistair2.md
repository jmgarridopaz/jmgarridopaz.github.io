---
title: Interview with Alistair Cockburn (2nd part) - Hexagonal Architecture
custom_article_title: Interview with Alistair Cockburn (2nd part)
custom_article_subtitle: About Hexagonal Architecture
custom_chapter_title:
custom_published_date: July 11, 2021
custom_author_url: https://github.com/jmgarridopaz
custom_author_name: Juan Manuel Garrido de Paz
layout: default
---

<p class="intro">These are some pending questions I could not ask before to Dr. Alistair Cockburn, the author of Hexagonal Architecture pattern. Now I have had the chance, so I publish them here in case anyone is interested. Thanks a lot to Alistair Cockburn for his deference in answering them.</p>
<hr>

__Question #1:__ _(Juan Garrido):_

__Regarding the symmetry of the pattern, first you thought it was perfectly symmetrical, but then you discovered the left-right asymmetry. Sometimes you've said that this asymmetry worries you. Why this discomfort?__

__Answer #1:__ _(Alistair Cockburn):_

I was looking for something with perfect symmetry, that didn't have left/right or up/down. Hexagonal Architecture was that way to start with. There is a pure symmetry here to be enjoyed and held onto. Then I found that in implementations, there is an asymmetry. That made me a bit unhappy for the above reason.

However, if you only talk about Configurable Dependency and Hexagonal Architecture, then there is no asymmetry - just that every port uses Configurable Dependency.

Then, in the implementation, some ports will use this or that variety of Configurable Dependency, and the asymmetry shows up in the implementation, not in the base concept.

=====

__(Juan Garrido):__

__What I don't understand is:__

__Why did you do the assumption of Hexagonal Architecture being perfectly symmetrical if you still didn't know it?__

__I mean, you define the solution in a way that you still don't know whether it can be achieved or not?__

__Shouldn't the order be: first we prove something by implementing it, and then we define what we have proved?__

__(Alistair Cockburn):__

Good question - I was definitely looking for something that had no left-right or up-down asymmetry ---- I just wanted inside/outside.

There were a couple of patterns that hinted it was possible, something called pedestal or similar, can't recall.

People abuse the left-right or top/bottom shape to do things that aren't healthy for the architecture and maintenance, that's why I was so determined to find a way to talk about only inside-outside and not any direction.

I didn't think about specific implementations - it wasn't until I wrote that article around 2003 that I encountered the left-right difference, and then it bothered me (still does) because I am afraid they will still abuse the concept.

=====

__(Juan Garrido):__

__Regarding Configurable Dependency and symmetry/asymmetry, my understanding is the following:__

__Left-right asymmetry comes from the fact of who triggers the conversation.__

- __At the left side, it's the actor who does the triggering, so the type of the "adapter ===> hexagon" dependency is "uses"__

- __At the right side, it's the hexagon who triggers (the hexagon uses its own port, implemented by the adapter), so the type of the "hexagon <=== adapter" dependency is "implements"__

__But there's still a symmetry: adapters always depend on hexagon.__

__Do you agree with me or do I miss anything?__

__(Alistair Cockburn):__

Yes - I can't see any way around it. I was even thinking of using the observer pattern, so the outgoing send message doesn't even know who is looking at or responding - there could be a sort of blackboard architecture that makes this work in some situations .... but then, even there, the hexagon has to know that it is an observer pattern event being used, and the event generator becomes the right-hand side adapter .... and thus the app has to know about the outgoing message adapter.
<hr>

__Question #2:__ _(Juan Garrido):_

__I'm not going to ask you for your opinion about Onion and Clean Architectures, because I know you say they are not your business. But does it bother you that they are said to be the same as Hexagonal Architecture?__

__Answer #2:__ _(Alistair Cockburn):_

Not hugely, because people like you clear up the misconception. :)

People are always borrowing, adjusting, perverting, tweaking each others ideas, people are always confusing things together that are different --- this is something just to live with.

"Choose your battles", as they say. This one isn't worth my time. Others do a fine job explaining the difference :)
<hr>

__Question #3:__ _(Juan Garrido):_

__Another (big) thing that is not your business is DDD (Domain Driven Design) and its lingo: application layer, ACL (Anti Corruption Layer), etc. Do you worry about many people thinking that Hexagonal Architecture is part of DDD too? Or you don't care?__

__Answer #3:__ _(Alistair Cockburn):_

I'm not a DDD specialist. I was actually working with Eric Evans on the project where he invented this stuff --- most DDD is just ordinary OO design to me, as I learned it in the early 1990s. Bounded Context is the thing I've never worked with. Since I've never worked with it, I can't have much in the way of opinions on it :)

I think that Hexagonal Architecture is popular with DDD people just because it gets the "noise" out of the way so they can do good DDD stuff. It's like cleaning the kitchen and putting all the dishes and appliances in the cupboards so that you can do some big cleaning or furniture movement. It's a preamble to DDD.

They make it part of DDD because they like it. Few other people care that much.

So I am happy they adopted it and write about it - makes it more used.
<hr>

__Question #4:__ _(Juan Garrido):_

__I remember that in your talk about Hexagonal Architecture at "Agiles2020" Latin American conference, you developed a simple example, to show people that Hexagonal Architecture is not as complicated as they think. On the Internet there is a large amount of material on the subject, but it is curious that much of that material is not faithful to your original article, or to your talks, in the sense that they invent things, complicate it, or misunderstand it.__

__Why do you think this happens? Does it bother you, or you don't care, that people don't understand the pattern?__

__Answer #4:__ _(Alistair Cockburn):_

a) People always misunderstand, that's an axiom.

b) Smart people like to make things more complicated - they look for corners and edges and wrinkles and complications. Keeping things really simple is hard work, especially for smart people.

c) My approach all my life is to make it look like "not a big step" for someone to adopt my ideas (e.g. Heart of Agile). The articles always say "Pro:... Con: complicated". I want to address that Con and show how easy it can be, reduce the barrier to use.
<hr>

__Question #5:__ _(Juan Garrido):_

__Last, but not least, is there anything you would like to say that I haven't asked you?__

__Answer #5:__ _(Alistair Cockburn):_

No idea :)

lol

Thanks.
<hr>

__(Juan Garrido):__

Thank you very much for your time and your attention.
