---
title: "Omarchy Setup Guide: What to Change in Your First Hour"
date: 2026-09-01
draft: false
tags: ["omarchy", "hyprland", "linux", "setup"]
summary: "Omarchy ships with strong opinionated defaults, configured through a Lua API rather than plain Hyprland conf files. Here's what's worth touching immediately, and what to leave alone."
---

Omarchy (Arch Linux + Hyprland, preconfigured by DHH's install script) is deliberately
opinionated: it hands you a working tiling setup instead of a blank Arch install and a
weekend of dotfiles archaeology. That's the whole pitch. But "opinionated" means some
defaults will fight your workflow until you change a handful of things.

## 1. Know the config is Lua now, not plain Hyprland conf

As of the 4.x line, `~/.config/hypr/` holds `.lua` files, not `.conf`. Your entry point is
`hyprland.lua`, which loads Omarchy's own defaults first (`require("default.hypr.omarchy")`)
and then your personal override files in order:

```lua
require("hypr.monitors")
require("hypr.input")
require("hypr.bindings")
require("hypr.looknfeel")
require("hypr.autostart")
```

The key idea: you never edit Omarchy's defaults directly. You add or unbind things in your
own files, which are loaded *after* the defaults — so package updates can improve Omarchy's
base config without ever touching or conflicting with your personal changes. Resist the urge
to go copy the whole default config into your override files "to see everything" — that
defeats the point and turns future updates into a manual merge chore.

## 2. See what you actually have before changing anything

Two commands answer almost every "how do I..." question before you touch a config file:

```bash
omarchy menu keybindings --print   # every active keybinding, including your overrides
hyprctl monitors                   # connected displays, names, resolutions
```

Run both before editing anything. The keybindings list in particular is generated live, so
it's always accurate — trust it over any cheat sheet (including the
[one on this site]({{< relref "posts/hyprland-keybindings-cheat-sheet" >}})) if they ever disagree.

## 3. Set your monitor layout explicitly if you run more than one display

Get exact monitor names from `hyprctl monitors`, then set explicit positions, refresh rate,
and scale in `~/.config/hypr/monitors.lua`. This alone eliminates most of the "window opened
on the wrong screen" annoyance in the first week with a multi-monitor setup.

## 4. Add bindings, don't fight the defaults

`~/.config/hypr/bindings.lua` ships mostly commented-out examples on a fresh install — you
add to it, you don't need to rewrite it. The pattern for adding a new bind:

```lua
o.bind("SUPER + SHIFT + R", "SSH", "alacritty -e ssh your-server")
```

(If `your-server` doesn't exist yet, see
[turning a spare laptop into a home server]({{< relref "posts/old-laptop-to-home-server" >}})
— cheapest way to get something worth SSH-ing into.)

And to change something Omarchy already binds, unbind first, then bind again — don't just
add a second binding on top of the first:

```lua
hl.unbind("SUPER + SPACE")
o.bind("SUPER + SPACE", "Omarchy menu", "omarchy-menu toggle root")
```

If you want a clean slate instead of layering on top of the defaults, set
`omarchy_default_bindings = false` in `hyprland.lua` *before* the
`require("default.hypr.omarchy")` line, then define everything yourself in `bindings.lua`.
This is a bigger commitment than most people need in week one.

## 5. Notice the bar isn't waybar

Current Omarchy renders its bar/menu system through `quickshell` (the process shows up as
`omarchy-shell` in `ps aux`), not waybar. If you're coming from a manual Hyprland setup and
go looking for a `waybar` config to tweak, it isn't there — bar customization goes through
Omarchy's own shell config, not a waybar `config.jsonc`.

## What to leave alone

The keybinding scheme and workspace behavior are well thought through and consistent with
each other (e.g. `SUPER+SHIFT+[1-9]` moves a window to a workspace, `SUPER+SHIFT+ALT+[1-9]`
does it silently without switching focus there). Don't rebind everything to match your old
window manager on day one — give the defaults a real week using
`omarchy menu keybindings --print` as your reference. Most people who do this end up keeping
the majority of them.
