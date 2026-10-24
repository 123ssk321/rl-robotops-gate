# RobotOps Gate

Simulation-based robot policy release checks, versioned artifacts and reproducible regression gates.

**Status: queued planning candidate. Implementation has not started.** These delivery plans do not demonstrate a working product. Publication is separate from the first functional public alpha at relative month 4, following explicit activation.

## Intended workflow

A simulation policy release workflow for teams comparing checkpoint versions, CI runs and supported environment adapters.

Alternative larger scope to SimGuard, not a second implementation to build concurrently. No physical safety certificate, real-robot control or CARLA dependency.

## Delivery documents

- [Execution plan](docs/EXECUTION_PLAN.md): scope, architecture, gates and budget.
- [Research brief](docs/RESEARCH_BRIEF.md): original research basis and limitations.
- [Backlog](planning/backlog.json): 36 bounded tasks across 6 milestones.
- [Implementer protocol](docs/IMPLEMENTER_PROTOCOL.md): activation, review and evidence rules.
- [Tracking](docs/TRACKING.md): canonical GitHub Project and live status.

## Constraints

16 hours/week shared across the portfolio; one active project after explicit selection. Local CPU-first workflow, optional 4GB GPU, no paid data/cloud. Public permitted data or automatically generated scenarios; no manual dataset construction. Conferences provide technical inspiration only.

The plan contains 336 task hours and 216 reserve hours (552 total). Each task is a bounded proposal, not a promise that its outcome is achievable unchanged. Unresolved rights, failed feasibility checks or absent product value can result in narrowing or stopping.
