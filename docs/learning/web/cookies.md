# Cookies

*Learned 2026-10-26 (Week 1, Day 1). Came up after spotting `set-cookie` headers in a 404 response from mi-hotel.ca.*

## What it is

A **cookie** is a small piece of text that a website asks the browser to store. The browser then sends it back to that same site on every later request.

## The problem it solves

HTTP is **stateless**: each request is independent, and the server can't tell whether two requests came from the same person. Logins, carts and preferences all need continuity, and cookies provide it.

## How it works

It's two headers:

```
Response:  Set-Cookie: session_id=abc123; HttpOnly; Secure; SameSite=Lax; Max-Age=3600
Requests:  Cookie: session_id=abc123      ← the browser attaches this automatically from then on
```

Login flow (the plan for Week 9):

```
POST /login (credentials) → server creates a session row abc123 → Set-Cookie: session_id=abc123
GET /admin/bookings  Cookie: session_id=abc123 → server looks up abc123 → "owner" → 200
```

> **Data analogy:** the cookie is a **foreign key**. The browser holds an opaque ID, and the real data lives in a server-side `sessions` table. Each request effectively runs `SELECT ... FROM sessions WHERE id = :cookie`.

## Attributes

| Attribute | Effect | Protects against |
|---|---|---|
| `Expires` / `Max-Age` | Deletion time. Without one, it's a session cookie that dies when the browser closes. | Sessions lasting forever |
| `HttpOnly` | JavaScript can't read it | Session theft through XSS (injected scripts) |
| `Secure` | Sent only over HTTPS | Interception on insecure networks |
| `SameSite` (`Strict` / `Lax` / `None`) | Whether it's sent on requests started by other sites | CSRF (forged requests) |
| `Domain` / `Path` | Which URLs receive it | Leaking it to other sites or paths |

A real example seen on mi-hotel.ca (Cloudflare's bot detection):
`__cf_bm=...; HttpOnly; SameSite=None; Secure; Path=/; Domain=www.mi-hotel.ca; Expires=...` (30 minutes)

## Misconceptions

- Cookies are text, not programs, and they can't execute anything.
- A site can only read its own cookies, because the browser scopes them by domain.
- **Third-party cookies** (set by embedded content such as ad networks) enable cross-site tracking, which is what consent banners are about. MiHotel needs only **first-party** cookies.

## For this project

The admin login (Week 9) will use a session cookie with `HttpOnly; Secure; SameSite=Lax`. The alternative is tokens (JWTs), and that choice will get its own decision record when we reach it.

## Try it

Chrome dev tools → **Application** → **Cookies** → pick the site.
