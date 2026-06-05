---
id: 01-mission
title: TRT Mission & Team Structure
order: 1
audience: new-hire

claims:
  channels:
    - forum-ocp-release-oversight
    - team-art
    - forum-ocp-crt
    - forum-ocp-testplatform
    - forum-ocp-ert
  urls:
    - https://docs.ci.openshift.org/
    - https://sippy.dptools.openshift.org/
    - https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit?tab=t.0#heading=h.yg3sfsodg686
    - https://docs.google.com/spreadsheets/d/1SysVHL12nRsRZq6su9puh7wNGtKM4ggj1uprTD4srUU/edit?gid=1723723419#gid=1723723419
  repos:
    - openshift/sippy
  tools: []

substeps:
  - id: mission
    type: learning
    group: what-trt-does
    summary: "Understand TRT's core mission"

  - id: revert-first
    type: learning
    group: what-trt-does
    summary: "Learn the 'Revert First' principle"

  - id: org-structure
    type: learning
    group: where-trt-fits
    summary: "See where TRT fits in the org"

  - id: sister-teams
    type: learning
    group: where-trt-fits
    summary: "Understand sister teams and ownership boundaries"

  - id: roles
    type: learning
    group: trt-internals
    summary: "Learn TRT's key roles"

  - id: scope
    type: learning
    group: trt-internals
    summary: "Understand what's in and out of scope"

  - id: shadow-watcher
    type: action
    summary: "Arrange to shadow a watcher rotation"
---

## Substep: mission

### Context

TRT acts as SRE (Site Reliability Engineering) for the
OpenShift release pipeline. The team's primary goal is
ensuring the highest sustainable rate of development across
all of OCP (OpenShift Container Platform).

Core responsibilities:
- Qualifying every OCP release
- Continuously monitoring CI (Continuous Integration)
  signals for go/no-go decisions
- Owning end-to-end (e2e) test job definitions
- Managing CI/CD pipelines via
  [Prow](https://docs.ci.openshift.org/) (the
  Kubernetes-based CI system used by OpenShift)

---

## Substep: revert-first

### Context

TRT's defining cultural principle is "Revert First, Ask
Questions Later." The default state of the release branch
must always be green.

How it works in practice:
1. [Sippy](https://sippy.dptools.openshift.org/) (TRT's CI
   analysis dashboard — you'll set this up in Step 3)
   detects a significant increase in test failures after a
   PR (pull request) merges
2. An alert is filed, tagging the component team, PR
   author, and TRT
3. TRT's primary action: revert the offending PR to unblock
   the pipeline
4. The component team then works on a "fix forward" PR that
   can be properly tested

This is not punitive — it's about keeping the release
pipeline healthy for all teams. The revert buys time for the
component team to fix the issue properly.

---

## Substep: org-structure

### Context

TRT's position in the organization:

    Hybrid Platforms (org)
      └─ Multi-product and Engineering Experience (org)
          └─ SHIP (team group)
              ├─ Technical Release Team (TRT)  ← you are here
              ├─ Automated Release Tooling (ART)
              ├─ Continuous Release Tooling (CRT)
              ├─ Errata Reliability Team (ERT)
              └─ Test Platform (DPTP)

TRT's public Slack channel is
[#forum-ocp-release-oversight](https://redhat-internal.enterprise.slack.com/archives/forum-ocp-release-oversight).

---

## Substep: sister-teams

### Context

TRT works closely with four sister teams within SHIP. Each
has distinct ownership:

- **ART (Automated Release Tooling)** — Builds and assembles
  release payloads (the installable OCP images). TRT
  qualifies them.
  Slack: [#team-art](https://redhat-internal.enterprise.slack.com/archives/team-art)

- **CRT (Continuous Release Tooling)** — Owns the
  release-controller infrastructure (the system that
  coordinates payload creation and verification). TRT owns
  the job definitions within it.
  Slack: [#forum-ocp-crt](https://redhat-internal.enterprise.slack.com/archives/forum-ocp-crt)

- **DPTP (Test Platform)** — Provides the CI platform
  ([Prow](https://docs.ci.openshift.org/)). Does not own
  the release jobs — TRT does.
  Slack: [#forum-ocp-testplatform](https://redhat-internal.enterprise.slack.com/archives/forum-ocp-testplatform)

- **ERT (Errata Reliability Team)** — Owns the errata
  (advisory) pipeline for shipping fixes to customers.
  Slack: [#forum-ocp-ert](https://redhat-internal.enterprise.slack.com/archives/forum-ocp-ert)

You will schedule introductory meetings with these teams
later in the onboarding process, after you have more context
on TRT's tools and workflows.

---

## Substep: roles

### Context

Two key roles within TRT:

**Watcher** — A rotating weekly role with hands-on-keyboard
responsibility for triaging CI problems in real time. This
is the role you'll learn first and is covered in detail in
Step 6.

**Patch Manager** — Rotated among staff engineers, focused
on timely patch releases for older supported versions and
ensuring z-stream (patch) nightlies are produced reliably.

The long-term goal is for component teams to self-monitor
via Component Readiness (a regression detection system —
covered in Step 7), with TRT providing oversight.

---

## Substep: scope

### Context

TRT is focused on the current development branch (e.g.
4.dev/master), not older supported releases. Anything
older than the current dev branch is handled by the patch
manager after GA (General Availability — when a release
ships to customers).

The handoff from TRT to the patch manager occurs at GA.

---

## Substep: shadow-watcher

### Action

Arrange to shadow a watcher rotation:

- Find out who the current watcher is. You can:
      - Type `@team-technical-release` in any Slack message
        compose box (without sending) to see who is
        currently in the watcher alias
      - Check the
        [watcher rotation spreadsheet](https://docs.google.com/spreadsheets/d/1SysVHL12nRsRZq6su9puh7wNGtKM4ggj1uprTD4srUU/edit?gid=1723723419#gid=1723723419)
      - Ask in #forum-release-oversight-staff
- Reach out to a current watcher and arrange to
      shadow their next rotation

The [watcher rotation doc](https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit?tab=t.0#heading=h.yg3sfsodg686)
describes how the rotation is managed. The schedule is
biweekly and adjustments (PTO, swaps) are coordinated in
#forum-release-oversight-staff.

This is the single most valuable thing you can do in your
first week. Watching a watcher work gives you context that
no document can provide. The watcher role is covered in
detail in Step 6, but observing it early accelerates
everything else.

---

## Known References

- TRT public channel: #forum-ocp-release-oversight
  (verified: 2026-06-03)
- Org: SHIP under Hybrid Platforms (verified: 2026-06-03)
- Sippy dashboard: https://sippy.dptools.openshift.org/
  (verified: 2026-06-03)
- Sippy repo: https://github.com/openshift/sippy
  (verified: 2026-06-03)
- Prow docs: https://docs.ci.openshift.org/
  (verified: 2026-06-03)
- ART channel: #team-art (verified: 2026-06-03)
- CRT channel: #forum-ocp-crt (verified: 2026-06-03)
- DPTP channel: #forum-ocp-testplatform (verified: 2026-06-03)
- ERT channel: #forum-ocp-ert (verified: 2026-06-03)
- Watcher rotation doc: https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit
  (verified: 2026-06-04)
- Watcher rotation spreadsheet: https://docs.google.com/spreadsheets/d/1SysVHL12nRsRZq6su9puh7wNGtKM4ggj1uprTD4srUU/edit
  (verified: 2026-06-04)
- Watcher Slack alias: @team-technical-release
  (verified: 2026-06-04)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"
