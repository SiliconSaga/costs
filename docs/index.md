# Cost of Care

A public record of what health care costs in Essex County, New Jersey: what each hospital is paid by each insurer for common procedures, what Medicare pays for the same thing, what freestanding centers charge, and what residents were actually billed. Built from the files hospitals and insurers are required to publish, with every script that turns a file into a table kept in the open so anyone can reproduce a figure.

**Status: design.** Nothing is published yet. This site is the design, and it is written to become the documentation of the running service as the pieces are built.

## How to read this site

- **[Why](why/problem.md)** says who this is for and what rules every page keeps.
- **[Layers](layers/index.md)** is the work, in the order it gets done: explainers and links first, then hospital files, then submitted bills, then insurer files.
- **[Architecture](architecture/overview.md)** is how the pieces run and where: GitHub Pages, a small public cluster, and a homelab that nothing outside the house can reach.
- **[Trust](trust/legal.md)** is the legal and privacy posture and the redaction rules.
- **[Reference](reference/facilities.md)** is the material a reader of the finished site will need: the facilities, the code list, where else to look, the glossary. It fills in as research lands.
- **[Plan](plan/milestones.md)** is the milestones, the research still owed, and the questions nobody has answered yet.

Two conventions run through every page:

!!! warning "Unverified"
    A box like this marks a claim that has not been checked against a primary source. This design site shows them openly; the price site, where residents will read figures, publishes nothing that is still in one. Research clears the box and the claim stays, or removes both.

Every page ends with an **Open** section listing what research must still settle on that page. The [Research](plan/research.md) page is the roll-up, so each finding has exactly one place to land.

## Open

- `costs.frontstate.org` is decided but not yet registered; the GitHub Pages URL serves until it is. See [Hosting](architecture/hosting.md#domain).
