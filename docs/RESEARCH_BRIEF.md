# Research basis

Source: RL project research, 16 October 2026. Extract preserved from the report; references were not revalidated during delivery publication on 24 October 2026. Product potential is a hypothesis. The execution plan is the narrower delivery contract if timing or scope differs.

## 1. RobotOps Gate — simulation-first policy qualification and release tooling

### Identity and user problem

**One sentence:** RobotOps Gate is a local-first continuous-integration tool that stress-tests, compares, versions and packages robot-control policies against randomized simulation scenarios and reproducible fault cases.

Robot teams train policies in simulation, but a high simulator score can conceal sensitivity to actuator, contact, sensor, latency or environment assumptions. A team needs repeatable evidence about what changed between policy versions and where a controller fails before it decides whether expensive hardware testing is justified. The product helps robotics developers and small autonomy teams qualify simulation artifacts and run regression suites; it does not certify safety or replace physical validation.

**Why now:** The official robosuite v1.5 release expanded robot embodiments, controllers and rendering (28 October 2024), while Gymnasium’s v1.2 release moved older MuJoCo environment versions into the separate Gymnasium-Robotics project (27 June 2025). This ecosystem breadth makes reproducible environment/version management a real engineering concern. Robotics sim-to-real transfer remains an open problem: simulator mismatch and sampling/safety limits are explicitly discussed in the primary literature.

### Evidence and competing approaches

- [robosuite paper and docs](https://robosuite.ai/) (project white paper describes the v1.5 release; official site records v1.5 on 28 October 2024): modular MuJoCo environments and benchmark tasks. It is an environment foundation, not a policy qualification product.
- [Gymnasium release notes](https://gymnasium.farama.org/gymnasium_release_notes/index.html), Gymnasium v1.2.0 released 27 June 2025: documents environment/API evolution and migration of older MuJoCo versions.
- Peng et al., [Sim-to-Real Transfer of Robotic Control with Dynamics Randomization](https://openai.com/index/sim-to-real-transfer-of-robotic-control-with-dynamics-randomization/), 2017: primary experimental demonstration on object pushing. It supports randomization as a technique; its experiment does not establish universal transfer.
- [Open X-Embodiment: Robotic Learning Datasets and RT-X Models](https://arxiv.org/abs/2310.08864), 2023: original collaboration paper reports 22 robot embodiments and 527 skills. Dataset access is not equivalent to permission to redistribute every component; inspect source-specific terms before any bundled sample or checkpoint.
- [D4RL](https://arxiv.org/abs/2004.07219), 2020: offline RL benchmark motivation and dataset suite. Older task IDs/versions and library packaging have changed since publication, so pin the actual supported dataset and simulator versions rather than copying old tutorial commands.
- Open alternatives include robosuite, Gymnasium/Gymnasium-Robotics, MuJoCo, Stable-Baselines3 and RL Baselines3 Zoo. The product differentiation is the repeatable release workflow, scenario manifests, failure classification and policy artifact contract—not a new simulator or assertion that the policy is better.

### Product concept and architecture

**MVP workflow:** install the CLI; select a supported task; train or import a small policy; define a scenario manifest; run seeded baseline and stress evaluations; inspect an HTML/JSON scorecard and replay failures; compare policy versions; export a signed/hashed policy bundle plus environment manifest. The default task should be low-dimensional state-based control to fit 4 GB VRAM and CPU simulation. Keep rendering optional and sparse.

**Architecture:**

1. Version-pinned environment adapters implement a documented `reset/step/close` contract.
2. Scenario generator samples bounded physical parameters (mass, friction, damping, actuator strength, observation noise, delay/dropout) with explicit distributions and seeds.
3. Policy adapter loads deterministic inference artifacts and applies action bounds, timeouts, and observation validation.
4. Evaluation runner executes reference controllers and policies across paired seeds, aggregates return, completion, constraint violations, latency and tail outcomes.
5. Results store immutable run metadata, dependency versions, configs, hashes and raw episode summaries; no database server required initially (SQLite/Parquet).
6. CLI and static report first; local web UI only after core evaluation API stabilizes. CI runs a tiny smoke matrix; scheduled full tests remain opt-in.

Use Python, PyTorch, Gymnasium, MuJoCo/robosuite, NumPy, Pydantic or JSON Schema, pytest, Ruff, SQLite/Parquet and a container/lockfile. Alternatives: JAX/Brax for faster vector simulation (but more setup and GPU compatibility risk); ROS 2/Gazebo integration is a later adapter, not an MVP dependency. A local-only deployment avoids cloud cost; a later team product could add a job queue and artifact store.

Do not base the local MVP on CARLA: its official [system requirements](https://carla.readthedocs.io/en/latest/build_system/) describe a substantially heavier Unreal Engine setup than this laptop’s 16 GB RAM and 4 GB GPU can comfortably support. CARLA could be a later collaborator-provided or remote evaluation target, after checking the exact version’s requirements. Start with headless MuJoCo tasks and low-dimensional observations.

**Data, governance and license:** no hand-built dataset is necessary. Generate scenario variations and trajectories directly from the simulator. Optionally use small, documented slices of public robot demonstrations for behavior-cloning baselines, but do not make the MVP download a huge dataset. Track simulator, model and asset licenses; Open X-Embodiment contains multi-institution sources, so check the license for each specific component before repackaging. Publish scripts and manifests rather than third-party data where redistribution terms are unclear. Do not collect camera footage or personal data.

**Evaluation:** compare a built-in controller, behavior cloning where a suitable demo set exists, and at least one RL policy (e.g. PPO for online simulation; SAC for continuous control). Use identical train/test scenario distributions and held-out ranges. Report completion, reward, constraint/failure frequency, worst-decile return, action smoothness, inference latency, success by perturbation group, and compute. Use paired seeds and confidence intervals; record failed runs and all tuning. Run ablations to determine whether randomization improves held-out robustness. A product success target is operational rather than a paper claim: clean installation; repeatable results from a fresh clone; no silent NaNs/crashes; reports that reveal version drift and failure modes; and a benchmark suite short enough to run in a developer’s local CI.

### Execution and feasibility

**Difficulty:** high (environment variability, reproducibility and useful failure taxonomy). **Dependencies:** none external for the simulation-only core. **Bottleneck:** simulation configuration and defining interpretable qualification claims, not GPU training. One person can do the first release. Physical validation requires access to a specific robot, operator expertise and a safety plan; it is explicitly out of scope until a collaborator and hardware exist.

- **Months 1–2:** choose one MuJoCo task; validate Windows/Linux installation path; implement baseline and artifact schema; create a tiny seeded scenario matrix; write threat model for malformed policy outputs and timeouts.
- **Months 3–4:** publish alpha CLI, scorecard and replay demo; add one policy and parameter randomization; verify clean-clone reproducibility.
- **Months 5–7:** add held-out shifts, paired statistical summaries, version comparison and API compatibility tests; use benchmark feedback to simplify setup.
- **Months 8–10:** add one ROS 2 or alternate simulator adapter only if a real user workflow justifies it; otherwise improve failure reports, CI runtime and packaging.
- **Months 11–12:** release 1.0 with documented support matrix, example project, security review for untrusted model artifacts, reproducible results and adoption notes.

**Stop/reduce gate:** if one supported task plus a useful qualification report cannot be delivered by month 4, drop custom UI, multiple algorithms and ROS integration. Publish only a tested CLI and a single benchmark. If simulated perturbation scores do not predict failures in any later hardware trial, keep product claims strictly about simulation regression.

### CV, product and risk

Artifacts: package/CLI, policy bundle format, environment adapter, synthetic scenario generator, run manifest, report, replay video, CI workflow, architecture and threat-model docs, and an issue-driven public roadmap. CV bullet template: “Built a reproducible robot-policy qualification CLI for **[N]** simulated scenarios and **[M]** policy versions; reduced evaluation setup from **[X]** to **[Y]** and surfaced **[measured failure category]** across **[K]** seeds.” Do not fill values until measured.

**Product potential: moderate, unvalidated.** Potential users include robotics startups, university labs and integrators who have simulation-based control development but weak regression tooling. Likely differentiation is a lightweight local tool that produces traceable, comparable artifacts across runs. Possible paths: open-core CLI with paid team dashboards/support; hosted policy CI if customers can provide compute; consulting/integration for simulator adapters. Keep core scenario schemas, formats and baseline runners open to build trust. Charge only after developer adoption and repeated requests for collaboration features. Product validation eventually requires interviews or observing a team workflow; the user constraint only removes that prerequisite for starting.

Risks: simulator-specific overconfidence, unsafe action replay, model artifact supply-chain issues, GPU incompatibility, scope creep into a generic robotics platform, and weak willingness to pay. Reduce risk by sandboxing loaded policies, limiting action rates, pinning dependencies, making every result simulator-labelled, and not claiming real-world readiness. **Verdict:** worth pursuing if robotics is the chosen focus and the deliverable stays a policy test/release tool rather than an ambitious general robot brain.
