---
id: 08-reading-list
title: Essential Reading & Documentation
order: 8
audience: new-hire

claims:
  channels: []
  urls:
    - https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit
    - https://docs.google.com/document/d/1r6V9E4ClOyfgKf-ruEFoEJDyYuc6uc5w1nDfXbX2OBM/edit
    - https://docs.ci.openshift.org/docs/release-oversight/
    - https://docs.ci.openshift.org/docs/release-oversight/the-technical-release-team/
    - https://docs.ci.openshift.org/docs/release-oversight/component-readiness/
    - https://docs.ci.openshift.org/docs/release-oversight/disruption-testing/
    - https://docs.ci.openshift.org/docs/release-oversight/payload-testing/
    - https://docs.ci.openshift.org/docs/release-oversight/improving-ci-signal/
    - https://docs.ci.openshift.org/docs/release-oversight/tooling/
    - https://docs.ci.openshift.org/docs/release-oversight/troubleshooting-failures/
    - https://docs.ci.openshift.org/docs/architecture/ci-operator/
    - https://github.com/openshift/sippy
    - https://github.com/openshift-eng/ci-test-mapping
    - https://github.com/openshift/origin
    - https://github.com/openshift/enhancements/blob/master/enhancements/release/improving-ci-signal.md
  repos:
    - openshift/sippy
    - openshift-eng/ci-test-mapping
    - openshift/enhancements
    - openshift/origin
  tools: []

substeps:
  - id: week-one-docs
    type: learning
    group: week-one
    summary: "Know the highest-priority documents for your first week"

  - id: cr-user-guide
    type: learning
    group: week-one
    summary: "Understand the Component Readiness User Guide"

  - id: ci-docs-overview
    type: learning
    group: ci-docs
    summary: "Navigate the Release Oversight section on docs.ci.openshift.org"

  - id: ci-docs-deep
    type: learning
    group: ci-docs
    summary: "Know which deeper CI docs pages to prioritize"

  - id: repo-readmes
    type: learning
    group: repos
    summary: "Read key repository READMEs for TRT-owned projects"

  - id: origin-tests
    type: learning
    group: repos
    summary: "Understand the openshift/origin test documentation"

  - id: design-docs
    type: learning
    group: design
    summary: "Find enhancement proposals and design documents behind TRT processes"

  - id: internal-docs
    type: learning
    group: design
    summary: "Know where to find internal team documents"

  - id: reading-plan
    type: action
    summary: "Create your personal reading plan"
---

## Substep: week-one-docs

### Context

Steps 1 through 7 introduced you to TRT's tools,
dashboards, and workflows. This step compiles the
documents you should read to deepen that understanding —
prioritized by impact on your daily work.

**Priority 1 — read this week:**

These three documents directly support the watcher role
(introduced in Step 6), which is where you'll spend most
of your first few weeks:

1. **[Watcher rotation process document](https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit)**
   (introduced in Step 1, used in Step 6) — Re-read the
   full document now that you understand the tools and
   workflows. On your first pass you lacked the context
   to absorb everything; with Steps 3–7 complete, the
   sections on investigation techniques and escalation
   procedures will make much more sense.

2. **Component Readiness User Guide** (Google Doc) — A
   detailed walkthrough of how to use the CR
   (Component Readiness) dashboard for triage. This is
   the most important new document in this step — see
   the next substep for details.

3. **The Technical Release Team** page on
   [docs.ci.openshift.org](https://docs.ci.openshift.org/docs/release-oversight/the-technical-release-team/) —
   The public-facing description of TRT's mission and
   the "Revert First, Ask Questions Later" policy. Short
   and worth reading in full. You saw this referenced in
   Step 1; now read the primary source.

---

## Substep: cr-user-guide

### Context

The
[Component Readiness User Guide](https://docs.google.com/document/d/1r6V9E4ClOyfgKf-ruEFoEJDyYuc6uc5w1nDfXbX2OBM/edit)
is a Google Doc maintained by the TRT team. It covers:

- How to interpret the CR matrix — what red, green, and
  grey cells mean in context
- How to navigate from the main grid to specific test
  failures (expanding on the workflow from Step 7)
- How triage records work — creating, editing, and
  resolving them in
  [sippy-auth](https://sippy-auth.dptools.openshift.org/sippy-ng/)
  (introduced in Step 3)
- The in-app FAQ that answers common questions directly
  within Sippy's interface

This guide is bookmarked in #forum-ocp-release-oversight
(joined in Step 2). If the link above doesn't work, check
the channel bookmarks.

**Access:** This document requires your Red Hat Google
Workspace account. If you cannot access it, verify that
you are a member of the
[openshift-technical-release-staff](https://groups.google.com/a/redhat.com/g/openshift-technical-release-staff/about)
Google Group (verified in Step 2) and try again.

Read this document before your first solo watcher rotation.
It is the single most useful reference for day-to-day CR
triage work.

---

## Substep: ci-docs-overview

### Context

The
[Release Oversight](https://docs.ci.openshift.org/docs/release-oversight/)
section of
[CI Docs](https://docs.ci.openshift.org/) (bookmarked in
Step 3) is the authoritative public documentation for
TRT's processes. It contains pages you've seen referenced
throughout onboarding — now is the time to read them end
to end.

**Suggested reading order** (most to least urgent for a
new watcher):

1. [The Technical Release Team](https://docs.ci.openshift.org/docs/release-oversight/the-technical-release-team/) —
   TRT's mission and the revert-first policy (5 min read)

2. [Component Readiness](https://docs.ci.openshift.org/docs/release-oversight/component-readiness/) —
   How the regression detection system works. Supplements
   the User Guide above with technical details on the
   statistical methodology (covered conceptually in
   Step 7).

3. [Payload Testing](https://docs.ci.openshift.org/docs/release-oversight/payload-testing/) —
   How the Release Controller (introduced in Step 3)
   verifies payloads. Explains `/payload` and
   `/payload-job` commands for testing PRs (pull
   requests) against release payloads.

4. [Improving CI Signal](https://docs.ci.openshift.org/docs/release-oversight/improving-ci-signal/) —
   How job aggregation helps detect payload regressions.
   Explains the architecture behind the aggregated jobs
   you'll see in the Release Controller.

---

## Substep: ci-docs-deep

### Context

These pages are important but can be read in your second
week once you're settling into the watcher role:

5. [Disruption Testing](https://docs.ci.openshift.org/docs/release-oversight/disruption-testing/) —
   How disruption (network connectivity, API server
   availability) is measured in CI test clusters. Includes
   sub-pages on data architecture, aggregated disruption,
   and code implementation. Relevant for the disruption
   review that TRT holds biweekly (introduced in Step 6).

6. [Tooling](https://docs.ci.openshift.org/docs/release-oversight/tooling/) —
   Links and descriptions for all tools available for
   analyzing CI signal and release quality. A useful
   reference page to bookmark alongside your dashboard
   bookmarks from Step 3.

7. [Troubleshooting Failures](https://docs.ci.openshift.org/docs/release-oversight/troubleshooting-failures/) —
   A practical guide to diagnosing Prow (introduced in
   Step 3) job failures. Complements the investigation
   techniques from Step 7.

8. [Pull Request Testing](https://docs.ci.openshift.org/docs/release-oversight/pull-request-testing/) —
   How presubmit testing works and the `/payload`
   commands available to test PRs against nightly
   payloads. Already introduced in Step 5.

**Beyond Release Oversight:**

The
[ci-operator architecture](https://docs.ci.openshift.org/docs/architecture/ci-operator/)
page explains how ci-operator (the tool that defines how
each repo's CI jobs run) works under the hood. Read this
when you want to understand job configuration files in the
[openshift/release](https://github.com/openshift/release)
repository (explored in Step 4).

---

## Substep: repo-readmes

### Context

The repositories you explored in Step 4 contain README
files with important details not covered in the
onboarding steps. Here are the ones to prioritize:

**[openshift/sippy](https://github.com/openshift/sippy)
README** — Start with the top-level README for a project
overview and data ingestion architecture. Then read:
- `pkg/api/README.md` — Documents 60+ API endpoints
  organized by function (release health, jobs, tests,
  component readiness). Essential if you need to write
  scripts or query Sippy data programmatically.
- `sippy-ng/README.md` — Frontend development setup.
  Useful if you plan to contribute UI improvements.

**[openshift-eng/ci-test-mapping](https://github.com/openshift-eng/ci-test-mapping)
README** — Explains how tests are mapped to Jira
components for Component Readiness (CR workflow from
Step 7). Key details:
- How to handle test renames (adding entries to
  `test-renames.yaml` to preserve history)
- How to onboard new components — the process requires
  creating a Jira component in
  [OCPBUGS](https://redhat.atlassian.net/projects/OCPBUGS/)
  first
- Running `make mapping` to regenerate
  `component-mapping.json` after changes

---

## Substep: origin-tests

### Context

**[openshift/origin](https://github.com/openshift/origin)**
contains the primary OpenShift end-to-end (e2e) test
suite. In Step 4 you saw this repo and its role; now read
the test documentation:

- `test/extended/README.md` — How to add and structure
  new e2e tests. Understanding this helps when you're
  investigating CR regressions and need to read the
  actual test code.
- Browse `test/extended/` subdirectories for
  domain-specific tests (networking, storage, upgrades).
  Familiarity with the test structure makes it easier to
  locate the source of a regression when Sippy (set up
  in Step 3) flags a test failure.

> **Note:** The origin test documentation is a work in
> progress — some subdirectories have better documentation
> than others. When documentation is sparse, reading the
> test code directly is often the fastest path.

---

## Substep: design-docs

### Context

These documents explain the "why" behind TRT's processes.
They're not urgent reading, but understanding the design
rationale helps when you need to explain TRT's decisions
to component teams or when you're proposing process
improvements.

**Enhancement proposals** in
[openshift/enhancements](https://github.com/openshift/enhancements):

- [Improving CI Signal](https://github.com/openshift/enhancements/blob/master/enhancements/release/improving-ci-signal.md) —
  The foundational document for TRT's approach to
  regression detection and the "Revert First, Ask
  Questions Later" policy. Explains why regressions
  can't all be caught pre-merge and why post-merge
  detection through payload testing and Component
  Readiness is necessary. This is the document linked
  from the TRT page on docs.ci.openshift.org.

- The Component Readiness enhancement proposal —
  Explains the design goals and multi-milestone plan
  for building the Sippy dashboard that maps tests to
  components and capabilities. Search the
  `enhancements/release/` directory for the latest
  version.

These proposals are public and version-controlled,
making them stable references you can share with anyone
asking about TRT's approach.

---

## Substep: internal-docs

### Context

TRT maintains several internal Google Docs that are shared
within the team. These are not listed with direct links
here because they change ownership and access settings
over time. Instead, find them through these channels:

**Bookmarks in #forum-ocp-release-oversight** (joined in
Step 2) — The channel bookmarks contain links to
frequently used documents including:
- Release checklists for code freeze and branching
- Disruption review agendas and notes
- Sippy design meeting agendas

**Bookmarks in #forum-release-oversight-staff** (joined
in Step 2) — Additional internal documents including
process guides and strategy documents.

**Your onboarding buddy** — Ask them to share links to:
- The TRT release checklist (used at each milestone)
- Any active strategy or planning documents
- The disruption biweekly review doc

**Access:** Most internal documents require membership in
the
[openshift-technical-release-staff](https://groups.google.com/a/redhat.com/g/openshift-technical-release-staff/about)
Google Group (verified in Step 2). If you can't open a
shared document, this is usually why.

---

## Substep: reading-plan

### Action

Create a personal reading plan using the priorities
below. You don't need to read everything before your first
watcher rotation — focus on Priority 1 this week and work
through the rest over the next two weeks.

**This week (before your first shadow rotation):**
- Re-read the [watcher rotation process document](https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit)
  (introduced in Step 1) with fresh eyes
- Read the Component Readiness User Guide (Google Doc
  linked above)
- Read "The Technical Release Team" on docs.ci.openshift.org

**Next week (during or after your shadow rotation):**
- Read the Release Oversight section on
  docs.ci.openshift.org (pages 2–4 from above)
- Read the openshift/sippy top-level README
- Read the openshift-eng/ci-test-mapping README

**Week 3+ (as questions arise):**
- Disruption Testing docs (before your first disruption
  review)
- The Improving CI Signal enhancement proposal (when you
  want to understand the design rationale)
- openshift/origin test documentation (when you need to
  investigate a specific test)
- CI operator architecture (when you need to understand
  job configuration)

Keep a list of questions as you read — bring them to your
onboarding buddy or to #forum-release-oversight-staff.
Questions from new team members often identify gaps in the
documentation.

---

## Known References

- Watcher rotation process document:
  https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit
  (verified: 2026-06-08)
- Component Readiness User Guide:
  https://docs.google.com/document/d/1r6V9E4ClOyfgKf-ruEFoEJDyYuc6uc5w1nDfXbX2OBM/edit
  (verified: 2026-06-08)
- Release Oversight (docs.ci.openshift.org):
  https://docs.ci.openshift.org/docs/release-oversight/
  (verified: 2026-06-08)
- The Technical Release Team:
  https://docs.ci.openshift.org/docs/release-oversight/the-technical-release-team/
  (verified: 2026-06-08)
- Component Readiness (docs.ci.openshift.org):
  https://docs.ci.openshift.org/docs/release-oversight/component-readiness/
  (verified: 2026-06-08)
- Disruption Testing:
  https://docs.ci.openshift.org/docs/release-oversight/disruption-testing/
  (verified: 2026-06-08)
- Payload Testing:
  https://docs.ci.openshift.org/docs/release-oversight/payload-testing/
  (verified: 2026-06-08)
- Improving CI Signal:
  https://docs.ci.openshift.org/docs/release-oversight/improving-ci-signal/
  (verified: 2026-06-08)
- Tooling:
  https://docs.ci.openshift.org/docs/release-oversight/tooling/
  (verified: 2026-06-08)
- Troubleshooting Failures:
  https://docs.ci.openshift.org/docs/release-oversight/troubleshooting-failures/
  (verified: 2026-06-08)
- Pull Request Testing:
  https://docs.ci.openshift.org/docs/release-oversight/pull-request-testing/
  (verified: 2026-06-08)
- ci-operator architecture:
  https://docs.ci.openshift.org/docs/architecture/ci-operator/
  (verified: 2026-06-08)
- Improving CI Signal enhancement:
  https://github.com/openshift/enhancements/blob/master/enhancements/release/improving-ci-signal.md
  (verified: 2026-06-08)
- openshift/sippy repo:
  https://github.com/openshift/sippy
  (verified: 2026-06-08)
- openshift-eng/ci-test-mapping repo:
  https://github.com/openshift-eng/ci-test-mapping
  (verified: 2026-06-08)
- openshift/origin repo:
  https://github.com/openshift/origin
  (verified: 2026-06-08)
- openshift/enhancements repo:
  https://github.com/openshift/enhancements
  (verified: 2026-06-08)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"
