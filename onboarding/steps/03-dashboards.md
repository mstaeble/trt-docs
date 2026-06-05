---
id: 03-dashboards
title: Dashboards & URLs
order: 3
audience: new-hire

claims:
  channels: []
  urls:
    - https://sippy.dptools.openshift.org/sippy-ng/
    - https://amd64.ocp.releases.ci.openshift.org/
    - https://prow.ci.openshift.org/
    - https://search.ci.openshift.org/
    - https://grafana-loki.ci.openshift.org/
    - https://ship-status.ci.openshift.org/
    - https://docs.ci.openshift.org/
    - https://redhat.atlassian.net/
  repos: []
  tools: []

substeps:
  - id: sippy
    type: learning
    group: release-signal
    summary: "Understand Sippy — TRT's primary CI analysis dashboard"

  - id: release-controller
    type: learning
    group: release-signal
    summary: "Understand the Release Controller — payload status"

  - id: prow
    type: learning
    group: ci-investigation
    summary: "Navigate Prow — CI job execution and results"

  - id: ci-search
    type: learning
    group: ci-investigation
    summary: "Use CI Search and CI Docs"

  - id: monitoring
    type: learning
    summary: "Know the monitoring dashboards"

  - id: jira
    type: learning
    summary: "Find your way around TRT's Jira boards"

  - id: bookmark
    type: action
    summary: "Bookmark your essential dashboards"

  - id: verify-access
    type: verification
    summary: "Verify you can access all dashboards"
---

## Substep: sippy

### Context

[Sippy](https://sippy.dptools.openshift.org/sippy-ng/) is
TRT's primary CI (Continuous Integration) analysis
dashboard. It was built by TRT to answer the question:
"Is OpenShift ready to ship?"

Sippy's most important feature for TRT is **Component
Readiness** — a regression detection system that compares
current test results against a known-good baseline release
using statistical analysis. When a component's tests start
failing significantly more often than the baseline, Sippy
flags it as a regression with a red cell in the Component
Readiness matrix.

Your daily workflow with Sippy:
- Check Component Readiness for new regressions (red
  cells)
- Investigate red cells to determine whether they
  represent real regressions or test flakes
- Triage regressions by filing Jira bugs against the
  responsible component team

Sippy data is ingested from CI job results and may lag a
few hours behind real time. For live payload status, use
the Release Controller (next substep).

You'll use Component Readiness extensively in the watcher
role (Step 6) and learn its full triage workflow in
Step 7.

---

## Substep: release-controller

### Context

The [Release Controller](https://amd64.ocp.releases.ci.openshift.org/)
automates OCP (OpenShift Container Platform) release
payload creation and verification. A "payload" is a
collection of container images that together make up a
specific OCP build.

The main page lists payloads grouped by release stream
(e.g. `4.19.0-0.nightly` for nightly builds). Each row
shows:
- **Name** — The payload tag (links to the detail page)
- **Phase** — Accepted (all blocking tests passed),
  Rejected (one or more blocking tests failed), or
  Pending (tests still running)
- **Failures** — Names of failed verification jobs, each
  linking to the Prow job results
- **Team Approvals** — Explicit sign-offs from teams like
  QE and TRT

Click a payload name to open the detail page, which
breaks verification jobs into categories: *Blocking*
(must pass for acceptance), *Informing* (provide signal
but don't gate), and others.

The Release Controller runs per CPU architecture. The one
you'll use most often is amd64, but others exist for
arm64, s390x, ppc64le, and multi (heterogeneous). Each
follows the same URL pattern:
`https://{arch}.ocp.releases.ci.openshift.org/`

Your daily workflow: check whether the latest nightly
payload was accepted or rejected. If rejected, click
through to the failing verification jobs to understand
what broke.

You'll learn the `oc adm release info` command for
inspecting the specific commits in a payload in Step 5.

---

## Substep: prow

### Context

[Prow](https://prow.ci.openshift.org/) is the
Kubernetes-based CI system that runs all OpenShift CI
jobs. You were introduced to Prow briefly in Step 1 —
here you'll learn how to navigate its interface.

What you'll use Prow for:
- **Viewing job results** — Every CI job (periodic nightly
  tests, PR presubmits, post-merge postsubmits) has a
  results page with logs and test artifacts
- **Investigating failures** — When a payload is rejected
  or a component test regresses, you trace the failure
  back to a specific Prow job to read the logs
- **Job history** — See pass/fail trends for a specific
  job over time

Prow job result URLs follow this pattern:
`https://prow.ci.openshift.org/view/gs/test-platform-results/logs/{JOB_NAME}/{BUILD_ID}`

Prow also includes a **Tide** dashboard at
https://prow.ci.openshift.org/tide that shows the
automated PR merge queue — useful for checking whether a
revert PR has been picked up for merge.

Job configuration lives in the
[openshift/release](https://github.com/openshift/release)
repository — you'll explore this repo in Step 4.

---

## Substep: ci-search

### Context

[CI Search](https://search.ci.openshift.org/) provides a
searchable index across all CI test results, build logs,
and job artifacts. When you see a test failure in Sippy
or Prow, CI Search helps you answer: "Is this failure
happening in other jobs too?"

Common uses:
- Search for a specific test name to see its pass/fail
  rate across all jobs
- Search for an error message to find which jobs are
  hitting it
- Check whether a failure is isolated to one platform
  (e.g. AWS only) or widespread

[CI Docs](https://docs.ci.openshift.org/) is the
reference documentation for the entire OpenShift CI
platform. It covers Prow job configuration,
[ci-operator](https://docs.ci.openshift.org/docs/architecture/ci-operator/)
workflows (the tool that defines how each repo's CI jobs
run), and how to write and run tests. Bookmark it as your
go-to reference for CI questions.

---

## Substep: monitoring

### Context

Two dashboards help you monitor CI infrastructure health:

**[Grafana](https://grafana-loki.ci.openshift.org/)** —
Provides CI observability dashboards built on Prometheus
metrics. The dashboards most relevant to TRT:

- **Disruption dashboard** — Tracks network disruption
  (connection drops, API server unavailability) in CI test
  clusters. TRT reviews this biweekly to identify and file
  bugs for disruptions that exceed acceptable thresholds.
- **Watch Counts** — Shows CI activity levels: how many
  jobs are queued, running, and completing. Useful for
  spotting unusual load patterns.

**[SHIP Status Dashboard](https://ship-status.ci.openshift.org/)** —
Shows real-time health of all SHIP infrastructure
components: build farms, Prow, the release controller,
image registries, and more. Check here first when
something seems broken across multiple jobs — it may be
an infrastructure outage rather than a test regression.

---

## Substep: jira

### Context

TRT tracks work and CI regressions in
[Jira](https://redhat.atlassian.net/). The two projects
you'll use most:

- **TRT** — The team's own project for internal work
  items, process improvements, and tooling enhancements.
- **OCPBUGS** — All OCP bugs are tracked here. When you
  triage a Component Readiness regression, you file the
  bug in OCPBUGS and assign it to the responsible
  component team.

Daily workflow: when Sippy's Component Readiness shows a
new regression, you create an OCPBUGS ticket with the test
failure details, link it to the Sippy regression, and tag
the owning team. TRT has automation that assists with
this — if a component column in Component Readiness has
four or more red cells, a Jira ticket is automatically
created.

The full triage workflow — including how to write
effective bug reports and engage component teams — is
covered in Step 7.

---

## Substep: bookmark

### Action

Bookmark these dashboards in your browser for daily use.
A suggested folder structure:

**TRT — Release Signal:**
- Sippy: https://sippy.dptools.openshift.org/sippy-ng/
- Release Controller (amd64):
  https://amd64.ocp.releases.ci.openshift.org/

**TRT — CI Investigation:**
- Prow: https://prow.ci.openshift.org/
- CI Search: https://search.ci.openshift.org/
- CI Docs: https://docs.ci.openshift.org/

**TRT — Monitoring:**
- Grafana: https://grafana-loki.ci.openshift.org/
- SHIP Status: https://ship-status.ci.openshift.org/

**TRT — Work Tracking:**
- Jira: https://redhat.atlassian.net/

---

## Substep: verify-access

### Verification

Open each URL below and confirm it loads without errors.
If any dashboard requires access you don't have, check
with your onboarding buddy.

- Sippy: https://sippy.dptools.openshift.org/sippy-ng/
- Release Controller:
  https://amd64.ocp.releases.ci.openshift.org/
- Prow: https://prow.ci.openshift.org/
- CI Search: https://search.ci.openshift.org/
- Grafana: https://grafana-loki.ci.openshift.org/
- SHIP Status: https://ship-status.ci.openshift.org/
- Jira: https://redhat.atlassian.net/
- CI Docs: https://docs.ci.openshift.org/

---

## Known References

- Sippy: https://sippy.dptools.openshift.org/sippy-ng/
  (verified: 2026-06-05)
- Release Controller (amd64):
  https://amd64.ocp.releases.ci.openshift.org/
  (verified: 2026-06-05)
- Prow: https://prow.ci.openshift.org/
  (verified: 2026-06-05)
- Prow Tide: https://prow.ci.openshift.org/tide
  (verified: 2026-06-05)
- CI Search: https://search.ci.openshift.org/
  (verified: 2026-06-05)
- CI Docs: https://docs.ci.openshift.org/
  (verified: 2026-06-05)
- Grafana: https://grafana-loki.ci.openshift.org/
  (verified: 2026-06-05)
- SHIP Status: https://ship-status.ci.openshift.org/
  (verified: 2026-06-05)
- Jira: https://redhat.atlassian.net/
  (verified: 2026-06-05)
- openshift/release repo:
  https://github.com/openshift/release
  (verified: 2026-06-05)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"
