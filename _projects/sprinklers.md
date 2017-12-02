---
layout: project
name:  sprinklers
title:  Automated irrigation system with Raspberry Pi
date:   2017-03-24
github: "callahanrts/sprinklers"
categories: raspberrypi shell node
image: /assets/projects/sprinklers.png
featured: true
description: >
    My existing timer wouldn't support the schedule I wanted to water my lawn with or allow for quick
    and painless testing during maintenance. So, I built my own sprinkler timer with a dust gathering
    raspberry pi to get more control and flexibility with my irrigation.
---

My existing timer wouldn't support the schedule I wanted to water my lawn with or allow for quick
and painless testing during maintenance. So, I built my own sprinkler timer with a dust gathering
raspberry pi to get more control and flexibility with my irrigation.

Starting with the raspberry pi I had laying around, I only needed to pick up a four channel relay
switch and a few jumper wires to get all the hardware for my sprinkler timer. Setting up the
raspberry pi's GPIO pins to control the relay switch turned out to be surprisingly easy. Once you
connect the right pins to the right inputs, you're just a short [shell](https://github.com/callahanrts/sprinklers/blob/master/pin.sh)
script away from activating
the switch.

Sprinkler valves are activated when the inner solenoid receives a 24VAC current. To set this up
with my relay switch, I used some wire to send power to each channel on the relay switch and
hooked up the sprinkler valve to the other end. Now when the switch becomes active, the corresponding
valve gets the 24VAC current and waters my lawn.

<div class="screenshots">
  <img src="/assets/projects/sprinklers-2.jpg">
</div>

The last step in the process was to set up a node server on the raspberry pi and configure the
hostname so I can create a sprinkler schedule and turn my sprinklers on from my phone.

An expressjs server runs on the pi and uses [node-schedule](https://github.com/node-schedule/node-schedule)
to schedule water timing with the cron syntax. To activate the valves, node executes the [shell](https://github.com/callahanrts/sprinklers/blob/master/pin.sh)
script I mentioned earlier. And, for testing, I set up a simple web page that allows me to specify
a valve and a duration (in minutes) to water the lawn.

<div class="screenshots">
  <img src="/assets/projects/sprinklers.png">
</div>
