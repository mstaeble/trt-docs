---
id: 00-pre-review
title: Pre-Onboarding Review (AI)
order: 0
audience: buddy

claims:
  channels: []
  urls: []
  repos: []
  tools: []
---

## Purpose

Before the new hire starts, their onboarding buddy triggers
an AI-driven review of the entire onboarding guide. The bot
audits every step file for drift and searches for gaps —
topics that TRT is actively working with but that aren't
covered in any step.

Run this at least one week before the new hire's start date
so there is time to review and merge any fix PRs.

## How to Run

Start a Chai Bot DM thread and paste:

> I'm a TRT team member preparing to onboard a new engineer.
> Please run a pre-onboarding review of the TRT onboarding
> guide.
>
> For each step file in the onboarding/steps/ directory of
> the trt-docs repo:
>
> 1. Read the file and extract all claims from the front
>    matter (channels, URLs, repos, tools)
> 2. Verify each claim against live state — check that
>    channels exist, URLs are reachable, repos exist, and
>    tools are available
> 3. Check the Known References section for any verified
>    dates older than 90 days
> 4. Search recent TRT Slack threads, Jira tickets, and
>    GitHub activity for topics not covered in any step file
>
> Produce a report with three sections:
> - ✅ Verified — claims that match live state
> - ⚠️ Drift — claims that no longer match
> - 🆕 Gaps — topics active in TRT's recent work but absent
>   from the guide
>
> For any drift detected, open a PR to fix it.

## After the Review

1. Review the bot's report
2. Review and merge any fix PRs the bot opened
3. For any gaps identified, decide whether to:
   - Add a substep to an existing step
   - Create a new step (and update manifest.yaml)
   - Defer — not everything needs to be in the onboarding
     guide
4. Confirm to the new hire's manager that the guide is ready

## When to Run This

- Every time a new hire is joining — ideally one week before
  their start date
- After a major team change (new tooling adopted, new
  responsibilities taken on, significant process changes)
