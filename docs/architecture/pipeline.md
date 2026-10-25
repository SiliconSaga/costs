# Pipeline and data

Python 3 with DuckDB reading the source files directly into Parquet, one Parquet per facility per file date. Small derived JSON per procedure and per facility for the site. Configuration in YAML; nothing county-specific in code.

## Layout

```text
config/
  county.yaml        # county name, state, time zone; the one file a second county edits first
  facilities.yaml    # hospitals and freestanding centers: name, NPI(s), owner, file URL, type
  codes.yaml         # the code list: code, code type, plain name, group
  payers.yaml        # payer-name normalisation across files; carrier grouping; "your plan is probably" helpers
pipeline/
  fetch/             # download, hash, record URL + date; cms-hpt.txt discovery
  validate/          # CMS validator wrapper; outcome per file
  extract/           # DuckDB: source file -> Parquet
  benchmark/         # Medicare OPPS / IPPS / PFS joins
  derive/            # Parquet -> per-procedure and per-facility JSON; county overview; diff report
data/
  manifest/
    hospital.json    # written only by the GitHub Action
    insurer.json     # written only by the homelab's pull requests
  derived/           # committed JSON the site reads (or release assets when large)
```

Raw files live on the homelab and are attached as release assets, never committed. Their URL, SHA-256, and retrieval date are recorded in `data/manifest/`, one file per source flow with one writer each, so the Action and the homelab never edit the same file; see [System overview](overview.md#homelab).

## Schedule

A monthly GitHub Action downloads, hashes, validates, and rebuilds. A diff report shows what changed between file versions, because prices that move are a story; the report is what [Knarr](integrations.md#knarr) publishes.

## Outcome model

Every check the pipeline makes about a facility produces one of three states, never a boolean.

| State | Meaning | Shown as |
|---|---|---|
| `pass` | The check held | The fact |
| `fail` | We looked and the answer was no (file missing, validator rejected it, attestation absent) | The failure, with detail |
| `unmeasured` | We could not look: no URL known, download failed, no resolver for this check | "Not checked," with the reason |

A missing file is `fail`: absence is the answer. An unreadable file is `unmeasured`. The two must never render alike, because a facility page that says "non-compliant" when the truth is "our fetch broke" is a false statement about a named hospital. The model is borrowed from the Leidangr fact-source design; see [Integrations](integrations.md#leidangr).

## Reading the files

**Hospital CSV.** `read_csv(skip=2, all_varchar=true)`, then cast. Rows one and two are file metadata and are captured separately; type inference must never see `999999999` and decide it is a price. Wide files carry payer and plan in the header (`standard_charge|<payer>|<plan>|negotiated_dollar`), so the header is parsed and the frame unpivoted to the tall shape before anything else happens.

**Hospital JSON.** One top-level object with a `standard_charge_information` array, typically tens to hundreds of megabytes; DuckDB's `read_json` handles it once `maximum_object_size` is raised.

**Insurer JSON.** One top-level object of many gigabytes. DuckDB's reader must hold the whole object, so it is not pointed at these files. A streaming pre-splitter (`ijson` with the yajl backend, or `jq --stream` with a reducer; plain `jq` parses the whole document first) emits gzipped newline-delimited JSON per top-level array; DuckDB reads that in parallel with `read_ndjson` and unnests to Parquet. Or one of the streaming parsers on [Sources and projects](../reference/sources.md#insurer-side) writes Parquet directly.

**Every row** carries the source file URL, SHA-256, retrieval date, the file's own `last_updated_on`, and the template or schema version. Raw and normalised code columns sit side by side.

## Benchmark joins

Medicare rates are joined by code and setting from downloaded CMS tables: OPPS Addendum B (HCPCS to APC, weight, national rate, quarterly), IPPS Table 5 (MS-DRG weights) and Table 3 (wage index by CBSA), and the Physician Fee Schedule RVU files with GPCIs. Essex County is CBSA 35084 (Newark, NJ metropolitan division) and PFS locality 12402/01 (Northern NJ). The benchmark is labelled approximate on every page. The PFS files embed AMA-licensed CPT descriptors, which are not redistributed; the rates are.

## The Essex NPI list

NPPES has no county field. The monthly full file is filtered on practice-location state and ZIP, and ZIP is mapped to Essex County (FIPS 34013) through the HUD USPS ZIP-county crosswalk, using the residential ratio for split ZIPs. The result is joined to the Department of Health facility list and to the Type 2 NPIs in the hospital files, and is what Layer B filters against while streaming.

## Open

- Normalising payer names across seven files, and the shape of `payers.yaml`; DoltHub's advice is to budget more for this than for parsing.
- How percentages and algorithm-only prices render when no dollar figure exists even after the 2026 percentile columns.
- The FY2026 wage index for CBSA 35084 and the 2026 GPCIs for 12402/01.
- Whether to build the pre-splitter or adopt one of the streaming parsers, decided at the Layer B spike.
