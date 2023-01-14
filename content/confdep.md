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

Configurable Dependency illustrated
