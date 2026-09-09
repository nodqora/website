# nodqora.com

The vendor site for [Nodqora](https://github.com/nodqora/nodqora), two static pages deployed to
Cloudflare from `public/`. No build step, no dependencies, no JavaScript.

## The two pages

- **`/`** — what Nodqora is, how to install it, health and outcome, the API, and today's status.
- **`/editions`** — Community vs Enterprise, the pricing unit, the trial, and the support
  commitment.

`/editions` is not decoration. ADR-0133 decides that the product names Enterprise inside the shell
as an inert menu item carrying one factual line and **one link to a single URL held in one
constant**. This page is that URL. If it moves, the constant in the frontend moves with it, and
every shipped Community build that predates the move points at a 404.

## Copy rules

The wording here is bound by the house voice the product already uses in its empty states and
inspector: flat declarative statements, no exclamation, no second-person imperative, no verbs like
*unlock*, no marketing adjectives.

Both pages are **derived surfaces**, and nothing is decided here:

- The overview reproduces claims settled in the product README — the health/outcome pair, the
  five-value scale that is not a ladder, the install route, the three GETs. Editing their wording
  on this page does not reopen them.
- The editions page is a condensation of
  [`docs/editions.md`](https://github.com/nodqora/nodqora/blob/main/docs/editions.md), which is
  itself a rendering of ADR-0107 through ADR-0113 (the ledger), ADR-0123 through ADR-0128 (how
  Enterprise is sold) and ADR-0135 through ADR-0140 (support). **Every commitment on that page is
  load-bearing** — the one-business-day response, the UTC+4 timezone, the thirty-day grace period,
  the five-deals-or-twelve-months trigger for publishing a price. Change one here and it disagrees
  with the ADR that owns it, which is the failure mode a condensation exists to risk.

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
- Deploy command: `npx wrangler deploy`.
- Pushing to `main` redeploys.

`/editions` resolves to `public/editions/index.html` through the default `auto-trailing-slash` HTML
handling. `_headers` is honoured by Workers static assets, same as it was under Pages.

To read it locally, serve `public/` over HTTP rather than opening the file directly — the pages link
to `/style.css` and `/editions` by absolute path:

```bash
python3 -m http.server 8000 --directory public
```
