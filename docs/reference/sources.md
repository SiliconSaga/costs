# Sources and projects

Where else to look, and what already exists so this project does not rebuild it. Each entry says who runs it, what it covers, what it costs, and what it misses for an Essex County reader. Surveyed 2026-10-23; several publishers' sites refuse automated fetches, so a few details lean on secondary sources and are marked.

## The gap, in one paragraph

Nobody publishes Essex County facility-level prices from the current hospital files in a free, reusable form. The community database stopped in mid-2023 and covers four of the seven Essex hospitals. The commercial tools have current files behind paid or account-gated interfaces with no right to redistribute. The employer-coalition and academic sources give percent-of-Medicare ratios from 2022 claims, not dollar amounts by payer. No free publisher uses the insurer files at all. Freestanding imaging and surgery centers are outside every hospital-file tool, because the hospital rule does not reach them. And nothing New Jersey runs exists or is imminent: the hospital association's charge-comparison site is dead, the state's affordability office publishes aggregate spending trends only, and the claims-database bill is in committee with self-insured employer plans excluded.

## Consumer-facing price lookups

### Turquoise Health

Commercial, venture-backed. Patient search at [turquoise.health/patients](https://turquoise.health/patients), no signup, by ZIP code, with an optional insurance entry; labelled beta with a limited set of services and plans, and excludes Medicare and Medicaid enrollees. Uses both hospital and insurer files plus claims, and marks an insured price "fully verified" when the two files agree. Cash prices from hospital files only. The underlying data is an enterprise subscription; Turquoise asserts copyright over its compilation ([terms](https://turquoise.health/legal/terms-of-service)). A free researcher dataset of 14 shoppable services was announced in 2021; whether it is still distributed is unverified. Also publishes a free quarterly hospital transparency scorecard. For Essex: national, ZIP-based, no redistribution.

### Trilliant Health — Oria

Commercial. An AI chatbot over hospital price files, launched October 16, 2025, in a free "Lite" tier (account required) and a premium tier that adds Trilliant's claims dataset ([trillianthealth.com/oria](https://www.trillianthealth.com/oria)). Hospital files only, not insurer files; Trilliant does not verify the hospital-reported data. Launch coverage was select states; whether New Jersey hospitals are loaded is unverified. Downloads are premium-only; no API.

### FAIR Health Consumer

Independent nonprofit formed in 2009 under a New York Attorney General settlement. Estimates from commercial and Medicare **claims**, not price files, by three-digit ZIP area, not by facility ([fairhealthconsumer.org](https://www.fairhealthconsumer.org/)). New Jersey areas are covered at that level; provider-specific figures exist only for New York State. Free for consumers, rate-limited; commercial use is licensed. Useful as a "what people in this area typically pay" context column, not as a facility price.

### CMS Procedure Price Lookup

Government. National-average Medicare payment and beneficiary copay for about 3,900 procedures at hospital outpatient departments versus ambulatory surgical centers ([medicare.gov/procedure-price-lookup](https://www.medicare.gov/procedure-price-lookup/)). Excludes physician fees and emergency procedures; 2025 payment year. No commercial rates, no facility. An [API](https://developer.cms.gov/ppl-api/) exists but requires an AMA CPT licence and a CMS key. This is the Medicare benchmark's consumer face and the source for Layer A's hospital-versus-freestanding instinct.

### NJ Hospital Price Compare

New Jersey Hospital Association. Launched 2007 to show hospital **charges** by diagnosis, which the association itself called a sticker price. Hospital websites still cite it.

!!! warning "Unverified"
    Appears unavailable as of October 23, 2026: the site refuses connections and the association's page describing it returns not found. No retirement notice found, so whether it is retired or merely down is unconfirmed.

### ClearHealthCosts

Small journalism company; crowdsourced and surveyed **cash prices**, with a submit-a-price form ([clearhealthcosts.com](https://clearhealthcosts.com/)). Active (posts dated October 2026). Regional partnerships with newsrooms in New York City, Philadelphia, and elsewhere; no New Jersey partner, so Essex is reachable only by radius search and is likely thin. Its API link returns 404. The closest existing model to Layer D, and the first place to ask about lessons from a decade of crowdsourced bills.

## Analyst and data-facing

### Sage Transparency

Employers' Forum of Indiana, a nonprofit employer coalition, through its Employer Price Transparency Project ([employerptp.org/sage-transparency](https://employerptp.org/sage-transparency/)). Free dashboard of hospital and ambulatory-surgery-center prices as percent of Medicare from RAND's claims study (round 5.1), plus NASHP cost-report breakevens, CMS quality ratings, and legislator-district data. Not from price files. Data refreshed September 15, 2026. New Jersey hospitals are included; per-facility Essex presence is unverified in the interface. Bulk data is sold; no API; provided as-is.

### Patient Rights Advocate

Advocacy nonprofit. A [hospital price files finder](https://hospitalpricingfiles.org/) with links to about 6,000 hospitals' files (no New Jersey state portal, though one exists for six other states), and semi-annual compliance reports. The [eighth report](https://www.patientrightsadvocate.org/eighth-annual-hospital-price-transparency-compliance-report-september-2026), September 10, 2026, judged 49.4% of 2,000 hospitals compliant and found only 18% post dollar prices for at least half their items; 28 hospitals have been fined by CMS to date. No New Jersey breakdown in the report page. Latest New Jersey figures found: 41% fully compliant ([The Record, February 2024](https://www.yahoo.com/news/battle-over-nj-hospital-prices-093047354.html)) and a 2025-cited review finding 3 of 32 compliant ([NJ Monitor](https://newjerseymonitor.com/2025/03/06/legislators-advocates-renew-fight-to-cap-soaring-hospital-costs/)). Free; no API.

### DoltHub

Commercial database vendor hosting community-collected data from paid bounties that ended in September 2023. The recommended database is [`dolthub/transparency-in-pricing`](https://www.dolthub.com/repositories/dolthub/transparency-in-pricing), about 1,000 hospitals, last data insert August 14, 2023, last commit November 2023; DoltHub's [January 2025 post](https://www.dolthub.com/blog/2025-01-28-state-of-hospital-price-transparency-data/) says collection stopped and offers free hosting to anyone who resumes it. **Stale**, and all pre-July-2024 schema. New Jersey: 24 hospitals with completed rates. Essex: Newark Beth Israel, Clara Maass, and Cooperman Barnabas (all April 2023) and East Orange General (January 2022); University Hospital, Saint Michael's, and Mountainside absent. Also hosts a file-URL list (`standard-charge-files`), a small 2022 insurer-file snapshot, and the `mrfutils` parser (see below). Free, with a SQL API. Licence not stated.

### Colorado — HCPF and CIVHC

The state precedent. Colorado's Medicaid agency, with CIVHC (the nonprofit that runs the state's claims database), launched a free [hospital price transparency tool](https://hcpf.colorado.gov/hospital-price-transparency) in September 2024: 82 hospitals' files downloaded in a one-week window, about 3 million negotiated prices, gross, cash, Medicare, and commercial rates, methodology v2.0 of March 2025, refreshed semi-annually. CIVHC separately sells the standardized file set (84 hospitals, 52 million rows) for $10,000 with discounts. Backed by state law SB23-252. CIVHC's postings reports show most Colorado hospitals refresh files once a year and quality ratings swinging between rounds. This is the closest thing to what Layer A does, done by a state with a statute behind it.

### RAND hospital price studies

RAND, funded by the Employers' Forum of Indiana. Employer and insurer claims re-priced to Medicare, with hospitals named since round 3. Round 5.1 (December 2024; 2020–2022 data) adds surgery centers and physician-administered drugs. Round 6 is enrolling contributors, not published. New Jersey facility-level figures are public through Sage Transparency and through the Brown memo below. RAND's own pages refused automated fetches; its hospital-level spreadsheets' current availability is unverified.

### NASHP Hospital Cost Tool

National Academy for State Health Policy, nonprofit. Costs, revenue, payer mix, margins, and the "commercial breakeven" from Medicare Cost Reports for about 4,500 hospitals, cost-report years 2011–2024 ([nashp.org/hospital-cost-tool](https://nashp.org/hospital-cost-tool/), updated February 2026). Cost side, not prices. Free web tool and Excel download; NASHP copyright, redistribution by permission. A context column, with attribution.

### Brown University CAHPR memo

Confirmed: "Estimated Impact of Reference-Based Pricing for New Jersey State Employees," Center for Advancing Health Policy through Research, Brown School of Public Health, dated September 4, 2025 ([PDF](https://cahpr.sph.brown.edu/sites/default/files/documents/Public%20Comments/09.04.2025%20CAHPR%20RBP%20Memo%20for%20New%20Jersey.pdf)). Method: RAND round 5.1 relative prices for 61 New Jersey hospitals plus the NASHP tool, modelling a 200%-of-Medicare cap for the State Health Benefits Program. The relative prices are what the state employee plan paid, about 11% of New Jersey commercial lives, not all-commercial. Its Essex County rows are on the [Facilities](facilities.md) page.

## Parsers and validators

Surveyed 2026-10-23 against GitHub, npm, and PyPI. The short version: the official validators are maintained and authoritative; no mature parsing library exists on either side; the community databases are frozen in 2023; and both schemas are mid-transition, so anything built now is version-stamped and expects a rewrite within a year.

### Hospital side

- **CMS data dictionary and schema** — [`CMSgov/hospital-price-transparency`](https://github.com/CMSgov/hospital-price-transparency). Template **v3.0.0**, effective January 1, 2026, enforced from April 1, 2026: adds the attestation and attester name, Type 2 NPIs, and the median, 10th and 90th percentile, and count of allowed amounts; removes the affirmation statement and `estimated_allowed_amount`. Tall and wide CSV templates and the JSON schema are in the repo. Active (pushed October 2026); no licence file, though US government work is public domain in practice.
- **CMS validator** — [`@cmsgov/hpt-validator`](https://github.com/CMSgov/hpt-validator) 2.6.0 (TypeScript, CC0, August 2026) and its CLI [`@cmsgov/hpt-validator-cli`](https://github.com/CMSgov/hpt-validator-cli) 1.10.8, Node 20+. Supports templates v2.1, v2.2, v3.0; streams CSV and JSON including gzipped; `-o json` gives machine-readable errors and alerts (including "nine 9s used for estimated amount"). The unscoped npm package `hpt-validator-cli` is stale; use the scoped one. The pipeline's first gate on every file.
- **CMS web tool** — [cmsgov.github.io/hpt-tool](https://cmsgov.github.io/hpt-tool/): in-browser validator, the `cms-hpt.txt` generator, and a file-naming wizard.
- **`hpt-parser-rs`** — [digitalashes/hpt-parser-rs](https://github.com/digitalashes/hpt-parser-rs), Rust, CC0. Streaming tall and wide CSV and JSON (v2.0 to v3.0) to Parquet, one flat row per item, payer, and plan; claims a 2.5 GB wide CSV in under five seconds at 62 MB resident. Days old, one author, one star: the only purpose-built converter, and unproven.
- **`mrf-etl`** — [PyPI](https://pypi.org/project/mrf-etl/) 0.1.1 (March 2026), Python, Apache-2.0, streaming CSV parser for mixed layouts. No public repository; schema version support unstated.
- **Discovery and archives** — [nathansutton/hospital-price-transparency](https://github.com/nathansutton/hospital-price-transparency) scrapes 5,000 hospitals daily with per-state URL lists (`dim/urls/NJ.json`; cash and gross only). [lkowalcz/hospital-price-history](https://github.com/lkowalcz/hospital-price-history) archives 107 hospitals' files daily, discovered via `cms-hpt.txt`, v3-aware, CC0. [price-files-bot](https://github.com/betsyflickinger-max/price-files-bot) checks link health for 5,255 hospitals daily with ETag and SHA-256 manifests, no licence. The 2023 URL lists from TPAFS and DoltHub are stale.
- **DoltHub's lessons post** — [How not to do hospital price transparency](https://www.dolthub.com/blog/2023-06-28-how-not-to-hpt/): use an analytical engine at hundreds of millions of rows, keep raw and normalised code columns side by side, one big rates table plus a hospital dimension, a payer-category field and a payer-name synonym map, and never compare outpatient line items naïvely. Still the best practical guide.

### Insurer side

- **CMS schema** — [`CMSgov/price-transparency-guide`](https://github.com/CMSgov/price-transparency-guide), version 2.2.1, active. Schemas for in-network rates, allowed amounts, table of contents, and provider references. CMS hosts no files and no index of payer URLs. Schema **3.0** arrives with the 2026 final rule; see [Layer B](../layers/b-insurer-files.md).
- **CMS validator** — [`price-transparency-guide-validator`](https://github.com/CMSgov/price-transparency-guide-validator), TypeScript over C++ RapidJSON, Apache-2.0, about 25 MB/s, maintained slowly.
- **`mrfutils`** — lives inside [dolthub/data-analysis](https://github.com/dolthub/data-analysis/tree/main/transparency-in-coverage/python/mrfutils), Python with `ijson`, streams in-network JSON to CSV with code and NPI filters. **Abandoned** since July 2023, schema 1.0/2.0 era. Historical interest only, as is DoltHub's small 2022 insurer snapshot.
- **`cms-mrf-extractor`** — [PyPI](https://pypi.org/project/cms-mrf-extractor/) 0.1.2 (August 2026), Python, MIT. Single pass with a pyarrow fast path and `ijson` fallback, zstd Parquet output, fetches remote provider-reference files. Three commits, zero stars.
- **`tic-mrf-parser`** — [AccessMRF/tic-mrf-parser](https://github.com/AccessMRF/tic-mrf-parser), Go, MIT, August 2026. Pipes curl through gunzip into a fan-out Parquet writer with bounded memory (a 3.4 GB file in 15 seconds at 285 MB resident). Hard-coded schema. The strongest candidate to evaluate first.
- **`mrfparse`** — [danielchalef/mrfparse](https://github.com/danielchalef/mrfparse), Go, Apache-2.0: 80 GB of Anthem in under five minutes. **Archived** December 2024, x86 AVX2 only.
- No Rust parser found; nothing targets schema 3.0 yet.

### DuckDB

`read_json` must materialise a whole top-level object, bounded by `maximum_object_size` (default 16 MiB), so pointing it at a multi-gigabyte single-object insurer file does not work and even sub-gigabyte files are memory-hungry. The working pattern is a streaming pre-splitter (`ijson`, or `jq --stream`; plain `jq` loads the whole document) emitting gzipped newline-delimited JSON per top-level array, then `read_ndjson` in parallel and `UNNEST` to Parquet. Hospital JSON files have the same shape but are tens to hundreds of megabytes, where raising the limit usually suffices. Hospital CSV reads directly with `skip=2` for the metadata rows; wide files need their `standard_charge|payer|plan|field` headers parsed and unpivoted. See [Pipeline and data](../architecture/pipeline.md).

### Supporting data

- **NPPES** — monthly full replacement plus weekly incrementals from [CMS data dissemination](https://www.cms.gov/medicare/regulations-guidance/administrative-simplification/data-dissemination); the zip is about 1 GB, the CSV many gigabytes. Practice-location address fields include city, state, and postal code but **no county**, so Essex is derived from ZIP through the [HUD USPS ZIP-county crosswalk](https://www.huduser.gov/portal/datasets/usps_crosswalk.html) (Essex County FIPS 34013). The download page was down for maintenance on the survey date.
- **Medicare tables** — OPPS [Addendum B](https://www.cms.gov/Medicare/Medicare-Fee-For-Service-Payment/HospitalOutpatientPPS/Addendum-A-and-Addendum-B-Updates) quarterly (HCPCS to APC, weight, national rate); [IPPS FY2026](https://www.cms.gov/medicare/payment/prospective-payment-systems/acute-inpatient-pps/fy-2026-ipps-final-rule-home-page) Table 5 (DRG weights) and Table 3 (wage index by CBSA); [PFS RVU files](https://www.cms.gov/medicare/payment/fee-schedules/physician/pfs-relative-value-files) with GPCIs and the locality-county map. Essex County sits in **CBSA 35084, Newark NJ metropolitan division** (with Hunterdon, Morris, Sussex, Union), and in Physician Fee Schedule **locality 12402/01, Northern NJ**. PFS files embed AMA-licensed CPT descriptors; the rates are free, the descriptors are not redistributable.

!!! warning "Unverified"
    That FY2026 IPPS adopted the 2023 OMB delineations unchanged (check the rule's county-to-CBSA crosswalk); the Northern NJ locality's county list; the 2026 GPCI values; whether the CMS web validator offers v3.0.

## Redaction and detection tools

Surveyed on the [Redaction](../trust/redaction.md) page.

## New Jersey government

- **Office of Health Care Affordability and Transparency** (Department of Health; [ohcat](https://www.nj.gov/health/about/organization/offices/ohcat)) runs the HART cost-growth benchmark with Rutgers and Mathematica. Reports: pre-benchmark 2018–19, transition year 2021–22, performance year 1 2022–23 (per-person spending up 6.1% to $11,319 in 2023), plus cost-driver reports on commercial spending, hospital labor, and the 2025 landscape, all January 2026. No price data, no facility figures, no dashboard, no download.
- **Department of Banking and Insurance** [HART page](https://www.nj.gov/dobi/division_insurance/HART/): the carrier submission side and a 2024 affordability standards report. No price data.
- **Department of Health Health Care Financing** [financial transparency reports](https://www.nj.gov/health/hcf/financial-reports/): Excel, calendar years 2021–2024, all 70 acute-care hospitals, margins, cash, payer mix. Financial, not price.
- **Department of Health ambulatory care facility search**: the only list of licensed freestanding centers; no bulk export. See [Facilities](facilities.md).
- **All-payer claims database: none.** HART collects aggregate submissions, not claims. Earlier bills (2014, 2016, 2018) died in committee.

### Pending bills, 2026–27 session

| Bill | What | Status found |
|---|---|---|
| [A5540](https://pub.njleg.gov/Bills/2026/A6000/5540_I1.HTM) (Freiman) / [S4501](https://pub.njleg.gov/Bills/2026/S5000/4501_I1.HTM) (Mukherji) | Requires DOBI to establish an all-payer claims database with a public dashboard; carriers, providers, PBMs, and TPAs report; **self-insured ERISA plans excluded** (voluntary only); penalties up to $1,000 a day; no appropriation | Introduced June 22 (Senate) and September 24, 2026 (Assembly); referred to committee; no further action |
| S3012 / A1729 | Health Care Cost Containment and Price Transparency Commission; codifies the affordability office; state penalties for 45 CFR 180 non-compliance; bars debt collection by non-compliant hospitals | Introduced January 13, 2026; Senate Health committee; no hearing found. Predecessors in 2024 and 2024–25 died |
| S3409 | Facility fees at hospital-owned outpatient offices | Not researched this round |

!!! warning "Unverified"
    Bill statuses are from secondary sources where the legislature's trackers refused automated fetches. S3409 is unresearched. Hilltop Institute's price-transparency state profiles (UMBC, 22 states as of November 2024) may include New Jersey.

## Open

- Research S3409 and confirm every bill status against the legislature's own site.
- Ask ClearHealthCosts what a decade of crowdsourced bills taught them, before Layer D's instructions are written.
- Check Sage Transparency's interface for each Essex facility and record what it shows.
