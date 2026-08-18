# Portfolio strategy — anton.immo

Working notes on how to stand up a portfolio that actually reads as *senior backend engineer*
rather than "developer with a website."

---

## 1. The recommendation

**Do all three, but not the way you framed them, and not in that order.**

| Surface | Verdict | Effort |
|---|---|---|
| `antonimmo/antonimmo` profile README | **Do it first.** Highest-traffic surface you own, currently the default template. | ~2 h |
| Custom webapp at `anton.immo` | **Do it, in its own public repo.** This is the portfolio. | 1 weekend for v1 |
| `anton.immo` → redirect to GitHub | **Don't.** See §2. | — |

The three options aren't alternatives — they're layers. The profile README is the *hook*,
`anton.immo` is the *narrative*, and the repos are the *evidence*. Each links to the next.

### Why not build the site inside `antonimmo/antonimmo`

That repo has one job: render your profile README. Dropping a Next.js app in there means
anyone who clicks through from your profile lands in a file browser full of `node_modules`
config instead of a README. It also isn't the magic Pages repo — that's `antonimmo.github.io`;
`antonimmo/antonimmo` would publish under a `/antonimmo/` path prefix.

Use a separate **public** repo (`antonimmo/anton.immo` or `antonimmo/portfolio`). Public matters:
the portfolio's own source is a work sample, and right now you have very little self-authored
public code (see §6).

---

## 2. Why the redirect is the weakest option

1. **You lose the room.** A redirect hands your visitor to a UI you don't control, styled by
   GitHub, ranked by GitHub's idea of what matters.
2. **A GitHub profile can't tell a story.** It's a contribution graph and a repo list. It can't
   say "here's a system I built, here's why it was hard, here's it running right now."
3. **A domain that only redirects reads as unfinished.** People notice.
4. **No analytics, no control over first impression, no way to A/B a pitch.**

Invert it: `anton.immo` is the destination, and the GitHub README's job is to send people there.

---

## 3. Architecture

The organizing principle: **you are a backend engineer, so the portfolio should be a backend,
not a brochure.** A static React page proves you can center a div.

```
anton.immo                    → static site (Cloudflare Pages)
anton.immo/api/*              → Go service (real API, OpenAPI spec, rate limits, ETags)
anton.immo/lab/*              → live demos, backed by the Go service
status.anton.immo             → the site reporting on itself
```

**Stack, opinionated:**

- **Frontend:** Next.js static export + Tailwind — you already know it, don't spend novelty
  budget here. (Astro is the better content-site tool if you want a nicer writing pipeline,
  but it's not worth learning for v1.)
- **Backend:** **Go.** You know it, and for a backend portfolio a Go service reads far stronger
  than a Node BFF. Single binary, trivial to containerize, trivial to host anywhere.
- **Hosting:** Static on Cloudflare Pages (you're already automating the Cloudflare API).
  Go service on Fly.io / Railway — *or* on your own Proxmox box behind a Cloudflare Tunnel.

**On the homelab:** you have Proxmox, Terraform, WireGuard, Headscale and Cloudflare dynamic DNS
on your CV. Serving the dynamic half of your portfolio from your own rack is a genuinely good
story — *and* a genuine uptime risk. The split above handles it: the static site never goes down,
and every live demo degrades to a cached snapshot with an honest "homelab is offline, here's the
last good run" banner. Owning that gracefully is itself the senior signal.

---

## 4. Ideas to showcase

Tiered by cost-to-credibility ratio. You do not need all of these — you need **two writeups and
one live demo** to be ahead of ~95% of backend portfolios.

### Tier A — live demos (highest impact, pick one to start)

**A1. Live ocean forecast API + map.** ⭐ *The one nobody else has.*
Expose a thin public slice of the forecasting system you already operate: an animated map of
Santander Bay currents / sea level, served by `GET /api/forecast`, with an OpenAPI spec and a
status panel showing last ingest time, volume processed, and p99 latency. Proves ETL at scale,
API design, and production ops — all at once, and it's beautiful.
→ *Caveat: clear licensing with IHCantabria first, or rebuild the slice on public Copernicus/CMEMS
data so it's unambiguously yours.*

**A2. Verifiable credential playground.**
Visitor gets an ECDSA-signed "you visited anton.immo" credential. They can inspect the JSON,
edit a field in the browser, and watch server-side verification reject it. Ships the applied-crypto
story in 30 seconds. Go backend, plus WASM if you want client-side verify too — which also
demos the cross-platform crypto work from tokenproof.

**A3. Rules engine sandbox.**
A public playground for the eligibility-engine pattern: type a rule in a small DSL
(`holds(NFT_X) AND holds(TICKET_Y) AND age > 18`), evaluate it, get back a per-predicate
execution trace with timings. Shows DSL design, evaluation strategy, and where the sub-2s
budget goes.

**A4. "3,000 users per minute" — load-test theater.**
Visitor presses a button, fires a burst at a queue-backed ticketing simulator, and watches a live
dashboard (Redis + WebSocket) show throughput, queue depth, backpressure kicking in, and graceful
degradation. Turns a CV bullet into something clickable. Rate-limit it hard so it isn't a free
DDoS button.

### Tier B — engineering writeups (cheapest credibility per hour)

**B1. "50× more particles, 4× faster."**
The IH-TESEO Fortran/OpenMP rework. Profiling methodology, memory layout changes, what the
OpenMP scheduling actually bought you, the netCDF I/O migration. With a scaling chart and a
flamegraph. Almost no one writes this kind of post — it's your single most differentiated asset.

**B2. "Partitioning waves from balanced motion across 2 TB of ocean output."**
Your PhD pipeline, framed as a *data engineering* war story rather than a physics paper: chunking
strategy, Dask scheduler tuning, what fell over, what it cost.

**B3. "Signing credentials on a $50 Android phone."**
The Go cross-platform mobile crypto library. gomobile pitfalls, ECDSA choices, on-device vs
backend verification trade-offs, low-end device constraints.

**B4. "Embedded wallets for people who don't want a wallet."**
Glyph, as product + engineering. The 2k-internal → +5k-third-party adoption arc, and the design
decisions that made non-crypto-natives able to use it.

### Tier C — structural touches (small, memorable)

**C1. Evidence-linked CV.** *This is the organizing principle of the whole site.* Every claim on
your CV page links to a repo, a writeup, or a live demo. Claims → proof, no exceptions. Any bullet
you can't link is a bullet worth turning into a Tier A/B item.

**C2. `curl anton.immo` returns a plain-text résumé.** Content negotiation: browsers get HTML,
`curl` gets ANSI-coloured text. ~20 lines of Go. Engineers screenshot this and share it.

**C3. A real `/api`.** Publish an OpenAPI spec for your own site's API — projects, posts, `/now` —
with rate limiting, ETags, and correct cache headers. A backend engineer's portfolio should have
an API worth reading.

**C4. Publish your own SLO.** The site displays its own p99 and error rate, pulled live. Practice
what the CV preaches.

**C5. Homelab status page.** Proxmox LXC inventory, Headscale mesh nodes, Terraform-managed, live
uptime. "This page is served from a rack in Santander over a Cloudflare Tunnel."

**C6. Local LLM benchmark rig.** llama.cpp parameter tuning on constrained hardware, published as
a live-updating dataset + charts. Grounds the AI/LLM tooling line in numbers instead of tool names.

---

## 5. Sequence

Ship in this order. Each step is independently valuable — you can stop at any point and still be
better off than you are now.

| When | What | Why |
|---|---|---|
| **Day 1** | Rewrite the profile README (draft in `docs/profile-readme-draft.md`). Fix pinned repos. | Biggest delta per hour. |
| **Weekend 1** | `anton.immo` v1: evidence-linked CV (C1), one writeup (**B1**), `curl` easter egg (C2). | A real site with real content beats a polished shell. |
| **Weeks 2–3** | One Tier A demo — **A1** if licensing clears, otherwise **A2**. | The thing people remember. |
| **Ongoing** | Second writeup, second demo, `/api` + SLO page. | Compounding. |

Deliberately **not** in v1: a blog engine, a CMS, dark-mode toggles, animation work, a newsletter.
Those are procrastination with a commit history.

---

## 6. Two honest problems to fix

**Your pinned repos are mostly forks.** `xmitgcm`, `Duke`, `viem`, `DefinitelyTyped`,
`public-wallet-connectors` are other people's projects. A pinned fork reads as "I once cloned
this." Unpin them. Pin things you authored — `ebc-wk-spectral-analysis`,
`opencerta-icerta-issuer-cli`, `glyph-sdk-react` — plus the new portfolio repo and each demo repo
as they ship.

**You have very little self-authored public code**, which is unusual for 10+ years of output and
is mostly an artifact of your work being closed-source or under employer orgs. This is precisely
why the demo-first approach beats a brochure: each Tier A/B item creates a public repo that didn't
exist before. In ~6 weeks the portfolio isn't just describing your work, it *is* new work.

---

## 7. Open questions

1. **IH-TESEO / forecast data** — what can be published, and under whose name? Decides A1 vs A2.
2. **Is Glyph's source public**, and can you link it directly as your own work?
3. **Homelab exposure** — comfortable serving public traffic through a Cloudflare Tunnel into
   Proxmox, or keep the demos on Fly.io and just *document* the homelab?
4. **Job-hunting or long-game?** If actively interviewing, compress to Day 1 + Weekend 1 and put
   everything into B1 and C1. Demos are a month-two investment.
