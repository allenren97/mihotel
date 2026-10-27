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

## Where cookies live

Cookies are stored by the **browser, on the user's machine**. The server only asks the browser to store one.

- Chrome on macOS keeps them in `~/Library/Application Support/Google/Chrome/Default/Cookies`, which is a **SQLite database** with a `cookies` table (`host_key`, `name`, `value` (encrypted), `expires_utc`, `is_httponly`, ...).
- Session cookies (no expiry) often live only in memory.
- They're separate for each browser, profile and device. Incognito windows start empty.
- "Clear cookies" deletes the rows, which logs you out.
- There's a limit of about 4 KB per cookie, and every matching request carries them.

**Rule: never trust cookie contents.** Users can view and edit their own cookies in dev tools. A cookie like `role=admin` could be forged in seconds. So store only an unguessable random ID, and keep the meaning (who, role, logged-in status) in the server's database.

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

## Worked example (httpbin.org)

```bash
# 1. Server sets it:  set-cookie: favourite_cat=mochi; Path=/
curl -sS -i "https://httpbin.org/cookies/set?favourite_cat=mochi" -c jar.txt

# 2. Client stores it (curl's jar; tab-separated, like Chrome's SQLite table)
#    domain       subdomains  path  secure  expires  name           value
#    httpbin.org  FALSE       /     FALSE   0        favourite_cat  mochi
cat jar.txt

# 3. Client sends it back automatically:  Cookie: favourite_cat=mochi
curl -sS https://httpbin.org/cookies -b jar.txt
# → {"cookies": {"favourite_cat": "mochi"}}

# 4. Forgery: the server accepts whatever the client sends
curl -sS https://httpbin.org/cookies -b "favourite_cat=garfield; role=admin"
# → {"cookies": {"favourite_cat": "garfield", "role": "admin"}}
```

`-c` saves cookies to a file, and `-b` sends cookies from a file or a string. Step 4 is the whole reason cookie contents must never be trusted.

## Try it

- Terminal: the commands above.
- Browser: visit `https://httpbin.org/cookies/set?favourite_cat=mochi`, then open dev tools → **Application** → **Cookies**. Edit the value and reload; the server believes the edited value.
