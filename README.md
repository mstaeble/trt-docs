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

**To start onboarding:**

1. Open a Chai Bot DM in Slack (click "Chai Bot" in your
   apps, then go to the "Messages" tab)
2. Start a new thread and paste:

> Start TRT onboarding from the repo mstaeble/trt-docs

The bot will clone this repo, read the bot instructions and
step files, and guide you through the process one substep at
a time. When you complete a step, the bot will give you the
prompt to paste in a new thread for the next step.

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
