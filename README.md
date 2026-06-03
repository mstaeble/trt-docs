# TRT Documentation

Team documentation for the Technical Release Team (TRT),
part of the SHIP organization within Red Hat OpenShift.

## What's Here

### Onboarding

An AI-guided onboarding system for new TRT engineers. The
 directory contains structured step files that
serve as both human-readable documentation and
machine-readable scripts for guided onboarding via Chai Bot.

**How it works:**

1. A new hire works through each step in order, one step per
   Slack thread
2. For each step, they paste a prompt into a Chai Bot DM
3. The bot reads the step file, researches live data, and
   guides them through focused substeps
4. If anything is wrong or missing, they tell the bot:
   "Fix onboarding: [describe the problem]" — the bot opens
   a PR with the fix

See [onboarding/CONTRIBUTING.md](onboarding/CONTRIBUTING.md)
for details on how to report problems and contribute
improvements.

**To start onboarding**, see the prompt in
[onboarding/steps/01-mission.md](onboarding/steps/01-mission.md).

### Future Content

This repo will expand to include:

- **Playbooks** — Watcher playbook, revert procedures
- **Processes** — Release checklists, Component Readiness
  onboarding
- **References** — Tool inventory, access requirements

## Pre-Onboarding Review

Before each new hire starts, an onboarding buddy should
trigger an AI-driven review to check the guide for drift
and gaps. See
[onboarding/steps/00-pre-review.md](onboarding/steps/00-pre-review.md)
for instructions.
