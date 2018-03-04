---
layout: post
comments: false
title: "Fixing Various HiDPI Issues on Archlinux"
date: 2016-03-26
categories: linux configs
---


## Spotify
Spotify can be too small. To fix it, spotify needs to be started with `--force-device-scale-factor=1.8`.
To avoid having to add the flag each time you start spotify, you can try a couple of things:

1. Edit `/usr/share/applications/spotify.desktop` and change the `Exec=...` line to
`Exec=spotify --force-device-scale-factor=1.8 %U`

2. Edit the spotify executable to automatically be called with the force-device-scale-factor flag.
`sudo vim /usr/bin/spotify`. Adjusted contents of this file at the time of this writing:

```bash
#!/bin/sh
LD_PRELOAD=libcurl.so.3 /usr/share/spotify/spotify --force-device-scale-factor=1.8 "$@"
```
