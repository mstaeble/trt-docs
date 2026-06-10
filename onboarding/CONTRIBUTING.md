# Contributing to TRT Onboarding

This guide is maintained by the people who use it. If you
find something wrong, missing, or confusing during
onboarding, you're in the best position to fix it.

## Reporting a Problem

During any onboarding step, tell Chai Bot:

> "Fix onboarding: [describe the problem]"

The bot will identify the affected step file, make the edit,
and open a PR for team review. Examples:

- "Fix onboarding: The Sippy URL in step 3 is wrong. It
  should be sippy-v2.dptools.openshift.org"
- "Fix onboarding: Step 2 is missing the
  #trt-disruption-alerts channel"
- "Fix onboarding: TRT now does disruption monitoring and
  it's not covered in any step"

For factual corrections and small additions, the bot opens a
normal PR. For larger gaps that require new content, the bot
opens a draft PR marked for careful review.

## Manual Contributions

You can also open PRs directly:

1. Fork or branch the repo
2. Edit the relevant step file in 
3. Update the  section in the front matter if you
   added or removed any channels, URLs, repos, or tools
4. Update the  date on any Known References you
   confirmed
5. Open a PR — the OWNERS file will assign reviewers

## Step File Structure

Each step file follows a consistent format. Preserve this
structure when editing:

- **Front matter** (YAML between  markers) — metadata
  and machine-readable claims used by the AI pre-review
- **Substeps** — each substep is a focused learning or
  action, presented one at a time by the bot
- **Known References** — verified facts with dates, checked
  during pre-review
- **Feedback section** — always keep this at the bottom so
  new hires know how to report problems

Note: Step files do not contain a "Next Step" section. The
bot generates the next step prompt automatically from
manifest.yaml (see BOT_INSTRUCTIONS.md).

## What Makes a Good Edit

- **Keep substeps small** — one action or one concept per
  substep. If you're writing a paragraph, it's too big.
- **Separate stable from volatile** — put facts that rarely
  change in the Context section. Let the bot query volatile
  details (current URLs, active versions) at runtime via the
  Prompt section.
- **Update claims** — if you add a channel, URL, repo, or
  tool to the content, add it to the  front matter
  too. The AI pre-review checks claims, not prose.
- **Date your verifications** — when you confirm a Known
  Reference is still accurate, update its  date.

## Pre-Onboarding Review

Before each new hire starts, run the AI pre-review described
in `steps/00-pre-review.md`. This audits all claims against
live state and searches for gaps. Fix any issues before the
new hire begins.
