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

A spike first: one carrier, one network, one month, on the homelab. The carrier is Cigna, because a volunteer holds a Cigna plan and can check the plan-to-network mapping, the hardest part of this data, against a real card and explanation of benefits rather than against the file alone.

1. Build an Essex NPI list from the NPPES bulk download filtered by practice-location county and ZIP, joined to the Department of Health facility list and to the Type 2 NPIs found in the hospital files.
2. Parse the carrier's table of contents, keep the in-network files whose plans are New Jersey-relevant, and dedupe by URL.
3. Stream each in-network file and keep only rows where the provider NPI is on the Essex list and the code is on the code list. DuckDB cannot read a multi-gigabyte single-object JSON file directly, so the stream is either a pre-splitter emitting newline-delimited JSON for DuckDB, or one of the Go or Python streaming parsers writing Parquet.
4. Write Parquet in the same shape as Layer A plus a facility-type column, so a procedure page shows hospital outpatient department, freestanding center, and physician office for the same code and insurer side by side.

Candidates to evaluate before writing anything, in order: `AccessMRF/tic-mrf-parser` (Go, bounded memory, Parquet out), `cms-mrf-extractor` (Python, Parquet out); both are new and thinly used. `mrfparse` is archived and `mrfutils` abandoned. Details on [Sources and projects](../reference/sources.md#insurer-side).

## Storage and compute

Raw monthly files per carrier of one to two terabytes compressed in the current format. Keep the last two months, archive the extracted Parquet, which is small. Fast fiber makes the download a matter of hours; parsing is the bottleneck, so the box wants cores and a fast working tier more than it wants RAM, and the raw archive can sit on slow bulk disk because it is read once, sequentially. The full shape, with the estimates behind it, is on [Homelab box](../architecture/homelab.md). Results flow to the same JSON the site reads. GitHub Actions never touches a raw insurer file. See [System overview](../architecture/overview.md).

## Cross-check

The same negotiated rate should appear in the hospital's file and in the insurer's file. Where it does not, that is a finding, but only after both rows have been matched on the same thing. The match key: the hospital's Type 2 NPI (and TIN where the insurer file groups by it) against the provider reference; billing code, code type, and modifiers; setting; billing class, which the two templates name differently, so the hospital file's `facility` maps to the insurer file's `institutional`, `professional` to `professional`, and a hospital row marked `both` bundles what the insurer file splits and is left unmatched; the insurer's network mapped to the hospital file's payer and plan through `payers.yaml`; rate type restricted to dollar-valued negotiated rates on both sides, so percentage and algorithm rows are excluded; and dates. Hospital rows carry no per-rate dates, so a hospital row's interval is its file's `last_updated_on` until the next file version we hold; an insurer row's interval runs from its file's `last_updated_on` to its `expiration_date`. The rule is interval overlap: two rows are comparable when their intervals share at least one day. The unit of comparison is one hospital row (facility, payer and plan, code, class, setting, file version). When it overlaps several insurer rows (a rate that changed mid-interval), every overlapping pair is compared and the unit gets one of three outcomes: **agree** when every pair agrees, **disagree** when none does, **mixed** when some do and some do not. All three counts are published per facility and carrier; the disagreement rate is disagree over agree plus disagree, with mixed shown beside it rather than folded into either, so an agreeing pair can never hide a conflicting one. Rows that cannot be matched on every element, including a hospital row whose interval overlaps no insurer row, are counted as unmatched and reported as such, not as disagreements. The disagreement rate per facility and carrier is published over matched rows only, beside the unmatched count, with the normalisation rules on the methods page.

## Open

- Which network names correspond to the plans local people actually hold; plan-name mapping is the hardest part of this data.
- Whether Medicare Advantage and Medicaid lines are useful here.
- Whether to touch the out-of-network allowed-amount files at all, which have been widely empty.
- Measure Cigna's table-of-contents and one network file before sizing the box: compressed and expanded size, parse throughput per core, peak memory per parser.
- Evaluate `tic-mrf-parser` and `cms-mrf-extractor` on that file.
- Confirm the regulatory cite and the Medicaid managed-care question.
