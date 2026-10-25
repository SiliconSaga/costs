# Research

The roll-up of every page's Open section. Each item names the page it lands on; the finding goes there, not here. An item is done when its page's Unverified box is gone; it stays here ticked and dated, so the list is also the record of what was checked and when.

## Regulatory

- [ ] Confirm the scope of the hospital rule (45 CFR 180.20) and the free-access language (180.50(d)) → [Layer A](../layers/a-hospital-files.md), [Legal](../trust/legal.md)
- [ ] Confirm the Transparency in Coverage cites (45 CFR 147.210 to 147.212) and access language → [Layer B](../layers/b-insurer-files.md), [Legal](../trust/legal.md)
- [x] Confirm CMS-9882-F and its phase-in → [Layer B](../layers/b-insurer-files.md), done 2026-10-23
- [x] Confirm the `cms-hpt.txt` requirement (180.50(d)(6)) → [Layer A](../layers/a-hospital-files.md), done 2026-10-23
- [ ] Research S3409; confirm A5540/S4501 and S3012/A1729 statuses against the legislature's own site → [Sources](../reference/sources.md)

## Facilities and codes

- [ ] Enumerate Essex acute-care hospitals from CMS data (is Saint Mary's, Orange, an eighth?); locate each file via `cms-hpt.txt`; URL, format, version, date; validator result → [Facilities](../reference/facilities.md)
- [ ] Confirm Saint Michael's owner and CareWell's licence holder → [Facilities](../reference/facilities.md)
- [ ] Department of Health facility search terms; bulk list by records request → [Facilities](../reference/facilities.md)
- [ ] Fix the code list with plain names; confirm the CMS 70 → [Code list](../reference/codes.md)

## Benchmark

- [x] Which CMS tables and which locality codes (CBSA 35084; PFS 12402/01) → [Pipeline](../architecture/pipeline.md), done 2026-10-23
- [ ] The FY2026 wage index value for CBSA 35084 and 2026 GPCIs; the inpatient approximation → [Layer A](../layers/a-hospital-files.md)
- [ ] What Sage Transparency and NASHP publish for the same facilities → [Sources](../reference/sources.md)

## Existing work

- [x] Survey publishers and datasets → [Sources and projects](../reference/sources.md), done 2026-10-23
- [x] Survey parsers and validators → [Sources](../reference/sources.md#parsers-and-validators), done 2026-10-23
- [x] DuckDB's JSON reader: not for multi-GB single-object files; pre-split to NDJSON → [Pipeline](../architecture/pipeline.md), done 2026-10-23
- [ ] Measure one carrier's table-of-contents and one network file; evaluate `tic-mrf-parser` and `cms-mrf-extractor` on it → [Layer B](../layers/b-insurer-files.md)
- [ ] Ask ClearHealthCosts and DoltHub what they learned; check Sage Transparency per Essex facility → [Sources](../reference/sources.md)

## Submitted bills

- [ ] Hands-on test of the redaction paths on a real EOB and itemized bill, per device; then the illustrated guide → [Redaction](../trust/redaction.md)
- [ ] Run Presidio with the carrier patterns on samples; measure misses → [Redaction](../trust/redaction.md)
- [ ] Confirm Cigna and Oxford member-ID formats, Cigna first from a volunteer's own documents; every carrier's claim-number format → [Redaction](../trust/redaction.md)
- [ ] Confirm the suppression rule's threshold and coarsening order → [Layer D](../layers/d-submitted-bills.md)

## Legal

- [ ] HIPAA reading with counsel before the form opens → [Legal](../trust/legal.md)
- [ ] New Jersey statutes; newer privacy law → [Legal](../trust/legal.md)
- [ ] Draft terms, consent text, disclaimers → [Legal](../trust/legal.md)

## Hardware

- [x] The box's shape as planning guidance: tiers of storage, memory, cores, virtualization → [Homelab box](../architecture/homelab.md), done 2026-10-24
- [ ] Confirm the shape against the Layer B measurement (one carrier file parsed end to end, throughput and peak memory recorded) before buying → [Homelab box](../architecture/homelab.md), [Layer B](../layers/b-insurer-files.md)

## Organization

- [x] One entity or two; the civic organization's name → Front State is the home of both sites; [Hosting](../architecture/hosting.md), done 2026-10-24
- [ ] Nonprofit; whether the repositories move to a Front State GitHub organization → [Hosting](../architecture/hosting.md), [Legal](../trust/legal.md)
- [x] Domain → a `frontstate.org` subdomain, label pending the name; [Hosting](../architecture/hosting.md), done 2026-10-24
- [x] The component's name → `costs`; [Lineage](../why/lineage.md#naming), done 2026-10-24
- [ ] Register `costs.frontstate.org`; Pages source to Actions → [Hosting](../architecture/hosting.md)
- [ ] Draft the public-records asks and park them → [Public-records asks](records-requests.md)
