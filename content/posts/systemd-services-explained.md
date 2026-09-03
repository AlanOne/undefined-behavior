---
title: "Systemd Services Explained: Writing Your Own Unit File"
date: 2026-09-03
draft: false
categories: ["Linux"]
tags: ["linux", "systemd", "self-hosting"]
summary: "Every guide shows you a unit file to copy. Here's what each line actually does, tested live, including the one gotcha that trips up nearly every first attempt at a user service."
---

Most systemd tutorials hand you a unit file to copy-paste without explaining what any of it
does. That's fine until you need to write your own for a script that doesn't come with one
— which is most of the time if you're self-hosting anything custom.

## The three sections you actually need

A minimal working unit file has three blocks. Here's one that keeps an arbitrary script
running and restarts it if it crashes:

```ini
[Unit]
Description=Heartbeat test service
After=network.target

[Service]
Type=simple
ExecStart=/home/you/scripts/heartbeat.sh
Restart=on-failure
RestartSec=3

[Install]
WantedBy=default.target
```

- **`[Unit]`** — metadata and ordering. `After=network.target` just means "don't start this
  before the network is up," not a hard dependency — use `Requires=` alongside `After=` if
  the service genuinely can't function without something else running first.
- **`[Service]`** — the actual behavior. `Type=simple` means systemd considers the service
  started as soon as it executes `ExecStart` — fine for a long-running foreground process
  like the example above. `Restart=on-failure` plus `RestartSec=3` means a crash gets
  retried after 3 seconds instead of staying dead.
- **`[Install]`** — what happens when you `enable` it. `WantedBy=` says which target pulls
  this service in at boot/login.

## The gotcha that catches almost everyone once

If you're writing a **user service** (one that runs as your own user, not system-wide —
placed in `~/.config/systemd/user/` and managed with `systemctl --user`), `WantedBy=multi-user.target`
looks correct because that's what every system-service example uses. It isn't correct here.
`multi-user.target` doesn't exist in a user session — enabling against it produces:

```
Unit /home/alan/.config/systemd/user/heartbeat-test.service is added as a dependency
to a non-existent unit multi-user.target.
```

The service still technically works when started manually, but it won't actually enable
correctly for auto-start. The fix, confirmed on a real system: use `WantedBy=default.target`
for user services instead. System-wide services (the ones in `/etc/systemd/system/`,
managed without `--user`) are the ones that correctly use `multi-user.target`.

## Putting it into place

For a user service:

```bash
mkdir -p ~/.config/systemd/user
cp heartbeat.service ~/.config/systemd/user/
systemctl --user daemon-reload
systemctl --user enable --now heartbeat.service
```

For a system-wide service, the same commands without `--user`, placing the file in
`/etc/systemd/system/` instead, and prefixed with `sudo`.

## Checking it actually worked

Two commands cover almost everything you need day to day:

```bash
systemctl --user status heartbeat.service   # is it running, and since when
journalctl --user -u heartbeat.service -f   # live logs, -f to follow
```

Drop `--user` for a system-wide service. `journalctl -f` in particular is worth knowing —
it's the systemd equivalent of `tail -f` for a service's output, and it's usually the
fastest way to see why something didn't start the way you expected.
