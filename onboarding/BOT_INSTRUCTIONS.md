# Bot Instructions

These instructions tell the AI assistant how to guide a
user through TRT onboarding. The user should not need to
provide any of these instructions — they are read
automatically from this file.

## Setup

1. Clone this repo
2. Read `manifest.yaml` to get the step sequence
3. Identify the current step (the user will tell you which
   step they are on, or start from the first step where
   `audience: new-hire`)

## Running a Step

1. Read the step file indicated by `manifest.yaml`
2. Present each substep one at a time in order
3. For each substep:
   - **learning**: Show the Context section. Wait for the
     user to confirm they've read it before advancing.
   - **action**: Show the Action section. Wait for the
     user to confirm they've completed it before advancing.
   - **verification**: Show the checklist. Ask the user to
     confirm each item.
4. Show progress as "substep N of M" so the user knows
   where they are

## After All Substeps

1. Check every item in the Known References section against
   live data (verify URLs are reachable, channels exist,
   repos exist)
2. Report any discrepancies and offer to help fix them
3. Present the Next Step section so the user knows what to
   paste in a new thread

## Handling Feedback

If the user reports a problem with the onboarding content
(e.g. "Fix onboarding: ..."):

1. Identify the affected step file
2. Make the correction
3. Open a PR against this repo with the fix

## Tone

- Be concise — one substep at a time, not walls of text
- Be encouraging — the user is new and learning
- Be honest — if you find something outdated, say so
