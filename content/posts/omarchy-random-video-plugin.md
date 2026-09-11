---
title: "A 'Random Video' Button for the Omarchy Bar"
date: 2026-09-11
draft: false
categories: ["Linux"]
tags: ["omarchy", "linux", "mpv", "yt-dlp", "youtube"]
summary: "A bar popup that plays a random video inline with a Reroll button — and the YouTube split-stream problem that turned out to be the actual hard part."
---

A small one: a bar button for [Omarchy](https://plugins.omarchy.org) that
opens a popup and plays a random video right there, inline, with a
"Reroll" button for another pick — [Random Video](https://github.com/AlanOne/omarchy-plugin-random-video).
Configure one or more sources once, then it's a single click for
something to have on in the background.

## What counts as a source

Each configured source is one of two kinds:

- **URL** — something that already serves a different video on every
  request (a personal random-video endpoint, a redirect service), or just
  a plain direct video/YouTube link.
- **Cmd** — a shell command whose stdout is such a URL, for sites where
  the "random video" only exists behind real client-side JavaScript, not
  anything a plain fetch can scrape.

That second case is the interesting one.
[ytroulette.com](https://ytroulette.com)'s page has genuinely nothing to
scrape — confirmed by checking, since even `yt-dlp`'s generic extractor
fails on the raw page — because its own JS picks a random category and
position client-side, POSTs those to its `roulette.php`, and gets back a
video ID as JSON.
[`resolvers/ytroulette.sh`](https://github.com/AlanOne/omarchy-plugin-random-video/blob/master/resolvers/ytroulette.sh)
in the repo replicates that exact call and prints a plain
`youtube.com/watch?v=...` URL — paste it in as a Cmd source, or use it as
a template for another site that needs the same treatment.

## The part that mattered more than the popup UI: YouTube's split streams

Modern YouTube's default web client essentially never serves a single URL
with both audio and video muxed together anymore — they come back as two
separate streams by design, which breaks a plain inline `<video>`-style
player that can only take one URL.

The fix was resolving YouTube links through `yt-dlp`'s **android** client
(`--extractor-args "youtube:player_client=android"`) instead of the
default one — noticeably faster (it skips slower client attempts that tend
to fail anyway), and for most regular videos it still exposes the classic
single muxed format the default client dropped, so the inline preview
plays with sound most of the time.

For the videos where even that doesn't yield a combined stream, there's no
way around it for an inline single-URL player — the preview plays
silently, clearly labeled as such in the popup. "Open in window" always
gets you guaranteed audio+video either way: when this plugin's own
resolution already has sound, `mpv` just plays that same URL directly; when
it came back silent, `mpv` falls back to doing its own separate resolution
via its `ytdl` hook.

## Installing it

```sh
omarchy plugin add https://github.com/AlanOne/omarchy-plugin-random-video --enable
```

Requires `yt-dlp` (resolves every source to a real playable stream — if
it's missing or fails on a given URL, the plugin just falls back to
treating that URL as already directly playable) and `mpv` for "Open in
window." Both are a normal part of an Arch/Omarchy setup already if
you've done any video work on this machine.

## A note on Cmd sources

A Cmd source runs exactly what you type, as your own user, via
`bash -c` — no different from running the same command yourself in a
terminal, but worth knowing plainly: it's not sandboxed in any way.

Full setup, the settings reference, and an optional
[uosc](https://github.com/tomasklaen/uosc) config for a nicer-looking "Open
in window" player are all at
[github.com/AlanOne/omarchy-plugin-random-video](https://github.com/AlanOne/omarchy-plugin-random-video).
