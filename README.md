# Revenue Opportunity Detector — Demo

**Designed & built by Greg Hood.**

A single-page dashboard that scans a short-let property portfolio, flags underperforming
properties against six revenue signals, sizes the recoverable revenue behind each flag, and
routes the fix to the right person.

**[▶ View the live demo](https://YOUR-USERNAME.github.io/revenue-opportunity-detector-demo/)**
&nbsp;·&nbsp; *(update this link once GitHub Pages is enabled — see below)*

> **This is a demo build.** Every property, host, account manager and figure is fictional
> sample data. It runs entirely in the browser — no API keys, no outbound requests, no
> connection to any live system. Actions that would normally write to a CRM simulate their
> result instead.

---

## What it does

The tool answers one question for a portfolio manager: **where is revenue leaking, how much
is it worth, and who should act on it?**

It scores every property against six signals:

| Signal | What it catches |
|---|---|
| 💰 **Below Market Price** | Realised nightly rate sits under comparable local listings |
| 📉 **Low Occupancy** | Booked nights well below the local market rate |
| 📅 **Availability Gap** | Nights blocked during periods that reliably sell |
| 📝 **Incomplete Listing** | Missing photos, thin description, unticked amenities |
| ⚠️ **Declining Performance** | Booking value falling year on year |
| 🚫 **High Cancellation Rate** | Cancellations concentrated on one property — and how much of that value was *never resold* |

Each flag carries a **£ recoverable value**, so the list sorts by what is actually worth
someone's afternoon rather than by which number looks worst.

## Things worth clicking in the demo

- **Sort by Highest Opportunity** — the ranking that makes the tool useful; biggest recoverable
  value first, not biggest percentage drop.
- **Performance History** on any card — 12-month occupancy / nightly rate / booking value
  sparklines, with new listings handled separately so three months of data isn't read as a decline.
- **Cancellations filter** → *Peak District Farmhouse* — shows gross cancelled value split into
  what was resold versus what was genuinely lost. A 34% cancellation rate with a 64% resell rate
  is a very different problem from the same rate with nothing recovered.
- **Email Host** on any card — generates a ready-to-send draft written for that property's
  specific signal, with its real numbers filled in. Editable before it goes anywhere.
- **Create Task** — in production this raises a CRM task against the property's account manager.
- **Run Analysis** — animates the pipeline the scheduled job runs each Monday.

## How the production build differs

The demo is the full interface running on a fixed sample set. In production:

1. A scheduled workflow queries the **data warehouse** for per-property booking value,
   occupancy, nightly rate and cancellation history over a rolling 12-month window.
2. **Pricing and market-benchmark** sources are layered on to establish what each property
   *should* be earning against comparable local listings.
3. A detection pass flags each property, sizes the recoverable revenue, and ranks the portfolio.
4. Flagged properties are matched to their **account manager and host record in the CRM**, so a
   task or a host email can be raised in one click.

The cancellation logic is the part most worth explaining: cancelled bookings are expanded to
individual nights, then checked against every night subsequently booked on that property. Nights
that were later resold are removed from the loss. What remains — *net unrecovered value* — is the
only figure worth chasing.

## Running it

It is one self-contained HTML file with no build step and no dependencies.

```bash
open index.html
```

Or serve it locally:

```bash
python3 -m http.server 8000
```

## Hosting on GitHub Pages

```bash
git init
git add .
git commit -m "Revenue Opportunity Detector — demo build"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/revenue-opportunity-detector-demo.git
git push -u origin main
```

Then in the repository: **Settings → Pages → Source: `Deploy from a branch` → Branch: `main` / `root` → Save.**

The site publishes at `https://YOUR-USERNAME.github.io/revenue-opportunity-detector-demo/`
within a minute or two. Update the demo link at the top of this file once it's live.

> If the repository is public, anyone with the URL can view it. Nothing in this build is
> confidential, but check the **Before you share** list below before pushing.

## Why I built it

I was spending too long manually pulling reports to work out which properties in the portfolio
were quietly underperforming — and even then I didn't know which ones were worth acting on first.
A 40% occupancy drop on a low-value listing matters far less than a 10% drop on a high-value one,
but a percentage-ranked report treats them the same.

So the tool does both halves: it finds the problem *and* prices it. Every flag carries a £ figure,
so the list sorts by what's genuinely worth someone's afternoon.

The cancellation signal is the piece I'm happiest with. A raw cancellation rate is close to
meaningless on its own — what matters is whether those nights got resold. So the query expands
cancelled bookings into individual nights, checks each one against every night later booked on
that property, and subtracts what was recovered. What's left is the only number worth chasing.

I built the whole thing solo: the warehouse SQL, the detection and sizing logic, the scheduled
pipeline, the CRM integration, and this interface.

**— Greg Hood**

## Before you share

- [ ] Sample data only — no real property IDs, hosts, colleagues or figures *(done in this build)*
- [ ] No API keys, tokens or webhook URLs in the source *(done — verified)*
- [ ] No internal hostnames or warehouse/table names *(done)*
- [ ] Attribution to me is intentional (banner byline, footer credit, `GH` avatar, greeting)
- [ ] Company branding is intentional and cleared for external use
- [ ] My email address is deliberately **not** in the page — a public GitHub Pages URL gets
      scraped. Add it to the footer credit only if you want to be contactable that way.
- [ ] Vendor names: this build labels data sources generically. If you'd rather name the
      actual products in the Settings → Data Integrations panel, edit the `.api-card` blocks.

## Customising the demo

Everything lives in `index.html`.

| To change | Look for |
|---|---|
| Sample properties | `const DATA = [` |
| Signal labels and icons | `const ISSUE_CFG = {` |
| Host email templates | `function buildEmailDraft(p)` |
| Demo banner wording / byline | `<div class="demo-banner">` |
| Footer credit | `<div class="build-credit">` |
| "Why I built it" in-app copy | Settings → search `Who built this` |
| Colours and type | the `:root` custom properties in `<style>` |

Headline figures (properties flagged, total opportunity, occupancy gap, high-priority count)
are all derived from `DATA` at load — edit the sample set and the KPIs follow.
