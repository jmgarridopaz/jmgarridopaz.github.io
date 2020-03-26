---
layout: default
---

<h1>Ports and Adapters Pattern (Hexagonal Architecture)</h1>

<p>Published on August 29, 2018 by <a href="https://github.com/jmgarridopaz">@jmgarridopaz</a></p>

<p><em>This article is for sharing my knowledge about Ports & Adapters pattern (also known as Hexagonal Architecture), hoping it can be helpful to someone interested in this subject. Here I expose my understanding of this architecture pattern, after being reading articles, watching talks and learning about it, since I first heard of it two years ago. This is a conceptual article, rather than a practical one. I hope to publish a pragmatic article soon, explaining my implementation of this architecture using Java 9 modules; and I hope to upload a proof of concept to github as well</em></p>

<h4>TABLE OF CONTENTS</h4>

<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc1">1.- Introduction.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc2">2.- The Architecture.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc2-1">2.1.- The Hexagon.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc2-2">2.2.- Actors.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc2-3">2.3.- Ports.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc2-4">2.4.- Adapters.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc2-5">2.5.- Summary.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc2-6">2.6.- Example.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc3">3.- Configurable Dependency Pattern.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc4">4.- From “Symmetrical Asymmetry” To “Asymmetrical Symmetry”.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc5">5.- Misconceptions.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc5-1">5.1.- Layered Architecture.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc5-2">5.2.- Why a hexagon?</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc5-3">5.3.- Ports outside Adapters.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6">6.- Pros and Cons.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-1">6.1.- Pros.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-1-1">6.1.1.- Testability improvement.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-1-2">6.1.2.- Maintainability improvement.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-1-3">6.1.3.- Flexibility.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-1-4">6.1.4.- Application immune to technology evolution.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-1-5">6.1.5.- Delay technological decisions.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-2">6.2.- Cons.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-2-1">6.2.1.- Complexity.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-2-2">6.2.2.- Build process performance.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc6-2-3">6.2.3.- Indirection and Mappings.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc7">7.- When to <em>“Hexagonal this!”</em>.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc8">8.- Implementation steps.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc8-1">8.1.- Test driver adapters / Mock driven adapters.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc8-2">8.2.- Real driver adapters / Mock driven adapters.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc8-3">8.3.- Test driver adapters / Real driven adapters.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc8-4">8.4.- Real driver adapters / Real driven adapters.</a><br>
<a href="https://jmgarridopaz.github.io/content/hexagonalarchitecture.html/#tc9">9.- Links.</a>

<div id="tc1">
<h3>1.- INTRODUCTION</h3>
</div>
<p>Ports and Adapters is an Object Structural Pattern coined by Dr. Alistair Cockburn, in an <a href="https://alistair.cockburn.us/hexagonal-architecture" target="_blank">article</a> he wrote in 2005.</p>
<p>If you are thinking… “Isn’t the article too old? How is it that it is still worth it nowadays, being software development a discipline in continous evolution where new technologies and frameworks arise every day and kill the one we used yesterday?” Well the answer is in the question. Ports & Adapters is a pattern that promotes <strong>decoupling from technology</strong> and frameworks. So no, it isn’t too old. Good things are timeless. They are like wine, they get better as time passes by.</p>
<p>The <strong>main idea of Ports & Adapters</strong> is to define the structure of an application so that it could be run by different kinds of clients (humans, tests cases, other applications,…), and it could be <strong>tested in isolation from external devices</strong> of the real world that the application depends on (databases, servers, other applications, …).</p>
<p>Let’s see how to achieve this.</p>