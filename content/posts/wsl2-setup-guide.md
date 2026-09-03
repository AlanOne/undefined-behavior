---
title: "WSL2 Setup Guide: A Real Linux Environment on Windows Without Dual-Booting"
date: 2026-09-03
draft: false
categories: ["Windows"]
tags: ["windows", "wsl", "wsl2", "linux", "setup"]
summary: "You don't need to dual-boot or run a VM to get a genuine Linux environment on Windows. Here's what WSL2 actually gets you, and the handful of things worth configuring on day one."
---

WSL2 isn't a compatibility shim like the original WSL1 — it runs a real, lightweight Linux
kernel in a managed VM, which means real filesystem semantics, real Docker support without
the old workarounds, and a genuine `apt`/package manager environment. If you need Linux
tooling for work but Windows for everything else, this is a better answer than dual-booting
or running a full desktop VM.

## 1. Install it with one command

Modern Windows makes this almost boring, which is the point:

```powershell
wsl --install
```

Run that from an administrator PowerShell or Command Prompt. It enables the required Windows
features, downloads a default Linux distribution (Ubuntu, unless you specify otherwise), and
sets up WSL2 as the default version. A reboot is typically required once.

To install a specific distribution instead of the default:

```powershell
wsl --install -d Debian
```

Run `wsl --list --online` to see the full list of what's available.

## 2. Know where your files actually live

The single most common WSL performance mistake: keeping your project files on the Windows
filesystem (`/mnt/c/...`) and editing them from the Linux side. Cross-filesystem access
between Windows and the WSL2 VM is slow — sometimes dramatically so for anything doing lots
of small file operations (`node_modules`, git operations, build tools).

Keep your actual working files inside the Linux filesystem itself (your Linux home directory,
something like `\\wsl$\Ubuntu\home\yourname\` if you need to reach it from Windows Explorer)
and you avoid this entirely. Treat `/mnt/c` as a bridge for occasionally sharing files, not
where your project lives day to day.

## 3. Set resource limits before WSL sets them for you

By default WSL2's VM can use most of your system's memory, which is fine until you're running
something memory-hungry on the Windows side simultaneously. Create (or edit)
`%UserProfile%\.wslconfig`:

```ini
[wsl2]
memory=8GB
processors=4
swap=2GB
```

Restart WSL for changes to take effect:

```powershell
wsl --shutdown
```

## 4. Use VS Code's Remote-WSL extension, not a shared filesystem

If you're editing WSL-side files, don't open them via the Windows path. Install the
**WSL** extension in VS Code and open the folder from inside your WSL terminal with `code .`
— this runs the actual VS Code server inside the Linux environment, giving you correct
line endings, correct file permissions, and no cross-filesystem performance penalty.

## 5. Know the actual limitations

WSL2 is not a full Linux desktop replacement, even with WSLg (which does let GUI Linux apps
run in windows on your Windows desktop now). GPU-heavy workloads, certain low-level hardware
access, and anything expecting a full systemd init system historically needed extra
configuration — systemd support has improved significantly in current WSL2 but isn't
universal across every distro image. If your workflow specifically needs full hardware
access or a complete desktop environment, WSL2 is the wrong tool — that's what a real Linux
install or dual-boot is still for.

## Who this is actually for

Developers who need Linux-native tooling (Docker, specific package managers, POSIX-behaving
scripts) but whose day-to-day work — email, meetings, Windows-only software — happens on
Windows. If you're doing this to avoid learning Linux at all, WSL2 will still make you learn
it; it's just a much lower-friction way to start.
