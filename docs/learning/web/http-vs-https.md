# HTTP vs. HTTPS

*Learned 2026-10-27 (Week 1, Day 1). Certificate and handshake details are from mi-hotel.ca.*

## In one sentence

**HTTPS is ordinary HTTP sent through a TLS connection**, which encrypts the traffic and verifies the server's identity. The HTTP messages (methods, headers, status codes) are identical; only the transport changes.

## Why it matters

A request passes through many machines: the home router, the ISP, backbone routers, the CDN (see the traceroute in [ip-addresses.md](../networking/ip-addresses.md)). Over plain HTTP, any of them can read or change it.

| | HTTP | HTTPS |
|---|---|---|
| Default port | 80 | 443 |
| Confidentiality | Others can read URLs, cookies, passwords and form data | Encrypted. Observers see only the destination host and IP. |
| Integrity | Content can be changed in transit | Tampering is detected |
| Authenticity | No proof of who answered | The certificate proves the server's identity |
| Browser UI | "Not Secure" | Padlock |

## Certificates and trust

The server presents a **certificate** that binds a domain to a public key, signed by a **Certificate Authority (CA)**. Operating systems and browsers ship with a list of trusted CA root certificates. A CA issues a certificate only after verifying control of the domain.

mi-hotel.ca's certificate on 2026-10-27:

```
subject = CN=mi-hotel.ca
SAN     = mi-hotel.ca, www.mi-hotel.ca
issuer  = Let's Encrypt (YR1)
valid   = 2026-09-30 → 2026-12-29   (90 days; renewal must be automated)
```

**Let's Encrypt** is a free, nonprofit CA, and the reason HTTPS is now everywhere.

## The TLS handshake (from `curl -v`)

```
Client hello      → supported TLS versions and ciphers
Server hello      ← chosen cipher
Certificate       ← server's certificate chain
CERT verify       ← proof the server holds the matching private key
Finished ⇄ Finished  → shared session key established
SSL connection using TLSv1.3 / AEAD-CHACHA20-POLY1305-SHA256
ALPN: server accepted h2         ← HTTP/2 negotiated during the handshake
SSL certificate verify ok.
```

This is why HTTPS requests came back as `HTTP/2` and plain-HTTP requests as `HTTP/1.1`.

> **Data analogy:** HTTPS is like `sslmode=verify-full` on a Postgres connection: encrypted, with the server's identity checked. Plain HTTP is like `sslmode=disable` across the public internet.

## Enforcing HTTPS

- **Redirect:** answer `http://` with `301 Location: https://...`, as mi-hotel.ca does.
- **HSTS:** the `Strict-Transport-Security: max-age=...` header tells browsers never to try HTTP with this site again.
- **`Secure` cookies:** session cookies are never sent over HTTP (see [cookies.md](cookies.md)).

## For this project

- The booking form collects personal information (names, contact details, cat health information). Canadian privacy law (PIPEDA) expects it to be protected, so HTTPS is required.
- Week 6: get a certificate (Let's Encrypt or the host's automatic TLS), automate renewal, redirect HTTP to HTTPS, and send HSTS.

## Try it

```bash
curl -sS -v -o /dev/null https://www.mi-hotel.ca 2>&1 | grep -E "TLS|subject|issuer|expire|verify"
echo | openssl s_client -connect www.mi-hotel.ca:443 -servername www.mi-hotel.ca 2>/dev/null \
  | openssl x509 -noout -subject -issuer -dates -ext subjectAltName
```

In Chrome: click the icon left of the address bar → **Connection is secure** → **Certificate is valid**.
