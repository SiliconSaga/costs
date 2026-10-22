# Cost of Care: a public price record for Essex County

**Status:** Draft v0, 2026-10-22. First-ideas document from the session that produced the schools ledger's [How Services Could Work](https://schools.frontstate.org/ledger/services) page. To be fleshed out with research in a later session. Items marked *verify* have not been checked against primary sources and must not reach the site until they have.

**Component:** `prices` (this repository). "Cost of Care" is the working title of the first section. The component name stays generic so the site can grow into other local prices later (insurance premiums, utilities, property tax, child care) without a rename. No Norse name: the civic components are expected to move to their own GitHub organization.

## 1. Why

What a hospital gets paid for a given scan or delivery, by each insurer, has been public by federal rule since January 1, 2021. Since January 1, 2026 the files must carry actual dollar figures and an attestation signed by a senior officer, with enforcement from April 1, 2026. Insurers have published their negotiated rates for every in-network provider since July 2022. Nobody has turned either into something an Essex County parent can read. The national tools (Turquoise Health, Trilliant Oria, FAIR Health Consumer, Sage Transparency) cover hospitals in aggregate, none shows a freestanding imaging center next to the hospital department two miles away, and New Jersey has no all-payer claims database and no state price tool.

The schools ledger established why this matters locally. The Brown University CAHPR memo of September 4, 2025 puts Essex hospitals at 175% to 349% of Medicare for outpatient care. The district's health plan premium is the contested number in the 2026 contract fight. The services page proposes a yearly local price table as the next piece of record-keeping. This site is that table, and the pipeline behind it.

Three audiences, in order:

1. Residents choosing where to get care they already know they need: an MRI, a colonoscopy, a birth, a child's broken arm.
2. The people negotiating health benefits locally: the district and its unions, the township, local employers, and the eventual health membership sketched on the services page.
3. Neighboring towns and counties that want the same thing, which is why the pipeline treats Essex as configuration, not as a hard-coded scope.

Success looks like: for each of about one hundred common procedures, a page showing what each Essex hospital and, later, each freestanding center is paid by each insurer, the cash price, and Medicare's rate for the same thing, with the file date and a link to the row in the source file; a yearly county price table the schools ledger can cite; and every figure reproducible from the raw public files by anyone who clones the repository.

What this is not: a plan-shopping tool, a provider rating, or advice. It is a record of prices and where they came from.

## 2. Principles, carried from the ledger

- **Record, not opinion.** Each figure is labelled by provenance: **Record** (from a posted file, linked), **Statement** (a provider's or insurer's own explanation, quoted), **Press** (reporting), and, new here, **Submitted** (a resident's bill, unverified; see §7).
- **Reproducible.** Raw files, then scripts, then tables. The scripts live in the repository; the raw files' URLs, hashes, and dates are recorded. Nothing is hand-typed into a table.
- **Dated.** Every figure carries the date of the file it came from. Stale files are marked stale, not silently kept.
- **Plain words.** The audience is frustrated parents, not health economists. One sentence of explanation per table, and a methods page for the rest.
- **County-agnostic.** Essex is a value in a configuration file.
- **Privacy by default.** No accounts, no tracking, no cookies. Submitted documents never enter Git and are never published in any form; see §7 and §8.
- **Not selling anything.** Links to the services page where relevant. No Front State or Demicracy pitch; those have their own sites.

## 3. The four layers and their order

| Layer | What | Source | When |
|---|---|---|---|
| A | Hospital negotiated and cash prices | Each hospital's CMS-template machine-readable file (45 CFR Part 180) | First |
| C | Explainer and curated links | Existing publishers, Medicare lookups, the state facility search | With A |
| D | Submitted bills: what people actually paid | Residents' explanations of benefits and itemized bills, redacted | A trickle from near the start; grows with the audience |
| B | Insurer in-network rates for every Essex provider, including freestanding imaging and surgery centers | Each carrier's Transparency in Coverage in-network files (45 CFR 147.210 to 147.212, *verify cite*) | When the homelab box is ready; target before the end of 2026 |

The order. A is small, public, and answers the Brown table's question with actual dollars. C costs almost nothing and frames A. D needs an audience and a privacy design, but a trickle early builds the habit, and contributing is itself an outlet for people who are angry and have nowhere to put it. B is the layer that makes the site unique, the hospital department beside the freestanding center for the same scan and the same insurer, and it is the heavy one. Its files shrink and gain a provider taxonomy under the October 2026 final rule, which lands for files around mid-2027, but the current files are usable now given disk and bandwidth, so B is gated on hardware, not on the rule.

## 4. Layer A: hospital files

**Sources.** Seven acute-care hospitals in Essex County, with owners as of 2025: Cooperman Barnabas, Clara Maass, and Newark Beth Israel (RWJBarnabas Health); Mountainside (Hackensack Meridian Health and Ardent joint venture); University Hospital (state public hospital); Saint Michael's (*verify owner*); CareWell Health, formerly East Orange General (transfer to ECR OPCO approved July 25, 2025). Known file locations: the RWJBarnabas files are served from mrfs.hyvehealthcare.com and University Hospital posts a CSV on uhnj.org; the rest are to be located. Each hospital must link the file from its site and publish a `cms-hpt.txt` at its domain root (*verify*). Hospital outpatient departments and any imaging site operating under a hospital license are in these files already.

**Format.** The CMS template, mandatory since July 1, 2024, in CSV (tall or wide) or JSON. Columns include description, code and code type (CPT, HCPCS, MS-DRG, APC, NDC, local), setting, gross charge, discounted cash price, payer name, plan name, negotiated dollar amount or percentage or algorithm, estimated allowed amount, methodology, and since 2026 the median and 10th and 90th percentile of actual allowed amounts where the price is a percentage or formula. The CMS validator on GitHub (CMSgov/hospital-price-transparency) checks a file against the schema. Run it on every file and publish the result, which is itself a compliance record.

**Code list.** The 70 CMS-specified shoppable services plus about 30 local picks chosen for the audience: vaginal and cesarean delivery and newborn care; pediatric fracture care; emergency visit levels 1 to 5; MRI of brain, knee, and spine with and without contrast; CT of head and abdomen; ultrasound; plain X-ray; colonoscopy and upper endoscopy; tonsillectomy and ear tubes; appendectomy; hernia repair; cataract; knee and hip replacement; common labs (complete blood count, metabolic panel, lipid panel, A1c); mammogram; sleep study; a physical therapy visit; mental-health visit codes. The final list is a research task, and each code gets a one-line plain-English name.

**Pipeline.** Python 3 with DuckDB reading the CSV or JSON directly into Parquet, one Parquet per hospital per file date. `codes.yaml` and `facilities.yaml` hold the configuration. Derived outputs are small JSON files per procedure and per facility for the site. A monthly GitHub Action downloads, hashes, validates, and rebuilds, and a diff report shows what changed between file versions, because prices that move are a story. Raw files are kept on the homelab and attached as release assets, not committed.

**Benchmark.** Every price is shown next to Medicare's rate for the same code: the OPPS ambulatory payment classification amount, wage-index adjusted for the Newark locality (*verify index value and year*), for hospital outpatient; the IPPS base rate times the DRG weight for inpatient, approximate; the Physician Fee Schedule for professional components. Label the benchmark approximate, and show percent-of-Medicare beside the Brown and NASHP ratios where both exist. Sage Transparency's RAND-based ratios for the same facilities are a supplement or cross-check.

**Presentation.** One page per procedure: rows are facility by payer; columns are negotiated price, cash price, Medicare, percent of Medicare, file date, and a link to the source row. One page per facility: its code list, validator status, file date, owner. A county overview, which is the yearly table: median percent-of-Medicare by facility across the code list. A methods page: what the files are and what they omit (physician fees billed separately, facility fees at off-campus sites, anesthesia, pathology, and the freestanding centers until layer B). Payer names appear as the files give them, grouped by carrier, with a "your plan is probably one of these" helper for the state and school employee plans and the common local employer plans.

**What A cannot show.** Freestanding imaging and surgery centers, physician offices, independent labs, urgent care. The hospital rule binds facilities licensed as hospitals and does not reach them (*verify against 45 CFR 180.20*). For these, layer A publishes the facility list, scraped with its search date from the New Jersey Department of Health's ambulatory care facility search (county and type filters for ambulatory surgery, CT, MRI, PET; no bulk export; *verify terms of use*), with each center's posted cash price where it posts one and Medicare's Procedure Price Lookup figures for the same scan in a hospital department versus a freestanding setting. That gives a parent the correct instinct, which is that the freestanding center is usually far cheaper, before the negotiated prices arrive with B.

**Open for A.** Data licence for the derived tables (proposal: CC0 for data, CC BY for text). Whether hospital website terms of service matter when the rule requires the file to be accessible without barriers (*verify 45 CFR 180.50(d)*). How to treat "N/A" and placeholder values. Normalising payer names across seven files.

## 5. Layer B: insurer files

**Sources.** Each carrier publishes a monthly table-of-contents file listing in-network rate files per plan or network. The in-network files are JSON per the CMS schema (GitHub CMSgov/price-transparency-guide; schema v2.0 assessed from February 2, 2026). Carriers that matter in Essex: Horizon Blue Cross Blue Shield of New Jersey (which administers the state and school employee plans), Aetna (the district's fund, SHIF), UnitedHealthcare and Oxford, Cigna, AmeriHealth New Jersey, and the Medicaid managed-care plans if their files are in scope (*verify*). The large carriers' files run to hundreds of gigabytes and up to about a terabyte each. The October 2026 final rule (CMS-9882-F) consolidates to one file per network, removes "ghost rates" for providers who never bill a code, adds utilization, taxonomy, and text files, and phases in over roughly five to eleven months after publication, so mid-2027.

**Approach.** A spike first: one carrier, one network, one month, on the homelab. Build an Essex NPI list from the NPPES bulk download filtered by practice-location county and ZIP, joined to the Department of Health facility list and to the Type 2 NPIs in the hospital files. Stream the in-network file and keep only rows where the provider NPI is on the Essex list and the code is on the code list. Streaming JSON (ijson in Python, DuckDB's JSON reader if the structure allows, or a small Rust or Go tool) avoids loading files into memory. Existing open-source work to evaluate before writing anything: DoltHub's Transparency in Coverage tooling and `mrfutils` (*verify names and maintenance state*), and any open components from Turquoise or Serif. Output takes the same shape as A with a facility-type column, so a procedure page shows hospital outpatient department, freestanding center, and physician office for the same code and insurer side by side.

**Storage and compute.** Raw monthly files per carrier of one to two terabytes in the current format. Keep the last two months, archive the extracted Parquet, which is small. Five-gigabit fiber makes the download a matter of hours; parsing is the bottleneck, so the box wants fast NVMe scratch and cores more than it wants RAM. Results flow to the same JSON the site reads. GitHub Actions never touches a raw insurer file.

**Cross-check.** The same negotiated rate appears in the hospital's file and in the insurer's file. Where they disagree, that is a finding. Publish the disagreement rate per facility and carrier.

**Open for B.** Which network names correspond to the plans local people actually hold; plan-name mapping is the hardest part of this data. Whether Medicare Advantage and Medicaid lines are useful here. Whether to touch the out-of-network allowed-amount files at all, which have been widely empty.

## 6. Layer C: explainer and curated links

One "where else to look" page and a handful of plain-language explainers, each written once and dated: how to read an explanation of benefits; what a facility fee is and why the same MRI costs several times more inside a hospital; what "in network" and "allowed amount" mean; how to ask for the cash price and when it beats using insurance; where to find the Medicare price; what the 2021 and 2026 rules require and how to report a hospital that is not complying (the CMS complaint form); where New Jersey's own efforts stand (the A5540 claims-database bill, S3012 on enforcement and debt collection, S3409 on facility fees).

Links: Turquoise Health, Trilliant Oria, FAIR Health Consumer, Sage Transparency, Patient Rights Advocate's hospital file finder, the CMS Procedure Price Lookup, the Department of Health facility search, the Brown CAHPR memo, and the schools ledger's services page. One page plus the methods page. The links are not the product.

## 7. Layer D: submitted bills

**Why.** The posted files say what an insurer agreed to pay. A bill says what a family was actually charged: the facility fee, the out-of-network anesthesiologist, the lab in another state, the balance after the deductible. Submitted bills are the check on layers A and B, and they are the one place a resident can do something with their anger that leaves a record. Both reasons count.

**What people submit.** An explanation of benefits from the insurer, an itemized bill from the provider, a pharmacy receipt, or a good-faith estimate, as a photo or PDF. Redacted by the submitter first and by us second.

**What is extracted and published.** Provider (facility name and NPI where known), facility type, procedure codes and plain names, month and year of service (never the day), insurer and plan type (HMO, PPO, HDHP; never the plan or group ID), network status, billed charge, allowed amount, plan paid, patient responsibility, and whether the bill was later corrected or reduced. Optionally a short free-text note, moderated before publication.

**What is never published.** Names, member and subscriber IDs, claim numbers, dates of birth, addresses, account numbers, diagnosis codes (dropped by default; in a small town they identify people), the names of clinicians or staff, and the original image or PDF in any form. Originals are deleted after extraction on a fixed schedule (proposal: 30 days), and the extraction record notes the deletion.

**Small-town re-identification.** Month precision, no diagnoses, and a suppression rule: a row that is the only one for a rare procedure at a facility in a month is published only with the date widened to a quarter or a year. The exact rule is a research item. The principle is that the record protects the submitter before it protects precision.

**Intake options, to decide in research.** (1) A form that uploads to a private store on the homelab, so no third party holds the bills; this needs a small, boring, well-firewalled service. (2) A dedicated email inbox, which is simplest but makes the mail provider a processor of health documents, which argues against a consumer mailbox. (3) In person at meetings, with a scanner and a paper consent slip. (4) Never GitHub issues or any public channel. Recommended start: option 3, then option 1 once the box exists. Until then the site carries the instructions and a "not yet open" note.

**Redaction-first instructions.** A page of instructions with free and open-source tools by device, each evaluated in the research session before it is recommended. The one rule stated first: redaction must remove the text, not cover it, so export a flattened image or PDF and check it by searching for your own name. Candidates to evaluate: Dangerzone (Freedom of the Press Foundation) to flatten a document to pixels and strip metadata; Stirling-PDF (self-hostable, has auto-redaction; we could host an instance); LibreOffice's Redact tool in Writer and Draw, which exports a pixelated or blackened PDF; GIMP, or the phone's native markup after a screenshot, then crop; mat2 or ExifTool for metadata. The phone path is the one most people will use: screenshot, crop, draw solid boxes, export as an image. Warn against annotation-only tools whose highlights leave the text underneath.

**Our redaction pass.** Every submission, redacted or not, goes through OCR (Tesseract), pattern detection for member IDs, claim numbers, dates of birth, addresses, phone numbers, and account numbers, named-entity detection (Microsoft Presidio or spaCy as candidates), and the GDD PII guard, then a human look before any extracted record is published. The statement to submitters: we redact as able and we never publish the document itself, but anything you upload to the internet is on the internet, so redact first.

**Agency, kept small.** A running count of submissions and facilities covered. A quarterly "what the bills taught us" note. Contributors acknowledged only in aggregate ("17 West Orange households"), never by name. A way for a submitter to withdraw a record, using a token issued at submission, with no account.

## 8. Legal and privacy posture

Today this is one resident's unincorporated project. It may become a nonprofit, and the design should not need rewriting for that step. The aim is to be conscientious without being paralyzed: state what we do, do it, publish, and correct.

- **HIPAA.** It binds providers, insurers, and their business associates. A resident sharing their own bill is not covered, and neither, on the common reading, is a site that receives it from them (*verify with counsel before launch of D*). The documents are treated as if HIPAA applied anyway: minimum necessary, a deletion schedule, access limited to the keeper, and a two-person rule when volunteers join.
- **New Jersey law.** The Identity Theft Prevention Act (N.J.S.A. 56:8-161 and following, *verify*) defines personal information (Social Security, driver's licence, and account numbers with access codes, later user names and passwords) and sets breach-notification duties. The design keeps none of these, which is the cheapest compliance. The Consumer Fraud Act and the state's newer privacy statutes are a research item.
- **Public files.** Prices are facts and facts are not copyrightable. The hospital rule requires the files to be accessible free of charge, without a login or personal information, and in machine-readable form (*verify 45 CFR 180.50(d)*); the insurer rule has an equivalent (*verify*). Hospital website terms of service may purport to restrict automated access. Note them, cite the rule, proceed.
- **Defamation and accuracy.** Publish records with provenance and dates. Label submitted data as submitted and unverified. Keep a corrections page and a documented process for a provider or insurer to dispute a figure: we check the source file and publish the exchange as a Statement. Free-text notes are moderated and never name a clinician.
- **Terms and disclaimers.** Not medical, legal, financial, or insurance advice. Prices change; verify with the provider. We are not a regulated entity, insurer, broker, or HIPAA covered entity. Submissions are voluntary, the consent text is shown at submission, and withdrawal is supported.
- **Privacy of the site itself.** No third-party analytics, or server-side counts at most. No cookies, no embeds. The repository runs the GDD PII guard, with a `.gdd-pii-allow` listing only addresses meant to be public.
- **Nonprofit later.** A 501(c)(3) educational purpose fits publishing public data and explaining it. The asset-lock language from the services page applies to any data or funds. A board of residents. Research item: whether the schools ledger and this site should share an entity, and what the civic GitHub organization is called.

## 9. Hosting and repositories

A static site on GitHub Pages from this repository, built with Jekyll; the theme is undecided (just-the-docs for parity with the ledger, or something plainer). Pipeline code under `pipeline/`, configuration under `config/`, derived JSON committed or attached as release assets depending on size, raw files on the homelab only. DuckDB-WASM in the browser is an option later for ad-hoc queries over the Parquet. Submission intake and storage live on the homelab and never on GitHub. The domain is undecided: a frontstate.org subdomain is available in principle, but the civic components are expected to move to their own organization, and the domain should follow that decision. The workspace's local catalog currently points this repository at Cervator/prices; move it when the organization exists.

## 10. Public-records asks: cheap to prepare, low expectations

Requests an agent can draft for the keeper to file when convenient, under New Jersey's Open Public Records Act: to the school district, as a member of its insurance fund, for the Aetna negotiated-rate file or the fund's paid amounts by facility in aggregate; to the township for its employee plan's equivalents; to the county; to the State Health Benefits Program and the School Employees' Health Benefits Program for their carriers' rate files or Essex paid-amount aggregates; to the Department of Health for a bulk export of the licensed ambulatory care facility list. Expect "proprietary" and "not a government record" responses. The exchange itself becomes a record on the site. Nothing is filed until the district's current requests are answered and the keeper says go.

## 11. Research list for the next session

- Confirm the scope of the hospital rule (45 CFR 180.20) and the access requirements (180.50(d)); confirm the Transparency in Coverage cites and access language.
- Locate all seven hospitals' machine-readable files and `cms-hpt.txt` entries; record URLs, formats, file dates; run the CMS validator on each.
- Confirm ownership of Saint Michael's and the current licence holder of CareWell.
- Settle the Medicare benchmark method and the Newark locality wage index; check what Sage Transparency and NASHP publish for the same facilities.
- Fix the code list, with plain names.
- Evaluate the existing open-source Transparency in Coverage parsers before writing one; measure one carrier's table-of-contents and one network file.
- Check the Department of Health facility search's terms and whether a records request yields a bulk list.
- Evaluate the redaction tools in §7 on a real explanation of benefits and write the instructions, by device.
- Evaluate Presidio and spaCy on redacted and unredacted samples; define the suppression rule.
- Confirm the HIPAA reading and the New Jersey statutes in §8; draft the terms, consent text, and disclaimers.
- Survey similar projects (ClearHealthCosts' crowdsourced pricing, DoltHub's hospital-price community, Patient Rights Advocate) for lessons and possible collaboration.
- Draft the public-records asks in §10 and park them.
- Decide theme, domain, and the organization question in §9.

## 12. Milestones

- **M0.** This document. Done 2026-10-22.
- **M1.** Layer A spike: one hospital, ten codes, one procedure page rendered from a Parquet file.
- **M2.** Layer A for all seven hospitals and the full code list, with benchmarks, facility pages, the county overview, the methods page, and the layer C explainers. The yearly county price table lands on the ledger's services page from here.
- **M3.** Layer D instructions page, consent text, and in-person intake; first submissions processed by hand.
- **M4.** Homelab box online with storage and the firewalled intake service; the layer D form opens.
- **M5.** Layer B spike: one carrier, one network, Essex NPIs only, merged into the procedure pages.
- **M6.** Layer B for all major carriers; the cross-check between hospital and insurer files published.
- **M7.** A second county runs the pipeline from its own configuration.

## 13. Open questions

- One entity or two for the ledger and this site, and the name of the civic organization.
- Whether submitted bills from outside Essex are accepted from the start (proposal: yes, published once a county has its own facility list).
- Whether to show individual submitted rows or only aggregates while counts are small; the suppression rule decides most of this.
- How to present prices for an audience that mostly has one insurer through work and cannot shop insurers, only facilities.
- Whether the yearly county table belongs here, on the ledger, or both; proposal: generated here, cited there.
