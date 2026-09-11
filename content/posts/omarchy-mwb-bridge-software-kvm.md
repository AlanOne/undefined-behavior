---
title: "A Software KVM for Hyprland, Since Synergy/Barrier Won't Work"
date: 2026-09-11
draft: false
categories: ["Linux", "Windows"]
tags: ["omarchy", "hyprland", "linux", "windows", "kvm", "rust", "wayland"]
summary: "Why Synergy, Barrier, Input Leap, and Deskflow all fail on Hyprland, and the Rust daemon I built instead — reimplementing PowerToys' Mouse Without Borders wire protocol against Hyprland's own wlroots input protocols."
---

Wanted my Windows PC's physical keyboard and mouse to control this
[Omarchy](https://plugins.omarchy.org) (Hyprland) laptop sitting next to
it — a same-desk software KVM, not remote desktop. Every mainstream tool
for this (Synergy, Barrier, Input Leap, Deskflow) currently fails on
Hyprland for the same underlying reason, so this turned into
[MWB Bridge](https://github.com/AlanOne/omarchy-plugin-mwb-bridge): an
Omarchy plugin with its own bundled Rust daemon that reimplements the wire
protocol of Microsoft PowerToys' **Mouse Without Borders** instead.

## Why the mainstream tools don't work

Synergy/Barrier/Input Leap/Deskflow all lean on the desktop portal's
`RemoteDesktop` interface to inject synthetic input on Linux — the
sandboxed, permission-gated way a Wayland compositor is supposed to accept
fake mouse/keyboard events from an app that isn't the compositor itself.
`xdg-desktop-portal-hyprland` doesn't implement that portal yet, so every
one of those tools fails the same way on Hyprland specifically, regardless
of which one you pick.

Hyprland does expose the lower-level pieces directly, though:
`zwlr_virtual_pointer_v1` and `zwp_virtual_keyboard_v1`, the same wlroots
protocols a compositor's own tools use internally. Nothing stops a
regular, unprivileged client from talking to those directly — there's just
no existing cross-platform KVM tool built to.

## Why Mouse Without Borders specifically

Rather than write a new protocol from scratch (and a new Windows-side
client to go with it), MWB Bridge's daemon speaks the real wire protocol
of PowerToys' **Mouse Without Borders** — genuinely MIT-licensed, so the
actual C# source was available to check byte-for-byte, not just guessed at
from watching traffic. The result: a normal, unmodified PowerToys install
on Windows, pointed at this machine's IP and a shared key, works with zero
changes on the Windows side. All the new code lives in this daemon.

The wire protocol turned out to be the easy part, confirmed correct early
on against real PowerToys debug dumps. The actual blockers were all
Windows-side state the classic Mouse Without Borders settings UI doesn't
reliably persist — matrix/machine-pool entries that silently don't save,
hostname-resolution races, that kind of thing. The full writeup (byte
offsets, handshake sequence, every one of those Windows-side gotchas) is
in the repo's [`PROTOCOL.md`](https://github.com/AlanOne/omarchy-plugin-mwb-bridge/blob/master/daemon/PROTOCOL.md)
for anyone hitting the same wall.

## What actually works

- Mouse movement, clicks (including a Logitech-style back/forward side
  button), and vertical/horizontal scroll.
- Keyboard, correctly mapped for a Slovenian (QWERTZ) layout — see below
  for the catch on other layouts.
- Switching machines either by moving the cursor off the shared screen
  edge, or PowerToys' own `Ctrl+Alt+F1`-style hotkey.
- Clipboard sync, both directions: plain text and images, small or large
  (a full-resolution screenshot goes over the same path as a file
  transfer, automatically).
- Copying a file on Windows makes it pasteable here as a real file — the
  reverse direction doesn't work yet (see Known bugs).
- Double-tapping Mouse Without Borders' own lock hotkey on Windows locks
  this machine too, within about half a second.
- An optional patch that dismisses Omarchy's decorative screensaver on
  raw cursor movement, not just a keypress — useful specifically because
  control usually arrives here as a mouse move with no keyboard involved.
- Runs as an auto-starting, auto-reconnecting `systemd --user` service,
  including reconnecting immediately on laptop wake instead of waiting out
  a socket timeout (a subscription to logind's own suspend/resume signal —
  the same trick Omarchy's own pre-suspend lock screen uses).

## Known bugs

- **Keyboard layout mapping is only verified against Slovenian.** Windows
  reassigns punctuation/OEM key codes per active layout in ways that
  aren't derivable from the key code alone, so another layout will likely
  need its own empirical fixes — `PROTOCOL.md` documents exactly how the
  Slovenian ones were worked out, as a template.
- **Occasional duplicate keystrokes**, not yet root-caused — a live
  wire-traffic capture during testing showed a clean 1:1 key-down/key-up
  pair every time, so it's rarer than easy to reproduce on demand.
- **File transfer back to Windows** (this machine → Windows) doesn't work,
  despite a genuinely thorough investigation — the exact real machine-switch
  packet sequence gets sent, byte-for-byte matching a live capture from a
  real PowerToys switch, and Windows still never connects to pull it.
  Documented as an investigated, unresolved limitation, not abandoned after
  a quick guess.

## Installing it

```sh
omarchy plugin add https://github.com/AlanOne/omarchy-plugin-mwb-bridge.git --enable
```

Then set the shared security key (from PowerToys' Mouse Without Borders
settings), this Windows PC's address, and a name for this machine from the
bar widget's popup — the plugin builds and installs the daemon itself on
first load, nothing to compile by hand.

Full setup steps, the complete protocol writeup, and the current bug list
are all at
[github.com/AlanOne/omarchy-plugin-mwb-bridge](https://github.com/AlanOne/omarchy-plugin-mwb-bridge).
