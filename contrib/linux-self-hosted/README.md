# Linux self-hosted build distribution

Status: design foundation for `B0x-world/packer:release/linux-self-hosted`. The Linux integration and selectable profiles described here are not implemented by this documentation commit.

Baseline: fork `main` at `4bb4b91351b5a62ed4c1df26ff230aecda5bfbe6`, inspected 2026-10-05 (Australia/Perth). This repository is a fork of [hashicorp/packer](https://github.com/hashicorp/packer).

## Goal

Provide a complete Linux-hosted image-building distribution around Packer and the official QEMU plugin, with reusable host setup, image templates, provisioning helpers, validation and packaging. Git CI Builder's configuration is one selectable profile.

The shared support must be usable without selecting the Git CI Builder profile. The first qualified host and guest target for that profile remains Ubuntu 24.04 x64. Further Linux targets require their own dependency mapping and verification; the branch name does not assert support for every distribution.

## Branch ownership

| Branch | Purpose | Allowed change flow |
|---|---|---|
| `main` | Upstream mirror | Deliberately authorized upstream synchronization only |
| `release/linux-self-hosted` | Maintained Linux integration and distribution | Reviewed integration work and selected upstream updates |
| Topic branches based on the release branch | Bounded implementation changes | Pull requests target `release/linux-self-hosted` |

Project work must not merge into `main`. Upstream synchronization and adoption into the release branch are separate operations. Record the adopted upstream revision and rerun relevant compatibility checks when it changes.

Creating this branch does not publish a release or establish a qualified Packer version. Distribution versions, upstream pins and release gates are selected during implementation.

## Main protection: required administrator action

Inspection found the inherited, active organization ruleset [protect-main](https://github.com/B0x-world/packer/rules/24319993). It blocks deletion and force pushes and requires a pull request. It does **not** block all PR merges. The repository still permits rebase merging.

Add a repository branch ruleset with this intended configuration:

| Setting | Value |
|---|---|
| Name | `lock-main-upstream-mirror` |
| Enforcement | Active |
| Target | Exactly `refs/heads/main` |
| Restrict updates | Enabled |
| Restrict deletions | Enabled |
| Block force pushes | Enabled |
| Bypass actors | None by default |

A fully locked mirror also blocks upstream synchronization. Any future synchronization must be deliberately enabled by an administrator, performed against a verified upstream revision, and followed by restoration and verification of the lock. The integration branch must not gain authority to update `main`.

GitHub's [ruleset documentation](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets) describes update restrictions and bypass permissions.

**Pending:** the connected account has repository write access but no administration access, and the available connector cannot edit repository rules. This file documents the required setting; it does not enforce it.

## Component responsibilities

| Component | Responsibility | Boundary |
|---|---|---|
| Packer core in this fork | Template evaluation, build orchestration and plugin integration | Reusable Linux distribution behavior should use existing extension points where possible; core changes require an identified capability gap |
| Official [QEMU plugin](https://github.com/hashicorp/packer-plugin-qemu) | Build VM, QEMU disk operations, provisioner connection and image artifact production | Separate versioned dependency; it is not automatically included by forking Packer |
| Linux distribution integration | Dependency setup, supported-host checks, profile selection, build invocation, evidence collection and packaging | Owns the build experience; does not silently manage unrelated host state |
| Project provisioning scripts and selected upstream helpers | Apply and verify the selected guest state | Helpers provide primitives; the profile supplies desired state and policy |
| Image profile | Source image, hardware requirements, packages, services, provisioning order and validation requirements | Selected explicitly; no Git CI Builder policy hard-coded into Packer core |
| Git CI Builder runtime | Agent registration/environment, image acceptance and promotion, job sessions, workspace and cleanup | Remains a separate consumer of the image artifacts |
| [PackerDocs](https://github.com/B0x-world/PackerDocs) | Semantic reference and source traceability | Knowledge source; not a runtime dependency or proof of compatibility |

The QEMU plugin must be included in implementation review, dependency installation, version pinning, compatibility checks and release validation. Its repository is `hashicorp/packer-plugin-qemu`, its HCL dependency source is `github.com/hashicorp/qemu`, and its builder type is `qemu`. The host QEMU binary and KVM capability are separate prerequisites.

Packer and the QEMU plugin own the temporary build VM. A consuming runtime owns later validation and job VMs. Successful image construction alone does not establish that an image is accepted or active in Git CI Builder.

## Selectable configuration

Two initial profile roles are proposed:

| Profile | Purpose |
|---|---|
| `linux-base` | Generic Linux/QEMU image construction and common guest preparation without Git CI Builder agent/session policy |
| `git-ci-builder` | Compose the common support with the existing Git CI Builder OS-base, guest runtime and validation requirements |

These are proposed distribution profiles, not existing upstream Packer profiles. Packer's normal template interface remains available.

A profile selects a compatible template, a provisioner sequence, pinned source/dependency data and validation requirements. Selection must be explicit and resolved inputs must be recorded with the build. Shared implementation is reused between profiles; selecting one profile must not activate another profile's services or policy.

The first implementation should use existing HCL templates and explicit variable files through a small distribution entry point. Whether a new Packer CLI feature is necessary must be justified by a concrete gap before changing the command surface or plugin SDK boundary.

Secrets are supplied separately at execution time. Repository-scoped runner registration must not be embedded in a reusable OS-base profile or image.

## Proposed layout

All paths below are planned except this README.

| Path | Purpose |
|---|---|
| `contrib/linux-self-hosted/bin/` | Distribution entry point and profile selection |
| `contrib/linux-self-hosted/host/` | Explicit Linux dependency setup and preflight |
| `contrib/linux-self-hosted/templates/` | Shared Packer/QEMU image templates |
| `contrib/linux-self-hosted/profiles/` | Generic and Git CI Builder profile definitions |
| `contrib/linux-self-hosted/provisioners/` | Common guest preparation and profile-specific composition |
| `contrib/linux-self-hosted/upstream/` | Selected helper source, revision/digest records and attribution |
| `contrib/linux-self-hosted/validation/` | Image/build integration checks |
| `contrib/linux-self-hosted/packaging/` | Distribution packaging inputs and dependency records |

Use an immutable imported revision for selected actions/runner-images helpers, retaining provenance. The existing helper assessment is recorded in the Git CI Builder boundary document linked below. Importing an entire upstream image toolset is not implied.

## Staged implementation

| Stage | Deliverable | Completion evidence |
|---|---|---|
| 0. Foundation | Release branch, responsibilities, profile model and main-lock requirement | This document; administrator confirms main lock separately |
| 1. Resolve inputs | Exact Packer/QEMU-plugin/QEMU compatibility, supported target, base image and helper selection | Recorded source revisions, checksums and capability review |
| 2. Host setup | Explicit dependency installation and preflight | Repeatable managed setup; usable KVM and required firmware/tools |
| 3. Generic image build | `linux-base` selection, provisioning and artifact receipt | A verified source produces a standalone bootable Linux image; source remains unchanged |
| 4. Git CI Builder profile | Existing OS-base requirements composed through the shared build path | Candidate boots through the consumer's validation path and meets its applicable guest requirements |
| 5. Distribution release | Package, usage documentation and fork-appropriate CI | Both profiles qualified; dependencies reproducible; failure and cleanup behavior verified |

Runtime agent-environment work, canary execution and promotion remain subject to Git CI Builder's existing gates. Stage 4 must not report a production-ready builder merely because Packer exited successfully.

Before enabling release automation, review inherited upstream workflows. The current `build`, `Go Test` and `Go Validate` workflows match `release/**`; nightly release and acceptance workflows run on schedules or manual dispatch. A branch push is not authorization to publish a distribution or run infrastructure acceptance tests.

Documentation setup requires link/content checks. Implementation validation will include invalid source hashes, missing KVM, provisioning failure, cancellation, residual VM/file ownership, temporary credential removal and independent boot of produced artifacts. Run the repository's applicable maintenance targets for Go or generated-code changes.

## Relationship to earlier design

The [Git CI Builder responsibility document](https://github.com/B0x-world/Git-CI-builder/blob/5e970e107aa96e70a0310219b74e4e3c36594117/docs/architecture/packer-qemu-responsibility-boundaries.md) records the earlier integration proposal and detailed consumer boundaries.

This fork now owns the proposed shared Linux build distribution. Its reusable build templates, dependency setup, helper integration and profile selection move into this plan. Git CI Builder retains its runtime and image-lifecycle responsibilities, and consumes this distribution through the selected profile and artifact handoff. Earlier proposed Git CI Builder-local build paths are historical proposals, not a requirement to duplicate the implementation in both repositories.
