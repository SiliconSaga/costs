# Legal and privacy

Today this is one resident's unincorporated project. It may become a nonprofit, and the design does not need rewriting for that step. The aim is to be conscientious without being paralyzed: state what we do, do it, publish, and correct.

## HIPAA

It binds providers, insurers, and their business associates. A resident sharing their own bill is not covered, and neither, on the common reading, is a site that receives it from them. The documents are treated as if HIPAA applied anyway: minimum necessary, a deletion schedule, access limited to the keeper, and a two-person rule when volunteers join.

!!! warning "Unverified"
    The HIPAA reading needs counsel before Layer D's form opens.

## New Jersey law

The Identity Theft Prevention Act (N.J.S.A. 56:8-161 and following) defines personal information (Social Security, driver's licence, and account numbers with access codes, later usernames and passwords) and sets destruction and breach-notification duties for records in an entity's custody. The published record keeps none of these. The intake path does, briefly: a submitted original may carry identifiers the submitter missed, and it sits on the homelab for up to 30 days before deletion. For that window the design treats the statute as applying: encrypted at rest, access limited to the keeper, deletion on schedule and logged, and a breach-notification plan for the window written before the form opens.

!!! warning "Unverified"
    The statute cite, and whether the Consumer Fraud Act or New Jersey's newer privacy statutes add duties.

## Public files

Prices are facts and facts are not copyrightable. The hospital rule requires the files to be accessible free of charge, without a login or personal information, and in machine-readable form; the insurer rule has an equivalent. Hospital website terms of service may purport to restrict automated access. The collector records each site's terms as it finds them and behaves like a polite reader of a file the rule requires to be public: it fetches the one file the hospital itself names in `cms-hpt.txt`, once a month, with an identifying user agent and a contact address, and it stops if asked. Collection beyond that posture, or from a site whose terms expressly forbid it, waits until the access language below is confirmed.

!!! warning "Unverified"
    45 CFR 180.50(d) for the hospital access language, and the equivalent Transparency in Coverage provision. Confirming them is a research item; whether confirmation needs counsel or a read of the rule text is decided then.

## Defamation and accuracy

Publish records with provenance and dates. Label submitted data as submitted and unverified. Keep a [corrections page](corrections.md) and a documented process for a provider or insurer to dispute a figure. Free-text notes are moderated and never name a clinician.

## Terms and disclaimers

Not medical, legal, financial, or insurance advice. Prices change; verify with the provider. We are not a regulated entity, insurer, broker, or HIPAA covered entity. Submissions are voluntary, the consent text is shown at submission, and withdrawal is supported by code.

## Privacy of the site itself

No third-party analytics, or server-side counts at most. No cookies, no embeds. The repository runs the GDD PII guard, with a `.gdd-pii-allow` listing only addresses meant to be public.

## Nonprofit later

A 501(c)(3) educational purpose fits publishing public data and explaining it. The asset-lock language from the ledger's services page applies to any data or funds. A board of residents.

!!! warning "Unverified"
    Whether this activity qualifies under 501(c)(3), and how an asset lock is written so it binds this site's data and funds, are counsel questions. Nothing here relies on the answer yet.

## Open

- Counsel on the HIPAA reading before Layer D's form opens.
- Counsel on the nonprofit qualification and the asset-lock drafting before the organization is formed.
- Write the breach-notification plan for the 30-day retention window before the form opens.
- Confirm the New Jersey statutes and whether newer privacy law applies.
- Draft the terms, consent text, and disclaimers.
- Whether the ledger and this site share an entity.
