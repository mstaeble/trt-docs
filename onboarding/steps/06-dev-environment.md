---
id: 06-dev-environment
title: Development Environment
order: 6
audience: new-hire

claims:
  urls:
    - https://go.dev/dl/
    - https://github.com/openshift/sippy/blob/main/DEVELOPMENT.md
    - https://github.com/openshift/sippy/blob/main/.devcontainer/README.md
    - https://docs.ci.openshift.org/docs/architecture/ci-operator/
    - https://docs.ci.openshift.org/docs/architecture/step-registry/
    - https://docs.ci.openshift.org/docs/how-tos/onboarding-a-new-component/
    - https://docs.ci.openshift.org/docs/how-tos/adding-changing-step-registry-content/
    - https://steps.ci.openshift.org/
  repos:
    - openshift/sippy
    - openshift/release
    - openshift-eng/ci-test-mapping
    - openshift-eng/ci-tools
  tools:
    - go
    - node
    - npm
    - podman
    - make

substeps:
  - id: go-dev-setup
    type: action
    group: dev-foundation
    summary: "Install Go and understand Go module conventions"

  - id: repo-dev-patterns
    type: learning
    group: dev-foundation
    summary: "How TRT repos are structured and common patterns"

  - id: sippy-local-dev
    type: action
    summary: "Set up Sippy for local development"

  - id: pr-workflow
    type: learning
    group: code-contribution
    summary: "OWNERS files, Prow commands, and the Tide merge process"

  - id: ci-config-basics
    type: learning
    group: code-contribution
    summary: "Understanding CI configuration in openshift/release"

  - id: verify-dev-env
    type: verification
    summary: "Verify your development environment is working"
---

## Substep: go-dev-setup

### Context

TRT's primary repositories — Sippy (explored in Step 4),
ci-test-mapping, and ci-tools — are Go projects. Go
is the dominant language across the OpenShift ecosystem,
and all TRT code contributions will be in Go (backend)
or TypeScript/React (Sippy frontend).

Each repository declares its required Go version in its
`go.mod` file at the repo root. When you clone a repo,
check `go.mod` to confirm you have a compatible version
installed. Go's backward compatibility means a newer Go
installation will generally work, but matching or
exceeding the declared version avoids surprises.

All TRT repos use Go modules with a vendored dependency
directory (`vendor/`). This means:
- Dependencies are committed to the repo — no network
  fetch needed during builds
- Build commands use `-mod=vendor` (or the `Makefile`
  handles this)
- When updating dependencies, run `go mod tidy` and
  `go mod vendor` to keep the vendor directory in sync

### Action

- Install Go from [go.dev/dl](https://go.dev/dl/) or
  via your package manager:
  - **macOS (Homebrew):** `brew install go`
  - **Fedora/RHEL:** `sudo dnf install golang`
- Verify your installation: `go version`
- Clone Sippy and check its required Go version:
  ```
  git clone https://github.com/openshift/sippy.git
  cd sippy
  head -2 go.mod
  ```
  Note the `go X.Y` line — your installed Go version
  must meet or exceed it.
- Verify you can build the backend: `make sippy`

---

## Substep: repo-dev-patterns

### Context

TRT maintains several repositories (explored in Step 4).
Here is how to approach development in each:

**[openshift/sippy](https://github.com/openshift/sippy)**
— The team's main product. A Go backend
with a React (TypeScript) frontend (`sippy-ng/`).
Building the full binary (backend + embedded frontend)
requires both Go and Node.js:
- `make` — builds the all-in-one binary
- `make sippy` — builds just the Go backend
- Frontend dev: `cd sippy-ng && npm start` runs the UI
  on `localhost:3000` with hot-reloading

**[openshift-eng/ci-test-mapping](https://github.com/openshift-eng/ci-test-mapping)**
— Maps test names to component owners. Standard Go
project — `make build` and `make test` are the main
targets.

**[openshift-eng/ci-tools](https://github.com/openshift-eng/ci-tools)**
— Shared CI tooling (co-owned with DPTP). Contains
`ci-operator` and supporting tools. This repo has a
large codebase; focus on the areas relevant to your
current task rather than trying to understand it all
at once.

**[openshift/release](https://github.com/openshift/release)**
(explored in Step 4) — CI configuration. Not a Go
project, but a repository of YAML configuration you'll
edit to add or modify CI jobs. Covered in detail in the
CI configuration substep below.

Each repo has a `Makefile` — always start with
`make help` (if available) or read the Makefile to
discover available targets.

---

## Substep: sippy-local-dev

### Context

Sippy is the most complex local development setup
because it requires a database (PostgreSQL), a cache
(Redis), and access to Google BigQuery for data loading
and Component Readiness queries.

There are two approaches:

**Devcontainer (recommended)** — A pre-configured
container that includes Go, Node.js, PostgreSQL, Redis,
and all build tools. Works with VS Code, Cursor, and
Claude Code. No manual dependency installation required.
See the
[devcontainer README](https://github.com/openshift/sippy/blob/main/.devcontainer/README.md)
for setup instructions.

**Manual setup** — Install and manage each dependency
yourself. The canonical instructions are in
[DEVELOPMENT.md](https://github.com/openshift/sippy/blob/main/DEVELOPMENT.md).
The key components:
1. PostgreSQL — run via Podman:
   `podman run --name sippy-postgres -e POSTGRES_PASSWORD=password -p 5432:5432 -d quay.io/enterprisedb/postgresql`
2. Redis — run via Podman:
   `podman run --name sippy-redis -p 6379:6379 -d redis`
3. Database migration: `./sippy migrate`
4. Data loading: `./sippy load` with appropriate flags
5. Server: `./sippy serve` with database and BigQuery
   connection flags

**BigQuery credentials** — Both `sippy load` and
`sippy serve` require a Google Service Account
credential file (`--google-service-account-credential-file`)
for accessing BigQuery. This is used for data queries
and Component Readiness analysis. Ask your onboarding
buddy or the team for the credential file — access to
the GCP project may require provisioning.

**Database shortcut** — Instead of loading data from
BigQuery (which can take a long time), you can restore
from a production database backup using `pg_restore`.
Instructions are in the "from-a-prod-sippy-backup"
section of DEVELOPMENT.md.

For frontend development, run the API and UI separately
for faster feedback — the Go backend on `:8080` and
`npm start` for the React UI on `:3000`.

### Action

- Choose your setup approach (devcontainer or manual)
  and follow the instructions linked above
- Obtain the BigQuery credential file from your
  onboarding buddy or the team
- Build Sippy: `make` (full binary) or `make sippy`
  (backend only)
- Start the database and Redis containers
- Run `./sippy migrate` to set up the schema
- Load data using either `sippy load` or `pg_restore`
  from a production backup
- Start the server: `./sippy serve` with the flags
  documented in DEVELOPMENT.md
- Verify the UI is accessible at `http://localhost:8080`
  (embedded mode) or `http://localhost:3000`
  (separate frontend)

> Note for Step 0: BigQuery credential file access may
> require provisioning. Add to the Access Provisioning
> checklist: "Obtain Sippy BigQuery credential file
> (Google Service Account) — ask the TRT team lead."

---

## Substep: pr-workflow

### Context

OpenShift repositories use Prow (the CI/CD system from
Step 3) to manage the PR review and merge process.
This is different from a standard GitHub workflow — you
interact with the system through slash commands in PR
comments rather than GitHub's built-in review buttons.

**OWNERS files** control who can approve changes. Each
directory can have an `OWNERS` file with two key lists:
- `approvers` — can use `/approve` to grant formal
  approval for merging
- `reviewers` — suggested for code review; Prow's
  Blunderbuss plugin auto-assigns from this list

OWNERS files are hierarchical — if a directory doesn't
have one, Prow looks in parent directories. A PR that
touches multiple directories needs an approver from the
OWNERS file covering each changed path.

Example OWNERS file:
```
approvers:
- alice
- bob
reviewers:
- alice
- bob
- charlie
```

**Key Prow commands** (type these as PR comments):

- `/lgtm` — Code review approval (any org member)
- `/approve` — Formal approval (OWNERS approvers only)
- `/retest` — Re-run failed CI jobs
- `/hold` — Block merge (`/hold cancel` to release)
- `/cherry-pick release-4.XX` — Backport to a release
  branch
- `/cc @username` — Request a review
- `/assign @username` — Assign the PR
- `/label ci-approved` — Add a label

**Tide** is the automatic merge controller. It merges
PRs when all criteria are met:
1. Has both `lgtm` and `approved` labels
2. All required CI checks pass
3. No blocking labels (`do-not-merge/hold`,
   `needs-rebase`, etc.)
4. PR targets a branch configured in Tide's queries

**Important nuances:**
- `/lgtm` and `/approve` are separate. A reviewer
  can `/lgtm` but only an OWNERS approver can
  `/approve`. Both labels are typically required.
- Use Prow commands, not GitHub's "Approve" button.
  Some repos configure GitHub approval reviews to
  act as `/lgtm`, but this is not universal.
- You cannot `/approve` your own PR unless the repo
  explicitly allows self-approval.
- `/cherry-pick release-4.XX` on a merged PR creates
  a backport PR. The bot clones the linked Jira bug.
  If it fails due to conflicts, create the
  cherry-pick manually and use
  `/jira cherrypick ORIGINAL_BUG` to clone the bug.

---

## Substep: ci-config-basics

### Context

The [openshift/release](https://github.com/openshift/release)
repository (explored in Step 4) is where all CI jobs are
configured. Understanding its structure lets you add
tests, modify job parameters, and debug CI failures at
the configuration level.

**Three key directories:**

1. `ci-operator/config/<org>/<repo>/` — Build and test
   definitions. Each YAML file defines what to build,
   which tests to run, and what resources they need.
   This is the file you edit most often.

2. `ci-operator/jobs/<org>/<repo>/` — Prow job
   triggers (presubmits, postsubmits, periodics).
   **These are auto-generated** — do not edit directly.

3. `ci-operator/step-registry/` — Reusable test
   components organized as:
   - **Steps** — atomic tasks (a YAML config + a shell
     script)
   - **Chains** — ordered sequences of steps
   - **Workflows** — complete test scenarios with `pre`
     (setup), `test` (execution), and `post` (cleanup)
     phases

   Browse the registry at
   [steps.ci.openshift.org](https://steps.ci.openshift.org/).

**How to modify a CI job:**

1. Edit the `ci-operator/config/` file for your repo
2. Run `make update` (or `make jobs`) from the repo
   root to regenerate Prow job definitions
3. Run `make checkconfig` to validate
4. Commit both the config and generated files
5. Open a PR — the `pj-rehearse` process will test
   your config changes before they merge

**Adding a new repo to CI:** Run `make new-repo` for an
interactive setup wizard, or follow the
[Onboarding guide](https://docs.ci.openshift.org/docs/how-tos/onboarding-a-new-component/).

**Key documentation** (expanding on Step 10's reading
list):
- [ci-operator architecture](https://docs.ci.openshift.org/docs/architecture/ci-operator/)
- [Step registry architecture](https://docs.ci.openshift.org/docs/architecture/step-registry/)
- [Adding/changing step registry content](https://docs.ci.openshift.org/docs/how-tos/adding-changing-step-registry-content/)

As a TRT engineer, you'll primarily interact with CI
configuration when investigating why specific jobs
exist, modifying test parameters, or adding new
verification jobs. Deep step-registry authoring is less
common for TRT but useful to understand when debugging
CI failures.

---

## Substep: verify-dev-env

### Verification

Verify your development environment is ready for TRT
coding work:

- `go version` outputs a version that meets or exceeds
  the version declared in Sippy's `go.mod`
- You can build the Sippy backend: `make sippy`
  completes without errors (from the `openshift/sippy`
  checkout)
- You understand the difference between `/lgtm` and
  `/approve` and when each is needed
- You can identify the `OWNERS` file that governs
  approval for a file you want to change
- You know where CI configuration lives
  (`ci-operator/config/` in `openshift/release`) and
  that `ci-operator/jobs/` is auto-generated

Optional (if you completed the full Sippy local setup):
- PostgreSQL and Redis containers are running
- `./sippy migrate` completed successfully
- Sippy serves the UI at `localhost:8080` or
  `localhost:3000`

---

## Known References

- Go downloads:
  https://go.dev/dl/
  (verified: 2026-06-08)
- Sippy DEVELOPMENT.md:
  https://github.com/openshift/sippy/blob/main/DEVELOPMENT.md
  (verified: 2026-06-08)
- Sippy devcontainer README:
  https://github.com/openshift/sippy/blob/main/.devcontainer/README.md
  (verified: 2026-06-08)
- ci-operator architecture:
  https://docs.ci.openshift.org/docs/architecture/ci-operator/
  (verified: 2026-06-08)
- Step registry architecture:
  https://docs.ci.openshift.org/docs/architecture/step-registry/
  (verified: 2026-06-08)
- Onboarding a new component:
  https://docs.ci.openshift.org/docs/how-tos/onboarding-a-new-component/
  (verified: 2026-06-08)
- Adding/changing step registry content:
  https://docs.ci.openshift.org/docs/how-tos/adding-changing-step-registry-content/
  (verified: 2026-06-08)
- Step registry browser:
  https://steps.ci.openshift.org/
  (verified: 2026-06-08)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"
