---
id: 02-slack-channels
title: Slack Channels & Communication
order: 2
audience: new-hire

claims:
  channels:
    - forum-ocp-release-oversight
    - forum-release-oversight-staff
    - trt-alert
    - forum-ocp-art
    - forum-ocp-testplatform
    - forum-ocp-crt
    - control-plane-cr
    - announce-testplatform
    - forum-ocp-release
  urls: []
  repos: []
  tools: []

substeps:
  - id: core-channels
    type: learning
    summary: "Learn about TRT's core channels"

  - id: join-core
    type: action
    summary: "Join TRT's core channels"

  - id: communication-norms
    type: learning
    summary: "Understand team communication norms"

  - id: sister-team-channels
    type: learning
    summary: "Learn about sister team channels"

  - id: join-sister
    type: action
    summary: "Join sister team channels"

  - id: alert-system
    type: learning
    summary: "Understand TRT's alert system"

  - id: join-alerts
    type: action
    summary: "Join key alert channels"

  - id: awareness-channels
    type: learning
    summary: "Learn about announcement and ad-hoc channels"

  - id: mailing-list
    type: action
    summary: "Join the TRT mailing list"

  - id: verification
    type: verification
    summary: "Verify you're in all required channels"
---

## Substep: core-channels

### Context

TRT has two primary Slack channels:

**#forum-ocp-release-oversight** (public) — This is TRT's
main hub. Everything happens here: CI job failure triage,
payload status, release-blocking decisions, and
coordination with component teams. Expect 20-50 messages
per day. TRT also holds weekly office hours on Thursdays
via Slack huddle in this channel.

**#forum-release-oversight-staff** (private) — TRT's
internal channel for team coordination: watcher rotation
handoffs, staffing discussions, PTO coverage, and
sensitive triage conversations. Most internal team
coordination happens asynchronously here rather than in
formal meetings.

**#trt-alert** — A general-purpose alert channel where
TRT receives critical automated notifications about
payload regressions, nightly build delays, and other
release-blocking events that need immediate attention.

---

## Substep: join-core

### Action

- [ ] Join #forum-ocp-release-oversight — search for it in
      Slack's channel browser
- [ ] Ask your buddy or manager to add you to
      #forum-release-oversight-staff (private channel)
- [ ] Join #trt-alert — search for it in Slack's channel
      browser

---

## Substep: communication-norms

### Context

**Team alias:** Type `@team-technical-release` in any
message to reach the current watcher. You can type it in
the compose box without sending to see who's currently in
the group.

**Office hours:** TRT holds open office hours every
Thursday via Slack huddle in #forum-ocp-release-oversight.
Anyone can join to ask questions about release health, CI
failures, or TRT processes.

**Async-first:** TRT works primarily asynchronously through
Slack. Most decisions and coordination happen in channel
threads, not meetings. When you have a question, post it
in the relevant channel rather than waiting for a meeting.

**Mailing list:** openshift-trt-staff@redhat.com is used
for administrative purposes like calendar invites and
document ownership. Slack covers day-to-day communication.

---

## Substep: sister-team-channels

### Context

TRT works closely with three sister teams within SHIP
(Strategic Horizontal Integration Projects). Monitoring
their channels gives you context on what's happening
upstream and downstream of TRT's work:

**#forum-ocp-art** — ART (Automated Release Tooling)
builds and assembles the release payloads that TRT
qualifies. Follow this channel to understand build-related
issues, yum mirror problems, and payload assembly status.

**#forum-ocp-testplatform** — DPTP (Developer Productivity
Test Platform) owns the CI infrastructure (Prow) that TRT's
jobs run on. Follow this channel for CI infrastructure
questions, job configuration changes, and test platform
updates.

**#forum-ocp-crt** — CRT (Continuous Release Tooling) owns
the release-controller that orchestrates payload creation
and verification. Follow this channel for release pipeline
discussions.

You don't need to read every message in these channels —
skim them once a day to build context.

---

## Substep: join-sister

### Action

- [ ] Join #forum-ocp-art
- [ ] Join #forum-ocp-testplatform
- [ ] Join #forum-ocp-crt

---

## Substep: alert-system

### Context

TRT operates an automated alerting system that monitors
CI job pass rates and Component Readiness status. This is
not a single bot — it's built from two mechanisms:

**Alertmanager routing** — Prometheus monitors Sippy (TRT's
CI analysis dashboard) metrics and fires alerts when pass
rates drop below configured thresholds or Component
Readiness turns red. Alertmanager routes these alerts to
the appropriate Slack channel based on label matchers.
The routing config lives in the `continuous-release-jobs`
repo.

**Prow reporter config** — Prow job definitions can include
a `reporter_config` section that sends failure/error
notifications directly to a designated Slack channel when
a job fails.

**#trt-alert** — The general-purpose alert channel. It
receives broad alerts and also serves as a testing ground
for new alert rules before they get routed to
component-specific channels.

**#control-plane-cr** — Consolidates Component Readiness
alerts for core control plane components (kube-apiserver,
etcd, schedulers). Important for monitoring core platform
stability.

**Component-specific alert channels** — Teams can request
their own dedicated alert channel so they only see alerts
relevant to their component. Examples:
- #trt-alert-mco — Machine Config Operator alerts
- #forum-ocp-catalogs-program — OLM/OperatorHub alerts
- #multiarch-ci-alerts — non-amd64 architecture alerts

The difference between #trt-alert and a component-specific
channel like #trt-alert-mco: #trt-alert gets everything,
while #trt-alert-mco only gets MCO-specific alerts. Teams
request their own channel by opening a TRT Jira ticket.

You don't need to join every alert channel. As you settle
into the watcher role (Step 6), you'll learn which ones
are most relevant to your triage work.

---

## Substep: join-alerts

### Action

- [ ] Join #control-plane-cr — this is the most broadly
      relevant alert channel for TRT work

---

## Substep: awareness-channels

### Context

A few more channels worth knowing about, though they
don't require daily monitoring:

**#announce-testplatform** — Announcements about CI
infrastructure changes (build farm upgrades, Sippy
updates). Low traffic, useful for awareness of changes
that could affect CI health.

**#forum-ocp-release** — Notifications about release
activities: new RPMs tagged, build sync status, and
release pipeline health.

**Component-specific forums** — When TRT encounters a CI
failure owned by a specific component team, you'll reach
out to that team's channel (e.g. #forum-mco,
#forum-ocp-updates, #forum-ocp-storage). You don't need
to join these proactively — you'll find them as needed
during triage.

---

## Substep: mailing-list

### Action

- [ ] Ask your manager to add you to the
      openshift-trt-staff@redhat.com mailing list

---

## Substep: verification

### Verification

Confirm you have joined or been added to:

- [ ] #forum-ocp-release-oversight (public)
- [ ] #forum-release-oversight-staff (private)
- [ ] #trt-alert
- [ ] #forum-ocp-art
- [ ] #forum-ocp-testplatform
- [ ] #forum-ocp-crt
- [ ] #control-plane-cr
- [ ] openshift-trt-staff@redhat.com mailing list

---

## Known References

- TRT primary channel: #forum-ocp-release-oversight
  (verified: 2026-06-04)
- TRT staff channel: #forum-release-oversight-staff
  (verified: 2026-06-04)
- TRT alert channel: #trt-alert (verified: 2026-06-04)
- ART channel: #forum-ocp-art (verified: 2026-06-04)
- DPTP channel: #forum-ocp-testplatform
  (verified: 2026-06-04)
- CRT channel: #forum-ocp-crt (verified: 2026-06-04)
- Component Readiness alerts: #control-plane-cr
  (verified: 2026-06-04)
- Announcements: #announce-testplatform
  (verified: 2026-06-04)
- Release notifications: #forum-ocp-release
  (verified: 2026-06-04)
- Team alias: @team-technical-release
  (verified: 2026-06-04)
- Mailing list: openshift-trt-staff@redhat.com
  (verified: 2026-06-04)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"
