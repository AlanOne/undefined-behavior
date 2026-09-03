---
title: "Reverse Proxy with Caddy: The Only Config You Need for HTTPS"
date: 2026-09-03
draft: false
categories: ["Self-Hosting"]
tags: ["self-hosting", "caddy", "https", "reverse-proxy", "docker"]
summary: "Once you're running more than one self-hosted app, a reverse proxy stops being optional. Caddy gets you there with a config file short enough to read in ten seconds."
---

The [self-hosted apps guide]({{< relref "posts/self-hosted-apps-home-server" >}}) on this
site already flagged Caddy as worth it "for how little configuration it needs compared to
nginx for the same result." Here's exactly how little.

## The whole config, for one app

This is a complete, working Caddyfile — not a trimmed-down example, the actual entire file:

```
yourdomain.com {
	reverse_proxy localhost:3000
}
```

That's it. Point a domain's DNS at your server, run Caddy with this file, and you get your
app served over HTTPS with a real, auto-renewing certificate — Caddy provisions it from
Let's Encrypt automatically the first time it sees a request for that domain. No separate
certbot setup, no cron job to renew certificates, no nginx config with a `server` block for
port 80 that redirects to another `server` block for port 443.

## Multiple apps, still one file

This is where a reverse proxy stops being a nice-to-have. Once you're running more than one
self-hosted service, a reverse proxy is what lets them all live behind port 443 on a single
server, routed by domain:

```
app-one.yourdomain.com {
	reverse_proxy localhost:3000
}

app-two.yourdomain.com {
	reverse_proxy localhost:8080
}

home.yourdomain.com {
	reverse_proxy localhost:8096
}
```

Each block is independent. Add a new self-hosted app, add four lines, reload Caddy — no
juggling port numbers in your head when you're trying to remember which service was on
which port.

## Verified with Docker

The Docker Compose pattern is just as short, and this exact setup was tested end to end —
Caddy reverse-proxying to a backend container by service name, real HTTP response coming
back through Caddy on the frontend port:

```yaml
services:
  caddy:
    image: caddy:2
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile

  your-app:
    image: your-app-image
```

```
yourdomain.com {
	reverse_proxy your-app:80
}
```

Docker Compose's built-in service-name DNS resolution means `your-app` in the Caddyfile
just works — no need to look up container IPs.

## One honest caveat

The reverse-proxy mechanics above are tested and confirmed working. The automatic HTTPS
certificate issuance specifically requires a real public domain with DNS pointed at a
publicly reachable server on ports 80/443 — that part wasn't (and can't be) verified from a
local sandbox, though it's Caddy's most well-documented and widely-relied-on feature.

If you don't have a server to point a domain at yet, that's the one prerequisite this whole
setup depends on — a small [DigitalOcean](https://m.do.co/c/4c0c0cf146da) droplet is enough
for everything described here, covered in more detail in the
[old laptop to home server]({{< relref "posts/old-laptop-to-home-server" >}}) post if you'd
rather use hardware you already own instead.
