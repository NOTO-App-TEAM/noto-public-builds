# Public builds for the NOTO desktop app

This repository has **no product code**. It exists for one reason: standard
GitHub-hosted runners are free in public repositories, so the builds that the
private server cannot do (Linux, macOS, iOS) and the ones that need an
interactive Windows Installer context (MSI) run here without consuming the
private repository's minutes.

Every job checks the private repository out at the commit named in the dispatch
payload, using the read-only `PRIVATE_REPO_TOKEN` secret. Nothing is copied
into this repository, and no artifact is published here — the private
workflow downloads the artifacts and publishes the release.

## Triggers

`repository_dispatch` (`noto-build`) or a manual `workflow_dispatch`.

There is deliberately **no `pull_request` trigger**: in a public repository that
would let anyone's fork run its own code with the private checkout token.

## Payload

| Field | Meaning |
| --- | --- |
| `sha` | Commit in `NOTO-App-TEAM/NOTO` to build |
| `ref` | Ref name used for labels |
| `tag` | Release tag the private workflow publishes |

## Secrets

| Secret | Purpose |
| --- | --- |
| `PRIVATE_REPO_TOKEN` | Fine-grained token, `NOTO-App-TEAM/NOTO` only, **Contents: read** |
| `APPLE_*` | Optional; without them the macOS bundle is ad-hoc signed |

## Jobs

`linux`, `macos`, `windows-installer`, `android`, `ios`.

`ios` is unsigned and needs Apple provisioning to go further, so it uploads
with `if-no-files-found: warn` and does not gate the release.
