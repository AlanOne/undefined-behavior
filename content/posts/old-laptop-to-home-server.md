---
title: "Turn an Old Laptop Into a Home Server (Step by Step)"
date: 2026-09-01
draft: false
tags: ["self-hosting", "linux", "home-server", "guide"]
summary: "You don't need a NAS or a rack to start self-hosting. An old laptop with a broken screen and a wall outlet is enough."
---

The laptop that's too slow for daily use is usually not too slow to run Jellyfin, Pi-hole,
or a handful of small self-hosted apps 24/7. That's the whole appeal: you already own the
hardware, and running it as a server converts a drawer object into something producing
value continuously.

## 1. Strip it down to a headless setup

Close the lid without it sleeping — edit the lid-switch behavior in your power manager
config (`logind.conf` if you're on systemd: set `HandleLidSwitch=ignore`) so the machine
keeps running lid-closed. Disable the display output entirely once you've confirmed SSH
access works; a dead/cracked screen is fine for this use case, you'll never look at it again.

## 2. Give it a static local IP

Set a DHCP reservation in your router for the laptop's MAC address, or configure a static
IP directly on the machine. Everything downstream — SSH, reverse proxy rules, app configs —
gets much more annoying to maintain if the IP can silently change after a reboot.

## 3. Lock down SSH before anything else

This machine is about to run continuously and be reachable on your network. Before
installing a single app:

```bash
sudo systemctl disable --now sshd  # temporarily, while you configure it
```

Then set up key-based auth, disable password login in `sshd_config`
(`PasswordAuthentication no`), and only re-enable the service once that's confirmed working
from another machine. Skipping this step is the single most common regret people report
after a few months of running a home server.

## 4. Install Docker and use Compose from day one

Even for a single-app server, Docker Compose gives you a reproducible, documented config
instead of a pile of manually-installed packages you'll forget how to reproduce:

```bash
sudo pacman -S docker docker-compose
sudo systemctl enable --now docker
```

Keep one `docker-compose.yml` per app in its own directory, or one big compose file with
multiple services — either works, but write it down instead of running ad-hoc `docker run`
commands you won't remember in six months.

## 5. Pick your first app, not your eventual stack

Don't try to stand up ten services on day one. Start with one you'll actually use daily —
see the [self-hosted apps guide](/posts/self-hosted-apps-home-server/) for a shortlist —
get comfortable with backups and updates for that one app, then add the next.

## 6. Set up backups before you have anything worth losing

Whatever you self-host, decide the backup story before you have six months of photos or
notes in it. A cron job that rsyncs the app's data directory to another machine or cloud
storage nightly is a fine starting point — don't let "I'll set up backups later" become the
default.

## What this actually costs to run

An old laptop idling with a handful of lightweight containers typically draws well under
20W. At most electricity rates that's a few dollars a month — cheap enough that the real
constraint is your time, not the power bill.
