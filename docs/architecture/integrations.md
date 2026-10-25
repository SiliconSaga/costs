# Integrations

Directions this site commits to, in the order they are likely to matter. Each is a decision about shape, not a dependency: the site works without any of them, and each arrives when its own milestone does. The common thread is independent services that are useful to more than one project, rather than one monolith that ends up duplicating them.

## Ting

[Ting](https://github.com/SiliconSaga/ting) is a structured-input service for a community: anonymous but verified by a code from a sealed envelope, no accounts, no PII, public aggregate. Its scope is the community, not schools in particular, and today it is early design with basic surveying and simple working codes. That is the intake posture Layer D needs, and the direction is to make Ting the target rather than build a sibling: Ting grows a cohort shape for submitted bills, issuing the code card at a meeting or at upload, holding the structured extracted record, and answering withdrawal and follow-up by code. The bill document itself never goes where Ting lives; see [Intake path](intake.md). The controls on that page (key derivation, rate limits, uniform answers) are the checklist Ting's code mechanics are measured against before the form opens.

## Knarr

[Knarr](https://github.com/SiliconSaga/knarr) carries messages to the channels people already use. Its backbone is self-hosted Matrix with Kafka behind it, run by nodes in the decentralized network rather than by a cloud provider. Two roles here. Outbound first: the monthly diff report is a natural publish request, "this hospital's price for this scan with this insurer moved from X to Y on this file date," rendered once per channel as a digest, a Matrix post, a site news item with a feed; Knarr stays outbound and the GitHub Action stays the poller. Inbound later, maybe: a community that trusts its own nodes more than a third-party cloud could use a Knarr-backed room as a local drop-off for sensitive records, standing in for the GKE receiver on the [intake path](intake.md#options). The payload would still be encrypted to the homelab key on the resident's device; the node only changes who holds the ciphertext in transit.

## Demicracy

[Demicracy](https://demicracy.org) is the platform; the Front State is a community project built on it (see [Lineage](../why/lineage.md#front-state)). The part of Demicracy that touches this site is its decentralized homelab stack, which is meant to help a household organize its own records at home, medical paperwork included, with permission-based access to them. That makes a future hop on the intake route: a resident digitizes their medical paperwork, or receives it digitally, keeps it organized on their own node, and from there can redact a document and release it for a secondary purpose such as submitting it here. The journey is toward people taking charge of their own medical data as individuals and as a community; this site is one destination on it, and the redaction happens where the records already live. It is a row in the [intake options](intake.md#options), marked later.

## Leidangr

[Leidangr](https://github.com/SiliconSaga/leidangr) is the Backstage control plane, and its fact-source design settled an outcome model (`pass | fail | unmeasured`, with unmeasured suppressing any verdict rather than counting as failure) and a trial shape in YAML. This site borrows both for `facilities.yaml` and the compliance record; see [Pipeline and data](pipeline.md#outcome-model). Backstage itself is for operators: later it can show pipeline runs and box health. It does not host public pages.

## Mimir

[Mimir](https://github.com/SiliconSaga/mimir) vends shared Postgres and MySQL on the clusters. The structured extracted records, the Essex NPI list, and Ting's own data take Postgres claims from it. DuckDB and Parquet stay the batch path; Postgres is for what a service reads live.

## Front State and the ledger

Front State is the home of both the schools ledger and this site. The yearly county table is generated here and cited on the ledger's services page. A second county adopting the pipeline writes its own `config/`; that is the Front State shared-template idea in its smallest working form.

## Anti-goals

- No chat-based or sandbox-agent intake for bills, ever. A bill in a chat message is a bill in a third party's logs and in an agent's context.
- No third-party analytics, scripts, fonts, or embeds on either site. Our own page counts are not the concern; someone else's code running in a reader's browser is. See [Principles](../why/principles.md#privacy-by-default).

## Open

- How far Ting's cohort model stretches to bills before it distorts its survey purpose; the lean is to stretch it and keep one code service.
- What Knarr's publish pipeline needs from the diff report's format.
- Whether a community node as drop-off adds enough trust to justify a second receiver implementation, or whether it should simply run the same receiver image on the node.
