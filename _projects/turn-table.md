---
layout: project
name:  turn-table
title:  Plug.dj Clone for internal use
date:   2016-05-21
github: "callahanrts/turn-table"
categories: meteor
image: /assets/projects/turntable-1.png
description: >
    When our favorite office social DJing platform got shut down for the second time, I built a clone
    to help preserve our office culture.
---


A [Plug.dj](https://plug.dj/) clone for shared office DJing on Friday afternoons.

When our favorite office social DJing platform got shut down for the second time, I built a clone
to help preserve our office culture.

Each DJ can set up a playlist of songs they can find on either YouTube or SoundCloud to use as
their DJing setlist. Once a playlist is activated, they can join the queue. After each song,
turn-table will automatically grab the next song in the next DJ's setlist and play it. Listening
DJs (the one's who are placed in the crowd in random locations) can thumbs up, thumbs down, or
save the current song to their playlists to show their appreciation
for the current DJ.

turn-table is built on [Meteor](https://www.meteor.com/) to accommodate the real time nature of
the application. Joining a room, interacting with the current song, and keeping the time in each
song are all propagated to each client instance in real time so everyone is always on the same page.

<div class="screenshots">
  <img src="/assets/projects/turntable-1.png" />
  <div class="caption"><i>Playing a song from SoundCloud</i></div>
  <img src="/assets/projects/turntable-4.png" />
  <div class="caption"><i>Playing a song from YouTube (room options and navigation visible)</i></div>
  <img src="/assets/projects/turntable-2.png" />
  <div class="caption"><i>Adding songs to a playlist</i></div>
  <img src="/assets/projects/turntable-3.png" />
  <div class="caption"><i>Joining a room of DJs</i></div>
</div>
