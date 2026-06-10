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

## Future Direction: Continuous Documentation Maintenance

### The broader problem

Onboarding is a rare event. The harder, more persistent
problem is documentation drift — docs go stale because
humans don't maintain them. TRT's repos, processes,
component ownership, and workflows change constantly.
Documentation that was accurate three months ago may be
misleading today, and nobody notices until someone relies
on it.

### Agent-maintained documentation

The onboarding guide's design — structured markdown,
machine-readable claims, AI pre-review against live
sources — is a prototype of a more general pattern:
agents that continuously monitor team activity and propose
documentation updates.

A documentation maintenance agent would:

- Review recent PRs, Jira activity, Slack discussions,
  and org config changes on a regular schedule
- Compare what happened against what's documented
- Propose targeted updates via PR when it detects drift,
  gaps, or stale content
- Use the same claims system to verify that references
  remain valid

This shifts documentation from a human discipline
("remember to update the docs") to an agent-assisted
workflow ("review and merge the agent's proposed update").

### How the current design supports this

Several decisions made for the onboarding guide were
chosen specifically because they generalize:

- **Directory-per-topic structure.** The onboarding guide
  lives under `onboarding/`. Other document types —
  runbooks, process docs, architecture references — can
  sit alongside it in sibling directories, each with their
  own manifest and content files.

- **The manifest as the agent's API.** The manifest
  declares what the document covers, how it's structured,
  what claims it makes, and what quality criteria apply.
  A documentation agent can discover manifests across the
  repo and use them to understand what each document is
  and how to review it.

- **Claims are document-agnostic.** The claims system
  (verifiable references to URLs, repos, channels, tools)
  works for any document type, not just onboarding steps.

- **Bot instructions can split.** Currently
  `BOT_INSTRUCTIONS.md` is onboarding-specific. When a
  second document type is added, repo-wide conventions
  can move to a root-level file, with per-directory
  instructions covering document-specific behavior.

- **The pre-review is a review spec.** Step 00's prompt
  defines exactly how to audit the onboarding guide
  against live sources. The same pattern — "cross-reference
  this document against these data sources, flag drift and
  gaps" — applies to any document. Each manifest could
  include its own review instructions.

### What changes when we get there

The current pre-review runs on-demand when a buddy triggers
it. The next step is making it scheduled — a Chai Bot task
that runs periodically, reviews all manifests in the repo,
and opens PRs for detected drift. This does not require
structural changes to the repo; it requires a scheduled
task configuration pointing at this repo.

The structural decisions above mean a second document type
can be added by creating a new directory with its own
manifest and content files, following the same patterns.
No refactoring of the existing onboarding guide is needed.
