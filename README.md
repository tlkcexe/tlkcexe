# tlkc

Software engineer working across backend architecture, authentication, and full-stack systems. I build the parts of an application most people would rather not think about — session lifecycles, access control, the failure paths — and treat them as the actual product, not the plumbing underneath it.

Two projects anchor this profile. Everything else here is in support of them.

---

## Flagship Projects

### KeyHold — an authentication system, not an auth demo

Most "auth starter" repositories are a login form wired to `localStorage`. **KeyHold** is what that login form should be sitting on top of: a token lifecycle that can be revoked, a password path that resists brute-forcing, and a session model that survives a stolen cookie.

The design decisions are deliberate, not default:

- **Two tokens, one tradeoff resolved.** A single long-lived JWT can't be revoked before it expires — that's the cost of statelessness. KeyHold splits the session into a short-lived, stateless access token and a long-lived, database-backed refresh token, keeping fast no-database-hit checks on every request while still being able to kill a session on logout, password change, or theft.
- **Reuse is treated as an incident, not an edge case.** Refresh tokens rotate on every use. If a token that's already been rotated past shows up again, that's a signature of a stolen token — and the response is to revoke the entire session family, not just flag the one token.
- **Nothing sensitive touches `localStorage`.** The access token lives in memory and disappears on reload; the refresh token sits in an `HttpOnly`, `SameSite` cookie scoped to the auth path. A single XSS bug in a dependency can't exfiltrate a session.
- **Enforced twice, trusted once.** Route protection and role checks are implemented independently on both the API and the client — neither side assumes the other got it right.

Passwords hashed with argon2id, refresh tokens stored hashed, rate limiting on every auth endpoint, no account enumeration, and an error handler that never leaks a stack trace. Fully typed, strict-mode TypeScript on both ends, with live Swagger docs and a one-command Docker setup.

**Stack:** Node.js · Express · PostgreSQL · Prisma · React · TypeScript · Docker

**[View repository →](https://github.com/tlkcexe/keyhold)**

<br>

### Facet — a storefront built on top of KeyHold as a hard boundary

**Facet** is an eCommerce platform that treats identity the way a real product would: as someone else's problem, integrated at arm's length. Facet's own API never sees a password, never issues a session, and never stores a user table — it trusts a JWT signed by KeyHold and verifies it locally, the same shape of integration you'd build against Auth0 or Clerk.

That constraint is the point. It forces a clean service boundary instead of the usual portfolio-project shortcut of bolting `next-auth` onto a shop with no real separation of concerns.

What makes it worth a second look:

- **Facet Compare** — pin up to three products and see price, stock, and specs lined up side by side, with the best value highlighted automatically. Most storefront clones skip this entirely; it's the feature Facet leads with.
- **A command palette, not just a search bar.** Press `⌘K` to jump anywhere or search the live catalog without touching the mouse — the kind of interaction detail that separates a SaaS-grade UI from a CRUD demo.
- **A cart that survives more than a page refresh.** Persisted state, an animated slide-over drawer instead of a page redirect, and sync across devices on login.
- **A trust boundary you can actually verify.** Facet's `requireAuth` middleware checks the JWT signature locally against a secret shared with KeyHold — no network round-trip on the hot path, no ambiguity about who owns identity.

Next.js 14 App Router on the frontend, Express and Prisma on the backend, Framer Motion running through every interaction instead of bolted on as an afterthought. Docker-ready, unit tested, documented.

**Stack:** Next.js · TypeScript · Express · PostgreSQL · Prisma · Tailwind CSS · Framer Motion · Docker

**[View repository →](https://github.com/tlkcexe/facet)**

---

## Other Projects

| Project | Description | Stack |
|---|---|---|
| [Crypto-Shredder](https://github.com/tlkcexe/crypto-shredder) | Memory-safe utility for irreversible file destruction via multi-pass overwriting | Rust |
| [Service-Sentinel](https://github.com/tlkcexe/service-sentinel) | Concurrent API and website health checker built on Goroutines | Go |
| [SysMon-Core](https://github.com/tlkcexe/sysmon-core) | Low-level Linux system monitor reading directly from kernel interfaces | C++ |
| [vault-cli](https://github.com/tlkcexe/vault-cli) | CLI tool for encrypting and managing sensitive data with AES-256 | Node.js |
| [PorkEngine](https://github.com/tlkcexe/PorkEngine) | Reusable, data-driven engine for text-based adventure games | Java |
| [Phantom-Grid](https://github.com/tlkcexe/phantom-grid) | Infrastructure-as-code blueprint for isolated microservice networks | Docker |

---

## Tech Stack

**Languages** — Rust · Go · TypeScript · JavaScript · Java · Python · C++
**Backend** — Node.js · Express · PostgreSQL · Prisma · REST API design · JWT-based auth
**Frontend** — React · Next.js · Tailwind CSS · Framer Motion
**Infrastructure** — Docker · Docker Compose · GitHub Actions · Nginx · Linux administration

---

## How I build

A system is only as trustworthy as its failure paths. I spend more time on what happens when a token is stolen, a request is malformed, or a dependency lies than on what happens when everything goes right — because the happy path was never the hard part.

That means threat-modeling before wiring up the UI, keeping services small enough to read in one sitting, and drawing boundaries between components deliberately rather than letting them blur over time. Code that's easy to trust six months from now is the actual deliverable — everything else is just the interface to it.

---

[GitHub](https://github.com/tlkcexe) · [LinkedIn](https://www.linkedin.com/in/archatz/)
