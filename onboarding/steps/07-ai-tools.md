---
id: 07-ai-tools
title: AI Tools
order: 7
audience: new-hire

claims:
  channels:
    - help-rh-code-assist
  urls:
    - https://docs.anthropic.com/en/docs/claude-code/overview
    - https://github.com/openshift-eng/ai-helpers
    - https://sippy-auth.dptools.openshift.org/sippy-ng/chat
  repos:
    - openshift-eng/ai-helpers
  tools:
    - claude

substeps:
  - id: claude-code-setup
    type: action
    group: ai-setup
    summary: "Install Claude Code and complete the access flow"

  - id: ai-helpers-plugins
    type: action
    group: ai-setup
    summary: "Install the ai-helpers plugin marketplace"

  - id: chai-bot-intro
    type: action
    summary: "Get started with Chai Bot in Slack"

  - id: ai-landscape
    type: learning
    summary: "When to use which AI tool"

  - id: verify-ai-tools
    type: verification
    summary: "Verify your AI tools are working"
---

## Substep: claude-code-setup

### Context

Claude Code is a terminal-based AI coding assistant that
TRT engineers use daily for code development, payload
analysis, query optimization, and CI investigation. It
runs in your terminal alongside your editor and has full
access to your local files and shell.

**Access flow at Red Hat:**
1. Review Red Hat's AI policy and the Claude Code User
   Guide — links are available in #help-rh-code-assist
2. Submit the acknowledgment form (linked from the setup
   resources)
3. Install Claude Code (see below)

Red Hat provides Claude Code through Google Vertex AI.
The default model is Sonnet; access to the more powerful
Opus model requires additional approval due to higher
cost. For setup questions, ask in #help-rh-code-assist.

**CLAUDE.md — project-level AI context:**
When Claude Code opens a repository, it automatically
reads a `CLAUDE.md` file at the repo root. This file
provides the AI with project-specific guidance:
architecture invariants, essential build commands, coding
conventions, and safety rails to prevent common mistakes.

Many TRT repos (including Sippy) have a `CLAUDE.md`.
When you start working in a repo, read its `CLAUDE.md`
to understand what guidance the AI is receiving — it
also serves as a concise project overview for you.

For your own projects, create a `CLAUDE.md` with
architecture decisions and essential commands. Smaller,
more focused files work better than verbose ones. You
can also create a personal `CLAUDE.local.md` (gitignored)
for user-specific preferences.

### Action

- Install Claude Code:
  - **Via curl:**
    `curl -fsSL https://claude.ai/install.sh | bash`
    (installs to `~/.local/bin/claude`)
  - **Via npm:**
    `npm install -g @anthropic-ai/claude-code --ignore-scripts`
- Set the required environment variable for Red Hat's
  Vertex AI contract (add to your shell profile):
  `export CLOUD_ML_REGION=global`
- Complete the Red Hat access flow via
  #help-rh-code-assist if you haven't already
- Launch Claude Code in a TRT repo — navigate to your
  Sippy checkout (cloned in Step 6) and run:
  `claude`
- Read the repo's `CLAUDE.md` to see the AI context

> Note for Step 0: Claude Code access requires
> completing an acknowledgment form. Add to the Access
> Provisioning checklist: "Complete the Claude Code
> access flow (AI policy review + acknowledgment form)
> — see #help-rh-code-assist."

---

## Substep: ai-helpers-plugins

### Context

The
[ai-helpers](https://github.com/openshift-eng/ai-helpers)
repository is a marketplace of Claude Code plugins built
by OpenShift engineering teams. Plugins extend Claude Code
with domain-specific commands (called "skills") that
encode team workflows as repeatable AI-assisted actions.

The most important plugin for TRT is `ci`, which provides
commands for payload analysis, job failure investigation,
and automated reverts. You'll use these commands
extensively during the watcher role (Step 8) and revert
workflow (Step 9).

**Available plugins include:**
- `ci` — CI/CD analysis and payload investigation
  (key commands: `/ci:analyze-payload`,
  `/ci:payload-revert`, `/ci:revert-pr`,
  `/ci:analyze-prow-job-test-failure`)
- `jira` — Jira issue lookups and management
- `golang` — Go language development support
- `code-review` — Code review assistance
- `must-gather` — Cluster diagnostics analysis

### Action

- Open Claude Code in any directory:
  `claude`
- Add the ai-helpers marketplace (one-time setup):
  `/plugin marketplace add openshift-eng/ai-helpers`
- Install the CI plugin:
  `/plugin install ci@ai-helpers`
- Install other useful plugins:
  `/plugin install jira@ai-helpers`
  `/plugin install golang@ai-helpers`
- Verify installation by typing `/ci:` — you should
  see a list of available CI commands
- To update plugins later:
  `/plugin marketplace update ai-helpers`

Some repositories pre-configure plugins in their
`.claude/settings.json` — these are installed
automatically when you open Claude Code in that repo.

---

## Substep: chai-bot-intro

### Context

Chai Bot is an AI assistant integrated into Slack that
TRT and other OpenShift teams use for real-time
investigation and task automation. Unlike Claude Code
(which runs locally in your terminal), Chai Bot operates
in Slack with access to centralized data sources:
Jira, GitHub, Sippy, BigQuery, release controllers, and
Slack channel history.

**How to interact:**
- **In channels:** Mention `@Chai Bot` followed by your
  question in any channel where the bot is enabled
- **In DMs:** Open a direct message with Chai Bot for
  private queries. In the Home tab, you can select a
  persona — a specialized configuration tuned for a
  specific domain (e.g., TRT, ART, Test Platform)
- **Consent:** The first time you interact, you may need
  to provide consent by sending `@Chai Bot /consent`

**What Chai Bot can do for TRT work:**
- Analyze rejected payloads and summarize failure root
  causes
- Query Sippy and BigQuery for CI data (pass rates, test
  history, flake analysis)
- Look up and create Jira issues
- Check release payload status and verification job
  results
- Search Slack channel history for prior discussions on
  a topic
- Trigger and monitor CI jobs

**This onboarding guide is powered by Chai Bot.** The
step files in this repository are designed to be
delivered by Chai Bot in Slack threads — each step in
its own thread, with substeps presented one group at a
time. The system design is documented in `DESIGN.md` at
the repo root.

### Action

- Open a DM with Chai Bot in Slack (search for
  "Chai Bot" in the Slack sidebar)
- Send `@Chai Bot /consent` if prompted
- Try a sample query:
  "What is the current status of the latest
  {{DEV_NIGHTLY_STREAM}} payload?"
- Explore the Home tab to see available personas

---

## Substep: ai-landscape

### Context

TRT has multiple AI tools, each suited to different
tasks. Here is when to use each:

**Claude Code + ai-helpers** — Your primary tool for
hands-on engineering work:
- Investigating individual job failures
  (`/ci:analyze-prow-job-test-failure`)
- Analyzing and acting on rejected payloads
  (`/ci:analyze-payload`, `/ci:payload-revert`)
- Creating revert PRs (`/ci:revert-pr`)
- Writing code, debugging, and development
- Querying Jira and creating issues
  (`/jira:fetch-issue`)

**Chai Bot** — Your primary tool for quick questions and
Slack-based investigation:
- "What's the status of this payload?"
- "Find open TRT bugs for component X"
- "What's the pass rate for e2e-aws-ovn this week?"
- Creating Jira issues from a Slack conversation
- Monitoring issues or tests over time

**Sippy Chat** — A specialized AI chat interface for
trend analysis, available at
[sippy-auth/chat](https://sippy-auth.dptools.openshift.org/sippy-ng/chat):
- "What tests are flaking on GCP this week?"
- "Show me the pass rate trend for this test over the
  last 30 days"
- Best for longer, data-heavy queries about CI trends
- Requires Red Hat SSO authentication

**Payload Analysis Agent** — Fully autonomous, not
something you invoke directly:
- Runs automatically when a nightly payload is rejected
- Inspects failing jobs, identifies the likely root
  cause PR, and generates an HTML analysis report
- Can auto-file Jira bugs and open revert PRs for
  high-confidence candidates
- Reports are linked from the Release Controller
  payload detail page
- Covered in detail in Step 8 (watcher role)

**When you're on watcher duty (Step 8):** Start with the
Payload Analysis Agent's HTML report (it's usually
already generated by the time you look). Use Claude Code
with the `ci` plugin for deeper investigation. Use Chai
Bot for quick follow-up questions. Use Sippy Chat for
trend analysis.

**When you're doing development work:** Use Claude Code
with `CLAUDE.md` context for coding. Use Chai Bot for
looking up related Jira issues or prior Slack
discussions.

---

## Substep: verify-ai-tools

### Verification

Verify your AI tools are set up and working:

- Claude Code launches successfully:
  `claude --version` shows a version number
- The `CLOUD_ML_REGION=global` environment variable is
  set (check with `echo $CLOUD_ML_REGION`)
- The ai-helpers marketplace is installed: typing
  `/plugin list` in Claude Code shows installed plugins
- The `ci` plugin is installed: typing `/ci:` in Claude
  Code shows available CI commands
- You can open a DM with Chai Bot and get a response
- You can access Sippy Chat at
  [sippy-auth/chat](https://sippy-auth.dptools.openshift.org/sippy-ng/chat)
  with your Red Hat SSO credentials

---

## Known References

- Claude Code documentation:
  https://docs.anthropic.com/en/docs/claude-code/overview
  (verified: 2026-06-08)
- ai-helpers repository:
  https://github.com/openshift-eng/ai-helpers
  (verified: 2026-06-08)
- Sippy Chat:
  https://sippy-auth.dptools.openshift.org/sippy-ng/chat
  (verified: 2026-06-08)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"
