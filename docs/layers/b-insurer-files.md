# Layer B — Insurer files

Each carrier publishes a monthly table-of-contents file listing in-network rate files per plan or network, and those files list the negotiated rate for every code at every in-network provider, including the freestanding imaging and surgery centers, physician offices, and labs that the hospital files cannot reach. Layer B filters those files down to Essex County providers and the code list, and merges the result into the same procedure pages Layer A renders.

## Sources

Carriers that matter in Essex: Horizon Blue Cross Blue Shield of New Jersey (which administers the state and school employee plans), Aetna (the district's fund, SHIF), UnitedHealthcare and Oxford, Cigna, AmeriHealth New Jersey, and the Medicaid managed-care plans if their files are in scope. The in-network files are JSON per the CMS schema ([`CMSgov/price-transparency-guide`](https://github.com/CMSgov/price-transparency-guide), version 2.2.1 as of October 2026). CMS hosts no files and no index of carrier URLs; each carrier's table-of-contents file is found on its own site.

Each table-of-contents file lists `reporting_structure` entries, each naming plans (by EIN or HIOS id, with the plan sponsor and market type) and the in-network files that cover them. A single in-network file is one JSON object with a `provider_references` array (NPIs and TINs by group id, sometimes in a separate remote file) and an `in_network` array of billing codes, each with negotiated rates per provider group: rate, type (negotiated, derived, fee schedule, percentage, per diem), expiration, billing class, setting, service codes, modifiers.

!!! warning "Unverified"
    The regulatory cite (45 CFR 147.210 to 147.212), and whether Medicaid managed-care plans publish under the rule.

## Size

A big carrier's index file is hundreds of megabytes with tens of thousands of entries pointing at the same in-network files; the in-network files are routinely one to over a hundred gigabytes uncompressed each (one UnitedHealthcare file: 4 GB gzipped, 122 GB expanded). The industry total has been estimated at about a petabyte a month. Dedupe by URL and filter by NPI while streaming, or the volume is not civic-sized.

**Record.** The Transparency in Coverage final rule **CMS-9882-F** was announced October 5, 2026 and published October 6 (91 FR 63748; [CMS fact sheet](https://www.cms.gov/newsroom/fact-sheets/transparency-coverage-final-rules-cms-9882-f); [DOL FAQ Part 75](https://www.dol.gov/agencies/ebsa/about-ebsa/our-activities/resource-center/faqs/aca-part-75)). It moves to **one in-network file per provider network** with a network name and identifier, removes "ghost rates" for provider-code pairs that never occur, mandates JSON, bans CAPTCHAs and download limits, requires an attestation in every file, and changes the cadence from monthly to **quarterly**. New files: a taxonomy file, a utilization file (which providers had at least one paid claim), and a text file at the site root listing file locations. Timeline: schema 3.0 published about February 1, 2027; first quarterly posting under it **April 1, 2027**; taxonomy and text files from October 1, 2027; utilization from July 1, 2028. Until April 2027 the current 2.x files continue.

Layer B is gated on hardware, not on the rule: the current files are usable now given disk and bandwidth. But anything built on the 2.x index and plan structure is rewritten within months, so the spike version-stamps every row and treats the parser as disposable.

## Approach

A spike first: one carrier, one network, one month, on the homelab.

1. Build an Essex NPI list from the NPPES bulk download filtered by practice-location county and ZIP, joined to the Department of Health facility list and to the Type 2 NPIs found in the hospital files.
2. Parse the carrier's table of contents, keep the in-network files whose plans are New Jersey-relevant, and dedupe by URL.
3. Stream each in-network file and keep only rows where the provider NPI is on the Essex list and the code is on the code list. DuckDB cannot read a multi-gigabyte single-object JSON file directly, so the stream is either a pre-splitter emitting newline-delimited JSON for DuckDB, or one of the Go or Python streaming parsers writing Parquet.
4. Write Parquet in the same shape as Layer A plus a facility-type column, so a procedure page shows hospital outpatient department, freestanding center, and physician office for the same code and insurer side by side.

Candidates to evaluate before writing anything, in order: `AccessMRF/tic-mrf-parser` (Go, bounded memory, Parquet out), `cms-mrf-extractor` (Python, Parquet out); both are new and thinly used. `mrfparse` is archived and `mrfutils` abandoned. Details on [Sources and projects](../reference/sources.md#insurer-side).

## Storage and compute

Raw monthly files per carrier of one to two terabytes in the current format. Keep the last two months, archive the extracted Parquet, which is small. Fast fiber makes the download a matter of hours; parsing is the bottleneck, so the box wants fast NVMe scratch and cores more than it wants RAM. Results flow to the same JSON the site reads. GitHub Actions never touches a raw insurer file. See [System overview](../architecture/overview.md).

## Cross-check

The same negotiated rate appears in the hospital's file and in the insurer's file. Where they disagree, that is a finding. The disagreement rate per facility and carrier is published.

## Open

- Which network names correspond to the plans local people actually hold; plan-name mapping is the hardest part of this data.
- Whether Medicare Advantage and Medicaid lines are useful here.
- Whether to touch the out-of-network allowed-amount files at all, which have been widely empty.
- Measure one carrier's table-of-contents and one network file before sizing the box.
- Evaluate `tic-mrf-parser` and `cms-mrf-extractor` on that file.
- Confirm the regulatory cite and the Medicaid managed-care question.
