# Intake path

How a resident's bill gets from their phone to an extracted record, with nobody outside the house ever able to read it.

## Options

| Option | Who holds the document | Verdict |
|---|---|---|
| In person at meetings, scanner and paper consent slip | The keeper, on a device that goes home | **Start here.** No service to build, consent on paper, a code card handed over. |
| A form that lands on the homelab through an encrypted hop | Ciphertext on GKE briefly; plaintext only on the homelab | **Next**, once the receiver exists. |
| A dedicated email inbox | The mail provider | No. It makes a consumer mailbox a processor of health documents. |
| GitHub issues, Discord, any chat bot, any public channel | A third party's logs and an agent's context | **Never.** |

## The encrypted hop

```mermaid
sequenceDiagram
    participant R as Resident
    participant G as Receiver (GKE)
    participant H as Homelab (tailnet)
    R->>G: upload redacted PDF/photo + consent tick
    G->>G: encrypt to homelab public key, discard plaintext
    G-->>R: code card (withdrawal + follow-up), no account
    H->>G: pull ciphertext over Tailscale
    H->>H: decrypt, OCR, PII pass, human review, extract record
    H->>G: delete ciphertext
    H->>H: delete original after 30 days; record notes deletion
```

The receiver is small and boring on purpose: accept a file, encrypt it, issue a code, wait to be pulled from. It never decrypts and never stores a key that could.

## Codes, not accounts

A submitter gets a code at submission, printed on a card in person or shown once on the form. The code lets them withdraw the record or answer a follow-up question ("was the bill later reduced?"). It is tied to the record, not to a person; the mapping from code to household is never kept. This is the Ting pattern; see [Integrations](integrations.md#ting).

## Our redaction pass

Every submission, redacted or not, goes through OCR, pattern detection for member IDs, claim numbers, dates of birth, addresses, phone numbers, and account numbers, named-entity detection, and the GDD PII guard, then a human look before any extracted record is published. The tools are evaluated on the [Redaction](../trust/redaction.md) page.

The statement to submitters: we redact as able and we never publish the document itself, but anything you upload to the internet is on the internet, so redact first.

## Open

- Whether the receiver is a new service or a Ting endpoint.
- Key custody for the homelab private key, and what happens to in-flight ciphertext if the key is rotated.
- Whether a second keeper (the two-person rule) is needed before the form opens, or only before volunteers join.
