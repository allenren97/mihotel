# IP addresses

*Learned 2026-10-26 (Week 1, Day 1), prompted by looking up mi-hotel.ca.*

## What it is

An **IP address** identifies a device on a network so that data can be delivered to it. Domain names like `mi-hotel.ca` are for humans; IP addresses are what the network actually routes on.

- **IPv4:** 32 bits, written as four bytes, e.g. `172.66.3.8`. About 4.3 billion addresses, and they've run out.
- **IPv6:** 128 bits, written in hex, e.g. `2606:4700:4400::ac40:9b1c`. Effectively unlimited. Both formats are in use side by side.

Every packet carries a source IP and a destination IP. Routers read the destination and forward the packet one hop closer to it.

> **Data analogy:** an IP is like a primary key for a machine. A packet is like a row with `from_id` and `to_id`.

## Public vs. private

- **Public:** unique across the internet, assigned by your internet provider. Servers must have one so others can reach them. Example: `172.66.3.8` is the Cloudflare server in front of mi-hotel.ca.
- **Private:** only meaningful inside a local network. The reserved ranges are `10.0.0.0/8`, `172.16.0.0/12` and `192.168.0.0/16`. Every home reuses the same ones.

A router translates between the two with **NAT** (network address translation). Outgoing requests leave with the router's public IP, and the router remembers which internal device to send each reply to.

```
Mac 192.168.1.23 ─┐
Phone 192.168.1.40 ┼─→ Router (NAT, public 99.x.x.x) ─→ internet
Camera 192.168.1.51┘
```

## Ports

An IP gets data to the right **machine**. A **port** (0 to 65535) gets it to the right **program** on that machine.

| Port | Typical use |
|---|---|
| 80 | HTTP |
| 443 | HTTPS |
| 5432 | PostgreSQL |
| 8000 | FastAPI in development |

## Why it matters for this project

- A machine behind a home or hotel router has only a private IP, so the internet can't reach it. That's why the app will be hosted with a cloud provider (Week 6) instead of on a computer at the hotel.
- DNS records (see `how-the-web-works.md`, coming up) map `mi-hotel.ca` to a public IP. Moving the site off Wix means changing those records.

## Try it

```bash
ipconfig getifaddr en0      # your Mac's private IP (macOS)
curl -s https://ifconfig.me # the public IP the internet sees
```
