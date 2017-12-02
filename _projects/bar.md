---
layout: project
name:   bar
title:  A lemonbar style status bar widget for Ubersicht on OS X
date:   2016-01-22
github: "callahanrts/bar"
categories: coffeescript chrome
image: /assets/projects/bar-promo.png
description: >
    An Übersicht widget meant to replace the OS X titlebar when using a tiling window manager like kwm.
---
An Übersicht widget meant to replace the OS X titlebar when using a tiling window manager like kwm.

I've been experimenting with tiling window managers for OS X, speficially [kwm](https://github.com/koekeishiya/kwm).
I set the default OS X title bar to hide unless hovered and set some extra padding at the top of
the screen for my windows to make room for bar. Then I added back some basic battery, date, time,
and active window widgets, as well as a widget for displaying the song that is currently playing.
To add Youtube and Soundcloud support for the currently playing track. I created another chrome
extension that sends the current track name to the desktop where bar can pick it up.

<div class="screenshots">
  <img src="/assets/projects/bar-1.png" />
  <img src="/assets/projects/bar-2.png" />
  <img src="/assets/projects/bar-3.png" />
</div>
