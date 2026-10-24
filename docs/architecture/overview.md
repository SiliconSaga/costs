# System overview

Three tiers, each holding only what it must.

```mermaid
flowchart TB
    subgraph pages[GitHub Pages · public, static]
        site[Price site<br/>procedure, facility, county pages]
        docs[This design site]
    end
    subgraph gke[GKE · public endpoints, small databases]
        ting[Ting-style codes<br/>withdrawal, follow-up]
        recv[Upload receiver<br/>encrypts to homelab key, holds ciphertext briefly]
        knarr[Knarr<br/>digests when prices move]
        db[(Mimir Postgres<br/>extracted records, NPI list)]
    end
    subgraph home[Homelab · tailnet only]
        raw[(Raw files<br/>hospital CSV/JSON, insurer TB JSON)]
        duck[DuckDB jobs<br/>Parquet per facility per file date]
        store[(Document store<br/>submitted originals, 30-day deletion)]
        key[Private key]
    end
    gha[GitHub Actions<br/>monthly: download, hash, validate, rebuild] --> site
    gha --> raw
    duck --> site
    recv -- Tailscale pull --> store
    key -. decrypts .-> store
    store --> ocr[OCR + PII pass + human review] --> db
    db --> site
    duck -. diff report .-> knarr
    ting <--> db
```

## GitHub Pages

Everything static and public: the price site and this design site. Built by GitHub Actions from committed derived JSON or from release assets when the JSON outgrows the repository. No server-side code, no analytics beyond server-side counts at most.

## GKE

Anything that needs a public endpoint or a database and holds no health document in readable form: the Ting-style code service, the upload receiver, Knarr for outbound digests, and a Mimir Postgres claim for the structured extracted records and the Essex NPI list. The receiver encrypts each upload to a public key whose private half exists only on the homelab, holds the ciphertext until the homelab pulls it over the tailnet, then deletes it. A compromise of the cluster yields ciphertext.

## Homelab

Reachable only over Tailscale. The terabyte insurer files, the DuckDB jobs, the document store with its deletion schedule, and the private key. Nothing on it is exposed to the internet. Until the large box exists, any current k8s node in the house can run the receiver side and the Layer A jobs; only Layer B needs the disks.

## Proving the shape before the data

Milestone M0.5 runs this topology end to end with a dummy file and no real data: a form on GKE accepts a file, encrypts it, a homelab job pulls and decrypts it over the tailnet, writes a one-row Parquet, and a Pages build renders it. No gigabytes, no health documents. It proves the hop, the key handling, the deletion, and the build trigger, which are the parts most likely to be wrong.

## Open

- Whether the GKE receiver is worth a service of its own or whether Ting grows an upload endpoint; see [Integrations](integrations.md).
- Key custody and rotation for the homelab private key.
- Whether Pages builds pull derived JSON from the repository or from release assets; depends on size after M2.
