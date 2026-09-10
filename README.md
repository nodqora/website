# nodqora.com

The vendor site for [Nodqora](https://github.com/nodqora/nodqora), one static page deployed to
Cloudflare from `public/`. No build step, no dependencies, no JavaScript.

## The one page

**`/`** — what Nodqora is, how to install it, health and outcome, the API, and today's status. The
nav carries two items, Overview and Source, and Source leaves for GitHub.

## Copy rules

The wording here is bound by the house voice the product already uses in its empty states and
inspector: flat declarative statements, no exclamation, no second-person imperative, no verbs like
*unlock*, no marketing adjectives.

The page is a **derived surface**, and nothing is decided here. It reproduces claims settled in the
product README — the health/outcome pair, the five-value scale that is not a ladder, the install
route, the three GETs. Editing their wording here does not reopen them.

## There is no /editions page, deliberately

The site had one. It condensed
[`docs/editions.md`](https://github.com/nodqora/nodqora/blob/main/docs/editions.md) — the ledger,
the pricing unit, the trial, the support commitment — and it was removed on 2026-09-10 because
there is nothing on the other side of that line to sell. A vendor page setting out how a paid
edition would be priced, trialled and supported, for an edition that does not exist, advertises a
product rather than describing one.

The commitments have not changed and have not moved: they live in `docs/editions.md` in the product
repository, where the overview links to them. What was dropped is the vendor framing, not the
promise.

**Two things have to happen before that page comes back**, and they are separate:

1. There is an Enterprise build. Until then the page has nothing to describe.
2. [ADR-0133](https://github.com/nodqora/nodqora/blob/main/docs/adr/0133-the-mention-is-an-inert-label-one-line-and-one-link.md)'s
   constant gets wired. That ADR has the product name Enterprise in-shell with **one link to a
   single URL held in one constant**, and `https://nodqora.com/editions` is the URL
   [#87](https://github.com/nodqora/nodqora/issues/87) pins it to. **The constant must not ship
   while this page is absent** — a shipped Community build pointing at a 404 is worse than one that
   says nothing, and unlike the page, a shipped build cannot be edited back.

The old page is one `git revert` away; see the commit that removed it for the full text, including
the *Today* column that marked which rows were built.

## What the roadmap column is for

`docs/editions.md` carries a **Roadmap** column — `§49 phase 8`, `§49 phase 12` and so on — and that
column is the only thing marking most of the ledger as unbuilt. A condensation that drops it turns a
roadmap into a feature list, on both sides of the line: traversal, blast radius, drift,
authentication and audit capture are all Community and none of them is shipped, and no part of
Enterprise exists at all. That mistake was made here once. If the page returns, the column returns
with it.

The status note on the overview is the one part specific to this site. It says v0.1.1 is the first
published release and that no test in the suite reaches real infrastructure. Both are true today and
both have to stay true for the note to stand. **Rewrite it when either changes; do not delete it.**
A page with no status says less than one that states today's.

## The logo

`public/mark.png` is the isometric `dq` monogram with the white keyed out, so the counters show the
page background and the same file works in light and dark. `public/og.png` is the full lockup —
mark, wordmark and the *Live Operational Topology* tagline — on the navy it was drawn against.

The tagline appears **only** on the share card. In the page itself the description is the product
README's line, *"a topology and health canvas for event-driven systems"*, so a reader never meets two
different descriptions of the same product one above the other.

Sources are the five PNGs of the original logo kit; nothing here is generated at build time.

## Deploying

A Cloudflare Worker serving static assets — the successor to Pages, and where the dashboard's
"Connect to Git" now lands. `wrangler.jsonc` declares `public/` as the asset directory and no Worker
script, so the files are served as-is.

- Build command: none.
- Deploy command: `npm run deploy`.
- Pushing to `main` redeploys.

```bash
npm ci             # wrangler is pinned to an exact version in package-lock.json
npm run dev        # local preview at http://localhost:8787
npm run check      # dry run: reads the assets, builds nothing, deploys nothing
npm run deploy
```

Wrangler is a dev dependency pinned to an **exact** version rather than a caret range, because this
repo has no tests: a wrangler that changes how `_headers` or HTML handling behaves would ship the
change to production unnoticed. Upgrade deliberately, and check the headers afterwards.

`_headers` is honoured by Workers static assets, same as it was under Pages. **Use `npm run dev`
rather than a plain static file server** — `wrangler dev` runs the same asset worker as production,
so it applies `_headers` and the `auto-trailing-slash` HTML handling. A `python3 -m http.server`
does neither, which is how a broken CSP reaches the internet.

Both `nodqora.com` and `www.nodqora.com` are bound in `wrangler.jsonc`. Declaring `routes` disables
the `workers.dev` URL by default — that is Cloudflare's behaviour, not a misconfiguration. Set
`"workers_dev": true` if a staging URL is wanted back, and know that it serves the same content on a
third hostname.

## Verifying a deploy

The headers are the part with no test behind them, so check them after any wrangler upgrade or
`_headers` edit:

```bash
curl -sSI https://nodqora.com/ | grep -iE "content-security-policy|strict-transport"
```

All five headers should be present. An empty result means `_headers` is not being applied.
