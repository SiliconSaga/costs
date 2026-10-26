# Hosting

## Repositories

One repository under the SiliconSaga GitHub organization for now, like the schools ledger. Pipeline code under `pipeline/`, configuration under `config/`, derived JSON under `data/derived/` or attached as release assets depending on size, this design site under `docs/`. Raw files on the homelab only. Submission intake and storage on the homelab and GKE as [System overview](overview.md) describes, never on GitHub.

## This site

mkdocs-material, built by GitHub Actions on every push to `main` and deployed to the repository's GitHub Pages. Pull requests build with `--strict` and do not deploy, so a broken link fails the check before it reaches the site. The theme and generator are a convenience, not a commitment; the content is plain Markdown and moves if the tooling does. The site loads nothing from anyone else: system fonts, and the theme's privacy plugin, which copies every external asset (the diagram library included) into the build and rewrites the reference; see [Principles](../why/principles.md#privacy-by-default). One exception is known and accepted: the theme fetches a `ResizeObserver` polyfill from unpkg only in a browser that lacks that API, which no current browser does.

## The costs site

Static, built from the derived JSON. Generator undecided: this site's mkdocs could grow a data-driven section, or a separate Jekyll or Astro build could sit beside it. DuckDB-WASM in the browser is an option later for ad-hoc queries over the Parquet. Decided at M1, when there is one real procedure page to render.

## Domain

`costs.frontstate.org`, beside [schools.frontstate.org](https://schools.frontstate.org): Front State is the civic home of both sites, and the label is the component's name (see [Lineage](../why/lineage.md#naming)). Until the record exists the GitHub Pages default URL serves; the switch is a `CNAME` file in `docs/`, one DNS record, and the repository's Pages source set to GitHub Actions so the deploy job lands.

## Open

- Whether the civic repositories move from SiliconSaga to a Front State GitHub organization, and when; whether Front State becomes a nonprofit (see [Legal and privacy](../trust/legal.md#nonprofit-later)).
- Register `costs.frontstate.org` and switch the Pages source to Actions.
- The costs site's generator, at M1.
