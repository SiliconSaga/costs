# Layer A — Hospital files

Seven acute-care hospitals in Essex County, each required to post a machine-readable file of its standard charges in the CMS template. Layer A reads those seven files, validates them, and renders one page per procedure and one per facility, with Medicare's rate beside every figure.

## Sources

The seven hospitals and their owners are listed on the [Facilities](../reference/facilities.md) reference page, with each file's URL, format, and date once located.

**Record.** 45 CFR 180.50(d)(6) requires the hospital's website to serve a text file at the root of its domain, named `cms-hpt.txt`, listing each location's name, the source page, the direct file URL, and a contact, and to carry a "Price Transparency" footer link on every page ([text](https://www.law.cornell.edu/cfr/text/45/180.50); [CMS FAQ](https://www.cms.gov/files/document/hospital-price-transparency-txt-file-frequently-asked-questions-faqs.pdf)). Files are named `<ein>_<hospital-name>_standardcharges.csv` or `.json`. CMS uses the text file for enforcement; about half of hospitals nationally do not comply, so discovery falls back to CMS's Hospital General Information (CCN and website) and the community URL lists on [Sources and projects](../reference/sources.md#hospital-side).

Hospital outpatient departments and any imaging site operating under the hospital's license are in these files already.

!!! warning "Unverified"
    The owners of Saint Michael's and CareWell Health; whether an eighth Essex acute-care facility (Saint Mary's, Orange) belongs on the list. Enumerate from CMS data. See [Facilities](../reference/facilities.md).

## Format

The CMS template, in CSV (tall or wide) or JSON. **Version 3.0.0** took effect January 1, 2026 with enforcement from April 1, 2026: it adds the attestation and attester name, the hospital's Type 2 NPIs, and the median, 10th and 90th percentile, and count of actual allowed amounts where a price is a percentage or formula, and removes the affirmation statement and the estimated allowed amount. Columns otherwise: description, code and code type (CPT, HCPCS, MS-DRG, APC, NDC, local), setting, gross charge, discounted cash price, payer name, plan name, negotiated dollar amount or percentage or algorithm, methodology. Rows one and two carry file-level metadata; headers are row three.

Expect to ingest v2.x files for a while: many hospitals still post them, and CMS found in May 2025 that 63% of a large-hospital sample encoded `999999999` as a placeholder estimated amount. The pipeline stamps every row with the template version and treats nine 9s as missing, never as a price.

The CMS validator ([`@cmsgov/hpt-validator-cli`](https://github.com/CMSgov/hpt-validator-cli), supporting v2.1, v2.2, and v3.0) checks a file against the schema and reports errors and alerts as JSON. The pipeline runs it on every file and publishes the result, which is itself a compliance record. See the [outcome model](../architecture/pipeline.md#outcome-model) for how a validator failure or an unreadable file is shown.

## Code list

The 70 CMS-specified shoppable services plus about 30 local picks chosen for the audience. The list, with a one-line plain-English name per code, is the [Code list](../reference/codes.md) reference page.

## Benchmark

Every price is shown next to Medicare's rate for the same code:

- Hospital outpatient: the OPPS ambulatory payment classification amount from Addendum B, wage-index adjusted for CBSA 35084 (Newark, NJ metropolitan division) using the 60% labor share.
- Inpatient: the IPPS national standardized amount times the MS-DRG relative weight from Table 5, wage-adjusted the same way; approximate, since it ignores outliers, teaching, and disproportionate-share adjustments.
- Professional components: the Physician Fee Schedule, locality 12402/01 (Northern NJ).

The tables and where they come from are on [Sources and projects](../reference/sources.md#supporting-data). The benchmark is labelled approximate. Percent-of-Medicare is shown beside the Brown and NASHP ratios where both exist; Sage Transparency's RAND-based ratios for the same facilities are a cross-check.

!!! warning "Unverified"
    The FY2026 wage index value for CBSA 35084 and the 2026 GPCIs for locality 12402/01; whether to add the IPPS hospital-specific adjustments or leave inpatient as a national-times-weight approximation.

## Presentation

- **One page per procedure.** Rows are facility by payer; columns are negotiated price, cash price, Medicare, percent of Medicare, file date, and a link to the source row.
- **One page per facility.** Its code list, validator status, file date, owner.
- **The county overview**, which is the yearly table: median percent-of-Medicare by facility across the code list. This is what the ledger's services page cites.
- **Payer names** appear as the files give them, grouped by carrier, with a "your plan is probably one of these" helper for the state and school employee plans and the common local employer plans.

## What A cannot show

Freestanding imaging and surgery centers, physician offices, independent labs, urgent care. The hospital rule binds facilities licensed as hospitals and does not reach them. Until [Layer B](b-insurer-files.md), Layer A publishes the facility list for these, scraped with its search date from the New Jersey Department of Health's ambulatory care facility search, with each center's posted cash price where it posts one and Medicare's Procedure Price Lookup figures for the same scan in a hospital department versus a freestanding setting. The Medicare figures are national averages, and they show Medicare paying hospital departments more than freestanding centers for the same scan; whether Essex commercial prices follow the same pattern, and by how much, is exactly what Layer B answers. Until then the page says that, and no more.

!!! warning "Unverified"
    Scope of the hospital rule (45 CFR 180.20) and the Department of Health facility search's terms of use.

## Open

- Enumerate Essex acute-care hospitals from CMS Hospital General Information; locate each file via `cms-hpt.txt`; record URL, format, template version, file date; run the validator on each.
- Look up the FY2026 wage index for CBSA 35084 and the 2026 GPCIs; decide the inpatient approximation.
- Fix the code list with plain names.
- Decide how to treat "N/A" and placeholder values, and how to normalise payer names across seven files.
- Whether hospital website terms of service matter when the rule requires the file to be accessible without barriers (45 CFR 180.50(d)).
