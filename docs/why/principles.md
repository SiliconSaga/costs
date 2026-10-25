# Principles

Carried from the schools ledger and extended for prices and for documents residents send in.

## Record, not opinion

Every figure on the site carries a provenance label.

| Label | Meaning | Published as |
|---|---|---|
| **Record** | From a posted file or a government table, linked to the row | Fact |
| **Statement** | A provider's or insurer's own explanation, quoted with date | Quotation |
| **Press** | Reported by a named outlet, linked | Attributed claim |
| **Submitted** | A resident's bill, redacted and extracted; see [Layer D](../layers/d-submitted-bills.md) | Unverified, labelled, aggregated when counts are small |

## Reproducible

Raw files, then scripts, then tables. The scripts live in the repository; the raw files' URLs, hashes, and dates are recorded. Nothing is hand-typed into a table.

## Dated

Every figure carries the date of the file it came from. A stale file is marked stale, never silently kept.

## Plain words

The audience is frustrated parents, not health economists. One sentence of explanation per table, and a [methods](../reference/glossary.md) page for the rest.

## County-agnostic

Essex is a value in a configuration file. Everything that names a county, a facility, or an insurer lives in `config/`, not in code.

## Privacy by default

No accounts, no tracking, no cookies, and nothing on a page that makes a reader's browser contact anyone but us: no third-party fonts, scripts, embeds, or analytics. There is no page counter today: GitHub's traffic view counts visits to the repository, not to the published site, and we do not pretend otherwise. If a counter is ever added it runs on our side (our own server's logs if the site moves off Pages) and sets nothing in the browser. This design site uses system fonts and self-hosts its diagram library at build time for the same reason. Submitted documents never enter Git and are never published in any form. The repository runs the GDD PII guard, with a `.gdd-pii-allow` listing only addresses meant to be public.

## Not selling anything

This is a Front State project and says so, but it does not argue for Front State or for Demicracy; those have their own sites. Links to the ledger's services page where a reader would want the argument.

## Honest about what we could not check

A figure we could not verify is shown as unverified, never as wrong. A facility whose file we could not read is shown as unmeasured, never as non-compliant. See the outcome model in [Pipeline and data](../architecture/pipeline.md#outcome-model).

## Open

- Data licence for derived tables: proposal is CC0 for data and CC BY 4.0 for text. Confirm nothing in the source files' terms conflicts.
