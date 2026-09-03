---
title: "PowerShell Basics for Developers Coming From Bash"
date: 2026-09-03
draft: true
categories: ["Windows"]
tags: ["windows", "powershell", "bash", "cli"]
summary: "PowerShell isn't Bash with different syntax — it's an object pipeline, not a text pipeline. That one difference explains most of what feels confusing at first."
---

> **Draft note:** written from stable, well-established PowerShell fundamentals rather than
> tested live (no Windows install available in this environment) — spot-check current
> cmdlet names/aliases against your actual PowerShell version before publishing, since
> Microsoft does occasionally deprecate aliases.

The instinct coming from Bash is to treat PowerShell as "Windows's version of a shell" and
expect it to work the same way with different command names. That instinct causes more
confusion than it solves, because the actual difference is more fundamental: Bash pipes
pass **text** between commands; PowerShell pipes pass **objects**.

## Why that difference actually matters

In Bash, piping `ls` into `grep` works because you're pattern-matching on text output. In
PowerShell, `Get-ChildItem | Where-Object { $_.Length -gt 1MB }` works because `Get-ChildItem`
returns actual file objects with real properties (`.Length`, `.Name`, `.LastWriteTime`) —
you're filtering on structured data, not parsing text output with regex. Once this clicks,
a lot of PowerShell's verbosity stops feeling like unnecessary ceremony and starts feeling
like the actual point.

## The naming convention is more helpful than it looks

Every built-in PowerShell command follows `Verb-Noun`: `Get-Process`, `Set-Location`,
`Copy-Item`, `Remove-Item`. This isn't just consistency for its own sake — it makes
commands genuinely guessable. If you know there's a `Get-Service`, you can reasonably guess
`Start-Service` and `Stop-Service` exist too, and you'd be right.

## Bash-to-PowerShell command map

| Bash | PowerShell | Common alias |
|---|---|---|
| `ls` | `Get-ChildItem` | `ls`, `dir`, `gci` |
| `cd` | `Set-Location` | `cd`, `sl` |
| `pwd` | `Get-Location` | `pwd`, `gl` |
| `cat` | `Get-Content` | `cat`, `gc`, `type` |
| `grep` | `Select-String` | `sls` |
| `rm` | `Remove-Item` | `rm`, `del`, `ri` |
| `cp` | `Copy-Item` | `cp`, `copy`, `ci` |
| `mv` | `Move-Item` | `mv`, `move`, `mi` |
| `ps` | `Get-Process` | `ps`, `gps` |
| `kill` | `Stop-Process` | `kill`, `spps` |
| `echo` | `Write-Output` | `echo`, `write` |
| `export VAR=val` | `$env:VAR = "val"` | — |
| `which` | `Get-Command` | `gcm` |

The aliases exist specifically to make the transition easier — `ls`, `cat`, and `rm` all
work as typed, but they're aliases pointing at the real `Verb-Noun` cmdlet underneath, which
is worth knowing once you start writing scripts instead of typing one-off commands. Scripts
should use the full cmdlet name, not the alias — aliases are for interactive convenience,
not for anything you intend to read again in six months.

## Variables and environment variables aren't the same syntax

```powershell
$name = "value"           # a regular variable
$env:PATH                 # an environment variable — note the $env: prefix
```

This trips people up specifically because Bash uses `$VAR` for both cases (with `export` to
promote a variable to the environment). PowerShell keeps them syntactically distinct.

## String interpolation works, but only in double quotes

```powershell
$name = "world"
Write-Output "Hello, $name"     # outputs: Hello, world
Write-Output 'Hello, $name'     # outputs literally: Hello, $name
```

Same rule as Bash, just easy to forget if you're switching between the two constantly.

## The one habit worth building immediately

Pipe anything unfamiliar into `Get-Member` to see what properties and methods it actually
has, instead of guessing:

```powershell
Get-Process | Get-Member
```

This is the PowerShell equivalent of reading a man page, except it's introspecting the
actual object in front of you rather than generic documentation — genuinely one of the
better discoverability features once you remember it exists.
