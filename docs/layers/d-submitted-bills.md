# Layer D — Submitted bills

The posted files say what an insurer agreed to pay. A bill says what a family was actually charged: the facility fee, the out-of-network anesthesiologist, the lab in another state, the balance after the deductible. Submitted bills are the check on Layers A and B, and they are the one place a resident can do something with their anger that leaves a record. Both reasons count.

## What people submit

An explanation of benefits from the insurer, an itemized bill from the provider, a pharmacy receipt, or a good-faith estimate, as a photo or PDF. Redacted by the submitter first, following the [Redaction](../trust/redaction.md) instructions, and by us second.

## What is extracted and published

Provider (facility name and NPI where known), facility type, procedure codes and plain names, month and year of service (never the day), insurer and plan type (HMO, PPO, HDHP; never the plan or group ID), network status, billed charge, allowed amount, plan paid, patient responsibility, and whether the bill was later corrected or reduced. Optionally a short free-text note, moderated before publication.

## What is never published

Names, member and subscriber IDs, claim numbers, dates of birth, addresses, account numbers, diagnosis codes (dropped by default; in a small town they identify people), the names of clinicians or staff, and the original image or PDF in any form. Originals are deleted after extraction on a fixed schedule, and the extraction record notes the deletion.

## Small-town re-identification

Month precision, no diagnoses, and a suppression rule: a row that is the only one for a rare procedure at a facility in a month is published only with the date widened to a quarter or a year. The principle is that the record protects the submitter before it protects precision.

## Intake

Intake is its own architecture page: [Intake path](../architecture/intake.md). In short: in person first, at meetings, with a scanner and a paper consent slip; then a form that lands on the homelab through an encrypted hop, never on a third party's disk in readable form; never a public channel, a chat bot, or GitHub issues. Until the form exists the site carries the instructions and a "not yet open" note.

## Agency, kept small

A running count of submissions and facilities covered. A quarterly "what the bills taught us" note. Contributors acknowledged only in aggregate ("17 West Orange households"), never by name. A way for a submitter to withdraw a record, using a code issued at submission, with no account.

## Open

- Define the suppression rule precisely, with the threshold counts.
- Decide whether to show individual submitted rows or only aggregates while counts are small; the suppression rule decides most of this.
- Confirm the deletion schedule for originals. Proposal: 30 days.
- Hands-on test of the redaction paths on a real EOB; see [Redaction](../trust/redaction.md).
