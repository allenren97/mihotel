# How the web works: what happens when you visit www.mi-hotel.ca

*Learned 2026-10-26 and 2026-10-27 (Week 1, Day 1). Every example is a real response from mi-hotel.ca.*

This is the overview note. The detailed notes it links to: [IP addresses](../networking/ip-addresses.md), [HTTP vs. HTTPS](http-vs-https.md), [cookies](cookies.md).

## The four steps

```
1. DNS      www.mi-hotel.ca  →  172.66.3.8               "where is it?"
2. Connect  TCP to port 443, then the TLS handshake      "open a secure line"
3. HTTP     GET /  →  200 OK + headers + HTML            "ask and answer"
4. Render   the browser fetches CSS, JS and images, then draws the page
```

The core model for the whole project: **the client asks, the server answers.** From Week 2 onward, we write the server.

## 1. DNS: the name becomes an address

DNS is a distributed, cached lookup table from names to IP addresses.

```
$ dig +short www.mi-hotel.ca
pointing.wixdns.net.     ← CNAME (an alias: "this name is really that name")
cdn3.wixdns.net.         ← CNAME
cfd.wixdns.net.          ← CNAME
172.66.3.8               ← A record (the actual IPv4 address)

$ dig +short NS mi-hotel.ca
ns11.domaincontrol.com.  ← NS: who answers DNS questions for this domain (GoDaddy)
```

| Record | Meaning |
|---|---|
| `A` / `AAAA` | Name → IPv4 / IPv6 address |
| `CNAME` | Name → another name (an alias) |
| `NS` | Which nameservers are authoritative for the domain |
| `MX` | Where email for the domain goes |
| `TXT` | Free text, used for domain verification and email security |

Answers are cached at every level (browser, OS, ISP) for each record's **TTL** (time to live). That's why DNS changes take time to "propagate."

**Project relevance:** the domain's DNS is managed at **GoDaddy**. In Week 6 we add a record for a test subdomain; in Week 12 we change `www` from Wix to the new app.

## 2. Connecting: TCP, port 443, TLS

The browser opens a connection to `172.66.3.8:443` and performs the TLS handshake (see [http-vs-https.md](http-vs-https.md)). Plain `http://` uses port 80 and has no encryption.

## 3. HTTP: request and response

A response is a **status line**, **headers** (metadata) and a **body**.

```
HTTP/2 200
content-type: text/html; charset=UTF-8
server: cloudflare
cf-cache-status: MISS
strict-transport-security: max-age=31556952
```

### Status codes

| Range | Meaning | Seen on mi-hotel.ca |
|---|---|---|
| 2xx | Success | `200 OK` for the home page |
| 3xx | Go elsewhere (see `Location`) | `301` from `http://` to `https://` |
| 4xx | Client error | `404` for `/this-page-does-not-exist` |
| 5xx | Server error | none seen |

- **301** is permanent (browsers remember it). **302** is temporary ("ask again next time").
- The post-redirect pattern (save, then `302` to a confirmation page) will be used for bookings.

### HTTP versions

`HTTP/1.1` over plain HTTP, `HTTP/2` over HTTPS (negotiated during TLS), and `alt-svc: h3` advertises HTTP/3.

## The CDN layer: who actually answered?

```
Browser (Toronto) → Cloudflare edge "YYZ" (Toronto) → Wix origin (Virginia)
                    cf-cache-status: HIT = answered here; MISS = fetched from the origin
```

A **CDN** caches content close to users. The **origin** is the real server behind it. This is the same idea as a cache in front of a slow source system.

## 4. Rendering: HTML and everything it pulls in

The HTML document starts with a `<head>` of metadata (`charset`, `viewport` for mobile, favicon links) and loads many more resources. Wix's home page is large and pulls in dozens of files. Finding: the site uses **Wix's default favicon**, logged in the [site audit](../../research/current-site-audit.md).

## curl cheat sheet

| Command | What it does |
|---|---|
| `curl URL` | GET the URL and print the body |
| `-s` / `-sS` | Silent / silent but still show errors (prefer `-sS`) |
| `-I` | Headers only |
| `-i` | Headers and body |
| `-L` | Follow redirects |
| `-v` | Verbose: the full conversation, including TLS |
| `-c file` / `-b file` | Save cookies / send cookies |
| `-X POST` | Use a different HTTP method |
| `cmd \| head -20` | Show only the first 20 lines |
| `cmd \| wc -c` | Count bytes |
