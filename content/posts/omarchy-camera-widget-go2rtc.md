---
title: "Building a Camera Bar Widget for Omarchy (Reolink + Google Nest)"
date: 2026-09-04
draft: false
categories: ["Linux", "Self-Hosting"]
tags: ["omarchy", "self-hosting", "go2rtc", "reolink", "nest", "docker"]
summary: "A snapshot pill in the Omarchy bar that switches between however many cameras you've got, with a popup UI to add or remove them — no config file editing, no fixed camera count."
---

Wanted a quick way to check the cameras without opening an app, so this turned into an
actual [Omarchy](https://plugins.omarchy.org) plugin:
[Cameras](https://github.com/AlanOne/omarchy-plugin-cameras), a bar-widget with a snapshot
pill, a popup camera switcher, and a one-click live-view launch via `mpv`. It works with
Reolink cameras and Google Nest cameras, and — the part that took the most iteration — it
doesn't hardcode either.

## Why it doesn't talk to the cameras directly

The plugin itself is deliberately thin. All the actual camera protocol handling — RTSP,
ONVIF, and Nest's OAuth-plus-WebRTC mess — is done by
[go2rtc](https://github.com/AlexxIT/go2rtc), a small self-hosted streaming server. The
plugin only ever talks to go2rtc's local HTTP API: list streams, add a stream, delete a
stream, grab a JPEG snapshot. See the
[self-hosted apps roundup]({{< relref "posts/self-hosted-apps-home-server" >}}) if you
haven't run something like this before — go2rtc is a `docker compose up -d` away, same as
everything else on that list.

Plugins in Omarchy's shell run unsandboxed, in the same long-running process as the rest of
the bar. That's exactly the kind of place you don't want a hand-rolled RTSP/WebRTC client
living. go2rtc already does it correctly; the plugin's job is UI and a handful of HTTP
calls.

## The part that mattered more than the streaming: no hardcoded cameras

First pass had the camera's stream name baked into the plugin's settings. Fine for one
camera, useless for a marketplace listing — nobody installing this plugin has a camera
called `e330_sub` sitting in their config already.

Fix ended up being straightforward once go2rtc's own API contract clicked:
`PUT /api/streams?name=X&src=Y` both starts a stream live *and* writes it into go2rtc's
config file for you. So the popup just has an "Add camera" form — a name, a stream URL,
optionally a lower-res URL for the thumbnail — and it calls that endpoint directly.
`DELETE` works the same way in reverse. A fresh install starts with zero cameras and the
popup opens straight to that form. Multiple cameras get grouped automatically and shown as
tabs across the top of the popup.

## Setting it up: Reolink

Any Reolink camera with direct RTSP/ONVIF support works — flip on RTSP in the Reolink app
(**Device Settings → Advanced Network Settings → Server Settings**), then it's a stream
URL: `rtsp://user:pass@camera-ip:554/Preview_01_main`. Paste that into the popup's Add
Camera form and it shows up immediately.

The [E330](https://www.amazon.com/s?k=reolink+e330&tag=alanone-20) is what this was built
against — no hub or NVR required, straight RTSP off the camera. If you're shopping for
one specifically because you want this kind of direct-access setup, check the spec sheet
for RTSP/ONVIF support before buying; some of Reolink's battery/wifi-only lineup only works
through a [Reolink Home Hub or NVR](https://www.amazon.com/s?k=reolink+nvr&tag=alanone-20),
which is a perfectly fine setup too, just point the stream URL at the hub's RTSP output
instead of the camera's.

## Setting it up: Google Nest

This is the one that ate an afternoon. Nest cameras go through Google's Smart Device
Management API, which means: a Google Cloud project, a one-time $5 Device Access
registration, an OAuth consent flow, and a device listing call before you even have a
device ID to work with. go2rtc's `nest://` source handles the actual WebRTC negotiation
once it has credentials — getting the credentials is the whole exercise.

Full step-by-step (Cloud project, OAuth client, Device Access registration, the
authorization URL, exchanging the code for a refresh token, listing devices) is in the
[plugin's README](https://github.com/AlanOne/omarchy-plugin-cameras#setting-up-a-google-nest-camera).
One thing worth flagging in advance: if a
[Nest Doorbell](https://www.amazon.com/s?k=google+nest+doorbell&tag=alanone-20) shows up as
"unreachable," check the battery before assuming the setup is wrong — it needs to be awake
to answer a stream request, and that's the most likely reason it isn't.

*(Affiliate links — see the [about page]({{< relref "about" >}}) for the disclosure.)*

## Grabbing the plugin

```sh
omarchy plugin add https://github.com/AlanOne/omarchy-plugin-cameras.git --enable
```

Repo, full setup guides for both camera types, and the security notes on what the plugin
actually talks to are all at
[github.com/AlanOne/omarchy-plugin-cameras](https://github.com/AlanOne/omarchy-plugin-cameras).
