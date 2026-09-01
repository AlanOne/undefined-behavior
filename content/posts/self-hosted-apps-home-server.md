---
title: "Best Self-Hosted Apps to Run on a Home Server in 2026"
date: 2026-09-01
draft: false
tags: ["self-hosting", "home-server", "linux"]
summary: "A shortlist of self-hosted apps that are actually worth the maintenance burden, organized by what problem they solve."
---

There are hundreds of things you *can* self-host. Most people who try to run all of them
burn out within a month. This is a shortlist organized by the problem each one solves, not
an exhaustive directory.

## Media: Jellyfin

Open-source, no phone-home telemetry, no subscription tier gating features behind a
paywall. Pairs well with the *arr stack (Sonarr/Radarr/Prowlarr) if you want automated
library management, but Jellyfin alone is a complete, useful project on its own — start
there before adding the automation layer.

## Photos: Immich

The most credible self-hosted alternative to a cloud photo backup service at this point —
mobile app with automatic background upload, facial recognition, timeline view. This is
one of the higher-value apps to self-host if you're currently paying for cloud photo
storage, since it directly replaces a recurring subscription.

## DNS-level ad blocking: Pi-hole or AdGuard Home

Runs comfortably on the weakest hardware in your house. Blocks ads and trackers network-wide
at the DNS level for every device, not just the one with a browser extension installed.
Usually the first thing people self-host because the payoff is immediate and the resource
cost is negligible.

## Password manager: Vaultwarden

A lightweight, self-hosted-friendly implementation of the Bitwarden server API — same
official mobile/browser clients, your own server. Worth the extra care on backups given
what's stored in it (see the backup note in the
[old laptop to home server guide]({{< relref "posts/old-laptop-to-home-server" >}})).

## File sync/cloud storage: Nextcloud

Heavier to run and maintain than the others on this list, but replaces Google Drive/Dropbox
functionality reasonably completely, including calendar and contacts sync if you want to
get off a cloud account entirely. Don't start here if this is your first self-hosted app —
start with Pi-hole or Jellyfin and work up to Nextcloud once you're comfortable with
updates and backups.

## A note on storage

Jellyfin, Immich, and Nextcloud all have the same hidden cost: they're only as useful as
the storage behind them, and a laptop's internal drive fills up fast once it's holding a
media library or a full photo backup. Two common ways to solve this: an
[external USB hard drive](https://www.amazon.com/s?k=external+hard+drive&tag=alanone-20)
plugged straight into the server for the simplest setup, or a small dedicated
[NAS enclosure](https://www.amazon.com/s?k=nas+enclosure&tag=alanone-20) if you want storage
that survives the server itself being replaced. Buy more than you think you need — media
and photo libraries only grow. (Affiliate links — see the [about page]({{< relref "about" >}})
for the disclosure.)

## Reverse proxy: Caddy or Traefik

Not user-facing on its own, but once you're running more than one or two services, a reverse
proxy with automatic HTTPS certificates makes everything else easier to reach and secure.
Caddy in particular is worth it for how little configuration it needs compared to nginx for
the same result.

## Picking your first one

If you only self-host one thing this month, make it Pi-hole or AdGuard Home — lowest
resource cost, immediate and visible payoff, and it teaches you the Docker Compose workflow
you'll reuse for everything else on this list.
