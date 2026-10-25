# Layer D — Submitted bills

The posted files say what an insurer agreed to pay. A bill says what a family was actually charged: the facility fee, the out-of-network anesthesiologist, the lab in another state, the balance after the deductible. Submitted bills are the check on Layers A and B, and they are the one place a resident can do something with their anger that leaves a record. Both reasons count.

## What people submit

An explanation of benefits from the insurer, an itemized bill from the provider, a pharmacy receipt, or a good-faith estimate, as a photo or PDF, together with the facility picked from our [facility list](../reference/facilities.md) (on the consent slip in person; inside the encrypted payload on the form). The facility's name on the document is kept, since it is the facility's identity and not the submitter's; the names of individual clinicians are redacted. Redacted by the submitter first, following the [Redaction](../trust/redaction.md) instructions, and by us second.

## What is extracted and published

Provider (facility name and NPI where known), facility type, procedure codes and plain names, month and year of service (never the day), insurer and plan type (HMO, PPO, HDHP; never the plan or group ID), network status, billed charge, allowed amount, plan paid, patient responsibility, and whether the bill was later corrected or reduced. Optionally a short free-text note, moderated before publication.

## What is never published

Names, member and subscriber IDs, claim numbers, dates of birth, addresses, account numbers, diagnosis codes (dropped by default; in a small town they identify people), the names of clinicians or staff, and the original image or PDF in any form. Originals are deleted 30 days after submission, the retention window every page of this design uses, and the extraction record notes the deletion.

## Small-town re-identification

Month precision, no diagnoses, and a suppression rule. A group is the records that share a facility, procedure, and plan type within the publication period. Nothing about a group is published, not a row, not a count, not a median, until at least a threshold number of distinct contributors (proposal: five) are in it, because a median of one is the submitted amount and a count of one says a neighbour was there. A contributor is one submission session: one consent, one code card, however many documents came with it, all of which count once. That is the most the design can know, since the code-to-household mapping is never kept, so the threshold is a floor on sessions, not a proof of five households; a household that submits in five separate sessions could fill a group alone, and in doing so exposes only its own figures to someone who already knows it submitted. The keeper, who sees the in-person batches, may tag sessions from one household as one contributor; the form path cannot. Below the threshold the group is held. Two things let it fill: widening the period, up to a year, and coarsening the group, dropping plan type first and then collapsing the procedure into its code group, each step applied only if the coarser group clears the threshold on its own. Neither is a substitute for the threshold. A record that is alone even at a year's width and the coarsest grouping stays in the overall running count of submissions and nowhere else. Once a group clears the threshold, individual rows within it are published only if the keeper has decided to show rows at all (see Open); the default is aggregates. The principle is that the record protects the submitter before it protects precision.

## Intake

Intake is its own architecture page: [Intake path](../architecture/intake.md). In short: in person first, at meetings, with a scanner and a paper consent slip; then a form that lands on the homelab through an encrypted hop, never on a third party's disk in readable form; never a public channel, a chat bot, or GitHub issues. Until the form exists the site carries the instructions and a "not yet open" note.

## Agency, kept small

A running count of submissions and facilities covered. A quarterly "what the bills taught us" note. Contributors acknowledged only in aggregate ("17 West Orange households"), never by name. A way for a submitter to withdraw a record, using a code issued at submission, with no account.

## Open

- Confirm the suppression threshold (proposal: five contributors) and which fields define a group.
- Decide whether to show individual submitted rows or only aggregates while counts are small; the suppression rule decides most of this.
- Hands-on test of the redaction paths on a real EOB; see [Redaction](../trust/redaction.md). A volunteer's own Cigna explanation of benefits is the first test document, with the volunteer's written consent to that use, handled exactly as a submission would be: on the homelab, through our pass, never in a third-party tool, Git, or anything published, and deleted within the same retention window. It also makes Cigna the first carrier whose identifier and claim formats are confirmed from a real document. Anything that must be shared or committed, a fixture for a test for instance, is synthetic.
