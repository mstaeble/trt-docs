---
id: 04-repositories
title: Repositories & Codebase Orientation
order: 4
audience: new-hire

claims:
  channels: []
  urls:
    - https://docs.ci.openshift.org/
  repos:
    - openshift/release
    - openshift/origin
    - openshift/ci-tools
    - openshift/sippy
    - openshift-eng/ci-test-mapping
    - openshift/release-controller
    - openshift/ci-docs
  tools: []

substeps:
  - id: owners-system
    type: learning
    group: how-owners-work
    summary: "Understand how OWNERS files control code review and approval"

  - id: release-repo
    type: learning
    group: release-repo
    summary: "Explore the openshift/release repository"

  - id: release-ownership
    type: learning
    group: release-repo
    summary: "Locate TRT's owned areas within openshift/release"

  - id: origin-repo
    type: learning
    group: origin-and-tests
    summary: "Understand openshift/origin — the home of openshift-tests"

  - id: ci-tools-repo
    type: learning
    group: trt-tooling
    summary: "Know openshift/ci-tools and TRT's tools within it"

  - id: trt-owned-repos
    type: learning
    group: trt-tooling
    summary: "Explore TRT's fully owned repositories"

  - id: ecosystem-repos
    type: learning
    summary: "Know the supporting ecosystem repositories"

  - id: where-to-look
    type: learning
    summary: "Quick reference — where to find things"

  - id: explore-repos
    type: action
    summary: "Browse the key repos and locate TRT's OWNERS entries"
---

## Substep: owners-system

### Context

All OpenShift repositories use the Prow
(Kubernetes-based CI system — you explored its dashboard
in Step 3) OWNERS system to manage code review and merge
permissions. Before diving into specific repos, you need
to understand how this system works — it determines who
can approve your changes and whose changes you'll
eventually review.

Two file types control permissions:

**OWNERS files** — Placed in directories throughout a
repo. Each OWNERS file lists `approvers` (can `/approve`
PRs touching files in that directory and its
subdirectories) and `reviewers` (automatically assigned
for review). Prow requires both a `/lgtm` from a
reviewer and an `/approve` from an approver before a PR
can merge.

**OWNERS_ALIASES** — A single file at the repo root that
defines named groups (aliases) you can reference in
OWNERS files. This avoids duplicating long lists of
usernames across many directories. For example, the alias
`technical-release-team-approvers` is defined once in the
openshift/release `OWNERS_ALIASES` file and referenced in
OWNERS files throughout the repo.

TRT defines two aliases:
- `technical-release-team-approvers` — Members who can
  `/approve` PRs in TRT-owned directories
- `technical-release-team-reviewers` — Members who are
  automatically assigned for review

When you're added to these aliases, you gain review and
approval authority across all directories that reference
them. Your onboarding buddy or manager will submit a PR
to add your GitHub username.

---

## Substep: release-repo

### Context

[openshift/release](https://github.com/openshift/release)
is the largest and most important repo in the OpenShift
CI ecosystem. It's the central configuration hub where CI
jobs, test workflows, and release pipeline settings are
defined. Nearly every team in OpenShift has configuration
here.

Key directories you'll work in:

**`ci-operator/config/`** — CI job configuration files
organized by org and repo (e.g.
`ci-operator/config/openshift/installer/`). Each YAML
file defines how ci-operator (the tool that orchestrates
CI workflows — covered in
[CI Docs](https://docs.ci.openshift.org/)) runs tests
for a specific repo and branch. These configs are the
source of truth — the Prow job YAML files in
`ci-operator/jobs/` are auto-generated from them.

**`ci-operator/step-registry/`** — Reusable test step
definitions. Steps are the building blocks of multi-stage
CI workflows — for example, a step that provisions an
OpenShift cluster, a step that runs e2e (end-to-end)
tests, and a step that gathers artifacts after the run.
You'll encounter these when investigating how specific
verification jobs work.

**`core-services/release-controller/_releases/`** —
Release controller configuration. This is where blocking
(must pass for payload acceptance) and informing
(advisory, don't gate) verification jobs are defined for
each release stream. This is a core TRT-owned area.

**`clusters/`** — Kubernetes manifests for the CI
clusters themselves (app.ci, build farms). TRT has its
own directory at `clusters/app.ci/trt/`.

For a full guide to the repo structure, see the
[CI Docs](https://docs.ci.openshift.org/) you bookmarked
in Step 3.

---

## Substep: release-ownership

### Context

TRT has formal OWNERS approval authority over specific
directories within openshift/release. These are the areas
where the `technical-release-team-approvers` alias grants
you approval rights:

**Release verification jobs** —
`core-services/release-controller/_releases/` — TRT owns
the job definitions that control which CI jobs block
release payload acceptance. When you need to add, remove,
or modify a release-blocking job, this is where you make
the change.

**Release job configurations** —
`ci-operator/config/openshift/release/` — Contains CI
configuration YAML for periodic release verification
jobs (e.g. nightly e2e tests). Changes here require TRT
approval.

**CI step definitions** —
`ci-operator/step-registry/` — TRT has approver rights
on key step directories, including gather steps (scripts
that collect logs and artifacts from CI runs) and IPI
(Installer-Provisioned Infrastructure) steps. TRT acts
as an owner of last resort for step registry changes that
don't have a more specific owner.

**TRT cluster resources** — `clusters/app.ci/trt/` —
RBAC (Role-Based Access Control) configurations for
TRT's service accounts and permissions on the app.ci
cluster.

As you settle into the role, you'll also review changes
from other teams that touch release-blocking job
definitions — these require a TRT approver to ensure
they don't unintentionally degrade the release signal.

---

## Substep: origin-repo

### Context

[openshift/origin](https://github.com/openshift/origin)
is the home of `openshift-tests` — the binary that runs
OpenShift's end-to-end (e2e) conformance test suite. TRT
is the primary owner of this repository.

What `openshift-tests` does: it's a single binary that
orchestrates all e2e tests for an OpenShift cluster. It
ships as the `tests` component in every OCP (OpenShift
Container Platform) release payload, and customers use
it to validate their clusters.

Key directories:

**`test/extended/`** — Contains the e2e test suites,
organized by component (e.g. `test/extended/networking/`,
`test/extended/storage/`). Individual component teams own
their specific test files via OWNERS files within these
subdirectories, but TRT owns the overall framework and
structure.

**`pkg/test/`** — The core test framework logic. This is
where test execution, sharding (splitting tests across
parallel jobs), and orchestration code lives.

**`pkg/monitortests/`** — Monitoring tests that observe
cluster behavior during test runs (e.g. tracking API
server disruption, operator restarts). TRT uses this
data for disruption analysis (covered in Step 6).

**`pkg/test/extensions/`** — The OTE (OpenShift Tests
Extension) framework. OTE is an evolving model where
component teams build test binaries in their own repos,
and `openshift-tests` discovers and runs them via
registration in this directory. TRT manages this
orchestration layer.

TRT's role in origin:
- Maintaining the test framework and execution
  infrastructure
- Reviewing test changes that affect release signal
  quality
- Ensuring test naming is consistent and tests are
  properly categorized
- Managing the transition toward the OTE model

You won't write many tests yourself — component teams
own their tests. But you'll frequently investigate test
failures by reading test code here, and you'll review
PRs (pull requests) that affect the framework or test
signal quality.

---

## Substep: ci-tools-repo

### Context

[openshift/ci-tools](https://github.com/openshift/ci-tools)
contains the tooling that powers OpenShift's CI platform.
The repo is primarily owned by DPTP (Test Platform — the
team that provides CI infrastructure), but TRT owns
specific tools within it.

TRT's key area:

**`pkg/jobrunaggregator/`** — The `job-run-aggregator`
tool, which collects and analyzes CI job data. It
aggregates results from multiple job runs and uploads
metrics (including backend disruption data) to BigQuery
(Google's data warehouse) for analysis. TRT uses this
data for disruption reviews and trend analysis. This
directory has its own OWNERS file listing TRT approvers.

You'll also encounter ci-tools when working on CI
infrastructure changes, since tools like `ci-operator`
(the engine that runs CI workflows) and
`config-brancher` (which manages configuration across
release branches) live here. You don't need to master
these, but knowing they exist helps when you need to
understand CI behavior or trace a problem.

---

## Substep: trt-owned-repos

### Context

TRT fully owns two additional repositories:

**[openshift/sippy](https://github.com/openshift/sippy)**
— The source code for the Sippy dashboard you explored
in Step 3. Sippy is a Go application that ingests CI job
results and provides Component Readiness analysis
(regression detection — covered in Step 7), test pass
rate trends, and release readiness assessments. TRT
members submit PRs directly to this repo for feature
development and bug fixes.

**[openshift-eng/ci-test-mapping](https://github.com/openshift-eng/ci-test-mapping)**
— Defines the mapping between tests and components.
This mapping is what allows Sippy's Component Readiness
view to attribute test failures to the correct component
team. When a component team needs to update which tests
map to their component, they open a PR here and TRT
reviews it.

Note: `ci-test-mapping` is in the `openshift-eng` GitHub
organization, which requires separate org membership from
the `openshift` org. Your onboarding buddy should have
arranged this access in Step 0. If you can't access the
repo, see the access note at the end of this step.

---

## Substep: ecosystem-repos

### Context

A few more repositories you'll encounter:

**[openshift/release-controller](https://github.com/openshift/release-controller)**
— The source code for the release controller whose
dashboard you use daily (Step 3). CRT (Continuous Release
Tooling) owns this repo, but TRT occasionally interacts
with it when proposing changes to payload management or
release verification behavior.

**[openshift/ci-docs](https://github.com/openshift/ci-docs)**
— The source for the
[CI Docs](https://docs.ci.openshift.org/) site. When you
need to look up how ci-operator workflows, step registry
steps, or Prow job configuration work, the published
docs site is the reference. If you find documentation
gaps, you can submit a PR to this repo.

You don't need to develop in these repos, but knowing
where the source code lives helps when you need to
understand behavior or trace a problem.

---

## Substep: where-to-look

### Context

A quick reference for common TRT tasks — where to go:

**"I need to add or modify a release-blocking job"**
→ `openshift/release` at
`core-services/release-controller/_releases/`

**"I need to change how a CI job runs for a component"**
→ `openshift/release` at `ci-operator/config/` (find the
org/repo directory for the component)

**"I need to investigate a test failure"**
→ `openshift/origin` at `test/extended/` (find the
component subdirectory for the failing test)

**"I need to understand a CI step definition"**
→ `openshift/release` at `ci-operator/step-registry/`
(steps are organized by name, e.g.
`ipi/install/`, `gather/must-gather/`)

**"I need to update a test-to-component mapping"**
→ `openshift-eng/ci-test-mapping`

**"I need to change Sippy's behavior"**
→ `openshift/sippy`

**"I need to change disruption data collection"**
→ `openshift/ci-tools` at `pkg/jobrunaggregator/`

**"I need to understand how ci-operator works"**
→ [CI Docs](https://docs.ci.openshift.org/) and
`openshift/ci-tools`

---

## Substep: explore-repos

### Action

Browse the key repos to build familiarity with their
structure:

- Open [openshift/release](https://github.com/openshift/release)
  and read the `OWNERS_ALIASES` file at the repo root.
  Search for `technical-release-team` to see TRT's alias
  definitions and current membership.

- In openshift/release, browse
  `core-services/release-controller/_releases/` and open
  one of the release configuration files (e.g. the one
  for the current development release stream) to see how
  blocking and informing verification jobs are defined.

- Open [openshift/origin](https://github.com/openshift/origin)
  and browse `test/extended/` to see how e2e tests are
  organized by component.

- Open [openshift/sippy](https://github.com/openshift/sippy)
  and read the README to understand the project structure.

- Open
  [openshift-eng/ci-test-mapping](https://github.com/openshift-eng/ci-test-mapping)
  and read the README to see how test-to-component
  mappings are structured.

---

## Known References

- openshift/release: https://github.com/openshift/release
  (verified: 2026-06-05)
- openshift/origin: https://github.com/openshift/origin
  (verified: 2026-06-05)
- openshift/ci-tools: https://github.com/openshift/ci-tools
  (verified: 2026-06-05)
- openshift/sippy: https://github.com/openshift/sippy
  (verified: 2026-06-05)
- openshift-eng/ci-test-mapping:
  https://github.com/openshift-eng/ci-test-mapping
  (verified: 2026-06-05)
- openshift/release-controller:
  https://github.com/openshift/release-controller
  (verified: 2026-06-05)
- openshift/ci-docs: https://github.com/openshift/ci-docs
  (verified: 2026-06-05)
- CI Docs: https://docs.ci.openshift.org/
  (verified: 2026-06-05)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"
