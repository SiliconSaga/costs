# prices

A public record of what health care costs in Essex County, New Jersey: what each hospital is paid by each insurer for common procedures, what Medicare pays for the same thing, what freestanding centers charge, and what residents were actually billed. Built from the files the hospitals and insurers are required to publish, with the scripts that turn them into tables kept here so anyone can reproduce a figure.

The component name is generic on purpose. "Cost of Care" is the first section; other local prices can follow without a rename.

**Status: design.** Nothing is published yet. The design lives at the repository's GitHub Pages site and in `docs/`; it is written to become the documentation of the running service as the pieces are built. Dated first-ideas and agent-facing implementation notes sit in `docs/plans/` and are not part of the site.

## Working on the design site

```bash
pip install -r requirements.txt
mkdocs serve          # http://127.0.0.1:8000
mkdocs build --strict # what the PR check runs
```

The site grew out of the schools ledger's [How Services Could Work](https://schools.frontstate.org/ledger/services) page, which explains why local prices matter to a school district's budget.

Prices on this site are records, not advice. Submitted bills are never published as documents, and personal details are never kept; see the Trust section of the design for the rules.
