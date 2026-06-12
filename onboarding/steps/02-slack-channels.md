---
id: 02-slack-channels
title: Communications & Access
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
  urls:
    - https://issues.redhat.com/secure/RapidBoard.jspa?rapidView=5922&projectKey=TRT
    - https://devservices.dpp.openshift.com/support/github_access_request/
  repos:
    - openshift/continuous-release-jobs
    - openshift/release
  tools: []

substeps:
  - id: core-channels
    type: action
    summary: "Join TRT's core channels"

  - id: communication-norms
    type: learning
    summary: "Understand team communication norms"

  - id: sprint-cadence
    type: learning
    summary: "Understand the sprint structure and team ceremonies"

  - id: jira-board
    type: action
    summary: "Explore the TRT Jira board and create your onboarding tracker"

  - id: sister-team-channels
    type: action
    summary: "Join sister team channels"

  - id: alert-system
    type: action
    summary: "Understand and join alert channels"

  - id: awareness-channels
    type: learning
    summary: "Learn about announcement and ad-hoc channels"

  - id: github-access
    type: action
    summary: "Request membership in the openshift GitHub organizations"

  - id: verify-access
    type: action
    summary: "Verify Rover groups and Google Group access"

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
per day. TRT also holds office hours in this channel
(see the communication-norms substep for details).

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

### Action

- Join #forum-ocp-release-oversight
- Ask your buddy or manager to add you to
      #forum-release-oversight-staff (private channel)
- Join #trt-alert

---

## Substep: communication-norms

### Context

**Team alias:** Type `@team-technical-release` in any
message to reach the current watcher. You can type it in
the compose box without sending to see who's currently in
the group.

**Office hours:** TRT has two recurring office hours
sessions. Both are open to anyone with questions about
release health, CI failures, or TRT processes:

- **Fridays 9:15–10:00 AM ET** — weekly, on the AOS Main
  Calendar
- **Thursdays 2:00–3:00 PM ET** — biweekly Slack huddle in
  #forum-ocp-release-oversight (watch the channel for the
  automated reminder to confirm which weeks it runs)

**Async-first:** TRT works primarily asynchronously through
Slack. Most decisions and coordination happen in channel
threads, not meetings. When you have a question, post it
in the relevant channel rather than waiting for a meeting.

**Google Group:** The team uses the
openshift-technical-release-staff Google Group for
administrative purposes like calendar invites and document
ownership. Slack covers day-to-day communication.

---

## Substep: sprint-cadence

### Context

TRT runs **three-week sprints**. Here is the recurring
meeting cadence within each sprint:

**Daily standup** — 9:00 AM ET, every weekday. A brief
sync where the team shares progress, flags blockers,
and coordinates handoffs. As a new hire, use this to hear
what the team is focused on and ask questions.

**Monday meeting** (rotating focus) — The agenda rotates
with the sprint cycle:
- **Week 1: Jira refinement** — Review and scope incoming
  work items, clarify acceptance criteria, and estimate
  effort
- **Week 2: Bug triage** — Triage bugs owned by TRT and
  bugs TRT has opened against other teams. This is where
  you'll see how the team tracks cross-team issues.
- **Week 3: Sprint planning** — Select work for the
  upcoming sprint based on the refined backlog and team
  capacity

**Wednesday meeting** (weekly) — A flexible time block
reserved for team architecture discussions, onboarding
sessions, demos of new tooling, and general
announcements. When external teams need to present to
TRT, they join this meeting.

**Sprint retrospective** (every 3 weeks, Thursdays) —
End-of-sprint reflection on what went well, what didn't,
and what to improve.

All meetings are on the AOS Main Calendar via the
openshift-technical-release-staff Google Group. If you
completed the verify-access substep above, you should
already see them on your calendar.

---

## Substep: jira-board

### Context

TRT tracks its work on a Kanban board in the
[TRT Jira project](https://issues.redhat.com/secure/RapidBoard.jspa?rapidView=5922&projectKey=TRT).
Issues flow through columns like Backlog, Refinement,
In Progress, and Done. The Monday rotation meetings
(refinement, bug triage, planning) drive items through
these stages across the sprint.

As an onboarding exercise, you will create a single
ticket to track your onboarding progress. This gives
you hands-on experience with the board and a place to
log notes and questions as you work through each step.

### Action

- Open the
  [TRT Kanban board](https://issues.redhat.com/secure/RapidBoard.jspa?rapidView=5922&projectKey=TRT)
  and browse the current items to get a feel for what
  the team is working on
- Create a new ticket in the TRT project:
  - Type: Task
  - Summary: "Onboarding: [Your Name]"
  - Add a comment noting which onboarding step you are
    currently on
- As you complete future steps, add a comment to your
  onboarding ticket noting what you finished and any
  questions that came up

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

You don't need to read every message in these channels.
Use Slack's "Summarize Channel" feature to get an AI
summary of recent activity. Check these summaries once a
day to build context on what the sister teams are working
on.

### Action

- Join #forum-ocp-art
- Join #forum-ocp-testplatform
- Join #forum-ocp-crt

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
The routing config lives in the
[continuous-release-jobs](https://github.com/openshift/continuous-release-jobs)
repo.

**Prow reporter config** — Prow job definitions can include
a `reporter_config` section that sends failure/error
notifications directly to a designated Slack channel when
a job fails. See the
[CI docs: Slack Alerts](https://docs.ci.openshift.org/docs/how-tos/notification/)
for configuration details and examples.

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
into the watcher role (Step 8), you'll learn which ones
are most relevant to your triage work.

### Action

- Join #control-plane-cr — this is the most broadly
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

## Substep: github-access

### Context

TRT's repositories are spread across two GitHub
organizations:

- **openshift** — The primary org containing origin,
  release, sippy, ci-tools, and most OpenShift repos
- **openshift-eng** — Contains ci-test-mapping and other
  engineering-internal repos

You need membership in both orgs to access private
repositories, review PRs, and use the OWNERS system
(covered in Step 4).

Before requesting org membership, your GitHub account
must meet Red Hat's requirements: the company field must
be set to "Red Hat" and two-factor authentication (2FA)
must be enabled. Org membership requests will be rejected
if these are not in place.

### Action

- If you don't have a GitHub account, create one at
  https://github.com/join
- Ensure your GitHub profile has **"Red Hat"** as the
  company:
  go to https://github.com/settings/profile and set the
  Company field to "Red Hat"
- Ensure two-factor authentication (2FA) is enabled:
  go to https://github.com/settings/security and enable
  2FA if it is not already on
- Connect to the Red Hat VPN (the request form is
  internal-only)
- Go to the
  [GitHub Access Request](https://devservices.dpp.openshift.com/support/github_access_request/)
  page and submit a request for the **openshift** org
- Submit a separate request for the **openshift-eng** org
- Approval may take up to one business day — continue
  with the next substeps while you wait

---

## Substep: verify-access

### Context

Your onboarding buddy should have provisioned your access
to the following before your first day (as part of the
Step 0 pre-review). Verify that you have access to each.
If anything is missing, ask your buddy or manager to
follow up.

### Action

- Verify you are in Rover group:
  [openshift-technical-release-team](https://rover.redhat.com/groups/group/openshift-technical-release-team)
- Verify you are in Rover group:
  [trt-sippy-postgresql](https://rover.redhat.com/groups/group/trt-sippy-postgresql)
- Verify you are in Google Group:
  [openshift-technical-release-staff](https://groups.google.com/a/redhat.com/g/openshift-technical-release-staff/about)
- Verify you are a member of the
  [openshift](https://github.com/openshift) GitHub
  organization
- Verify you are a member of the
  [openshift-eng](https://github.com/openshift-eng) GitHub
  organization

---

## Substep: verification

### Verification

Confirm you have joined or been added to:

- #forum-ocp-release-oversight (public)
- #forum-release-oversight-staff (private)
- #trt-alert
- #forum-ocp-art
- #forum-ocp-testplatform
- #forum-ocp-crt
- #control-plane-cr
- Rover group: openshift-technical-release-team
- Rover group: trt-sippy-postgresql
- Google Group: openshift-technical-release-staff
- Member of the openshift GitHub organization
- Member of the openshift-eng GitHub organization
- Created your onboarding tracking ticket in the TRT
  Jira project

---

## Known References

- TRT primary channel: #forum-ocp-release-oversight
  (verified: 2026-06-05)
- TRT staff channel: #forum-release-oversight-staff
  (verified: 2026-06-05)
- TRT alert channel: #trt-alert (verified: 2026-06-05)
- ART channel: #forum-ocp-art (verified: 2026-06-05)
- DPTP channel: #forum-ocp-testplatform
  (verified: 2026-06-05)
- CRT channel: #forum-ocp-crt (verified: 2026-06-05)
- Component Readiness alerts: #control-plane-cr
  (verified: 2026-06-05)
- Announcements: #announce-testplatform
  (verified: 2026-06-05)
- Release notifications: #forum-ocp-release
  (verified: 2026-06-05)
- TRT Kanban board: https://issues.redhat.com/secure/RapidBoard.jspa?rapidView=5922&projectKey=TRT
  (verified: 2026-06-09)
- Team alias: @team-technical-release
  (verified: 2026-06-05)
- Google Group: openshift-technical-release-staff
  (verified: 2026-06-05)
- Rover group: openshift-technical-release-team
  (verified: 2026-06-05)
- Rover group: trt-sippy-postgresql
  (verified: 2026-06-05)

- GitHub Access Request (VPN required):
  https://devservices.dpp.openshift.com/support/github_access_request/
  (verified: 2026-06-12)
- openshift GitHub org: https://github.com/openshift
  (verified: 2026-06-12)
- openshift-eng GitHub org: https://github.com/openshift-eng
  (verified: 2026-06-12)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"
