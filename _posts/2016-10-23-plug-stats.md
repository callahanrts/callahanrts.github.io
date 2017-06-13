---
layout: post
title:  "plug-stats"
date:   2016-10-23
github: "callahanrts/plug-stats"
categories: Elm chrome
promo: /assets/projects/plug-stats-promo.png
---

A chrome extension written in Elm to add some statistics and sorting to tracks that are played
in plug.dj. Admittedly, it was also an excuse for me to try out Elm.

Something about Elm's tagline really caught my eye and I just had to try it out.

> A delightful language for reliable webapps.<br>
Generate JavaScript with great performance and no runtime exceptions.

In case you didn't catch that
> no runtime exceptions.

Whoa! A javascript transpiler that promises no runtime exceptions?! Pretty increditble. They even
back this up by claiming any runtime exceptions are considered compiler errors. With claims like
this, I had to try it out.

It turns out they were able to achieve this by static typing and the Elm architecture which
consists of three major pieces:

- **Model**, which contains the state of your application. It consists of all of your variables
for each component. They can also be used in a similar fashion to 2-way binding, which is really
just a change in state.

- **Update**, which defines how your state will be updated. You can use these as event handlers
or other methods that will change the state of your component.

- **View**, which is reminiscent of Reactjs and others in that Elm acts as a DSL for generating
HTML elements and binding attributes, events, and values to them.

The only problem with Elm, is that it's super terse and haskell-like. It makes starting with
Elm unusually difficult.

<div class="screenshots">
  <img src="/assets/projects/plug-stats.png">
</div>
