---
id: 11-thirty-day-goals
title: 30-Day Goals
order: 11
audience: new-hire

claims:
  channels:
    - forum-ocp-release-oversight
    - forum-release-oversight-staff
  urls:
    - https://sippy.dptools.openshift.org/sippy-ng/component_readiness/main
    - https://amd64.ocp.releases.ci.openshift.org/
    - https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit
    - https://docs.google.com/spreadsheets/d/1SysVHL12nRsRZq6su9puh7wNGtKM4ggj1uprTD4srUU/edit
  repos: []
  tools: []

substeps:
  - id: overview
    type: learning
    group: orientation
    summary: "Understand what you should accomplish in your first 30 days"

  - id: week-one
    type: learning
    group: orientation
    summary: "Know your Week 1 milestones"

  - id: week-two
    type: learning
    group: shadowing
    summary: "Know your Week 2 milestones"

  - id: shadow-rotation
    type: learning
    group: shadowing
    summary: "Understand what a successful shadow rotation looks like"

  - id: week-three
    type: learning
    group: independence
    summary: "Know your Week 3 milestones"

  - id: week-four
    type: learning
    group: independence
    summary: "Know your Week 4 milestones"

  - id: checkpoint
    type: learning
    group: assessment
    summary: "Understand the Day 30 self-assessment"

  - id: stretch-goals
    type: learning
    group: assessment
    summary: "Know the stretch goals beyond 30 days"

  - id: plan
    type: action
    summary: "Create your 30-day plan with your onboarding buddy"
---

## Substep: overview

### Context

You've completed the structured onboarding (Steps 1–8).
You understand TRT's mission, tools, dashboards,
repositories, workflows, and documentation. Now it's time
to put that knowledge into practice.

These 30-day goals are designed to take you from
"I understand TRT conceptually" to "I can do TRT work
independently." Each milestone builds on what you learned
in the onboarding steps.

**Guiding principle:** The goal is not speed — it's
confidence. You should feel ready to handle a watcher
rotation without needing to ask your buddy about every
decision. Some of these milestones will happen naturally
during your rotations; others require deliberate effort.

**Tracking:** Share these goals with your onboarding
buddy so they can help create opportunities for you
(e.g., scheduling your shadow rotation during a week with
active regressions, or flagging a good first-triage
candidate).

---

## Substep: week-one

### Context

**Week 1: Foundation**

By the end of your first week, you should have:

- Completed onboarding Steps 1–5 (mission, channels,
  dashboards, repositories, and tools)
- Verified all access (Rover groups, Google Groups, Jira
  projects, cluster login) — Step 2
- Bookmarked your essential dashboards — Step 3
- Installed the CLI tools on your workstation (`oc`, `yq`,
  pull secret) — Step 5
- Successfully logged into the CI cluster and run
  `oc adm release info` against a recent payload — Step 5
- Started reading the Priority 1 documents from Step 10
  (watcher rotation doc, Component Readiness User Guide)

**Measurable check:** You can navigate to the
[Release Controller](https://amd64.ocp.releases.ci.openshift.org/)
(introduced in Step 3), find the latest nightly payload,
and explain whether it was accepted or rejected and why.

---

## Substep: week-two

### Context

**Week 2: Shadowing**

By the end of your second week, you should have:

- Completed onboarding Steps 6–8 (watcher role, processes,
  reading list)
- Shadowed at least one full watcher rotation (see next
  substep for details)
- Walked through the
  [Component Readiness](https://sippy.dptools.openshift.org/sippy-ng/component_readiness/main)
  dashboard (set up in Step 3) with your buddy and
  identified current regressions
- Observed or discussed at least one revert workflow
  (introduced in Step 9) — either a live one or a recent
  example from #forum-ocp-release-oversight (joined in
  Step 2)
- Finished the Priority 1 reading from Step 10

**Measurable check:** You can explain the difference
between a blocking and informing verification job, what
makes a Component Readiness cell turn red, and what the
SBAR (Situation, Background, Assessment, Recommendation)
process is for.

---

## Substep: shadow-rotation

### Context

Shadowing a watcher rotation is the single most important
milestone in your first two weeks. Here is what a
successful shadow looks like:

**Before the rotation:**
- Confirm the schedule on the
  [watcher rotation spreadsheet](https://docs.google.com/spreadsheets/d/1SysVHL12nRsRZq6su9puh7wNGtKM4ggj1uprTD4srUU/edit)
  (introduced in Step 1)
- Read the
  [watcher rotation process document](https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit)
  (introduced in Step 1) one more time
- Set up your browser with the watcher's daily dashboard
  tabs from Step 8

**During the rotation:**
- Follow along as the watcher performs the daily checks
  (Step 8): payload status, Component Readiness, disruption
  dashboard
- Ask the watcher to narrate their decision-making: "Why
  did you investigate that test?" "How did you decide this
  was infrastructure vs. a real regression?"
- Practice the investigation flow yourself on a current
  failure — use Sippy (set up in Step 3), Prow (Step 3),
  and `oc adm release info` (Step 5) to trace a rejected
  payload back to specific PRs (pull requests)
- If a triage action is needed, watch the watcher file the
  Jira bug and link it to the Component Readiness
  regression (Step 9)

**After the rotation:**
- Write down what surprised you or confused you
- Discuss with your buddy: What would you have done
  differently? What did you miss?

---

## Substep: week-three

### Context

**Week 3: First Solo Rotation**

By the end of your third week, you should have:

- Completed your first solo watcher rotation with your
  buddy available as backup (not watching over your
  shoulder, but reachable for questions)
- Performed the daily watcher checks independently:
  Release Controller status, Component Readiness review,
  disruption dashboard scan (all from Step 8)
- Triaged at least one Component Readiness regression
  end-to-end: investigated the failure, determined
  whether it's a real regression or infrastructure noise,
  and either filed a Jira bug or confirmed an existing
  triage record (Step 9)
- Posted at least one update in
  #forum-ocp-release-oversight (joined in Step 2) about
  payload status or a regression you investigated
- Started reading the Week 2 documents from your Step 10
  reading plan (docs.ci.openshift.org deep dive, repo
  READMEs)

**Measurable check:** You can complete a full day's watcher
duties — from morning payload check through end-of-day
handoff — without needing to ask your buddy for help on
the basic workflow. (Asking about edge cases or unfamiliar
failures is expected and encouraged.)

---

## Substep: week-four

### Context

**Week 4: Consolidation**

By the end of your fourth week, you should have:

- Completed a second solo watcher rotation, handling any
  issues that arise with increasing confidence
- Participated in a revert workflow in some capacity:
  opened a revert PR using revertomatic (introduced in
  Step 9), reviewed someone else's revert, or assisted
  the watcher with a revert decision
- Contributed at least one fix to this onboarding guide
  — either something you noticed during your own
  onboarding or feedback from testing a step. Use the
  "Fix onboarding" command (from CONTRIBUTING.md) or
  open a PR directly.
- Attended and participated in at least one of TRT's
  recurring meetings: bug triage, disruption review, or
  team standup
- Read the Improving CI Signal enhancement proposal
  from your Step 10 reading plan to understand the
  design rationale behind TRT's approach

**Measurable check:** When a payload is rejected, you can
independently investigate the failure, determine whether
it's a real regression or infrastructure, and take the
appropriate action (file a bug, initiate a revert, or
force-accept) — escalating to the team only when the
situation is genuinely ambiguous.

---

## Substep: checkpoint

### Context

**Day 30 Self-Assessment**

At the end of your first month, review these questions
with your onboarding buddy:

*Can I do the job?*
- Can I complete a watcher rotation independently?
- Can I triage a Component Readiness regression from
  red cell to filed Jira bug?
- Can I trace a rejected payload back to the offending
  PR and decide whether a revert is warranted?
- Do I know when to force-accept a payload and when not
  to?

*Do I know where to find things?*
- Can I navigate Sippy, the Release Controller, Prow,
  and CI Search without looking up URLs?
- Do I know which Slack channel to use for which purpose?
- Can I find the relevant documentation when I encounter
  something unfamiliar?

*Am I part of the team?*
- Have I posted in #forum-ocp-release-oversight during
  a rotation?
- Have I contributed a fix or improvement to the
  onboarding guide?
- Have I attended at least one recurring team meeting?
- Do I know who to ask when I'm stuck?

It's fine if some answers are "not yet" — discuss them
with your buddy and plan for month two.

---

## Substep: stretch-goals

### Context

These goals are aspirational — don't feel pressured to
complete them in 30 days, but keep them in mind as you
settle into the role:

**Month 2–3:**
- Contribute a code change to
  [openshift/sippy](https://github.com/openshift/sippy)
  (explored in Step 4) — a bug fix, dashboard improvement,
  or test coverage addition
- Onboard a new component to Component Readiness by
  working with a component team (process from Step 9)
- Shadow the release process through a milestone
  transition (Feature Freeze, Code Freeze, or GA —
  from Step 9)
- Update or add a test mapping in
  [openshift-eng/ci-test-mapping](https://github.com/openshift-eng/ci-test-mapping)
  (explored in Step 4)

**Ongoing:**
- Develop expertise in a specific area: disruption
  analysis, payload infrastructure, statistical methods,
  or CI tooling
- Mentor the next new team member through this
  onboarding guide — the best way to validate the guide
  is to use it
- Propose improvements to TRT processes through Jira
  cards in the TRT project (introduced in Step 3)

---

## Substep: plan

### Action

Meet with your onboarding buddy to create your personal
30-day plan:

- Review the milestones above and confirm which ones
  apply to the current team situation (e.g., if no
  reverts happen during your first month, the revert
  participation goal shifts to month two)
- Schedule your shadow rotation — coordinate with the
  current watcher via the
  [watcher rotation spreadsheet](https://docs.google.com/spreadsheets/d/1SysVHL12nRsRZq6su9puh7wNGtKM4ggj1uprTD4srUU/edit)
  (introduced in Step 1)
- Schedule your first solo rotation for week 3, with
  your buddy as backup
- Identify any access or tooling gaps that still need
  to be resolved
- Set a Day 30 check-in meeting with your buddy to go
  through the self-assessment

Share your plan in #forum-release-oversight-staff (joined
in Step 2) so the team knows your timeline and can support
you.

---

## Known References

- Component Readiness (main view):
  https://sippy.dptools.openshift.org/sippy-ng/component_readiness/main
  (verified: 2026-06-08)
- Release Controller (amd64):
  https://amd64.ocp.releases.ci.openshift.org/
  (verified: 2026-06-08)
- Watcher rotation process document:
  https://docs.google.com/document/d/1h5BDGJDX1_WyRfX81dvzEgiUg1gYDtfiYcylEjl3LK4/edit
  (verified: 2026-06-08)
- Watcher rotation spreadsheet:
  https://docs.google.com/spreadsheets/d/1SysVHL12nRsRZq6su9puh7wNGtKM4ggj1uprTD4srUU/edit
  (verified: 2026-06-08)
- openshift/sippy repo:
  https://github.com/openshift/sippy
  (verified: 2026-06-08)
- openshift-eng/ci-test-mapping repo:
  https://github.com/openshift-eng/ci-test-mapping
  (verified: 2026-06-08)
- TRT public channel: #forum-ocp-release-oversight
  (verified: 2026-06-08)
- TRT staff channel: #forum-release-oversight-staff
  (verified: 2026-06-08)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"
