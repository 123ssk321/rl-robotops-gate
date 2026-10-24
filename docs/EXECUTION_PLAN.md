# RobotOps Gate execution plan

Prepared 24 October 2026 from the 16 October 2026 RL research reports. **Queued; implementation inactive.**

## Product contract

A simulation policy release workflow for teams comparing checkpoint versions, CI runs and supported environment adapters.

Alternative larger scope to SimGuard, not a second implementation to build concurrently. No physical safety certificate, real-robot control or CARLA dependency.

User workflow: choose a supported scenario/configuration → automatically generate inputs → run baseline and candidate policies → inspect constraints, uncertainty and failures → export a replayable result. Product value is a hypothesis until independent use supports it. No interviews or external data access are required to build the first slice.

## Architecture and data

Versioned policy bundle + adapter registry → isolated evaluator → scenario matrix → regression gate → CI result and signed/hash-verifiable evidence bundle.

Use a pinned MuJoCo/Gymnasium task with state observations and scripted scenario generation. Record simulator, robot asset and policy checkpoint licenses independently. No camera datasets or manual trajectory curation.

Use Python, typed schemas, a maintained RL implementation where suitable, pytest, local CLI and versioned file/SQLite storage. Add a local API only after the CLI contracts stabilize. Pin simulator and library versions during implementation; the plan does not assert a currently tested compatibility matrix. Separate generator, environment, policy and evaluation interfaces; record seed, version and hashes for every run.

## Evaluation contract

Task success or return distribution, constraint events, worst-case slices, p95 inference latency, timeout/crash counts and reproducibility tolerance.

Core single-task qualification must work before an adapter or team feature is added. Release evidence must catch seeded regressions and reject incompatible artifacts without claiming sim-to-real validity.

Choose reward components, metrics and constraints before comparing results. Reserve held-out scenario families and independent seeds; use paired runs and report failed trials, denominators and uncertainty. Simulator determinism must be measured rather than assumed bit-for-bit across hardware. Synthetic performance is not an operational, clinical or physical-safety claim.

Initial compute targets (unmeasured): CPU smoke run below 30 minutes and 12GB process RAM; optional CUDA below 3.5GB VRAM; initially cap a training run at 4 wall-clock hours and 4 validation configurations. Reduce environment, horizon, model or trial count if exceeded. These caps are engineering decisions to validate, not benchmark results. Baseline fallback must remain available.

## Milestones and hours

All months are relative to future activation; calendars stay blank while queued. First functional alpha: month 4. Stable release/product decision: by month 12, within the 12-month ceiling. 552 hours total at 16 hours/week; this is one candidate's allocation, not six simultaneous commitments. Windows are checkpoints; early completed work waits for acceptance gates rather than inflating scope.

| Milestone | Relative window | Task hours | Reserve | Cumulative |
| --- | --- | ---: | ---: | ---: |
| RO-M1: Scope, rights and laptop feasibility | Month 1 | 56 | 24 | 80 |
| RO-M2: Internal end-to-end slice | Months 2–3 | 56 | 24 | 160 |
| RO-M3: First functional public alpha | Month 4 | 56 | 24 | 240 |
| RO-M4: Baselines, held-out evaluation and RL decision | Months 5–7 | 56 | 48 | 344 |
| RO-M5: Reliability and local product interfaces | Months 9–11 | 56 | 48 | 448 |
| RO-M6: Stable release and product decision | Month 12 | 56 | 48 | 552 |

Reserve is for unknown integration, simulator defects and rework. Track actual hours per issue; report every reserve draw. The first-alpha cumulative budget must fit the month-4 capacity. If it does not after re-estimation, reduce scope or move the release within months 3–12 before adding features.

## Release evidence and review

Alpha requires clean install, scripted generation, working baseline comparison, reproducible report, measured resource envelope and explicit limitations. A research result or mocked dashboard alone does not pass. Stable release adds input contracts, recoverability, bounded execution, version compatibility, operational docs and a recorded demonstration. Each issue defines outputs and acceptance checks; all require reviewed evidence before Done.

Repository files published now are plans only. Proposed src/tests/examples/reports paths in issues are future outputs. Implementation activates one issue at a time only after the owner selects the project and portfolio capacity is clear.

## Product and stop decisions

Begin with an installable local tool and open evaluation formats. Consider paid integrations/support only after repeat workflow evidence; a hosted offering needs a separate compute and governance decision. No pricing or market-size claim is verified here. At each gate record continue, narrow or park. Keep the evaluation useful if RL loses to a simpler method. No conference submission, travel, manual labeling or paid service is in scope.

For interviews, discuss domain modeling, simulator validation, baseline choice, failure analysis, engineering trade-offs and honest evidence boundaries. CV claims must use measured outcomes from completed implementation, explicitly described as self-directed work.
