---
id: 05-tools-setup
title: Tools & CLI Setup
order: 5
audience: new-hire

claims:
  channels:
    - forum-ocp-crt
    - forum-ocp-testplatform
  urls:
    - https://console.redhat.com/openshift/downloads
    - https://console.redhat.com/openshift/install/pull-secret
    - https://github.com/mikefarah/yq
    - https://console-openshift-console.apps.ci.l2s4.p1.openshiftapps.com/
    - https://docs.ci.openshift.org/docs/release-oversight/pull-request-testing/
  repos:
    - openshift/release
  tools:
    - oc
    - jq
    - yq
    - cluster-bot

substeps:
  - id: install-cli-tools
    type: action
    summary: "Install the OpenShift CLI and supporting tools"

  - id: pull-secret
    type: action
    summary: "Download your Red Hat pull secret"

  - id: payload-inspection
    type: learning
    summary: "Learn the oc adm release commands for payload inspection"

  - id: app-ci-access
    type: action
    summary: "Log in to the app.ci cluster"

  - id: build-farm-access
    type: learning
    summary: "Understand build farm cluster access"

  - id: payload-commands
    type: learning
    group: testing-tools
    summary: "Test PRs against release payloads with /payload commands"

  - id: cluster-bot
    type: learning
    group: testing-tools
    summary: "Launch test clusters with cluster-bot"

  - id: verify-setup
    type: verification
    summary: "Verify your tools and access are working"
---

## Substep: install-cli-tools

### Context

Three CLI tools form the foundation of a TRT engineer's
daily work:

**`oc` (OpenShift CLI)** — The primary command-line tool
for interacting with OpenShift clusters. You'll use it to
log in to CI clusters, inspect release payloads, and
investigate test failures. `oc` includes all `kubectl`
commands plus OpenShift-specific subcommands like
`oc adm release info` (covered in the next substep).

**`jq`** — A command-line JSON processor. OpenShift
commands frequently output JSON, and `jq` lets you
extract specific fields. For example, extracting image
names from a release payload:
```
OC_VERSION=$(oc version --client -o json | jq -r '.releaseClientVersion')
oc adm release info \
  quay.io/openshift-release-dev/ocp-release:${OC_VERSION}-x86_64 \
  --output=json | jq '.references.spec.tags[].name'
```

**`yq`** — Like `jq`, but for YAML. Many CI
configurations are YAML files, and `yq` lets you parse
them on the command line. TRT engineers commonly use `yq`
to extract kubeconfigs from Kubernetes secrets:
```
oc get secret my-secret -o yaml | yq -r '.data.kubeconfig' | base64 -d
```

Note: there are multiple tools named `yq`. The one
commonly used in TRT is the Go-based
[yq by Mike Farah](https://github.com/mikefarah/yq).

### Action

- Install `oc`:
  - **macOS (Homebrew):** `brew install openshift-cli`
  - **Linux/macOS (direct download):** Download from
    [console.redhat.com/openshift/downloads](https://console.redhat.com/openshift/downloads)
    under "OpenShift command-line interface" — choose the
    binary for your OS and architecture, extract the
    archive, and move `oc` to a directory in your `$PATH`
    (e.g. `/usr/local/bin/`)
  - Verify: run `oc version --client`

- Install `jq`:
  - **macOS:** `brew install jq`
  - **Fedora/RHEL:** `sudo dnf install jq`

- Install `yq`:
  - **macOS:** `brew install yq`
  - **Fedora/RHEL:** `sudo dnf install yq`
  - Verify it's the Go-based version: `yq --version`
    should show `mikefarah/yq`

---

## Substep: pull-secret

### Context

A pull secret contains authentication tokens for Red
Hat's container registries (Quay, registry.redhat.io,
and others). You need it when pulling image content —
primarily with `oc adm release extract` (covered in the
next substep). Reading payload metadata with
`oc adm release info` does not require a pull secret for
GA release images on `quay.io`.

The pull secret is a JSON file you download once from the
Red Hat Hybrid Cloud Console, store locally, and
reference with the `-a` flag in `oc` commands:
```
oc adm release extract -a ~/pull-secret.json \
  --command=openshift-install \
  --from=quay.io/openshift-release-dev/ocp-release:4.18.0-x86_64
```

### Action

- Download your pull secret from
  [console.redhat.com/openshift/install/pull-secret](https://console.redhat.com/openshift/install/pull-secret)
  — log in with your Red Hat SSO credentials
- Save the file to a permanent location on your
  workstation (e.g. `~/pull-secret.json`)
- Verify the file is valid JSON:
  `jq . ~/pull-secret.json`

---

## Substep: payload-inspection

### Context

Two `oc adm` subcommands are central to TRT's payload
investigation work. Step 3 introduced the Release
Controller dashboard — these commands let you dig deeper
from the command line.

**`oc adm release info`** inspects a release payload's
contents and metadata. It requires a **full image
pullspec** — bare version tags like `4.18.0` don't
resolve unless you're logged in to a cluster that knows
about that release. The full pullspec is unambiguous and
always works.

GA release images live at:
`quay.io/openshift-release-dev/ocp-release:<version>-<arch>`

where `<version>` matches an OCP release (e.g. `4.18.0`)
and `<arch>` is `x86_64`, `aarch64`, etc. A convenient
version to start with is your installed `oc` client
version:

```
OC_VERSION=$(oc version --client -o json | jq -r '.releaseClientVersion')
```

View all components in a payload:
```
oc adm release info \
  quay.io/openshift-release-dev/ocp-release:${OC_VERSION}-x86_64
```

Find the image for a specific component:
```
oc adm release info --image-for=tests \
  quay.io/openshift-release-dev/ocp-release:${OC_VERSION}-x86_64
```

Show the Git commits included in each component (useful
for identifying which code is in a rejected payload):
```
oc adm release info --commit-urls \
  quay.io/openshift-release-dev/ocp-release:${OC_VERSION}-x86_64
```

Use JSON output for scripting:
```
oc adm release info \
  quay.io/openshift-release-dev/ocp-release:${OC_VERSION}-x86_64 \
  --output=json \
  | jq '.references.spec.tags[] | select(.name=="cluster-version-operator")'
```

If you're on Apple Silicon, substitute `-aarch64` for
`-x86_64` — both architectures contain the same release
metadata.

**`oc adm release extract`** pulls files from a release
payload. The most common use is extracting the
`openshift-install` binary for a specific release:

```
oc adm release extract -a ~/pull-secret.json \
  --command=openshift-install \
  --from=quay.io/openshift-release-dev/ocp-release:${OC_VERSION}-x86_64
```

Extract all client tools at once:
```
oc adm release extract -a ~/pull-secret.json \
  --tools \
  quay.io/openshift-release-dev/ocp-release:${OC_VERSION}-x86_64
```

`oc adm release info` (reading metadata) does not require
a pull secret for GA images on `quay.io`.
`oc adm release extract` (pulling image content) does —
pass `-a ~/pull-secret.json` as shown above. For CI
nightly builds (hosted on `registry.ci.openshift.org`),
you may not need the `-a` flag if you're already logged
in to the CI registry.

You'll use these commands extensively during the watcher
role (Step 6) to investigate which commits are in
rejected payloads.

---

## Substep: app-ci-access

### Context

The `app.ci` cluster is the control plane for OpenShift's
CI system — it runs Prow (the CI job scheduler you
explored in Step 3), the release controller, and other
core CI services. TRT has its own RBAC (Role-Based Access
Control) configuration on this cluster, defined at
`clusters/app.ci/trt/` in the
[openshift/release](https://github.com/openshift/release)
repository (which you explored in Step 4).

As a TRT engineer, you need access to `app.ci` for:
- Inspecting release payload resources
- Viewing CI job configurations and state
- Debugging CI infrastructure issues during your watcher
  rotation (Step 6)

Access is available to all Red Hat employees via SSO
(Single Sign-On). No special Rover group is needed for
basic read access.

### Action

- Open the `app.ci` web console:
  [console-openshift-console.apps.ci.l2s4.p1.openshiftapps.com](https://console-openshift-console.apps.ci.l2s4.p1.openshiftapps.com/)
- Log in with your Red Hat SSO credentials
- Click your username in the top-right corner and select
  "Copy login command"
- Click "Display Token"
- Copy the `oc login` command shown and run it in your
  terminal — it will look like:
  `oc login --token=sha256~... --server=https://api.ci.l2s4.p1.openshiftapps.com:6443`
- Verify you're logged in: `oc whoami` should show your
  Kerberos ID
- Rename the context for convenience:
  ```
  oc config rename-context $(oc config current-context) app.ci
  ```
  This lets you switch to the `app.ci` context with
  `oc config use-context app.ci` instead of remembering
  the full context name

---

## Substep: build-farm-access

### Context

CI jobs don't run on `app.ci` — they execute on build
farm clusters (`build01` through `build13` and beyond).
These clusters run thousands of test jobs daily. When a
Prow job fails and you need to investigate what happened
at the cluster level, you may need access to the build
farm cluster where the job ran.

**For most TRT work, you don't need direct build farm
access.** Prow job logs, CI Search, and Sippy (all
covered in Step 3) provide the data you need for
investigation. Direct cluster access is an advanced
debugging technique.

**How access works:**
- Build farm access is controlled by Rover groups and
  managed by DPTP (Developer Productivity and Test
  Platform — the team whose channel
  #forum-ocp-testplatform you joined in Step 2)
- PR authors automatically get administrator access to
  the temporary namespace (`ci-op-*`) created for their
  job on the build farm cluster — so you can debug your
  own CI jobs without special permissions
- For broader read-only access, request it in
  #forum-ocp-testplatform — DPTP evaluates these requests
  carefully because build clusters contain secrets

**Extracting kubeconfigs for ephemeral test clusters:**
If you ever need to connect directly to a test cluster
launched by a CI job, the kubeconfig is stored in a
secret within the job's namespace on the build farm. You
can extract it with:
```
oc --context build01 get secret -n <ci-op-namespace> <job-name> \
  -o yaml | yq -r '.data.kubeconfig' | base64 -d
```
This requires access to the build farm cluster. The job's
Prow logs show which build cluster ran the job.

For your first weeks, focus on using Prow logs, CI
Search, and Sippy for investigation. You can request
build farm access if and when a specific situation
requires it.

---

## Substep: payload-commands

### Context

When a PR needs testing against a full release payload,
the `/payload` family of GitHub PR commands lets you
trigger verification jobs directly from a PR comment.

**Basic syntax:**
```
/payload <ocp_version> <stream> <job_category>
```

Example — run all blocking nightly verification jobs with
your PR's changes:
```
/payload 4.19 nightly blocking
```

This builds a payload from the latest accepted nightly,
incorporates your PR's changes, and runs the specified
category of verification jobs against it.

**Useful variants:**

Run a specific periodic job multiple times to rule out
infrastructure flakes:
```
/payload-aggregate <periodic_job_name> <run_count>
```

Test multiple PRs together in a single payload:
```
/payload-job-with-prs <periodic_job_name> org/repo#123 org/repo#456
```

Cancel running payload jobs:
```
/payload abort
```

Full documentation and examples:
[CI Docs: Pull Request Testing](https://docs.ci.openshift.org/docs/release-oversight/pull-request-testing/)

You'll use these commands when validating whether a
specific change causes (or fixes) a CI regression.
During the watcher role (Step 6), payload commands are
a key tool for verifying fixes and reverts.

---

## Substep: cluster-bot

### Context

The `@cluster-bot` is a Slack app that launches OpenShift
test clusters on demand. You interact with it via direct
message in Slack.

**Common uses:**

Launch a cluster with a specific nightly build:
```
launch 4.19.0-0.nightly-2026-06-01-021531 aws
```

Launch a cluster with Tech Preview features enabled:
```
launch 4.19 gcp,techpreview
```

The bot provisions the cluster and sends you a kubeconfig
file when it's ready. Clusters have a limited lifetime
(approximately 2 hours by default).

**When to use cluster-bot vs. /payload commands:**
- `/payload` commands are preferred for CI validation —
  they run official verification tests and produce
  results visible in Prow
- `cluster-bot` is useful when you need a live cluster
  to manually inspect behavior, reproduce an issue
  interactively, or test changes from multiple
  repositories together

To get started, add the `@cluster-bot` app in Slack and
send it `help` to see all available commands.

For questions about cluster-bot, ask in #forum-ocp-crt
(the Continuous Release Tooling team's channel, which you
joined in Step 2).

---

## Substep: verify-setup

### Verification

Verify your tools and access are ready:

- `oc version --client` outputs a version number
- `jq --version` outputs a version number
- `yq --version` outputs a version number and shows
  `mikefarah/yq`
- Your pull secret file exists and is valid JSON
  (`jq . ~/pull-secret.json` succeeds)
- `oc adm release info quay.io/openshift-release-dev/ocp-release:$(oc version --client -o json | jq -r '.releaseClientVersion')-x86_64`
  returns payload information (substitute `-aarch64` on
  Apple Silicon)
- `oc whoami` with the `app.ci` context shows your
  Kerberos ID (`oc config use-context app.ci` first if
  you have multiple contexts)

---

## Known References

- oc CLI downloads:
  https://console.redhat.com/openshift/downloads
  (verified: 2026-06-05)
- yq (Go-based):
  https://github.com/mikefarah/yq
  (verified: 2026-06-05)
- Pull secret download:
  https://console.redhat.com/openshift/install/pull-secret
  (verified: 2026-06-05)
- `app.ci` web console:
  https://console-openshift-console.apps.ci.l2s4.p1.openshiftapps.com/
  (verified: 2026-06-05)
- openshift/release:
  https://github.com/openshift/release
  (verified: 2026-06-05)
- CI Docs - Pull Request Testing:
  https://docs.ci.openshift.org/docs/release-oversight/pull-request-testing/
  (verified: 2026-06-05)

## Feedback

If anything in this step was wrong, missing, or confusing,
tell the bot:

> "Fix onboarding: [describe the problem]"
