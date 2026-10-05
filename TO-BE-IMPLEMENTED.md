# To be implemented: Linux builder and runtime integration

Status: **required implementation backlog**.
Repository: `B0x-world/packer`.
Working branch: `release/linux-self-hosted`.
Created: 2026-10-05.
Master tracker: [#1](https://github.com/B0x-world/packer/issues/1).

This backlog merges the requested Git CI Builder features with the imported Packer implementation and the additional GUI, reporting, debugging, plugin and platform requirements. **Missing or incomplete features are work to implement, not merely future design options.** Investigation, required changes to imported code, documentation and validation are part of delivery.

The [architecture document](contrib/linux-self-hosted/README.md) explains behavior and boundaries. This document defines the required work. Linked GitHub issues own live progress; the status snapshot here is all open/to be implemented when created. Completing this backlog means implementing and qualifying the features, not creating their issues or writing their design.

## Baseline and initial gap assessment

- Packer release-branch input: `7d18b124d110779ccbb337dbc26ada6ec7e1c80d`, based on upstream fork baseline `4bb4b91351b5a62ed4c1df26ff230aecda5bfbe6`.
- Git CI Builder source: `dabb0dec437d84aba4f642da33a931004895c4ba`.
- Only design documentation currently exists under `contrib/linux-self-hosted/`.
- The issue tracker was enabled and empty at intake; these issues record the new workstream.
- Main is protected. Work and PRs target the release branch; this backlog does not authorize merging project changes into main.

The following source samples establish reusable primitives, not complete feature coverage. LSH-001 performs the full affected-code investigation and LSH-002 records and implements required imported-code changes.

| Existing source in imported Packer | Observed reusable primitive | Remaining integration |
|---|---|---|
| [build command](https://github.com/B0x-world/packer/blob/7d18b124d110779ccbb337dbc26ada6ec7e1c80d/command/build.go) | Build orchestration, cancellation and on-error choices | Profile policy, ownership, runtime handoff, qualified cleanup and release gates |
| [validate command](https://github.com/B0x-world/packer/blob/7d18b124d110779ccbb337dbc26ada6ec7e1c80d/command/validate.go) | Template/configuration validation entry point | Declared-state, exact-target, live security and deployment qualification |
| [init command](https://github.com/B0x-world/packer/blob/7d18b124d110779ccbb337dbc26ada6ec7e1c80d/command/init.go), [plugin install](https://github.com/B0x-world/packer/blob/7d18b124d110779ccbb337dbc26ada6ec7e1c80d/command/plugins_install.go), [plugin removal](https://github.com/B0x-world/packer/blob/7d18b124d110779ccbb337dbc26ada6ec7e1c80d/command/plugins_remove.go) | Plugin acquisition, constraints/checksums and removal | Exact qualified sets, in-use protection, controlled updates and QEMU integration |
| [required_plugins decoding](https://github.com/B0x-world/packer/blob/7d18b124d110779ccbb337dbc26ada6ec7e1c80d/hcl2template/types.required_plugins.go) | Source/version configuration | Distribution dependency records and compatibility evidence |
| [shell provisioner](https://github.com/B0x-world/packer/blob/7d18b124d110779ccbb337dbc26ada6ec7e1c80d/provisioner/shell/provisioner.go), [file provisioner](https://github.com/B0x-world/packer/blob/7d18b124d110779ccbb337dbc26ada6ec7e1c80d/provisioner/file/provisioner.go) | Guest script execution and file transfer through a communicator | Controlled preparation, state transitions, secret/control separation and post-provisioning validation |
| [manifest post-processor](https://github.com/B0x-world/packer/blob/7d18b124d110779ccbb337dbc26ada6ec7e1c80d/post-processor/manifest/post-processor.go) | Build artifact metadata output | Git CI Builder immutable sidecars, evidence currency, active-state transactions and promotion |
| External [QEMU plugin][P2] | Separately maintained builder backend | Pin, inspect, install and qualify it; the Packer fork does not contain it |
| Git CI Builder [schemas][S11], [runtime records][S12] and [implementation status][S9] | Contracts, fixtures and partial runtime helpers | Reuse/adapt after review; missing real runtime/canary evidence remains required work |

No Packer or VM tests were run for this initial assessment. Source presence is recorded as reuse evidence; it is not proof of behavior on the selected deployment.

## Required work groups

| Group | Items |
|---|---|
| A. Imported code and contracts | [LSH-001](#lsh-001), [LSH-002](#lsh-002), [LSH-003](#lsh-003) |
| B. Declared configuration and operator interfaces | [LSH-004](#lsh-004), [LSH-005](#lsh-005), [LSH-006](#lsh-006), [LSH-007](#lsh-007), [LSH-008](#lsh-008) |
| C. Host, build and dependencies | [LSH-009](#lsh-009), [LSH-010](#lsh-010), [LSH-011](#lsh-011), [LSH-012](#lsh-012) |
| D. Images and workspace | [LSH-013](#lsh-013), [LSH-014](#lsh-014), [LSH-015](#lsh-015) |
| E. Runtime, servicing and channels | [LSH-016](#lsh-016), [LSH-017](#lsh-017), [LSH-018](#lsh-018), [LSH-019](#lsh-019), [LSH-020](#lsh-020), [LSH-021](#lsh-021) |
| F. Security and network | [LSH-022](#lsh-022), [LSH-023](#lsh-023), [LSH-024](#lsh-024) |
| G. Updates, lifecycle and recovery | [LSH-025](#lsh-025), [LSH-026](#lsh-026), [LSH-027](#lsh-027) |
| H. Validation and qualification | [LSH-028](#lsh-028), [LSH-029](#lsh-029) |
| I. Debugging | [LSH-030](#lsh-030) |
| J. Swappable platforms and deployment | [LSH-031](#lsh-031), [LSH-032](#lsh-032), [LSH-033](#lsh-033) |
| K. Repository, documentation and delivery | [LSH-034](#lsh-034), [LSH-035](#lsh-035), [LSH-036](#lsh-036) |

## Implementation register

All 36 items are open at creation. Local `LSH-###` identifiers remain stable when issue titles or sequencing change.

| ID | Implementation item | Initial status | GitHub issue |
|---|---|---|---|
| [LSH-001](#lsh-001) | Audit imported Packer, QEMU plugin and Git CI Builder reuse | To be implemented | [#2](https://github.com/B0x-world/packer/issues/2) |
| [LSH-002](#lsh-002) | Implement required changes to imported Packer integration points | To be implemented | [#3](https://github.com/B0x-world/packer/issues/3) |
| [LSH-003](#lsh-003) | Version the shared contracts, schemas and metadata | To be implemented | [#4](https://github.com/B0x-world/packer/issues/4) |
| [LSH-004](#lsh-004) | Implement the common package and capability library | To be implemented | [#5](https://github.com/B0x-world/packer/issues/5) |
| [LSH-005](#lsh-005) | Implement resolution, version locking and observed inventory | To be implemented | [#6](https://github.com/B0x-world/packer/issues/6) |
| [LSH-006](#lsh-006) | Implement the package and configuration state machine | To be implemented | [#7](https://github.com/B0x-world/packer/issues/7) |
| [LSH-007](#lsh-007) | Implement a common CLI and lightweight TUI | To be implemented | [#8](https://github.com/B0x-world/packer/issues/8) |
| [LSH-008](#lsh-008) | Implement the GUI over shared configuration operations | To be implemented | [#9](https://github.com/B0x-world/packer/issues/9) |
| [LSH-009](#lsh-009) | Implement the distribution installer and host preparation | To be implemented | [#10](https://github.com/B0x-world/packer/issues/10) |
| [LSH-010](#lsh-010) | Integrate pinned Packer and QEMU plugin management | To be implemented | [#11](https://github.com/B0x-world/packer/issues/11) |
| [LSH-011](#lsh-011) | Qualify and adapt selected upstream provisioning helpers | To be implemented | [#12](https://github.com/B0x-world/packer/issues/12) |
| [LSH-012](#lsh-012) | Implement Packer/QEMU templates and candidate artifact handoff | To be implemented | [#13](https://github.com/B0x-world/packer/issues/13) |
| [LSH-013](#lsh-013) | Implement QCOW2 image sets, manifests and ownership locks | To be implemented | [#14](https://github.com/B0x-world/packer/issues/14) |
| [LSH-014](#lsh-014) | Implement agent-environment construction and registration | To be implemented | [#15](https://github.com/B0x-world/packer/issues/15) |
| [LSH-015](#lsh-015) | Implement the bounded RAM workspace and resource policy | To be implemented | [#16](https://github.com/B0x-world/packer/issues/16) |
| [LSH-016](#lsh-016) | Implement host/guest runtime orchestration and job admission | To be implemented | [#17](https://github.com/B0x-world/packer/issues/17) |
| [LSH-017](#lsh-017) | Implement service integration and image-servicing modes | To be implemented | [#18](https://github.com/B0x-world/packer/issues/18) |
| [LSH-018](#lsh-018) | Implement protected injection and authenticated boot control | To be implemented | [#19](https://github.com/B0x-world/packer/issues/19) |
| [LSH-019](#lsh-019) | Implement readiness, result and diagnostic reporting | To be implemented | [#20](https://github.com/B0x-world/packer/issues/20) |
| [LSH-020](#lsh-020) | Implement independent every-boot state reporting | To be implemented | [#21](https://github.com/B0x-world/packer/issues/21) |
| [LSH-021](#lsh-021) | Implement single-job and bounded-batch execution | To be implemented | [#22](https://github.com/B0x-world/packer/issues/22) |
| [LSH-022](#lsh-022) | Implement phase-specific network configuration and enforcement | To be implemented | [#23](https://github.com/B0x-world/packer/issues/23) |
| [LSH-023](#lsh-023) | Implement declared hardening and privilege boundaries | To be implemented | [#24](https://github.com/B0x-world/packer/issues/24) |
| [LSH-024](#lsh-024) | Implement secret handling, redaction and log lifecycle | To be implemented | [#25](https://github.com/B0x-world/packer/issues/25) |
| [LSH-025](#lsh-025) | Implement updates and resumable image servicing | To be implemented | [#26](https://github.com/B0x-world/packer/issues/26) |
| [LSH-026](#lsh-026) | Implement candidate qualification, atomic promotion and rollback | To be implemented | [#27](https://github.com/B0x-world/packer/issues/27) |
| [LSH-027](#lsh-027) | Implement cancellation, orphan recovery and quarantine | To be implemented | [#28](https://github.com/B0x-world/packer/issues/28) |
| [LSH-028](#lsh-028) | Implement configuration, contract and validator qualification | To be implemented | [#29](https://github.com/B0x-world/packer/issues/29) |
| [LSH-029](#lsh-029) | Implement real build, runtime and GitHub canary gates | To be implemented | [#30](https://github.com/B0x-world/packer/issues/30) |
| [LSH-030](#lsh-030) | Create the debugging branch and diagnostic workflow | To be implemented | [#31](https://github.com/B0x-world/packer/issues/31) |
| [LSH-031](#lsh-031) | Implement independently selectable build-host and guest OS adapters | To be implemented | [#32](https://github.com/B0x-world/packer/issues/32) |
| [LSH-032](#lsh-032) | Implement swappable VM/build platforms and artifact adapters | To be implemented | [#33](https://github.com/B0x-world/packer/issues/33) |
| [LSH-033](#lsh-033) | Implement cloud and self-hosted deployment choices | To be implemented | [#34](https://github.com/B0x-world/packer/issues/34) |
| [LSH-034](#lsh-034) | Adapt imported CI, issue templates and release workflows | To be implemented | [#35](https://github.com/B0x-world/packer/issues/35) |
| [LSH-035](#lsh-035) | Merge and maintain all feature documentation and traceability | To be implemented | [#36](https://github.com/B0x-world/packer/issues/36) |
| [LSH-036](#lsh-036) | Package and release the qualified distribution | To be implemented | [#37](https://github.com/B0x-world/packer/issues/37) |

## Work and completion rules

1. Start from the pinned sources and read applicable repository instructions. Each item investigates existing code before implementing or adapting it.
2. Record exact reused, modified and new paths. Preserve component ownership: Packer core, separate QEMU plugin, distribution adapters and Git CI Builder runtime are distinct.
3. A required core/plugin change needs an evidenced gap and regression coverage. If no core change is needed, record why existing extension points satisfy the requirement; do not manufacture a patch.
4. Apply schema/contract changes explicitly. Do not guess B1 exit categories, B2 bounds, B3 policy members, a log-retention duration or a missing license grant.
5. Deliver documentation and validation with code. Each issue remains open until its acceptance evidence, changed paths and relevant documentation are linked.
6. Every discovered required subtask must be added to its issue or split into a linked issue and registered here. Do not silently drop a requirement because an upstream repository lacks it.
7. Cross-repository plugin/SDK/Git CI Builder changes must be explicitly scoped and tracked before execution. This backlog update changes only this repository and its tracker.
8. GitHub issues hold current status; update this register when scope/dependencies or completion evidence changes. The master issue summarizes the workstream without replacing leaf issues.
9. Runtime and infrastructure execution, release publication and main-branch changes are separate actions from creating this backlog.

## Delivery order

Investigate imported code and settle contracts first (A). Build common configuration and host/dependency support next (B–C), then images, workspace, runtime and security (D–F). Complete servicing, recovery and real qualification (G–H). Diagnostic work supports those stages (I). Deliver the requested platform/location substitutions (J), GUI and independent reporter as tracked requirements; retain their open status through earlier partial milestones. Repository adaptation, documentation and release work (K) run alongside the implementation.

Dependencies below identify implementation prerequisites, not permission to omit later required features. No milestone may claim full delivery while required items remain unresolved.

## A. Imported code and contracts

### LSH-001

**Audit imported Packer, QEMU plugin and Git CI Builder reuse**

- Status: **To be implemented**; tracker: [#2](https://github.com/B0x-world/packer/issues/2).
- Dependencies: None; entry investigation.
- Sources: [P1], [P2], [P3], [S9], [S14].

**Present state / gap:** Existing Packer build, validation, plugin, shell/file and manifest primitives were sampled; no distribution implementation exists under contrib/linux-self-hosted.

**Investigation:** Inspect the complete affected call paths, tests, upstream defaults and failure behavior at pinned revisions; inspect the separate QEMU plugin, Git CI Builder runtime and selected runner-images helpers.

**Implementation:** Produce a capability-to-code gap matrix classifying each requested feature as reusable, adaptable, missing or unverified, with exact files, owners and proposed patches. Feed discovered required changes into the linked implementation issues.

**Documentation:** Record revisions, evidence, rejected reuse options and boundaries; correct inaccurate imported assumptions.

**Acceptance:**

- [ ] Every requested concern maps to source evidence and a tracked implementation item.
- [ ] No feature is marked complete solely because a related upstream command exists.

### LSH-002

**Implement required changes to imported Packer integration points**

- Status: **To be implemented**; tracker: [#3](https://github.com/B0x-world/packer/issues/3).
- Dependencies: [LSH-001](#lsh-001)
- Sources: [P1], [P2].

**Present state / gap:** Packer core is imported; no required core-patch assessment or distribution-specific core changes are complete.

**Investigation:** Use LSH-001 to inspect command registration, HCL evaluation, plugin loading, artifact handling, cancellation, cleanup and compatibility; choose extension points before core changes.

**Implementation:** Implement each evidenced required core/integration correction in this fork. Regenerate derived Go/HCL code when its source changes and preserve existing templates. Record upstream plugin/SDK changes as separately owned work rather than silently editing other repositories.

**Documentation:** Maintain a patch register with upstream revision, reason, affected interfaces, regression evidence and update/rebase notes; document an evidenced no-core-patch outcome if appropriate.

**Acceptance:**

- [ ] Every required patch has regression tests and a documented compatibility impact.
- [ ] A no-patch decision is supported by the completed gap assessment; unresolved gaps remain open.

### LSH-003

**Version the shared contracts, schemas and metadata**

- Status: **To be implemented**; tracker: [#4](https://github.com/B0x-world/packer/issues/4).
- Dependencies: [LSH-001](#lsh-001)
- Sources: [S1], [S2], [S4], [S11], [S12], [S13]. Functional coverage: F01, F17.

**Present state / gap:** Git CI Builder has versioned schemas, but the fork has not integrated them; expanded targets and B1/B2/B3 remain unresolved.

**Investigation:** Map configuration, runtime, ownership, plan, result and evidence records; identify closed v1 constraints and decisions needed for exit categories, operation bounds and policy members.

**Implementation:** Bundle compatible contracts and migrations for independent host/guest/backend/location selections. Resolve and record required decisions before defining new policy members or numeric limits; use typed capability/unsupported results.

**Documentation:** Publish contract ownership, schema/version rules, metadata placement and the accepted decision register.

**Acceptance:**

- [ ] Existing v1 records remain valid through an explicit compatibility path.
- [ ] New target/configuration contracts have valid and invalid fixtures; B1/B2/B3 are not guessed.

## B. Declared configuration and operator interfaces

### LSH-004

**Implement the common package and capability library**

- Status: **To be implemented**; tracker: [#5](https://github.com/B0x-world/packer/issues/5).
- Dependencies: [LSH-003](#lsh-003)
- Sources: [S2], [S5], [S11]. Functional coverage: F01, F02, F03.

**Present state / gap:** Semantic definitions and inheritance are designed in Git CI Builder; no distribution catalogue is implemented.

**Investigation:** Map common host/guest requirements to exact distro/release/architecture package, service and artifact bindings; separate native package dependencies from semantic requirements.

**Implementation:** Create versioned reusable definitions/components and linux-base/git-ci-builder profiles, with exact-target verification, storage placement, conflicts, activation and required validation.

**Documentation:** Document adding/editing package capabilities and distro/version definitions without copying whole package lists.

**Acceptance:**

- [ ] Two profiles reuse common definitions and retain target-specific differences.
- [ ] Missing exact-target coverage blocks normal apply; qualification does not silently certify a target.

### LSH-005

**Implement resolution, version locking and observed inventory**

- Status: **To be implemented**; tracker: [#6](https://github.com/B0x-world/packer/issues/6).
- Dependencies: [LSH-004](#lsh-004)
- Sources: [S1], [S2], [S3], [S11], [S12]. Functional coverage: F01, F06, F17.

**Present state / gap:** Git CI Builder has schema/record-level resolution and image-selection contracts; complete distribution resolution is absent.

**Investigation:** Inspect existing variable handling and GCB resolver helpers; define authored intent versus exact resolved inputs and observed installation state.

**Implementation:** Resolve dependency graphs, reject cycles/conflicts/ambiguity, retain value provenance, pin actual versions/digests, record native transactions and detect drift. Select compatible base/environment sets without runtime installation.

**Documentation:** Document explain/diff output, unavailable pins, version changes and capability-based image selection.

**Acceptance:**

- [ ] Equivalent input yields the same resolved identities; unavailable pins never silently float.
- [ ] Inventory and configuration/service checks detect differences from the selected revision.

### LSH-006

**Implement the package and configuration state machine**

- Status: **To be implemented**; tracker: [#7](https://github.com/B0x-world/packer/issues/7).
- Dependencies: [LSH-003](#lsh-003), [LSH-005](#lsh-005)
- Sources: [S4], [S5], [S12]. Functional coverage: F02, F03, F12, F17.

**Present state / gap:** Plan/apply/validate/resume contracts exist; full convergent host/guest preparation is missing.

**Investigation:** Inspect package simulation, ownership, state persistence and interruption cases across package/config/service/artifact adapters.

**Implementation:** Implement detect, resolve, verify, plan, approval, apply, reboot/resume and validate transitions with immutable plans, locks, receipts, stale-plan rejection and idempotent repair.

**Documentation:** Document state transitions, recoverable versus blocked failures and approved operation bounds.

**Acceptance:**

- [ ] Repeated apply makes no unintended changes.
- [ ] Changed target/profile/plan or interrupted native transactions cannot be treated as completed work.

### LSH-007

**Implement a common CLI and lightweight TUI**

- Status: **To be implemented**; tracker: [#8](https://github.com/B0x-world/packer/issues/8).
- Dependencies: [LSH-003](#lsh-003), [LSH-005](#lsh-005), [LSH-006](#lsh-006)
- Sources: [S1], [S4], [S5]. Functional coverage: F01, F16, F17.

**Present state / gap:** Packer has a CLI; Git CI Builder requires shared non-interactive and whiptail operations; the new operator surface is absent.

**Investigation:** Map operation names, precedence, input/output contracts and existing CLI extension points without duplicating business logic.

**Implementation:** Expose profile/package/version selection, resolve/explain/diff, plan/apply/validate/status and servicing operations through one API used by CLI and whiptail. Keep secret input separate.

**Documentation:** Write command reference, menu workflows, configuration precedence and script/agent examples.

**Acceptance:**

- [ ] CLI and TUI produce equivalent plans/results for the same inputs.
- [ ] Editing/saving configuration does not apply changes until the matching plan is authorized.

### LSH-008

**Implement the GUI over shared configuration operations**

- Status: **To be implemented**; tracker: [#9](https://github.com/B0x-world/packer/issues/9).
- Dependencies: [LSH-007](#lsh-007)
- Sources: [S1], [S2], [U1]. Functional coverage: F16.

**Present state / gap:** A GUI was requested and has no implementation; it is required backlog work, not optional because v1 lacked it.

**Investigation:** Select a lightweight local GUI approach and schema-driven editing model; assess packaging and privilege separation against the common operation API.

**Implementation:** Implement package/component catalogue editing, target/version selection, inherited-value explanation, plan preview, apply/status and validation display through the same operations as CLI/TUI.

**Documentation:** Document installation, editing, accessibility/error behavior and feature parity limits.

**Acceptance:**

- [ ] The same saved profile resolves equivalently through GUI, TUI and CLI.
- [ ] The GUI cannot bypass validation, plan approval or secret-handling boundaries.

## C. Host, build and dependencies

### LSH-009

**Implement the distribution installer and host preparation**

- Status: **To be implemented**; tracker: [#10](https://github.com/B0x-world/packer/issues/10).
- Dependencies: [LSH-004](#lsh-004), [LSH-006](#lsh-006)
- Sources: [S1], [S4], [S5]. Functional coverage: F02.

**Present state / gap:** The project installer is missing; imported helper install.sh is not an application installer. Preflight primitives do not install dependencies.

**Investigation:** Inspect host prerequisites, service accounts, package simulation, paths and actual KVM readiness; distinguish build host from deployment host.

**Implementation:** Implement explicit setup/update/repair for the application, required runtime, QEMU/KVM/OVMF and selected tooling, with preservation rules and owned/adopted state.

**Documentation:** Provide install/uninstall or retirement procedures, supported targets, privilege requirements and host recovery instructions.

**Acceptance:**

- [ ] A clean supported host can be prepared repeatably without damaging unrelated state.
- [ ] Preflight proves actual required capabilities and rejects insufficient resources or acceleration.

### LSH-010

**Integrate pinned Packer and QEMU plugin management**

- Status: **To be implemented**; tracker: [#11](https://github.com/B0x-world/packer/issues/11).
- Dependencies: [LSH-001](#lsh-001), [LSH-003](#lsh-003), [LSH-009](#lsh-009)
- Sources: [P1], [P2], [S1], [U1].

**Present state / gap:** Packer init/plugins commands and version/checksum primitives exist; lifecycle policy and QEMU qualification are missing.

**Investigation:** Inspect command/init.go, command/plugins_*.go, plugin-getter and the separate QEMU plugin; qualify Packer/plugin/QEMU compatibility and acquisition.

**Implementation:** Reuse existing mechanisms behind explicit install/list/verify/update/remove operations; pin versions/digests per build, prevent in-use removal and uncontrolled upgrade, and expose adapter capabilities.

**Documentation:** Document plugin source identity, version selection, compatibility, rollback and trusted-code boundaries.

**Acceptance:**

- [ ] The official QEMU plugin is installed and independently identified from Packer and host QEMU.
- [ ] A running build retains its selected dependency set; unsupported or mismatched plugins fail before resource creation.

### LSH-011

**Qualify and adapt selected upstream provisioning helpers**

- Status: **To be implemented**; tracker: [#12](https://github.com/B0x-world/packer/issues/12).
- Dependencies: [LSH-001](#lsh-001), [LSH-003](#lsh-003), [LSH-004](#lsh-004)
- Sources: [S14], [U1]. Functional coverage: F03.

**Present state / gap:** The runner-images helper is imported in Git CI Builder; it is not integrated or qualified for this distribution.

**Investigation:** Review selected Bash functions, transitive tool dependencies, Ubuntu-version assumptions, retries, release lookup and checksum behavior at immutable source revisions.

**Implementation:** Import only selected helper material with provenance/attribution; implement controlled wrappers and mandatory integrity checks; keep host setup and guest desired state project-owned.

**Documentation:** Publish the import inventory, reuse/adaptation decisions, wrapper API and source-update procedure.

**Acceptance:**

- [ ] Each used helper function has target qualification and negative cases.
- [ ] Floating release discovery and imported retry constants cannot silently set build inputs or project policy.

### LSH-012

**Implement Packer/QEMU templates and candidate artifact handoff**

- Status: **To be implemented**; tracker: [#13](https://github.com/B0x-world/packer/issues/13).
- Dependencies: [LSH-002](#lsh-002), [LSH-005](#lsh-005), [LSH-009](#lsh-009), [LSH-010](#lsh-010), [LSH-011](#lsh-011)
- Sources: [S1], [S3], [S14], [P1], [P2]. Functional coverage: F03, F04.

**Present state / gap:** Packer already builds images; profile templates and the distribution adapter are missing.

**Investigation:** Map resolved hardware, source and provisioning requirements to actual QEMU plugin behavior, output files, firmware state and cleanup paths.

**Implementation:** Implement exact-source disk-image builds, explicit KVM/Q35/OVMF policy for GCB, provisioner ordering, temporary access removal, cancellation accounting and normalized candidate receipts.

**Documentation:** Document inputs/outputs, owned build paths, troubleshooting and the distinction between build success and image acceptance.

**Acceptance:**

- [ ] One generic base and the GCB profile produce traceable candidates without changing source images.
- [ ] Independent boot uses fresh firmware state and no builder-only credentials or dependencies.

## D. Images and workspace

### LSH-013

**Implement QCOW2 image sets, manifests and ownership locks**

- Status: **To be implemented**; tracker: [#14](https://github.com/B0x-world/packer/issues/14).
- Dependencies: [LSH-003](#lsh-003), [LSH-012](#lsh-012)
- Sources: [S1], [S3], [S12]. Functional coverage: F04, F06.

**Present state / gap:** Git CI Builder has digest/overlay helpers and lifecycle schemas; full transactions are absent from the fork.

**Investigation:** Inspect qemu-img/backing-chain behavior and existing image helpers; map image/session/candidate identities and lock ownership.

**Implementation:** Implement independent OS/environment source chains with fresh overlays, immutable manifest sidecars, per-session full digest/size verification, storage checks and explicit artifact import.

**Documentation:** Document image layouts, names, UUID identities, backing relationships and invalid/untrusted artifact handling.

**Acceptance:**

- [ ] Normal writes cannot reach immutable sources and no session reuses another overlay.
- [ ] Invalid sidecars, digests or backing references block attachment.

### LSH-014

**Implement agent-environment construction and registration**

- Status: **To be implemented**; tracker: [#15](https://github.com/B0x-world/packer/issues/15).
- Dependencies: [LSH-003](#lsh-003), [LSH-005](#lsh-005), [LSH-013](#lsh-013), [LSH-018](#lsh-018), [LSH-024](#lsh-024)
- Sources: [S1], [S3], [S7], [S12]. Functional coverage: F07, F08.

**Present state / gap:** Git CI Builder has agent guards and schemas; provider-runner installation/execution and complete registration are missing.

**Investigation:** Inspect official runner installation/update requirements and GCB repository-identity, filesystem and credential contracts.

**Implementation:** Create versioned environment images with pinned runner/portable tools, ext4 UUID/label checks, dynamic mounts, declared writable paths and explicit registration/deregistration/replacement operations.

**Documentation:** Document environment compatibility, runner identities/labels, token input and repository changes.

**Acceptance:**

- [ ] Registration state stays out of reusable OS-base images and only one active VM uses a registration.
- [ ] Runner/version/tool inventory and filesystem ownership match the environment manifest.

### LSH-015

**Implement the bounded RAM workspace and resource policy**

- Status: **To be implemented**; tracker: [#16](https://github.com/B0x-world/packer/issues/16).
- Dependencies: [LSH-003](#lsh-003), [LSH-006](#lsh-006), [LSH-013](#lsh-013)
- Sources: [S1], [S3], [S6], [S7]. Functional coverage: F10.

**Present state / gap:** Resource calculations exist in Git CI Builder; actual tmpfs lifecycle and session qualification remain incomplete.

**Investigation:** Inspect guest memory pressure, workspace executable behavior and mount/swap support on exact targets.

**Implementation:** Implement host/guest reserves, bounded workspace sizing, tmpfs creation/identity/permissions, runner binding, emptiness checks, monitoring and disposal; reject impossible budgets.

**Documentation:** Document sizing inputs, memory competition, failure behavior and why no fixed large ramdisk is assumed.

**Acceptance:**

- [ ] Jobs run with an empty executable workspace at the required path and no guest swap.
- [ ] Memory/workspace failures terminate safely and no job data persists into the next session.

## E. Runtime, servicing and channels

### LSH-016

**Implement host/guest runtime orchestration and job admission**

- Status: **To be implemented**; tracker: [#17](https://github.com/B0x-world/packer/issues/17).
- Dependencies: [LSH-003](#lsh-003), [LSH-013](#lsh-013), [LSH-015](#lsh-015), [LSH-018](#lsh-018), [LSH-019](#lsh-019), [LSH-022](#lsh-022), [LSH-023](#lsh-023)
- Sources: [S1], [S4], [S7], [S9]. Functional coverage: F05, F09, F17.

**Present state / gap:** Git CI Builder has partial direct-QEMU and dispatch paths; runtime gates and integration are incomplete.

**Investigation:** Inspect every launch path, gate bypass, VM owner, dispatcher and prerequisite ordering; separate Packer build VM ownership from runtime VM ownership.

**Implementation:** Integrate host plans/locks/selection/monitoring with guest control validation, environment mount, readiness, workspace and permitted runner launch; default to one VM/session.

**Documentation:** Document host/guest responsibilities, startup/shutdown ordering and operation results.

**Acceptance:**

- [ ] Every launch path enforces the required profile gates.
- [ ] Missing/invalid host requests never start the runner; correlated state is recovered before cleanup.

### LSH-017

**Implement service integration and image-servicing modes**

- Status: **To be implemented**; tracker: [#18](https://github.com/B0x-world/packer/issues/18).
- Dependencies: [LSH-006](#lsh-006), [LSH-016](#lsh-016)
- Sources: [S1], [S5], [S7]. Functional coverage: F03, F11, F12.

**Present state / gap:** Boot intents and service-placement contracts are designed; complete service and maintenance dispatch is not implemented.

**Investigation:** Map run, batch, validate, update-os, update-agent, update-all, register, maintenance-shell and disable intents to the versioned schemas and service-manager operations.

**Implementation:** Install guest dispatcher/runtime/service integration; implement one-shot and maintenance operation composition, controlled reboot/resume, health/order checks and runner suppression during servicing.

**Documentation:** Document mode transitions, service units, allowed access and the separate status of any future host daemon/API.

**Acceptance:**

- [ ] Each supported intent follows its declared writable-state and job-admission policy.
- [ ] Maintenance access is revoked before sealing; unknown modes and unhealthy required services fail.

### LSH-018

**Implement protected injection and authenticated boot control**

- Status: **To be implemented**; tracker: [#19](https://github.com/B0x-world/packer/issues/19).
- Dependencies: [LSH-003](#lsh-003), [LSH-013](#lsh-013)
- Sources: [S1], [S4], [S7], [S12]. Functional coverage: F11.

**Present state / gap:** Git CI Builder checks some HMAC/request records; actual protected control-media/secret transport is incomplete.

**Investigation:** Inspect request parsing, control-media protection, session secrets, replay/freshness checks and platform transport constraints.

**Implementation:** Implement data-only configuration/control delivery, read-only boot media, single-session authentication, constrained dispatch and protected single-use secret injection.

**Documentation:** Document channel trust boundaries, request lifecycle, adapter obligations and failure responses.

**Acceptance:**

- [ ] Replayed, stale, mismatched or malformed requests are rejected.
- [ ] Request/configuration data is never evaluated as shell and control material cannot be changed by the job account.

### LSH-019

**Implement readiness, result and diagnostic reporting**

- Status: **To be implemented**; tracker: [#20](https://github.com/B0x-world/packer/issues/20).
- Dependencies: [LSH-003](#lsh-003), [LSH-018](#lsh-018), [LSH-024](#lsh-024)
- Sources: [S1], [S7], [S10], [S12]. Functional coverage: F05, F11, F17.

**Present state / gap:** Record schemas and some diagnostic helpers exist; end-to-end result retrieval/acknowledgement is missing.

**Investigation:** Inspect QEMU Guest Agent operations, serial output, result authentication, persistence and host/guest correlation.

**Implementation:** Implement readiness/lifecycle queries, structured result retrieval, acknowledgement, redacted diagnostic bundles and failure handling when results are lost or cannot persist.

**Documentation:** Document machine-result versus log channels, output formats and troubleshooting evidence.

**Acceptance:**

- [ ] Unauthenticated or wrong-session results cannot satisfy a gate.
- [ ] Missing results or persistence failure produce failure/quarantine rather than apparent success.

### LSH-020

**Implement independent every-boot state reporting**

- Status: **To be implemented**; tracker: [#21](https://github.com/B0x-world/packer/issues/21).
- Dependencies: [LSH-003](#lsh-003), [LSH-018](#lsh-018), [LSH-019](#lsh-019), [LSH-023](#lsh-023), [LSH-028](#lsh-028)
- Sources: [S1], [S3], [U1]. Functional coverage: F11, F17.

**Present state / gap:** This was deferred in GCB v1 but is explicitly requested for this distribution and remains to be implemented.

**Investigation:** Define transport, authentication, payload bounds, freshness, acknowledgement, restart/loss behavior and evidence independent of the provider runner.

**Implementation:** Implement a boot/runtime-state reporter for declared configuration, security and services, and a host startup gate before job admission; qualify platform adapters.

**Documentation:** Publish the reporting protocol, state model and fault/recovery procedures.

**Acceptance:**

- [ ] The host receives a fresh correlated report on every supported boot before accepting jobs.
- [ ] Missing, stale or contradictory reports fail the gate; runner logs cannot substitute for the report.

### LSH-021

**Implement single-job and bounded-batch execution**

- Status: **To be implemented**; tracker: [#22](https://github.com/B0x-world/packer/issues/22).
- Dependencies: [LSH-014](#lsh-014), [LSH-015](#lsh-015), [LSH-016](#lsh-016), [LSH-017](#lsh-017), [LSH-019](#lsh-019), [LSH-027](#lsh-027)
- Sources: [S1], [S6]. Functional coverage: F09, F10.

**Present state / gap:** Session and cleanup contracts plus record checks exist; complete runner/hook/session execution remains missing.

**Investigation:** Inspect provider hooks, account privileges, job completion correlation and contamination detection.

**Implementation:** Implement single-job default and explicit same-agent batch limits, synchronous hooks, strict cleanup, controlled shutdown and disposal of overlays/workspace.

**Documentation:** Document session limits, cleanup scope, contamination results and registration exclusivity.

**Acceptance:**

- [ ] Missing/degraded/stale cleanup, hook/runner crash or lost completion ends the session.
- [ ] Successive sessions have fresh overlays/workspaces and batch execution cannot switch repository/environment.

## F. Security and network

### LSH-022

**Implement phase-specific network configuration and enforcement**

- Status: **To be implemented**; tracker: [#23](https://github.com/B0x-world/packer/issues/23).
- Dependencies: [LSH-003](#lsh-003), [LSH-009](#lsh-009), [LSH-012](#lsh-012)
- Sources: [S1], [S7], [U1]. Functional coverage: F04, F15.

**Present state / gap:** Network intent/evidence records exist; actual external/guest enforcement is not complete.

**Investigation:** Inspect owned/adopted local and cloud networking, required endpoints, external infrastructure prerequisites and phase-specific access.

**Implementation:** Implement declared attachment/DNS/time/routes/egress policy, no normal inbound or lateral traffic, external plus guest enforcement, and time-bounded maintenance access.

**Documentation:** Document ownership, permitted traffic, deployment prerequisites and validation probes per phase/backend.

**Acceptance:**

- [ ] Permitted connectivity works and prohibited inbound/lateral/egress paths are demonstrably blocked.
- [ ] NAT or successful downloads cannot be accepted as enforcement evidence.

### LSH-023

**Implement declared hardening and privilege boundaries**

- Status: **To be implemented**; tracker: [#24](https://github.com/B0x-world/packer/issues/24).
- Dependencies: [LSH-003](#lsh-003), [LSH-004](#lsh-004), [LSH-006](#lsh-006)
- Sources: [S1], [S3], [S7], [S11]. Functional coverage: F15.

**Present state / gap:** Security requirements exist; complete enforcement and live conformance are unqualified.

**Investigation:** Map controls to exact target-specific accounts, services, files, mounts, kernel settings and listeners; inspect existing control schemas and B3 gaps.

**Implementation:** Implement minimal declared guest state, locked runner identity, narrow privileged operations, protected persistent paths and host-preservation controls.

**Documentation:** Publish the threat model, control catalogue, target coverage and explicit Secure Boot/at-rest-encryption limitations.

**Acceptance:**

- [ ] Each required control has a live check and a demonstrated negative detection case.
- [ ] Jobs cannot use unrestricted sudo, mutate undeclared persistent paths or activate undeclared services.

### LSH-024

**Implement secret handling, redaction and log lifecycle**

- Status: **To be implemented**; tracker: [#25](https://github.com/B0x-world/packer/issues/25).
- Dependencies: [LSH-003](#lsh-003)
- Sources: [S1], [S10], [S12]. Functional coverage: F15, F17.

**Present state / gap:** Secret/redaction requirements and tests exist in GCB; complete transport/storage/log enforcement and a retention decision are missing.

**Investigation:** Inspect tokens, persistent provider credentials, Packer/provisioner logs and diagnostic export paths; resolve log retention separately from image retention.

**Implementation:** Implement protected token input/deletion, narrow credential access, redaction-before-retention and result persistence rules across build/runtime/debug flows.

**Documentation:** Document secret lifetimes, permitted storage, retention policy and incident/diagnostic handling.

**Acceptance:**

- [ ] Secrets never appear in retained plans, manifests, results or diagnostic bundles.
- [ ] Acknowledged/failed single-use delivery removes temporary material; redaction/persistence failure blocks retention or completion.

## G. Updates, lifecycle and recovery

### LSH-025

**Implement updates and resumable image servicing**

- Status: **To be implemented**; tracker: [#26](https://github.com/B0x-world/packer/issues/26).
- Dependencies: [LSH-006](#lsh-006), [LSH-010](#lsh-010), [LSH-013](#lsh-013), [LSH-014](#lsh-014), [LSH-017](#lsh-017)
- Sources: [S1], [S3], [S5]. Functional coverage: F12.

**Present state / gap:** Update workflows are specified; complete host/guest/runner/artifact/plugin servicing is absent.

**Investigation:** Inspect native update/reboot behavior, runner self-updates, source replacement, compatibility and host-specific recovery.

**Implementation:** Implement controlled OS/environment candidates, explicit distribution/host/plugin updates, versioned config changes and bounded identity-matched resume; never update immutable sources during normal jobs.

**Documentation:** Document update matrices, disruption/reboot behavior, pinned input changes and recovery procedures.

**Acceptance:**

- [ ] Guest/runner updates produce new validated generations and preserve known-good state.
- [ ] Host update failures use a declared host recovery procedure; guest rollback is not misrepresented as host rollback.

### LSH-026

**Implement candidate qualification, atomic promotion and rollback**

- Status: **To be implemented**; tracker: [#27](https://github.com/B0x-world/packer/issues/27).
- Dependencies: [LSH-013](#lsh-013), [LSH-025](#lsh-025), [LSH-028](#lsh-028), [LSH-029](#lsh-029)
- Sources: [S1], [S3], [S12]. Functional coverage: F06, F13, F14.

**Present state / gap:** In-memory lifecycle logic and manifests exist; real two-stage validation and durable active-state transactions are missing.

**Investigation:** Inspect candidate sealing, flattening, evidence currency, locks and atomic durability requirements.

**Implementation:** Implement fresh-overlay candidate validation, standalone-generation validation, required GCB canary gating, durable atomic active.json selection and explicit audited rollback with inherited retention.

**Documentation:** Document promotion/rollback runbooks, artifact identities, approvals and failed-candidate behavior.

**Acceptance:**

- [ ] Promotion cannot proceed with missing/stale evidence or builder-only boot state.
- [ ] Active/source files remain immutable; selection changes atomically and rollback requires explicit intent.

### LSH-027

**Implement cancellation, orphan recovery and quarantine**

- Status: **To be implemented**; tracker: [#28](https://github.com/B0x-world/packer/issues/28).
- Dependencies: [LSH-013](#lsh-013), [LSH-019](#lsh-019), [LSH-024](#lsh-024)
- Sources: [S1], [S3], [S6], [P1]. Functional coverage: F05, F14.

**Present state / gap:** Cleanup/quarantine record checks exist; real process/attachment/resource recovery is incomplete.

**Investigation:** Inspect Packer cancellation/on-error behavior, VM attachment detection, orphan discovery and diagnostics before cleanup.

**Implementation:** Implement owned-resource accounting, stop confirmation, redacted recovery, read-only quarantine and dependency-aware cleanup; preserve active/attached/locked/backing-referenced artifacts.

**Documentation:** Document interruption and quarantine runbooks, explicit cleanup and the inherited orphan-threshold semantics.

**Acceptance:**

- [ ] Cancellation or failed boot cannot delete a live VM's disks or reusable sources.
- [ ] Failed/interrupted sessions cannot be reused, and every residual resource has accountable state.

## H. Validation and qualification

### LSH-028

**Implement configuration, contract and validator qualification**

- Status: **To be implemented**; tracker: [#29](https://github.com/B0x-world/packer/issues/29).
- Dependencies: [LSH-003](#lsh-003), [LSH-005](#lsh-005), [LSH-006](#lsh-006), [LSH-023](#lsh-023)
- Sources: [S1], [S3], [S9], [S11], [S12]. Functional coverage: F13, F17.

**Present state / gap:** Existing Packer validate and GCB schemas/fixtures are reusable but do not prove platform or runtime conformance.

**Investigation:** Map requirements to schema, semantic, contract, negative and domain checks; inspect validator versioning and manual-evidence rules.

**Implementation:** Implement traceable tests for configs, resolved graphs, packages/services/hardening, invalid requests, stale evidence and bad artifacts; qualify replacement validators by known-good/bad corpus and shadow runs.

**Documentation:** Publish the requirements-to-validation matrix, evidence contract, validator-change and revalidation procedure.

**Acceptance:**

- [ ] Every mandatory requirement has a named test/failure condition and retained evidence.
- [ ] Schema/static passes cannot stand in for live qualification; changed validators invalidate affected evidence.

### LSH-029

**Implement real build, runtime and GitHub canary gates**

- Status: **To be implemented**; tracker: [#30](https://github.com/B0x-world/packer/issues/30).
- Dependencies: [LSH-012](#lsh-012), [LSH-014](#lsh-014), [LSH-015](#lsh-015), [LSH-016](#lsh-016), [LSH-017](#lsh-017), [LSH-021](#lsh-021), [LSH-022](#lsh-022), [LSH-023](#lsh-023), [LSH-027](#lsh-027), [LSH-028](#lsh-028)
- Sources: [S1], [S3], [S6], [S9]. Functional coverage: F03, F09, F13.

**Present state / gap:** GCB reports missing canary evidence and launch paths that bypass recorded gates; no qualified distribution run exists.

**Investigation:** Identify all launch/release paths and required physical-host/platform fixtures; distinguish TCG observations from KVM qualification.

**Implementation:** Run and integrate real KVM builds, independent candidate/standalone boots, configuration/security checks, unique-label GitHub canary and cleanup/recovery tests. Enforce outcomes at every applicable entry point.

**Documentation:** Record reproducible environment prerequisites, exact tested tuples, failures and evidence locations.

**Acceptance:**

- [ ] All launch and promotion entry points enforce their required gates.
- [ ] A real end-to-end job and negative/recovery cases pass for each released tuple; skipped tests remain unqualified.

## I. Debugging

### LSH-030

**Create the debugging branch and diagnostic workflow**

- Status: **To be implemented**; tracker: [#31](https://github.com/B0x-world/packer/issues/31).
- Dependencies: [LSH-001](#lsh-001), [LSH-019](#lsh-019), [LSH-023](#lsh-023), [LSH-024](#lsh-024), [LSH-027](#lsh-027)
- Sources: [U1], [S3], [S9]. Functional coverage: F14, F17.

**Present state / gap:** The debug branch/profile is designed but has not been created or implemented.

**Investigation:** Define branching from a recorded release commit, safe diagnostic changes, output segregation and fault-reproduction requirements.

**Implementation:** Create debug/linux-self-hosted when implementation begins; add controlled diagnostic profiles/instrumentation and transfer reviewed fixes through normal release qualification.

**Documentation:** Publish debugging, fault injection, sanitized evidence collection and fix-return procedures.

**Acceptance:**

- [ ] Debug artifacts cannot be selected/promoted as production images.
- [ ] A reproduced failure captures exact inputs/platform facts and the reviewed fix passes normal qualification.

## J. Swappable platforms and deployment

### LSH-031

**Implement independently selectable build-host and guest OS adapters**

- Status: **To be implemented**; tracker: [#32](https://github.com/B0x-world/packer/issues/32).
- Dependencies: [LSH-003](#lsh-003), [LSH-004](#lsh-004), [LSH-005](#lsh-005), [LSH-006](#lsh-006), [LSH-009](#lsh-009), [LSH-028](#lsh-028)
- Sources: [S1], [S2], [S4], [U1]. Functional coverage: F02, F03.

**Present state / gap:** v1 schemas constrain Ubuntu 24.04; expanded host/guest choices are a requested implementation requirement.

**Investigation:** Choose additional exact Linux targets and map package/service/boot/architecture behavior while keeping build-host and deployment-host identities separate.

**Implementation:** Implement independently selectable host/guest definitions and adapters with reusable common components and explicit capability negotiation; qualify target combinations.

**Documentation:** Publish the support matrix, adding-a-target guide and incompatible-combination diagnostics.

**Acceptance:**

- [ ] Changing the supported host OS does not require rewriting the guest profile, or vice versa.
- [ ] Each offered exact combination has evidence; family inheritance alone never grants support.

### LSH-032

**Implement swappable VM/build platforms and artifact adapters**

- Status: **To be implemented**; tracker: [#33](https://github.com/B0x-world/packer/issues/33).
- Dependencies: [LSH-003](#lsh-003), [LSH-012](#lsh-012), [LSH-013](#lsh-013), [LSH-018](#lsh-018), [LSH-019](#lsh-019), [LSH-028](#lsh-028)
- Sources: [S3], [S4], [P2], [U1]. Functional coverage: F04, F05, F06.

**Present state / gap:** QEMU is the initial backend; other VM platforms and equivalent storage/control semantics are unimplemented.

**Investigation:** Select additional backends, inspect Packer plugins versus runtime VM adapters, and map firmware/drivers/image formats/overlays/control/reporting/cleanup capabilities.

**Implementation:** Implement the platform boundary and required export/import/conversion adapters; preserve immutable-source/disposable-write semantics or reject incompatible profiles.

**Documentation:** Publish backend contracts, migration guidance and tested compatibility/equivalence limits.

**Acceptance:**

- [ ] Backend selection uses stable contracts without rewriting profile intent.
- [ ] Converted/exported artifacts receive new identities and destination validation; unsupported semantics fail explicitly.

### LSH-033

**Implement cloud and self-hosted deployment choices**

- Status: **To be implemented**; tracker: [#34](https://github.com/B0x-world/packer/issues/34).
- Dependencies: [LSH-022](#lsh-022), [LSH-024](#lsh-024), [LSH-027](#lsh-027), [LSH-031](#lsh-031), [LSH-032](#lsh-032)
- Sources: [S1], [S4], [U1]. Functional coverage: F04, F05, F15.

**Present state / gap:** Self-hosted is the initial design; cloud execution/deployment remains required backlog work.

**Investigation:** Select a cloud integration, distinguish cloud-hosted QEMU from cloud-native builders, and assess identity, acceleration, resource cost, networks, storage and reporting.

**Implementation:** Implement location adapters for owned account/project/region resources, pinned build inputs, deployment, cancellation and orphan cleanup; qualify supported host/guest/backend/location tuples.

**Documentation:** Document credentials, external prerequisites, resource ownership/cost controls and supported cloud/self-hosted combinations.

**Acceptance:**

- [ ] The same compatible profile can be resolved for qualified self-hosted and cloud locations.
- [ ] Wrong account/region, missing isolation or unavailable nested acceleration fails before unsafe resource use.

## K. Repository, documentation and delivery

### LSH-034

**Adapt imported CI, issue templates and release workflows**

- Status: **To be implemented**; tracker: [#35](https://github.com/B0x-world/packer/issues/35).
- Dependencies: [LSH-001](#lsh-001), [LSH-028](#lsh-028)
- Sources: [P1], [U2].

**Present state / gap:** Upstream workflows/templates are imported; they are not tailored to this fork or its implementation tracker.

**Investigation:** Audit release/** triggers, scheduling, external integrations, issue automation, permissions, secrets requirements and stale-issue handling against the new workstream.

**Implementation:** Adapt only required fork workflows/templates; add scoped configuration/contract/integration checks and gate publication on qualified tuples. Preserve main protection and target release-branch PRs.

**Documentation:** Document contributor workflow, issue lifecycle, safe CI execution and manual infrastructure-test authorization.

**Acceptance:**

- [ ] Imported automation cannot silently close required backlog work or publish an unqualified distribution.
- [ ] CI validates the relevant layers and release workflows use explicit fork-owned destinations.

### LSH-035

**Merge and maintain all feature documentation and traceability**

- Status: **To be implemented**; tracker: [#36](https://github.com/B0x-world/packer/issues/36).
- Dependencies: [LSH-001](#lsh-001), [LSH-003](#lsh-003)
- Sources: [S1], [S2], [S3], [S4], [S5], [S6], [S7], [S8], [S9], [S10], [S11], [S12], [S13], [S14], [P1], [P3], [U1], [U2]. Functional coverage: F01, F02, F03, F04, F05, F06, F07, F08, F09, F10, F11, F12, F13, F14, F15, F16, F17.

**Present state / gap:** The expanded design consolidates concerns, but implementation runbooks and migrated contract documentation are incomplete.

**Investigation:** Reconcile imported Packer docs, Git CI Builder authoritative contracts, the helper assessment and PackerDocs; identify contradictions and documentation that must change with code.

**Implementation:** Update architecture, interfaces, examples, configuration/state diagrams, install/update/servicing/debug/operator guides and requirements-to-issue-to-test mapping alongside implementation. Record any required GCB-side documentation changes as explicitly scoped cross-repo work.

**Documentation:** Keep TO-BE-IMPLEMENTED.md and the design synchronized with linked issue status, implemented paths, supported combinations and evidence; preserve source provenance and supersession.

**Acceptance:**

- [ ] Every requested feature has a linked issue, implementation reference, validation reference and current documentation before closure.
- [ ] No requested GUI/reporter/cloud/platform feature disappears under an untracked deferred label.

### LSH-036

**Package and release the qualified distribution**

- Status: **To be implemented**; tracker: [#37](https://github.com/B0x-world/packer/issues/37).
- Dependencies: [LSH-008](#lsh-008), [LSH-009](#lsh-009), [LSH-010](#lsh-010), [LSH-020](#lsh-020), [LSH-026](#lsh-026), [LSH-029](#lsh-029), [LSH-030](#lsh-030), [LSH-031](#lsh-031), [LSH-032](#lsh-032), [LSH-033](#lsh-033), [LSH-034](#lsh-034), [LSH-035](#lsh-035)
- Sources: [S1], [S9], [P1], [U2].

**Present state / gap:** No distribution package or qualified release exists; branch creation and documentation are not releases.

**Investigation:** Define packaged components, exact dependency/schema inputs, supported matrix, update compatibility and inherited license/notice/release prerequisites.

**Implementation:** Package installer, profiles, templates, adapters/runtime integration, plugin requirements and versioned contracts; produce inventories/checksums and a reproducible release process after required gates pass.

**Documentation:** Publish release notes, supported feature/target matrix, installation/update/rollback instructions and remaining limitations.

**Acceptance:**

- [ ] A clean supported installation can build and service the qualified profiles from pinned inputs.
- [ ] Release artifacts include traceable dependencies and evidence; licensing or runtime blockers are resolved rather than assumed.

## Feature coverage from Git CI Builder

The source functional decomposition [S8] defines F01–F17. Documentation reconciliation LSH-035 applies to every row in addition to the implementation owners listed here.

| Concern | Implementation items |
|---|---|
| F01 Configuration and repository scoping | [LSH-003](#lsh-003), [LSH-004](#lsh-004), [LSH-005](#lsh-005), [LSH-007](#lsh-007) |
| F02 Host OS preparation | [LSH-004](#lsh-004), [LSH-006](#lsh-006), [LSH-009](#lsh-009), [LSH-031](#lsh-031) |
| F03 Guest provisioning/preparation | [LSH-004](#lsh-004), [LSH-006](#lsh-006), [LSH-011](#lsh-011), [LSH-012](#lsh-012), [LSH-017](#lsh-017), [LSH-029](#lsh-029), [LSH-031](#lsh-031) |
| F04 VM provisioning | [LSH-012](#lsh-012), [LSH-013](#lsh-013), [LSH-022](#lsh-022), [LSH-032](#lsh-032), [LSH-033](#lsh-033) |
| F05 VM management | [LSH-016](#lsh-016), [LSH-019](#lsh-019), [LSH-027](#lsh-027), [LSH-032](#lsh-032), [LSH-033](#lsh-033) |
| F06 Image-set management | [LSH-005](#lsh-005), [LSH-013](#lsh-013), [LSH-026](#lsh-026), [LSH-032](#lsh-032) |
| F07 Agent installation | [LSH-014](#lsh-014) |
| F08 Agent configuration/registration | [LSH-014](#lsh-014) |
| F09 Job-session execution | [LSH-016](#lsh-016), [LSH-021](#lsh-021), [LSH-029](#lsh-029) |
| F10 RAM workspace | [LSH-015](#lsh-015), [LSH-021](#lsh-021) |
| F11 VM control transport | [LSH-017](#lsh-017), [LSH-018](#lsh-018), [LSH-019](#lsh-019), [LSH-020](#lsh-020) |
| F12 Maintenance | [LSH-006](#lsh-006), [LSH-017](#lsh-017), [LSH-025](#lsh-025) |
| F13 Validation/promotion | [LSH-026](#lsh-026), [LSH-028](#lsh-028), [LSH-029](#lsh-029) |
| F14 Recovery/rollback | [LSH-026](#lsh-026), [LSH-027](#lsh-027), [LSH-030](#lsh-030) |
| F15 Security enforcement | [LSH-022](#lsh-022), [LSH-023](#lsh-023), [LSH-024](#lsh-024), [LSH-033](#lsh-033) |
| F16 Operator interface | [LSH-007](#lsh-007), [LSH-008](#lsh-008) |
| F17 State/status/evidence | [LSH-003](#lsh-003), [LSH-005](#lsh-005), [LSH-006](#lsh-006), [LSH-007](#lsh-007), [LSH-016](#lsh-016), [LSH-019](#lsh-019), [LSH-020](#lsh-020), [LSH-024](#lsh-024), [LSH-028](#lsh-028), [LSH-030](#lsh-030) |

| Additional explicitly requested concern | Implementation items |
|---|---|
| Investigation and modifications of imported repository | LSH-001, LSH-002, LSH-034 |
| Documentation updates and requirements reconciliation | LSH-035 and the documentation deliverable in every item |
| Common package set, exact distro/version definitions and version tracking | LSH-004, LSH-005, LSH-006 |
| Lightweight TUI and GUI | LSH-007, LSH-008 |
| QEMU plugin and plugin management | LSH-010, LSH-012 |
| Debugging branch and diagnostic workflow | LSH-030 |
| Independent every-boot reporting | LSH-020 |
| Swappable builder OS / guest OS / VM platform | LSH-031, LSH-032 |
| Cloud and self-hosted deployment | LSH-033 |

## Scope limits retained from the source

Additional GUI/reporting/platform/cloud requirements are now implementation items despite their absence or deferral in the old v1 design. This is an explicit extension of that workstream.

Unrequested expansions such as public multi-tenant hosting, automatic fleet scaling, persistent caches, Secure Boot, at-rest encryption, ISO/autoinstall, a remote service API or extra image retention do not become implementation commitments merely because the source mentions them. Security/platform investigations must record whether a requested feature creates a concrete need for one; any resulting scope addition requires an explicit recorded decision and linked work item.

## Source references

[S1]–[S14] preserve the same source authority and revisions as the architecture document. [P1]–[P3] identify implementation/reference repositories; inspection must record exact revisions rather than assume current documentation proves compatibility.

- **U1:** User's 2026-10-05 expanded requirements, consolidated in the [design baseline](https://github.com/B0x-world/packer/blob/7d18b124d110779ccbb337dbc26ada6ec7e1c80d/contrib/linux-self-hosted/README.md).
- **U2:** User's 2026-10-05 request to merge requirements, record missing features as to be implemented, include imported-code investigation/modifications and documentation updates, and populate this repository's issue tracker.

[S1]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/docs/architecture/v1-operational-contract.md
[S2]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/docs/architecture/definition-standard.md
[S3]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/docs/architecture/image-storage-standard.md
[S4]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/docs/architecture/reusable-engines.md
[S5]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/docs/functions/os-preparation.md
[S6]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/docs/job-execution-and-cleanup.md
[S7]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/docs/concept.md
[S8]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/docs/functionality.md
[S9]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/docs/implementation/stage-status.md
[S10]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/docs/log-retention.md
[S11]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/schemas/v1/configuration.json
[S12]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/schemas/v1/runtime.json
[S13]: https://github.com/B0x-world/Git-CI-builder/blob/dabb0dec437d84aba4f642da33a931004895c4ba/docs/decisions-and-open-questions.md
[S14]: https://github.com/B0x-world/Git-CI-builder/blob/5e970e107aa96e70a0310219b74e4e3c36594117/docs/architecture/packer-qemu-responsibility-boundaries.md
[P1]: https://github.com/hashicorp/packer
[P2]: https://github.com/hashicorp/packer-plugin-qemu
[P3]: https://github.com/B0x-world/PackerDocs
[U1]: https://github.com/B0x-world/packer/blob/7d18b124d110779ccbb337dbc26ada6ec7e1c80d/contrib/linux-self-hosted/README.md
[U2]: #source-references
