# Onboarding System Design

This document explains the design decisions behind TRT's
AI-guided onboarding system.

## Problem

TRT's onboarding knowledge is fragmented across Google Docs,
Slack conversations, and tribal knowledge. Previous
approaches each have failure modes:

- Static docs go stale with no mechanism to detect drift
- Ad-hoc AI conversations depend entirely on the new hire's
  prompts — they can't ask about things they don't know exist
- Complex AI prompts can exceed processing limits and return
  no output at all
- Ad-hoc AI conversations give the team no visibility into
  what the new hire learned or missed

## Core Design Decisions

### The repo is the source of truth

All onboarding content lives in version-controlled markdown
files with structured YAML front matter. This provides:

- PR-based review for every change
- Audit trail of what changed and why
- Prow OWNERS enforcement for approvals
- Supporting artifacts (scripts, configs) alongside docs

### One step per Slack thread

Each onboarding step is completed in a separate chat thread.
This keeps the AI context window focused on a single topic
rather than accumulating baggage from previous steps. AI
context is zero-sum — every token from a previous step
displaces a token relevant to the current one.

### Substeps instead of walls of text

Each step is broken into small, focused substeps — each one
either a single learning to digest or a single action to
perform. The bot presents one substep at a time with a
button to advance. This:

- Controls pacing so the new hire is never overwhelmed
- Makes progress visible ("substep 3 of 5")
- Keeps each interaction small enough for the AI to handle
  reliably

### Stable context plus live queries

Each step file contains stable background information that
rarely changes (mission statements, role descriptions, team
structure) alongside prompts that the bot runs against live
data sources (current URLs, active channels, latest tool
versions). This separation means the file doesn't go stale
when volatile details change — the bot fills those in at
runtime.

### The repo is the persistence layer

The bot has no memory across threads. Instead of trying to
build persistence into the AI, the repo itself tracks state.
The new hire knows which step they're on by which step
file they're working through. If they lose a thread, they
start a new one at the current step — no context to recover.

### Machine-readable claims

Each step file declares its verifiable facts (channels,
URLs, repos, tools) in a structured claims section in the
YAML front matter. This enables the AI pre-review to
programmatically audit the guide rather than trying to parse
prose.

## Maintenance Model

### Self-maintaining through use

Every time someone goes through the onboarding, they
validate the guide against reality. When something has
drifted, they tell the bot, and the bot opens a PR to fix
it. The guide stays fresh because it gets used, not through
dedicated maintenance effort.

### AI pre-review before each onboarding

Before a new hire starts, the bot audits every step file's
claims against live state and searches for topics active in
TRT's recent work that aren't covered in any step. This
catches both drift (things that changed) and gaps (things
that are new).

### Feedback-to-PR loop
[11:33 AM]Anyone — new hire or existing team member — can report a
problem by telling the bot "Fix onboarding: [description]."
The bot edits the file and opens a PR. No issues to triage,
no backlog to groom. Feedback goes directly to a reviewable
fix.

## Alternatives Considered

### Google Doc with prompts

A Google Doc where each section contains a prompt for the
new hire to paste into Chai Bot.

Not chosen because: no machine-readable structure for
automated pre-reviews, no ability for the bot to directly
edit the guide and open reviewable changes, and no commit
messages explaining why changes were made.

### Standalone AI assistant with no structured guide

New hire asks the bot for onboarding guidance directly.

Not chosen because: quality depends entirely on the new
hire's prompt, complex prompts can time out with no output,
and the team has no visibility into what was covered.

### AI knowledge base (learn: lessons)

Capture onboarding knowledge as verified lessons.

Not chosen because: AI context is zero-sum and the lesson
system is additive-only. Onboarding-specific lessons would
increase noise for all queries, not just onboarding. Adding
more knowledge to a finite context window eventually
degrades answer quality across the board.

### Periodic automated verification

A CI job that runs weekly to check all claims.

Not chosen because: onboarding is a rare event. The same
verification happens naturally as a pre-step of each
onboarding, triggered by a real event rather than a timer.
