---
layout: project
name: leon
title:  leon, experimenting with how browsers work--by building one
date:   2017-04-13
github: "callahanrts/leon"
categories: rust browser-engine
image: /assets/projects/leon.png
featured: true
description: >
    Nothing to write home about yet. An ambitious and over my head project to get more familiar with
    how browsers work from the inside out. The goal is to create a _working_ web browser from the
    ground up while spending some time with rust.
---

Nothing to write home about yet. An ambitious and over my head project to get more familiar with
how browsers work from the inside out. The goal is to create a _working_ web browser from the
ground up while spending some time with rust.

I don't have a lot to say about this project yet. It started because I wanted to gain a deeper
understanding of how browsers worked internally. It's obvious they parse HTML, CSS, and Javascript
and use the result to display a web page. What's not obvious is how the browser is managing all
of that under the hood. I had no idea how HTML got compiled to a DOM tree, CSS parsed into rules,
the result of the two composing a separate tree, or how reflows and painting fit in to the equation.

While researching how browsers work, I stumbled upon [this blog post](https://limpet.net/mbrubeck/2014/08/08/toy-layout-engine-1.html)
which details how to create a __very__ simple browser engine in rust. The result of this tutorial
is a simple browser engine that renders some basic HTML into an image. When I reached the image
rendering portion of the tutorial, I branched off and went my own way.

Since I wanted to create an actual browser, complete with real windows, I swapped out the image
rendering portion, with an Open GL wrapper for rust. Right now, it creates a window and renders
some divs with background color (shown below).

The CSS, and HTML parsers are currently super primitive. I'm in the process of replacing them with
full CSS3 and HTML5 parsers. There are a couple that exist for rust already, but I'm working on
building my own from the specifications for each. I'm mostly done with the CSS3 parser (there are
a couple methods missing). The HTML5 tokenizer is complete, but the parser is still lacking.

Next Steps:
- Replace the existing HTML and CSS parsers with my HTML5 and CSS3 parsers
- Implement rendering functionality for some CSS properties, HTML elements, and text

<div class="screenshots">
  <img src="/assets/projects/leon.png" />
</div>
