---
id: 08-watcher-role
title: The Watcher Role
order: 8
audience: new-hire

claims:
  channels:
    - forum-ocp-release-oversight
    - forum-release-oversight-staff
    - trt-alert
    - team-art
    - forum-ocp-crt
    - forum-ocp-testplatform
    - forum-ocp-ert
  urls:
    - https://sippy.dptools.openshift.org/sippy-ng/component_readiness/main
    - https://amd64.ocp.releases.ci.openshift.org/
    - https://grafana-loki.ci.openshift.org/d/ISnBj4LVk/disruption?orgId=1
    - https://sippy-auth.dptools.openshift.org/sippy-ng/chat
    - https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit
    - https://docs.google.com/spreadsheets/d/1SysVHL12nRsRZq6su9puh7wNGtKM4ggj1uprTD4srUU/edit
  repos: []
  tools: []

substeps:
  - id: watcher-mission
    type: learning
    group: what-watchers-do
    summary: "Understand the watcher's core mission"

  - id: daily-routine
    type: learning
    group: what-watchers-do
    summary: "Learn the watcher's daily routine"

  - id: monitoring-payloads
    type: learning
    group: watcher-toolkit
    summary: "Monitor payloads and Component Readiness"

  - id: investigating-failures
    type: learning
    group: watcher-toolkit
    summary: "Investigate failures with CI tools"

  - id: responding-reverts
    type: learning
    group: responding-reverts
    summary: "Respond to product regressions with reverts"

  - id: responding-other
    type: learning
    group: responding-other
    summary: "Handle infrastructure failures, test regressions, and escalation"

  - id: ai-tools
    type: learning
    group: ai-tools
    summary: "Use AI-assisted analysis tools"

  - id: rotation
    type: learning
    group: logistics
    summary: "Understand the biweekly rotation"

  - id: meetings
    type: learning
    group: logistics
    summary: "Know the watcher meeting cadence"

  - id: shadow-rotation
    type: action
    summary: "Shadow a watcher rotation"

  - id: sister-team-intros
    type: action
    summary: "Schedule introductions with sister teams"
---

## Substep: watcher-mission

### Context

The watcher is TRT's hands-on-keyboard role — a rotating
assignment where one team member takes primary
responsibility for monitoring OpenShift CI health in real
time.

The watcher's core mission: **detect regressions quickly
and hand them off to the right team.** The watcher is a
low-latency connection between a new failure appearing in
CI and the component team that owns the failing code
starting to debug it.

What the watcher **does:**
- Monitor active payload streams (nightly and CI builds)
  for blocking failures
- Investigate rejected payloads to identify which PR
  (pull request) caused the regression
- Monitor Component Readiness (CR — the regression
  detection system introduced in Step 3) for new
  regressions
- File Jira tickets and tag the responsible component
  team
- Revert offending PRs when regressions are confirmed
  (the "Revert First" principle from Step 1)
- Force-accept payloads when failures are caused by
  infrastructure issues, not product regressions
- Escalate infrastructure problems to the appropriate
  team (DPTP for CI platform, cloud providers for cloud
  issues)

What the watcher **does not do:**
- Fix the underlying issue — that's the component team's
  responsibility
- Debug flaky tests — file a ticket and hand it off
- Debug CI infrastructure — escalate to DPTP
  (#forum-ocp-testplatform)

The watcher provides light investigation and fast
hand-off. Think of it as triage, not treatment.

---

## Substep: daily-routine

### Context

A typical watcher day follows this rhythm:

**Morning check-in:**
1. Check the
   [Release Controller](https://amd64.ocp.releases.ci.openshift.org/)
   for the latest payload status — were overnight
   nightlies accepted or rejected?
2. Check
   [Component Readiness](https://sippy.dptools.openshift.org/sippy-ng/component_readiness/main)
   for new red cells (regressions)
3. Scan #forum-ocp-release-oversight and #trt-alert for
   overnight activity you may need to follow up on
4. Attend the watcher sync meeting (M/W/F) or the
   watcher-focused meeting (Tu/Th)

**Throughout the day:**
- Watch for new payload rejections as nightlies build
- Investigate any new blocking failures
- Monitor #forum-ocp-release-oversight for questions from
  component teams about CI health
- Follow up on outstanding triage items

**Categorizing failures:**
When you investigate a failure, you'll categorize it as
one of:
- **PRODUCT_REGRESSION** — A merged PR broke something.
  Revert it.
- **TEST_REGRESSION** — A test change caused the failure,
  not the product itself. File a bug against the test
  owner.
- **TEST_FLAKE** — An intermittent test failure unrelated
  to any recent change. Note it and move on.
- **CLOUD_INFRA** — A cloud provider issue (e.g. AWS
  outage, GCP quota exhaustion). Force-accept the
  payload.
- **RH_INFRA** — A Red Hat infrastructure issue (e.g. CI
  cluster problem, registry outage). Force-accept or
  escalate.

These categories are tracked in
[Sippy](https://sippy.dptools.openshift.org/sippy-ng/)
(introduced in Step 3) and used to inform release
promotion decisions.

---

## Substep: monitoring-payloads

### Context

Step 3 introduced the dashboards you'll use every day.
Here's how the watcher uses them specifically:

**Release Controller** — Your starting point for payload
status. Open the
[amd64 Release Controller](https://amd64.ocp.releases.ci.openshift.org/)
and look at the active development stream (e.g.
`{{DEV_NIGHTLY_STREAM}}`). Focus on:
- Recent payloads marked "Rejected" — click through to
  see which blocking jobs failed
- Payloads still in "Pending" — monitor for completion
- The overall cadence — are accepted payloads being
  produced regularly, or has the stream stalled?

TRT members have admin access to the Release Controller,
which allows you to manually accept or reject payloads
when needed (covered in the responding-to-failures
substep below).

**Component Readiness** — Open the
[main view](https://sippy.dptools.openshift.org/sippy-ng/component_readiness/main)
to see the regression matrix. Each cell represents a
component-platform-network combination. Red cells indicate
statistically significant regressions compared to the
baseline release. Your job: investigate red cells,
determine the root cause, and file Jira tickets against
the owning team. The full triage workflow is covered in
Step 9.

As a watcher, you'll use the authenticated Sippy instance
([sippy-auth](https://sippy-auth.dptools.openshift.org/sippy-ng/))
for all triage work. The public instance
(introduced in Step 3) is read-only — you can view
reports but can't act on them. Sippy-auth requires Red
Hat SSO and is where you create triage records, link
failures to Jira bugs, and file bugs directly from test
detail pages. Bookmark sippy-auth as your default — as a
watcher, you'll almost always need the write actions.

**Disruption Dashboard** — The
[Grafana disruption dashboard](https://grafana-loki.ci.openshift.org/d/ISnBj4LVk/disruption?orgId=1)
tracks network disruption (connection drops, API server
unavailability) during CI test runs. TRT reviews this
biweekly and files bugs for significant regressions. As a
watcher, monitor for sudden spikes that correlate with
recent changes.

**SHIP Status Dashboard** — Check
[SHIP Status](https://ship-status.ci.openshift.org/) when
you suspect an infrastructure-wide issue. If multiple
unrelated jobs are failing simultaneously, an
infrastructure outage is more likely than a product
regression.

---

## Substep: investigating-failures

### Context

When a payload is rejected or a Component Readiness cell
turns red, investigation follows this general pattern:

1. **Identify the failing job** — From the Release
   Controller or Sippy, click through to the
   [Prow](https://prow.ci.openshift.org/) job results
   page
2. **Read the job logs** — Look at the test failure
   output, particularly the JUnit results and the
   `aggregation-jobrun-summary.html` artifact (an HTML
   summary of all test runs in the job)
3. **Check for patterns** — Use
   [CI Search](https://search.ci.openshift.org/) to see
   if the same error appears in other jobs or platforms.
   CI Search also has a charting view to visualize failure
   frequency over time.
4. **Find the culprit** — Use `oc adm release info
   --commit-urls` (from Step 5) to see which PRs are in
   the rejected payload but not in the last accepted one.
   Cross-reference with the test failure to identify the
   offending change.
5. **Hand off** — File a Jira ticket and tag the owning
   team, or initiate a revert if it's a clear product
   regression.

**Important:** Only analyze **blocking** jobs when
investigating payload rejections. If no blocking jobs
failed, the payload should not be rejected. Informing
jobs provide useful signal but do not gate payload
acceptance.

---

## Substep: responding-reverts

### Context

Once you've identified a product regression, TRT's
"Revert First, Ask Questions Later" principle (from
Step 1) applies: the default action is to revert the
offending PR. This unblocks the release pipeline
immediately. The component team then works on a "fix
forward" PR that can be properly tested.

The revert process at a high level:
1. Identify the offending PR using the investigation
   steps above
2. Create a revert PR in the component's repository
3. File a Jira ticket in TRT or OCPBUGS linking the
   revert to the original PR
4. Notify the original PR author and the component team
5. Post in #forum-ocp-release-oversight so the team is
   aware

The full revert workflow — including tooling and
templates — is covered in detail in Step 9.

---

## Substep: responding-other

### Context

Not every failure is a product regression. Here's how
to handle the other categories:

**Infrastructure issue → Force-accept the payload**

If the failure is caused by infrastructure instability
(cloud provider issues, CI cluster problems, registry
outages) rather than a product regression, you can
force-accept the payload. This requires Release
Controller admin access, which TRT members have.
Force-accepting is appropriate when:
- The failure is clearly infrastructure-related (e.g.
  GCP quota exhaustion, AWS region outage)
- The same tests pass in other recent payloads
- Retrying the job would likely succeed

**Test regression → File a bug**

If a test itself is broken (not the product it tests),
file a Jira ticket against the test owner and categorize
it as a test regression. This is separate from a product
regression — the product works correctly, but a test
needs fixing.

**Persistent blocking failure → Move job to informing**

As a last resort, if a blocking job fails consistently
due to a known issue that can't be quickly resolved, TRT
may temporarily move it from the "blocking" suite to
"informing." This is a stop-gap — the job still runs but
no longer gates payload acceptance. This decision is made
as a team, not unilaterally by the watcher.

---

## Substep: ai-tools

### Context

TRT has developed AI-assisted tools that significantly
speed up payload analysis. These are actively evolving —
what's described here reflects the current state as of
your onboarding.

**Payload Analysis Agent** — An autonomous agent that
analyzes rejected release payloads. When a nightly is
rejected, the agent automatically:
- Inspects the failing jobs and test results
- Identifies the likely root cause PR
- Generates a detailed HTML report with its analysis
- Can auto-file Jira bugs and create revert PRs

Reports are linked from the payload detail page on the
Release Controller. Look for the AI analysis link when
investigating a rejected payload.

**`/ci:` Slack commands** — Interactive commands available
in Slack via the `ai-helpers` plugin:
- `/ci:analyze-payload` — Analyze a rejected payload
- `/ci:analyze-prow-job-test-failure` — Root-cause
  analysis of a specific Prow job failure
- `/ci:payload-revert` — Generate a revert PR for a
  payload regression

Type any of these in a Slack message to get started. The
bot will prompt you for the required parameters.

**Sippy Chat** — An AI-powered chat interface for
querying CI data, available at
[sippy-auth/chat](https://sippy-auth.dptools.openshift.org/sippy-ng/chat).
Ask natural-language questions like "What is the pass
rate for e2e-aws-ovn on {{DEV_VERSION}}?" or "What tests are
flaking on GCP this week?" Requires Red Hat SSO
authentication.

As a new watcher, start by reviewing the AI-generated
payload analysis reports to understand how experienced
watchers analyze failures. The reports model the
investigation process and will accelerate your learning.

---

## Substep: rotation

### Context

The watcher rotation is biweekly — each team member
serves a two-week rotation before handing off to the next
person.

**Rotation schedule:**
- The schedule is maintained in the
  [watcher rotation spreadsheet](https://docs.google.com/spreadsheets/d/1SysVHL12nRsRZq6su9puh7wNGtKM4ggj1uprTD4srUU/edit?gid=1723723419#gid=1723723419)
- The
  [rotation process document](https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit?tab=t.0#heading=h.yg3sfsodg686)
  describes how the rotation is managed

**Finding the current watcher:**
- Type `@team-technical-release` in any Slack compose
  box (without sending) to see who's in the watcher alias
- Check the rotation spreadsheet
- Ask in #forum-release-oversight-staff

**Schedule adjustments:**
- Swaps for PTO or other conflicts are coordinated in
  #forum-release-oversight-staff
- The rotation pauses during official company holidays

**Primary and secondary:**
The rotation uses a primary/secondary model. The primary
watcher has hands-on-keyboard responsibility. The
secondary (typically the previous rotation's primary)
provides backup and monitors secondary concerns like the
disruption dashboard.

---

## Substep: meetings

### Context

The watcher participates in several recurring meetings:

**Watcher Sync (Monday / Wednesday / Friday)** — Morning
kick-off to review overnight CI health, discuss active
regressions, and coordinate the day's triage work. This
is the most important meeting during your rotation.

**Watcher-focused meetings (Tuesday / Thursday)** — More
focused sessions for deeper investigation of ongoing
issues, disruption review, and process improvements.

**QSE Meeting (Monday / Wednesday / Friday, 8:00 AM
ET)** — Quality, Stability, and Enablement meeting where
non-blocking regressions and Component Readiness status
are discussed with stakeholders beyond TRT.

**TRT Office Hours (Thursday)** — An open session in
#forum-ocp-release-oversight where anyone can ask
questions about release health, CI failures, or TRT
processes. This was introduced in Step 2.

All meeting invites come through the
openshift-technical-release-staff Google Group, which you
verified in Step 2. If you're missing any invites, check
that your Google Group membership is active.

---

## Substep: shadow-rotation

### Action

If you arranged to shadow a watcher in Step 1, now is
the time to do it. If you haven't yet, reach out to the
current watcher and arrange to shadow their rotation.

Shadowing means:
- Attend all watcher meetings during their rotation
- Watch how they investigate payload rejections in real
  time
- Ask questions about their decision-making process
  (when to revert vs. wait, when to force-accept)
- Try doing your own parallel investigation alongside
  them — compare your conclusions with theirs

After shadowing, discuss with your manager when you'll
take your first solo rotation. Most new hires complete
one shadow rotation before going solo, with a secondary
watcher available as backup.

---

## Substep: sister-team-intros

### Action

Now that you have context from Steps 1 through 5 on
TRT's tools, repositories, and workflows, schedule
introductory meetings with TRT's sister teams. These
introductions were deferred from Step 1 so you'd have
enough background to ask informed questions.

Schedule a 30-minute introduction with a contact from
each team. Ask your manager or onboarding buddy for the
right person to reach out to, or post in each team's
channel:

- **ART (Automated Release Tooling)** — Builds and
  assembles the release payloads that TRT qualifies. Ask
  about the payload assembly process and how build
  failures affect the release pipeline.
  Slack: #team-art

- **CRT (Continuous Release Tooling)** — Owns the
  release-controller infrastructure. Ask about how
  payload verification is orchestrated and how blocking
  vs. informing jobs are configured.
  Slack: #forum-ocp-crt

- **DPTP (Developer Productivity and Test Platform)** —
  Provides the CI platform (Prow). Ask about CI
  infrastructure architecture and how to escalate
  infrastructure issues during your watcher rotation.
  Slack: #forum-ocp-testplatform

- **ERT (Errata Reliability Team)** — Owns the errata
  (advisory) pipeline for shipping fixes to customers.
  Ask about how the z-stream (patch release) process
  works and how TRT's release qualification feeds into
  errata.
  Slack: #forum-ocp-ert

These conversations will give you working relationships
with the people you'll collaborate with most during your
watcher rotations.

---

## Known References

- Component Readiness (main view):
  https://sippy.dptools.openshift.org/sippy-ng/component_readiness/main
  (verified: 2026-06-05)
- Release Controller (amd64):
  https://amd64.ocp.releases.ci.openshift.org/
  (verified: 2026-06-05)
- Grafana disruption dashboard:
  https://grafana-loki.ci.openshift.org/d/ISnBj4LVk/disruption?orgId=1
  (verified: 2026-06-05)
- Sippy Chat:
  https://sippy-auth.dptools.openshift.org/sippy-ng/chat
  (verified: 2026-06-05)
- SHIP Status Dashboard:
  https://ship-status.ci.openshift.org/
  (verified: 2026-06-05)
- Watcher rotation doc:
  https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit
  (verified: 2026-06-05)
- Watcher rotation spreadsheet:
  https://docs.google.com/spreadsheets/d/1SysVHL12nRsRZq6su9puh7wNGtKM4ggj1uprTD4srUU/edit
  (verified: 2026-06-05)
- Watcher Slack alias: @team-technical-release
  (verified: 2026-06-05)
- TRT public channel: #forum-ocp-release-oversight
  (verified: 2026-06-05)
- TRT staff channel: #forum-release-oversight-staff
  (verified: 2026-06-05)
- TRT alert channel: #trt-alert
  (verified: 2026-06-05)
- ART channel: #team-art
  (verified: 2026-06-05)
- CRT channel: #forum-ocp-crt
  (verified: 2026-06-05)
- DPTP channel: #forum-ocp-testplatform
  (verified: 2026-06-05)
- ERT channel: #forum-ocp-ert
  (verified: 2026-06-05)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"
