---
id: 07-processes
title: Key Processes & Workflows
order: 7
audience: new-hire

claims:
  channels:
    - forum-ocp-release-oversight
    - forum-release-oversight-staff
  urls:
    - https://sippy.dptools.openshift.org/sippy-ng/component_readiness/main
    - https://amd64.ocp.releases.ci.openshift.org/
    - https://sippy-auth.dptools.openshift.org/sippy-ng/
    - https://github.com/openshift-eng/ci-test-mapping
    - https://github.com/openshift/release
    - https://github.com/openshift/sippy
  repos:
    - openshift-eng/ci-test-mapping
    - openshift/release
    - openshift/sippy
  tools: []

substeps:
  - id: revert-workflow
    type: learning
    group: revert-flow
    summary: "Understand the full revert workflow"

  - id: revert-tooling
    type: learning
    group: revert-flow
    summary: "Learn the revert tooling and conventions"

  - id: cr-statistics
    type: learning
    group: component-readiness
    summary: "Understand how Component Readiness detects regressions"

  - id: cr-triage
    type: learning
    group: component-readiness
    summary: "Learn the Component Readiness triage workflow"

  - id: cr-onboarding
    type: learning
    group: cr-onboarding
    summary: "Understand how new components are onboarded to Component Readiness"

  - id: payload-lifecycle
    type: learning
    group: payload-management
    summary: "Understand the payload lifecycle"

  - id: payload-overrides
    type: learning
    group: payload-management
    summary: "Learn when and how to force-accept or force-reject payloads"

  - id: release-milestones
    type: learning
    group: release-cycle
    summary: "Understand release cycle milestones and TRT's role"

  - id: trt-milestone-tasks
    type: learning
    group: release-cycle
    summary: "Learn TRT's specific tasks at each milestone"

  - id: bug-triage
    type: learning
    group: bug-management
    summary: "Learn the bug triage process"

  - id: release-blockers
    type: learning
    group: bug-management
    summary: "Understand release blockers and the SBAR exception process"

  - id: practice-triage
    type: action
    summary: "Practice investigating a Component Readiness regression"
---

## Substep: revert-workflow

### Context

Step 6 introduced the watcher's role in initiating reverts.
Here is the full end-to-end workflow TRT follows when a
product regression is detected.

**Detection:**
A regression can surface through multiple channels:
- The Payload Analysis Agent (introduced in Step 6)
  automatically analyzes rejected nightlies and identifies
  candidate PRs (pull requests) with a confidence score
- A watcher notices a new blocking failure and traces it to
  a specific PR using `oc adm release info --commit-urls`
  (from Step 5)
- A Component Readiness (CR — the regression detection
  system from Step 3) red cell appears and investigation
  links it to a recent change

**Decision to revert:**
TRT's default stance is "Revert First, Ask Questions Later"
(from Step 1). A revert is initiated when:
- The Payload Analysis Agent identifies a candidate with
  high confidence (85% or above) — immediate revert is
  recommended
- A watcher confirms a product regression through manual
  investigation
- Even changes that break non-blocking "informing" jobs are
  typically reverted

The revert is mechanical and non-punitive — it restores the
pipeline to a known-good state. The originating team then
works on an "un-revert" (a new PR that includes both the
original change and a fix for the regression).

**The revert process:**
1. Identify the offending PR using investigation tools
2. Create a revert PR in the component's repository (using
   revertomatic or `/ci:payload-revert` — see next substep)
3. File a Jira ticket in the TRT project linking the revert
   to the original PR. Use the `trt-incident` label.
4. Notify the original PR author and the component team
5. Post in #forum-ocp-release-oversight so the broader team
   is aware

**Approval policy:**
- The team that delivered the original PR is expected to
  approve the revert — this ensures they are part of the
  resolution process
- TRT uses a dual-approval policy: the person requesting
  the revert cannot be the same person who approves it
- If the originating team is unresponsive, the issue may be
  escalated to prevent the release from remaining regressed

**After the revert:**
- The component team can re-land their change once it
  passes the full suite of payload acceptance checks
- If a team believes their change was reverted incorrectly,
  they can "re-revert" and layer a fix on top
- After Feature Freeze (covered later in this step), TRT
  will make exceptions to allow un-reverts of feature PRs

---

## Substep: revert-tooling

### Context

TRT uses dedicated tooling to create revert PRs with
standardized formatting and metadata.

**revertomatic** — A CLI (command-line interface) tool that
automates revert PR creation. It clones the target
repository, creates a revert branch, and opens a pull
request with a standardized template that includes the Jira
reference, verification instructions, and a CC to the
original author.

Usage:

```
./revertomatic \
    -p https://github.com/openshift/some-operator/pull/123 \
    -j TRT-9999 \
    -v "/payload {{DEV_VERSION}} nightly blocking" \
    -c "This PR caused test X to fail in all nightly payloads"
```

Flags:
- `-p` — URL of the pull request to revert
- `-j` — Associated Jira ticket (e.g., TRT-9999)
- `-v` — Verification steps (e.g., which payload jobs to
  run to confirm the fix)
- `-c` — Context summary of the issue and impact

revertomatic is preferred over GitHub's native "Revert"
button because the button only appears if you have a
personal fork of the repository. revertomatic also ensures
consistent template formatting across all TRT reverts.

**Installation:**

revertomatic requires
[Go](https://go.dev/doc/install) and a GitHub
**classic** personal access token. The tool needs broad
repository access (it must operate on whichever
`openshift/*` repo had the regression), which maps
to classic token scopes rather than the newer
fine-grained tokens. Create a
[classic token](https://github.com/settings/tokens)
with these scopes:

- `repo` — read PRs, commit statuses; create forks
  and revert PRs
- `read:org` — read organization membership
- `user` — read your profile (used to find your fork)

Then:

```
export GITHUB_TOKEN="<your-token>"
git clone https://github.com/stbenjam/revertomatic.git
cd revertomatic
go build -o revertomatic .
# Optional: move the binary onto your PATH
mv revertomatic ~/go/bin/
```

For large repositories (e.g., openshift/kubernetes), a
temporary clone is slow. Pass a local clone with the
`-l`, `-u`, and `-r` flags:

- `-l` — Path to a local clone of the repository
- `-u` — Name of the upstream remote (default: "upstream")
- `-r` — Name of your fork remote (default: "origin")

> **Alternative:** If you use
> [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview)
> or Cursor, the `ci` plugin from
> [openshift-eng/ai-helpers](https://github.com/openshift-eng/ai-helpers)
> provides `/ci:revert-pr <pr-url> <jira-ticket>` — a
> reimplementation of the revertomatic workflow as an
> AI-assisted command. Install it with:
> `/plugin marketplace add openshift-eng/ai-helpers`
> then `/plugin install ci@ai-helpers`.

**`/ci:payload-revert`** — A command from the
[ai-helpers](https://github.com/openshift-eng/ai-helpers)
`ci` plugin (introduced in Step 6) that can automate the
creation of both the Jira bug and the revert PR for
high-confidence Payload Analysis Agent candidates.

**Conventions:**
- Revert PR titles must include a Jira reference (e.g.,
  `TRT-1234: Revert "original PR title"`)
- The PR body should explain what regression the revert
  addresses and link to the Payload Analysis Agent report
  if one exists
- CC the original PR author so they're aware and can begin
  work on a fix-forward

---

## Substep: cr-statistics

### Context

Step 3 introduced Component Readiness as a dashboard, and
Step 6 covered how watchers use it day-to-day. Here is how
the system works under the hood.

Component Readiness compares test pass rates between a
"sample" period (typically the current 7 days) and a "base"
period (the equivalent window from the previous release).
It uses Fisher's Exact Test — a statistical test for
determining whether two proportions differ significantly —
to flag regressions.

**A test is flagged as a regression when ALL three criteria
are met:**

1. **Statistical significance** — Fisher's Exact Test
   indicates with statistical confidence that the test's
   performance has worsened. The system is calibrated to be
   wrong about 5% of the time.

2. **Minimum failure count** — At least 3 failures must
   occur within a one-week period. This prevents flagging
   single flaky runs.

3. **Pity factor (pass-rate drop of 5% or more)** — The
   pass rate must drop by at least 5% from its historical
   baseline. This prevents flagging statistically
   significant but practically insignificant drops (e.g.,
   100% to 99.5%).

**Regression categories:**
- **Significant regression** — Meets all three criteria.
  Shown as a red cell in the CR dashboard.
- **Extreme regression** — The pass-rate drop is 15% or
  greater (e.g., 100% to 83%). These are the most urgent
  regressions.

**New test requirements:**
- Tests without a historical baseline from the prior
  release (marked with a "%" icon in CR) must meet a 95%
  pass rate to avoid being flagged
- This prevents teams from landing new, low-quality tests
  that immediately show as regressions

Every red cell in Component Readiness is treated as a
release blocker unless an explicit exception is granted
through the SBAR (Situation, Background, Assessment,
Recommendation) process (covered later in this step).

---

## Substep: cr-triage

### Context

When a red cell appears in
[Component Readiness](https://sippy.dptools.openshift.org/sippy-ng/component_readiness/main),
triage follows this workflow:

**Step 1: Investigate the regression**
- Click the red cell to open the test details page
- Review the failure pattern — is it consistent or
  intermittent?
- Check whether the regression correlates with a specific
  PR or payload

**Step 2: File a Jira bug**
- Create a bug in the OCPBUGS project with details about
  the failure, affected platforms, and reproduction steps
- Include the exact test name in the bug description

**Step 3: Link the bug to the regression**

There are two methods to establish the link between the
Jira bug and the CR regression:

*Method A — Mention the test name in Jira:*
- Include the exact test name (without prefixes like
  `openshift-tests-upgrade`) in the bug description or a
  comment
- Wrap the test name in a code block to prevent Jira from
  mangling special characters
- Sippy (set up in Step 3) automatically detects the
  mention and links the bug within about 1 hour

*Method B — Use the Sippy triage UI:*
- From the test details page in
  [sippy-auth](https://sippy-auth.dptools.openshift.org/sippy-ng/)
  (introduced in Step 6), click the "triage" button
- Navigate to the "new triage" tab
- Paste the Jira bug URL and fill in the description and
  triage type (e.g., Product, CIInfra)
- Click to create the triage record

**Triage record lifecycle:**
- When the fix PR merges or the bug moves to ON_QA, the
  triage icon changes to "believed fixed"
- When the fix lands in a payload and tests stop failing,
  the record auto-resolves and Sippy posts a comment on the
  Jira bug confirming all regressions are resolved
- If a regression reappears after being resolved, Sippy
  flags it with a warning icon

**Bulk regression handling:**
- If a component has 4 or more red cells, Component
  Readiness can automatically generate a summary Jira card
- Teams should then create linked issues for each specific
  regression

---

## Substep: cr-onboarding

### Context

When a new feature or component needs to be monitored in
Component Readiness, it goes through an onboarding process.

**Prerequisites:**
- At least 5 tests running at least 7 times per week on
  all supported platforms
- Each test must pass at or above 95% for the last 7 days
- Onboarding requires a lead time of 2 to 3 sprints before
  Code Freeze

**Onboarding steps:**
1. **Create a Jira component** — Register the component in
   the OCPBUGS Jira project. Creating a new component
   requires approval from an OpenShift Staff Engineer.

2. **Map tests to the component** — Use the
   [ci-test-mapping](https://github.com/openshift-eng/ci-test-mapping)
   repository to associate tests with the new component.
   Tests are mapped by suite name (mapping by individual
   job name is discouraged).

3. **Configure Sippy** — In the
   [openshift/sippy](https://github.com/openshift/sippy)
   repository, update the job-name parsing regexes to
   correctly extract variants (e.g., Platform, Owner) for
   the new component's jobs.

The end goal is for component teams to self-monitor their
components in the CR dashboard, with TRT providing
oversight. This aligns with the long-term vision described
in Step 1.

---

## Substep: payload-lifecycle

### Context

An OpenShift release payload is a collection of container
images that together form an installable OCP (OpenShift
Container Platform) version. The Release Controller
(introduced in Step 3) manages the lifecycle of these
payloads.

**How payloads move through the pipeline:**

```
  Source images (PRs merge)
        |
  CI payload assembled
        |
  Verification jobs run
        |
  Blocking jobs pass?
    YES --> Accepted
    NO  --> Rejected
        |
  (if accepted)
  Candidate stream
        |
  (after further qualification)
  Stable stream (GA release)
```

**Verification job tiers:**
- **Blocking** — Must pass for the payload to be accepted.
  If any blocking job fails, the payload is rejected.
  These include critical e2e (end-to-end) tests on major
  platforms.
- **Informing** — Provides signal but does not gate
  acceptance. Failures are monitored but do not prevent
  promotion.
- **Async** — Long-running jobs that do not block
  acceptance. Results are tracked for trend analysis.

The Release Controller creates ProwJob custom resources,
which invoke ci-operator to execute the verification tests.
Job definitions live in the
[openshift/release](https://github.com/openshift/release)
repository.

**Key concept:** A payload can pass all blocking jobs even
if individual tests within those jobs have a high failure
rate — the test may have passed in that particular run.
This is why Component Readiness (which tracks pass rates
over time) catches regressions that individual payload runs
miss.

---

## Substep: payload-overrides

### Context

TRT members with admin access to the
[Release Controller](https://amd64.ocp.releases.ci.openshift.org/)
can manually override the automated acceptance process.

**Force-accept** — Override a rejected payload to
"Accepted." Appropriate when:
- The failure is clearly infrastructure-related (e.g., AWS
  region outage, CI cluster problem, GCP quota exhaustion)
- The same tests pass in other recent payloads
- No payload has been accepted for an extended period
  (typically 18 or more hours), and the failures are
  confirmed as transient

**Force-reject** — Override a payload to "Rejected" before
verification completes. Appropriate when:
- The payload is known to contain a breaking change
  (DOA — dead on arrival)
- Rejecting early avoids wasting CI resources on
  verification jobs that will inevitably fail

**When NOT to override:**
- Never force-accept if there is any possibility the
  failure is a real product regression
- Never force-reject based on speculation — only when the
  regression is confirmed

Force-accept and force-reject actions are tracked in
Sippy (set up in Step 3) and recorded in the release
payload metadata for auditability. The watcher who performs
the override posts in #forum-ocp-release-oversight with the
rationale.

---

## Substep: release-milestones

### Context

Each OCP release follows a lifecycle with defined
milestones. Here are the key ones and what they mean for
TRT:

**Feature Freeze**
- After this date, no new features are accepted into the
  release branch
- Any changes generally require an associated Jira bug
- TRT intensifies stabilization efforts — the focus shifts
  from enabling features to preventing regressions
- Exception: TRT will allow "un-reverts" of feature PRs
  that were reverted due to regressions during the
  development phase

**Code Freeze**
- Only critical bug fixes are allowed after this point
- TRT's role in managing release blockers and triaging
  bugs becomes paramount
- As Code Freeze approaches, there is often a large queue
  of PRs waiting to merge — TRT may manage merges in
  batches to mitigate regression risk
- Priority is given to complete features with tests that
  can be merged in any order to facilitate fast reverts

**Go/No-Go Decision**
- A scheduled meeting where release readiness is assessed
  based on Component Readiness board status, outstanding
  release blockers, and overall CI signal
- Typically occurs shortly before the GA delivery window
  opens

**General Availability (GA)**
- The release ships to customers
- TRT's primary responsibilities shift — see next substep

The release schedule is published as a Gantt chart on
Product Pages (Red Hat's internal release planning tool).
Your onboarding buddy can help you find the current
schedule.

---

## Substep: trt-milestone-tasks

### Context

TRT performs specific tasks at each release milestone:

**Before Feature Freeze:**
- Ensure aggregated release-gating jobs are correctly
  configured for the upcoming release
- Verify Component Readiness baselines are established

**At Code Freeze:**
- Focus shifts entirely to stabilization
- Aggressively revert any changes that cause instability
- Monitor the merge queue and manage ordering to minimize
  regression risk

**At Branch Cut** (when the main branch opens for the next
release):
- Add the new development release to Sippy (set up in
  Step 3) and Component Readiness
- Force-accept the first two payloads for each
  architecture on the new branch — this is necessary to
  bootstrap upgrade jobs and multi-architecture payload
  construction
- Update monitoring alerts and dashboards to include the
  new release

**At GA:**
- Shut down aggregated jobs for the GA'd release
- Stop active monitoring of the GA'd release — it
  transitions to the patch manager (introduced in Step 1)
- Update release tables and configurations that drive
  dashboards and metrics
- Transition alerting and CI focus to the next development
  branch

TRT tracks these tasks using Jira cards with subtasks in
the TRT project, ensuring nothing is missed across the
milestone transitions.

---

## Substep: bug-triage

### Context

TRT acts as an "expediter" for bugs that affect release
quality. The triage process moves bugs from unactioned to
assigned and owned:

**Identifying bugs to triage:**
- Component Readiness regressions (automatically filed)
- Incoming reports in #forum-ocp-release-oversight
- Bugs with the `trt` label in Jira
- The TRT Triage dashboard in Jira (filtered to show
  active bugs, excluding CLOSED, VERIFIED, and ON_QA)

**Jira labels used by TRT:**
- `component-regression` — Auto-added for bugs created
  from Component Readiness regressions
- `trt` — General label for bugs TRT is tracking
- `trt-incident` — High-priority issues (reverts,
  payload-blocking problems)
- `sbar-candidate` — Bugs undergoing the SBAR exception
  process (see next substep)
- `trt-standup` — Issues TRT wants to discuss at standups

**Triage workflow (NEW to ASSIGNED):**
1. Pick up a NEW bug from the triage dashboard or assign
   it to yourself
2. Verify the bug is filed under the correct product and
   component — reassign if needed
3. Set priority and severity according to OCP bug process
   guidelines
4. Set the target version to the current development
   release
5. Route the bug to the responsible team or individual
6. For bugs without a clear owner, move to the "unknown"
   component for further routing

**Follow-up:**
- TRT monitors bugs through resolution, acting as
  expediters for bugs in POST and ON_QA states
- If a PR review conversation goes stale, TRT may reach
  out to reviewers to unblock progress
- The triage dashboard is formally reviewed weekly on
  Mondays

---

## Substep: release-blockers

### Context

Release blockers are bugs that must be resolved before a
release can ship.

**Default policy:**
- All Component Readiness regressions are automatically
  marked as `Release Blocker: Approved`
- A bot enforces this — if someone removes the release
  blocker label while the test regression is still active,
  the bot re-applies it
- Other critical bugs can also be proposed as blockers by
  TRT or other teams

**Release Blocker field values:**
- **Proposed** — A bug is proposed as a blocker and needs
  review
- **Approved** — Confirmed as a blocker — must be fixed or
  have an accepted SBAR
- **Rejected** — The blocker exception has been approved
  (via SBAR), allowing the release to proceed

**The SBAR process (exception mechanism):**

When a release blocker cannot be fixed before GA
(General Availability), the responsible team uses the SBAR
(Situation, Background, Assessment, Recommendation)
process:

1. Add the `sbar-candidate` label to the Jira bug
2. Write the SBAR document:
   - **Situation** — Clear description of the problem
   - **Background** — Context and impact analysis
   - **Assessment** — Risk evaluation: why it is safe to
     ship despite the regression
   - **Recommendation** — Proposed course of action
3. Submit the SBAR to leadership for review
4. If approved: the bug's Release Blocker field is changed
   to "Rejected", and the release can ship with the known
   issue
5. If denied: the team must continue working to fix the
   issue before GA

The principle is: every Component Readiness regression
results in either a fix or an approved SBAR — no
exceptions.

---

## Substep: practice-triage

### Action

Open the
[Component Readiness dashboard](https://sippy.dptools.openshift.org/sippy-ng/component_readiness/main)
for the current development release
({{DEV_VERSION}}) and practice investigating a regression:

- Find a red cell in the dashboard (if none exist on the
  main view, try selecting a specific platform or network
  variant)
- Click through to the test details page
- Review the failure history and identify when the
  regression started
- Cross-reference the timeline with recent PRs using
  `oc adm release info --commit-urls` (from Step 5)
- Check whether a triage record already exists (look for
  the triage icon on the test details page)

You do not need to file a real bug — this is practice.
Discuss what you found with your onboarding buddy or the
current watcher to validate your investigation approach.

---

## Known References

- Component Readiness (main view):
  https://sippy.dptools.openshift.org/sippy-ng/component_readiness/main
  (verified: 2026-06-08)
- Release Controller (amd64):
  https://amd64.ocp.releases.ci.openshift.org/
  (verified: 2026-06-08)
- Sippy (authenticated):
  https://sippy-auth.dptools.openshift.org/sippy-ng/
  (verified: 2026-06-08)
- ci-test-mapping repo:
  https://github.com/openshift-eng/ci-test-mapping
  (verified: 2026-06-08)
- openshift/release repo:
  https://github.com/openshift/release
  (verified: 2026-06-08)
- openshift/sippy repo:
  https://github.com/openshift/sippy
  (verified: 2026-06-08)
- TRT public channel: #forum-ocp-release-oversight
  (verified: 2026-06-08)
- TRT staff channel: #forum-release-oversight-staff
  (verified: 2026-06-08)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"
