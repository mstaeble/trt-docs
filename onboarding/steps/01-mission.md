---
id: 01-mission
title: TRT Mission & Team Structure
order: 1
audience: new-hire

claims:
  channels:
    - forum-ocp-release-oversight
  urls: []
  repos: []
  tools: []

substeps:
  - id: mission
    type: learning
    summary: "Understand TRT's core mission"

  - id: revert-first
    type: learning
    summary: "Learn the 'Revert First' principle"

  - id: org-structure
    type: learning
    summary: "See where TRT fits in the org"

  - id: sister-teams
    type: learning
    summary: "Understand sister teams and ownership boundaries"

  - id: roles
    type: learning
    summary: "Learn TRT's key roles"

  - id: scope
    type: learning
    summary: "Understand what's in and out of scope"

  - id: meet-and-greets
    type: action
    summary: "Schedule introductions"
---

## Substep: mission

### Context

TRT acts as SRE for the OpenShift release pipeline. The
team's primary goal is ensuring the highest sustainable rate
of development across all of OpenShift.

Core responsibilities:
- Qualifying every OCP release
- Continuously monitoring CI signals for go/no-go decisions
- Owning end-to-end (e2e) test job definitions
- Managing CI/CD pipelines via Prow

---

## Substep: revert-first

### Context

TRT's defining cultural principle is "Revert First, Ask
Questions Later." The default state of the release branch
must always be green.

How it works in practice:
1. Sippy detects a significant increase in test failures
   after a PR merges
2. An alert is filed, tagging the component team, PR author,
   and TRT
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

```
    Hybrid Platforms (org)
      └─ Multi-product and Engineering Experience (org)
          └─ SHIP (team group)
              ├─ Technical Release Team (TRT)  ← you are here
              ├─ Automated Release Tooling (ART)
              ├─ Continuous Release Tooling (CRT)
              ├─ Errata Reliability Team (ERT)
              └─ Test Platform (DPTP)
```

TRT's public Slack channel is #forum-ocp-release-oversight.

---
## Substep: sister-teams

### Context

TRT works closely with four sister teams within SHIP. Each
has distinct ownership:

- **ART (Automated Release Tooling)** — Builds and assembles
  release payloads. TRT qualifies them.
- **CRT (Continuous Release Tooling)** — Owns the
  release-controller infrastructure. TRT owns the job
  definitions within it.
- **DPTP (Test Platform)** — Provides the CI platform (Prow).
  Does not own the release jobs — TRT does.
- **ERT (Errata Reliability Team)** — Owns the errata
  pipeline for shipping advisories.

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
ensuring z-stream nightlies are produced reliably.

The long-term goal is for component teams to self-monitor
via Component Readiness, with TRT providing oversight.

---

## Substep: scope

### Context

TRT is focused on the current development branch (e.g.
4.dev/master), not older supported releases. Anything
older than the current dev branch is handled by the patch
manager after GA.

The handoff from TRT to the patch manager occurs at GA.

---

## Substep: meet-and-greets

### Action

Schedule introductory meetings with:

- [ ] Your TRT manager — align on 30/60/90-day goals
- [ ] Current TRT watchers — arrange to shadow a rotation
- [ ] An ART contact — understand how payloads are built
- [ ] A CRT contact — understand the release-controller
- [ ] A DPTP contact — understand the CI platform

Ask your manager or buddy who the right contacts are for
each.

---

## Known References

- TRT public channel: #forum-ocp-release-oversight
  (verified: 2026-06-03)
- Org: SHIP under Hybrid Platforms (verified: 2026-06-03)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"

## Next Step

Start a new chat thread and paste:

> I'm onboarding to TRT. I've completed Step 1 (Mission &
> Team Structure). Please guide me through Step 2: Slack
> Channels & Communication. Research the current Slack
> channels a TRT engineer should join or monitor, what each
> is used for, and any channel conventions. Present each
> channel or group of channels as a separate substep — one
> action at a time.
