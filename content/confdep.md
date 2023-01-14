---
title: Configurable Dependency
description:
custom_article_title: Configurable Dependency
custom_article_subtitle:
custom_chapter_title:
custom_published_date: January 14, 2023
custom_author_url: https://github.com/jmgarridopaz
custom_author_name: Juan Manuel Garrido de Paz
layout: default
---

<p class="intro">This is a copy of an article written by Alistair Cockburn, who posted it on his website on March 18, 2012</p>

### Pattern name: Configurable Dependency

To replace Dependency Injection and Inversion of Control, the former a specific implementation, the latter a double negative.

(This pattern needs to be fleshed out).

My dissatisfaction with Inversion of Control and Dependency Injection goes back to their first naming - both names are wrong in type. The term Inversion of Control says, "You did it wrong, now go back and invert it." But what would you teach someone so that they never get it wrong? The term Dependency Injection says what to do: pass the dependency information in. But that's an action.

A good pattern names a common property of good examples of something (software design in this case). The property version of Dependency Injection would be "Injected Dependency", but that still doesn't name what it is that we are after - the property we want present in the design.

![Configurable Dependency illustrated](/assets/images/confdep/conf_dep_illustrated_800px.jpeg)
<p class="figure">Configurable Dependency illustrated</p>

Gerard Meszaros today pointed out that the property we want is that the dependency is configurable, "configurable dependencies", he said. Gerard clarified further that Dependency Injection is only one way to get this property (which we still had not named at this point in the discussion, I was busy angling for "Parameterized Dependency"). Dependency Lookup would be another. That's when I decided that Configurable Dependency is the best (current) name for the property and the pattern, with Dependency Injection as one implementation pattern and Dependency Lookup as another.

The good news about this renaming is that the pattern becomes something that is phrased in the positive, so I can put in my undergrad text on Software Engineering, and teach it to beginners so they just "get things right" from the beginning, not having to do it wrong first and right second. It also names the property we are after, so we can argue over when we want that property present, and when not (it introduces complexity, so one wouldn't want it everywhere).

And finally, as with any pattern, there is an upside and a downside. The upside is configurability, the downside is complexity. So there should always be a discussion – do we want this benefit for this cost? That's the way a pattern should work

The pattern still needs to be written in detail. Anyone care to help with that?

### How do we choose a name?

The ideal name is a noun that names a property that is present in well-constructed implementations. (The proper term for Patterns should be a phrase with a dangling referent: _Common Properties of Good Examples of "X"_. Thus you have "X" Patterns, as you should). That's why I don't like either Dependency Injection or Inversion of Control.

In an ideal world, let's pretend for a minute that Configurable or Swappable passed our tests, I would be able to teach new students design according to this list:

- What is a dependency? What isn't?
- When do we want our dependency to be swappable/configurable?
- What are ways to make it swappable/configurable? (Dependency Injection and Dependency Lookup being two alternative implementation routes)

Other names we looked at were:

- Parameterized Dependency
- Swappable Dependency
- Externalized Dependency

Needing an icon or image, but for now:

![Yin Yang](/assets/images/confdep/yin_yang.gif)

___

### Discussion

I would not mind helping write it up

-by Chris on 3/18/2012 at 11:06 PM

_email me? thx AC_

___

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

_Thanks, Martin – I was unaware of that – will research and write Kevlin…. could be I just came up with the same thing. I notice that he chose an imperative-verb name. thx._

-by Alistair on 3/19/2012 at 10:33 AM

___
