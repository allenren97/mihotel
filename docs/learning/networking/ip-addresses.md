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

## Special addresses and CIDR notation

**CIDR notation**, as in `192.168.0.0/16`, gives a starting address and how many leading bits are fixed. `/16` fixes the first 16 bits (`192.168`), so the range runs from `192.168.0.0` to `192.168.255.255`. A larger number after the slash means a smaller range.

| Address | Meaning |
|---|---|
| `127.0.0.1` (`localhost`) | This machine itself. A server listening here is reachable only from the same computer. |
| `0.0.0.0` | For a server: listen on every network interface, so other devices can connect too. Part of the reserved `0.0.0.0/8` block, never used on the public internet. |
| `192.168.x.x`, `10.x.x.x` | Private ranges (see above) |

This matters in Week 2, when the server has to be told which address to listen on.

## Who owns an IP: `whois`

Addresses are delegated down a hierarchy:

```
IANA (global) → regional registry (ARIN for North America) → ISP (e.g. Rogers) → customer router
```

`whois <ip> | grep -i orgname` shows which organization holds the block an address belongs to. Home IPs are usually **dynamic**: the provider can change them, for example when the modem restarts. Servers need a **static** IP so DNS can keep pointing at them.

## Routing: how packets get there

There's no direct wire between two machines. Data is split into **packets**, and each **router** forwards a packet one hop closer based only on its destination IP. No router knows the whole path. If one fails, traffic is routed around it.

`traceroute` shows the hops. A real run from a home in Toronto to mi-hotel.ca (addresses redacted):

```
 1  192.168.1.1        home Wi-Fi router            ~4 ms
 2  10.0.0.1           ISP modem, also a router      ~4 ms   ← two private hops = "double NAT"
 3  99.x.x.1           ISP gateway (first public IP) ~14 ms
 4-6 ...rogers.com     ISP backbone in Toronto       ~16 ms
 7  * * *              router that doesn't reply (normal)
 8  108.162.239.x      Cloudflare (several routers = load balancing)
 9  172.66.3.8         destination                   ~16 ms
```

Lessons from that run:

- The times are **round-trip** milliseconds. A nearby server takes about 16 ms; one overseas would be roughly 90 ms or more. That's why CDNs exist, and why the hosting region matters (Week 6).
- **Double NAT** is fine for browsing, but it's another reason not to host a server from home.
- `* * *` is not an error. Some routers just don't answer traceroute's probes.

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
ipconfig getifaddr en0                 # your Mac's private IP (macOS)
curl -sS https://ifconfig.me           # the public IP the internet sees
whois <public-ip> | grep -i orgname    # who owns that IP (your ISP)
traceroute -m 15 www.mi-hotel.ca       # every router hop to the site
```

## Gotchas I hit

- `https//` (missing colon) → curl treats `https` as a hostname, and the DNS lookup fails. `-s` hid the error, so nothing printed. **Use `-sS`.**
- A URL on its own isn't a command. The first word of a shell line is the program to run.
- `00.241...` is read as `0.241...`, which falls in the reserved `0.0.0.0/8` block.
- **Never commit personal details** like your public IP to a public repo. Redact before committing.
