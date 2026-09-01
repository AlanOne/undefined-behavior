---
title: "Hyprland Keybindings Cheat Sheet You'll Actually Use"
date: 2026-09-01
draft: true
tags: ["hyprland", "omarchy", "keybindings", "cheatsheet"]
summary: "The core Hyprland binds worth memorizing first, organized by what you're actually trying to do rather than alphabetically."
---

> **Draft note:** pull your actual bind list with `hyprctl binds` or by reading
> `~/.config/hypr/bindings.conf` and reconcile against this before publishing — defaults
> vary by distro/config and this needs to match what's really installed.

Most Hyprland cheat sheets are just a dump of the default config file. That's useless until
you already know the system. This groups binds by intent instead.

## Window movement and focus

- Switch focus between windows: `SUPER + arrow keys` (or `hjkl` if you use the vim-style bind set)
- Move a window in the tiling layout: `SUPER + SHIFT + arrow keys`
- Toggle floating for the focused window: `SUPER + V`
- Fullscreen the focused window: `SUPER + F`
- Close the focused window: `SUPER + Q`

## Workspaces

- Jump to workspace N: `SUPER + [1-9,0]`
- Move focused window to workspace N: `SUPER + SHIFT + [1-9,0]`
- Cycle to the next/previous workspace: `SUPER + mouse scroll` over the workspace indicator

Workspaces in Hyprland are per-monitor by default in most configs — if a bind seems to "do
nothing," check you're not already on the target workspace on a different screen.

## Launching things

- Open terminal: `SUPER + Return`
- App launcher: `SUPER + D` (or `SUPER + Space` depending on config)
- Screenshot region: usually bound through a screenshot utility like `grim`/`slurp` —
  check your bindings file for the exact combo, it's one of the most-customized binds

## Resizing and layout

- Resize the focused window: `SUPER + R` then arrow keys, or `SUPER + mouse right-drag`
- Toggle split direction in dwindle layout: `SUPER + P`
- Swap window position with the next one: `SUPER + J`

## The one worth memorizing first

If you memorize nothing else in week one: focus switching, workspace jump, and close window.
That's 90% of daily navigation. Everything else you'll pick up by needing it once and looking
it up — don't try to memorize the whole file on day one.

## Making your own reference

Once you've customized binds, generate your own live reference instead of trusting any
cheat sheet (including this one) to stay in sync:

```bash
hyprctl binds
```

Pipe that through `grep`/`column` for something you can actually print, or keep it in a
scratch note next to your monitor for the first couple of weeks.
