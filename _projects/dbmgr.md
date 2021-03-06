---
layout: project
name:   dbmgr
title:  dbmgr, Command line tool to back up, restore, and provision development databases
date:   2016-12-18
github: "callahanrts/dbmgr"
categories: ruby rubygems homebrew
featured: true
description: >
  dbmgr is a command line tool for backing up and restoring databases. It's a useful tool for
  sharing databases between developers, provisioning new Vagrant vms and Docker images, and
  provisioning new developers with a working database
---

dbmgr is a command line tool for backing up and restoring databases. It's a useful tool for:<br>
- Sharing databases between developers
- Provisioning new Vagrant vms and Docker images
- Provisioning new developers with a working database

When we first moved over to docker for hosting and developing our various microservices, I found
myself constantly losing my database volumes. Every time this happened I would have to recreate
and reseed my database and it started costing me too much time.

The first, and quickest, solution I thought of, was just to write [some shell scripts](https://github.com/callahanrts/dotfiles/blob/macos/dots/.zsh/docker#L50-L107)
to quickly back up and restore my database. This helped a ton, but eventually I figured out that
the reason I kept losing my volumes, was because I was running `docker-compose down` on all my
containers. When you do this with a MySQL container, it destroys your database volume (oops!).

Later, I realized these scripts were super helpful for saving off a copy of my database to give
to another developer to restore from when they lose their database or they need to work from a
specific database state. It's also really helpful for setting up new developers with a working
database, but that happens much less often.

I eventually got around to porting my bash functions into a ruby gem and configuring a homebrew
formula to install from. Now I have a nice CLI that can be installed with a simple `brew install dbmgr`.
