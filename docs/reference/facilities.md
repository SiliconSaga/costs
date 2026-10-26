# Facilities

The acute-care hospitals in Essex County, with their owners, and the location of each one's machine-readable price file. This page becomes `config/facilities.yaml` and the per-facility pages of the costs site. Freestanding centers join it with Layer A's facility list and get negotiated prices with Layer B.

## Hospitals

| Hospital | CCN | Owner | File location | Format | File date | Validator |
|---|---|---|---|---|---|---|
| Cooperman Barnabas Medical Center, Livingston | 310076 | RWJBarnabas Health | `mrfs.hyvehealthcare.com` | | | not checked |
| Clara Maass Medical Center, Belleville | 310009 | RWJBarnabas Health | `mrfs.hyvehealthcare.com` | | | not checked |
| Newark Beth Israel Medical Center | 310002 | RWJBarnabas Health | `mrfs.hyvehealthcare.com` | | | not checked |
| Mountainside Medical Center, Montclair | 310054 | Hackensack Meridian Health and Ardent (joint venture) | | | | not checked |
| University Hospital, Newark | 310119 | State public hospital | CSV on `uhnj.org` | | | not checked |
| Saint Michael's Medical Center, Newark | 310096 | | | | | not checked |
| CareWell Health Medical Center, East Orange (formerly East Orange General) | 310083 | ECR OPCO (transfer approved July 25, 2025) | | | | not checked |

CCN is the CMS certification number, the key that joins a hospital to Medicare cost reports, the RAND and Brown tables, and the NASHP tool.

!!! warning "Unverified"
    Owners as of 2025 from v0 of the design; Saint Michael's owner and CareWell's current licence holder are unconfirmed. File locations are known only for RWJBarnabas and University Hospital. "Not checked" is the `unmeasured` state, not a finding.

## What is already published about them

**Record.** The Brown CAHPR memo of September 4, 2025 ([PDF](https://cahpr.sph.brown.edu/sites/default/files/documents/Public%20Comments/09.04.2025%20CAHPR%20RBP%20Memo%20for%20New%20Jersey.pdf), Table 1) gives each hospital's price to the State Health Benefits Program as a percent of Medicare, from RAND round 5.1 claims for 2022. Inpatient figures marked — were suppressed in the source for low volume.

| Hospital | Inpatient, % of Medicare | Outpatient, % of Medicare |
|---|---|---|
| Newark Beth Israel | 221.2 | 349.2 |
| Clara Maass | 235.7 | 256.7 |
| Mountainside | 257.3 | 282.0 |
| Cooperman Barnabas | — | 279.4 |
| East Orange General (now CareWell) | — | 213.6 |
| Saint Michael's | — | 201.7 |
| University Hospital | 102.7 | 175.0 |

These are what one payer paid in 2022, not what the hospital's file says today. They are the figures the site's dollar tables will be set beside.

**Record.** DoltHub's community database holds pre-2024-schema rate data for Newark Beth Israel, Clara Maass, and Cooperman Barnabas (files dated April 2023) and East Orange General (January 2022); nothing for University Hospital, Saint Michael's, or Mountainside. See [Sources and projects](sources.md#dolthub).

## Freestanding centers

Scraped, with the search date, from the New Jersey Department of Health's ambulatory care facility search (county and type filters for ambulatory surgery, CT, MRI, PET; no bulk export). Listed here once Layer A's facility list exists.

## Open

- Locate every file and `cms-hpt.txt`; record URL, format, file date; run the validator; fill the table.
- Confirm Saint Michael's owner and CareWell's licence holder.
- Confirm the Department of Health search's terms of use, and whether a records request yields a bulk list.
