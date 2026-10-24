# Integrations

Directions this site commits to, in the order they are likely to matter. Each is a decision about shape, not a dependency: the site works without any of them, and each arrives when its own milestone does.

## Ting

[Ting](https://github.com/SiliconSaga/ting) is a structured-input site for one school's families: anonymous but verified by a code from a sealed envelope, no accounts, no PII, public aggregate. That is the intake posture Layer D needs. The direction: Ting grows a cohort shape for submitted bills, issuing the code card at a meeting or at upload, holding the structured extracted record, and answering withdrawal and follow-up by code. The bill document itself never goes where Ting lives; see [Intake path](intake.md).

## Knarr

[Knarr](https://github.com/SiliconSaga/knarr) carries messages to the channels people already use. The monthly diff report is a natural publish request: "this hospital's price for this scan with this insurer moved from X to Y on this file date," rendered once per channel as a digest, a Matrix post, a site news item with a feed. Knarr stays outbound; the GitHub Action stays the poller.

## Leidangr

[Leidangr](https://github.com/SiliconSaga/leidangr) is the Backstage control plane, and its fact-source design settled an outcome model (`pass | fail | unmeasured`, with unmeasured suppressing any verdict rather than counting as failure) and a trial shape in YAML. This site borrows both for `facilities.yaml` and the compliance record; see [Pipeline and data](pipeline.md#outcome-model). Backstage itself is for operators: later it can show pipeline runs and box health. It does not host public pages.

## Mimir

[Mimir](https://github.com/SiliconSaga/mimir) vends shared Postgres and MySQL on the clusters. The structured extracted records, the Essex NPI list, and Ting's own data take Postgres claims from it. DuckDB and Parquet stay the batch path; Postgres is for what a service reads live.

## Front State and the ledger

The yearly county table is generated here and cited on the ledger's services page. A second county adopting the pipeline writes its own `config/`; that is the Front State shared-template idea in its smallest working form.

## Anti-goals

- No chat-based or sandbox-agent intake for bills, ever. A bill in a chat message is a bill in a third party's logs and in an agent's context.
- No analytics or embeds from anyone else on either site.

## Open

- Whether Ting's cohort model stretches to bills without distorting its school-survey purpose, or whether a sibling service with the same code mechanics is cleaner.
- What Knarr's publish pipeline needs from the diff report's format.
