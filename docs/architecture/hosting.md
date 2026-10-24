# Hosting

## Repositories

One repository, `prices`, under the SiliconSaga GitHub organization for now, like the schools ledger. Both move when the civic organization exists. Pipeline code under `pipeline/`, configuration under `config/`, derived JSON under `data/derived/` or attached as release assets depending on size, this design site under `docs/`. Raw files on the homelab only. Submission intake and storage on the homelab and GKE as [System overview](overview.md) describes, never on GitHub.

## This site

mkdocs-material, built by GitHub Actions on every push to `main` and deployed to the repository's GitHub Pages. Pull requests build with `--strict` and do not deploy, so a broken link fails the check before it reaches the site. The theme and generator are a convenience, not a commitment; the content is plain Markdown and moves if the tooling does.

## The price site

Static, built from the derived JSON. Generator undecided: this site's mkdocs could grow a data-driven section, or a separate Jekyll or Astro build could sit beside it. DuckDB-WASM in the browser is an option later for ad-hoc queries over the Parquet. Decided at M1, when there is one real procedure page to render.

## Domain

Undecided. A `frontstate.org` subdomain is available in principle, but the civic components are expected to move to their own organization, and the domain should follow that decision. Until then the GitHub Pages default URL serves.

## Open

- One entity or two for the ledger and this site; the name of the civic organization; whether it becomes a nonprofit (see [Legal and privacy](../trust/legal.md#nonprofit-later)).
- The domain, once the organization question is settled.
- The price site's generator, at M1.
