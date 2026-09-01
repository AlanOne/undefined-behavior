---
title: "Omarchy Keybindings Cheat Sheet You'll Actually Use"
date: 2026-09-01
draft: false
tags: ["hyprland", "omarchy", "keybindings", "cheatsheet"]
summary: "The core Omarchy binds worth memorizing first, organized by what you're actually trying to do — pulled from the live binding list, not a copy-pasted default config."
---

Most keybinding cheat sheets are a dump of a default config file, alphabetized and useless
until you already know the system. This is grouped by intent, and — unlike most cheat
sheets floating around — pulled directly from the live binding list on the machine instead
of copied from an old blog post.

## Get your own live list first

Don't fully trust this page or any other. Omarchy prints your actual current bindings,
including any personal overrides in `~/.config/hypr/bindings.lua`, with:

```bash
omarchy menu keybindings --print
```

Everything below is the *default* set — if you've customized bindings, yours may differ.

## Windows

| Bind | Action |
|---|---|
| `SUPER + W` | Close window |
| `CTRL + ALT + DELETE` | Close all windows |
| `SUPER + F` | Full screen |
| `SUPER + ALT + F` | Full width |
| `SUPER + T` | Toggle floating/tiling |
| `SUPER + J` | Toggle window split |
| `SUPER + O` | Pop window out (float & pin) |

## Workspaces

| Bind | Action |
|---|---|
| `SUPER + [0-9]` | Switch to workspace [0-9] (0 = workspace 10) |
| `SUPER + SHIFT + [0-9]` | Move window to workspace, and switch to it |
| `SUPER + SHIFT + ALT + [0-9]` | Move window to workspace *silently* (stay put) |
| `SUPER + TAB` | Next workspace |
| `SUPER + SHIFT + TAB` | Previous workspace |
| `SUPER + CTRL + TAB` | Former workspace (jump back) |

The silent-move bind (`SUPER+SHIFT+ALT+N`) is the one people miss and then miss again once
they learn it exists — it's the difference between organizing windows and losing your
current focus every time you file something away.

## Launching & system

| Bind | Action |
|---|---|
| `SUPER + RETURN` | Terminal |
| `SUPER + SHIFT + RETURN` | Browser |
| `SUPER + SHIFT + ALT + B` | Browser (private) |
| `SUPER + SHIFT + F` | File manager |
| `SUPER + SHIFT + ALT + F` | File manager (current working dir) |
| `SUPER + SPACE` | Omarchy menu |
| `SUPER + ESCAPE` | System menu |
| `SUPER + K` | Show keybindings |
| `SUPER + CTRL + L` | Lock system |
| `SUPER + SHIFT + CTRL + SPACE` | Theme menu |

## Copy/paste/screenshots (the ones people forget exist)

| Bind | Action |
|---|---|
| `SUPER + C` / `V` / `X` | Universal copy / paste / cut |
| `SUPER + CTRL + V` | Clipboard manager |
| `SUPER + CTRL + E` | Emojis |
| `SUPER + PRINT` | Color picker |
| `PRINT` | Screenshot |
| `ALT + PRINT` | Screen recording |

## The ones worth memorizing first

If you memorize nothing else in week one: `SUPER+RETURN` (terminal), workspace switching
(`SUPER+[0-9]`), and `SUPER+W` (close window). That covers most daily navigation. Everything
else you'll pick up by needing it once and running `omarchy menu keybindings --print` to
find it — don't try to memorize the whole list on day one.
