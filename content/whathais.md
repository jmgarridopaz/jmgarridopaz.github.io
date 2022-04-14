---
title: What Hexagonal Architecture is
custom_article_title: Hexagonal Architecture
custom_article_subtitle: What is it?
custom_chapter_title:
custom_published_date: April 14, 2022
custom_author_url: https://github.com/jmgarridopaz
custom_author_name: Juan Manuel Garrido de Paz
layout: default
---

#### TABLE OF CONTENTS

1. [The Original Article](#tc1)
2. [What a Pattern is](#tc2)
3. [Design Patterns](#tc3)
4. [The Definition](#tc4)
5. [Links](#tc5)

<div id="tc1"></div>
### 1.- THE ORIGINAL ARTICLE.

During the last years, from about the mid 10’s, there’s been an increasing interest in this thing called “Hexagonal Architecture”. Why? Well, I don’t really know. I have a guess, but I won’t say it, it’s not our business now. The fact is that, for whatever reasons, Hexagonal Architecture has become buzzwords. Everybody talks about it on social media networks, blog posts, articles, videos, and so on.

But, what is Hexagonal Architecture? I will try to answer the question by going straight ahead to the roots, to the place where it is described by its author, and extracting a definition from there.

___Hexagonal Architecture__ is an alternative name for a pattern, described by Alistair Cockburn in an article he published on 2005, January 4. The main name of the pattern is Ports and Adapters._

<div id="tc2"></div>
### 2.- WHAT A PATTERN IS.

So the next question would be: What is a pattern? In order to answer this, I will go again to its roots. Design patterns movement in Computer Science was influenced by Christopher Alexander’s work. This man was an architect who created the “pattern language”, a theory not only applicable to Architecture but to other fields like Software Engineering as well. He defined what a pattern is as follows:

_Each __pattern__ describes a problem which occurs over and over again in our environment, and then describes the core of the solution to that problem, in such a way that you can use this solution a million times over, without ever doing it the same way twice._

Alistair Cockburn, based on Alexander’s work also, came up with the following definition:

___Patterns of “X”__ (or __“X” patterns__) are common characteristics of good examples of “X”. Where “X” could be anything. For example: “architecture”, “project management”, “software design.”_

He wrote it this way to clarify that the word “pattern” refers to properties of something across different projects, not only within one project. That’s the trick of the word “pattern”.

<div id="tc3"></div>
### 3.- DESIGN PATTERNS.

In particular, the Hexagonal Architecture original article classifies the pattern as “object structural”. What does this mean? This time we have to go to the “Design Patterns: Elements of Reusable Object-Oriented Software” book, written by the GoF (Gang of Four): Erich Gamma, Richard Helm, Ralph Johnson and John Vlissides. In this book, the authors describe 23 software design patterns, organized in a catalog, and classify them by two criteria: purpose and scope. The purpose is about what a pattern does, and the scope is about whether a pattern applies to classes or to objects. So that we can extract the following definitions:

___Structural patterns__ are a type of software design patterns, according to the purpose, that deal with the composition of classes or objects. They are concerned with how classes and objects are composed to form larger structures._

___Object patterns__ are a type of software design patterns, according to the scope, that deal with object relationships, which can be changed at run-time, and are more dynamic than those established between classes, fixed at compile-time._

___Object structural patterns__ describe ways to compose objects to realize new functionality. The added flexibility of object composition comes from the ability to change the composition at run-time, which is impossible with static class composition._

Design patterns are described using a template that divides the content into sections, giving them a uniform structure. So Hexagonal Architecture, as a pattern, is structured in the following sections:

- Name
- Classification
- Alternative names
- Intent
- Motivation
- Nature of the solution
- Structure
- Sample code
- Application notes
- Known uses
- Related patterns
- Acknowledgements
- References and Related reading

<div id="tc4"></div>
### 4.- THE DEFINITION.

![Figure 1: Who am I ?](/assets/images/whathais/figure1.png)
<p class="figure">Figure 1: Who am I ?</p>

Reading the first sections of the pattern, and considering the above definitions, we can finally write a definition for Hexagonal Architecture:

___HEXAGONAL ARCHITECTURE__ is a pattern that addresses a problem concerning software design at both sides of an application: the front, where business logic might leak into the User Interface; and the back, where business logic is tied to a database, or any other external service or device._

_For solving this problem in a symmetrical way, Hexagonal Architecture describes the solution defining two elements: ports and adapters. These elements are composed with relationships that can be changed at run time, to form the whole structure of the solution._

_Hexagonal Architecture is an alternative name for the pattern. __PORTS AND ADAPTERS__ is the main name, which is more suitable for an object structural pattern, since it refers to common characteristics of good examples of object structures._

<div id="tc5"></div>
### 5.- LINKS.

- Hexagonal Architecture resources:

<a target="_blank" href="https://jmgarridopaz.github.io/content/resources.html">https://jmgarridopaz.github.io/content/resources.html</a>

<br/>

<div class="commentbox"></div>
