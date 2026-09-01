---
title: "Omarchy Setup Guide: What to Change in Your First Hour"
date: 2026-09-01
draft: true
tags: ["omarchy", "hyprland", "linux", "setup"]
summary: "Omarchy ships with strong opinionated defaults. Here's what's worth touching immediately, and what to leave alone."
---

> **Draft note:** verify every command below against your currently installed Omarchy version
> before publishing — package names and config file layout can shift between releases.

Omarchy (Arch Linux + Hyprland, preconfigured by DHH's install script) is deliberately
opinionated: it hands you a working tiling setup instead of a blank Arch install and a
weekend of dotfiles archaeology. That's the whole pitch. But "opinionated" means some
defaults will fight your workflow until you change a handful of things.

## 1. Know where the config actually lives

Everything Hyprland-related sits under `~/.config/hypr/`, split into separate files rather
than one giant `hyprland.conf` — bindings, monitors, autostart, and environment variables
each get their own file that gets sourced in. Before changing anything, open that directory
and skim what's there:

```bash
ls ~/.config/hypr/
```

Resist the urge to dump everything into one file. Keeping the split makes it much easier to
diff your changes later and to merge upstream Omarchy updates without conflicts.

## 2. Set your monitor layout explicitly

If you run more than one display, or a laptop + external monitor combo, don't rely on
autodetection. Find your monitor names with:

```bash
hyprctl monitors
```

Then set explicit positions, refresh rates, and scale in the monitors config file. This
alone eliminates most of the "window opened on the wrong screen" annoyance in the first week.

## 3. Decide on your terminal and stick with it

Omarchy defaults to a modern terminal emulator (Alacritty or a similar GPU-accelerated
option depending on your install). Don't switch terminals in week one just because you're
used to something else — give the default a real week before deciding it's wrong for you.
If you do switch, update both the terminal's own config and the Hyprland keybind that
launches it, or you'll end up with two terminal configs half-applied.

## 4. Tame idle/lock behavior before it annoys you

`hypridle` and `hyprlock` handle screen locking and suspend behavior. The default timeouts
are sane for most people, but if you're on a laptop doing anything long-running (renders,
downloads, the resource-sharing setup from this site's other guides), check the idle config
so the machine doesn't suspend mid-task.

## 5. Pick a wallpaper/theme strategy and move on

Omarchy includes theme switching out of the box. It's easy to burn an evening cycling
through options. Pick one, use it for a month, then revisit — theming is the classic
productivity sink of any tiling WM setup.

## What to leave alone

The keybinding scheme, workspace behavior, and waybar layout are all well thought through.
Don't rebind everything to match your old window manager on day one — give the defaults a
real week. Most people who do this end up keeping 80% of them. See the
[keybindings cheat sheet](/posts/hyprland-keybindings-cheat-sheet/) for the full default set
before you start remapping.
