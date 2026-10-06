# forgejo-cli macOS builds

This repository publishes **unofficial Apple Silicon (arm64) builds** of
[forgejo-cli](https://codeberg.org/forgejo-contrib/forgejo-cli). The application
source and development remain on Codeberg. This repository contains only the
build workflow; it is not a source mirror or an upstream project.

## How releases stay current

The [GitHub Actions workflow](.github/workflows/release.yml) checks Codeberg
when the build recipe changes and daily for the newest stable `vX.Y.Z` tag.
If its macOS build has not been published yet, an Apple Silicon runner checks
out that tag, builds with the
upstream release feature, verifies the binary, and publishes an archive and
SHA-256 checksum as a GitHub Release. Re-running the workflow is safe because
it skips tags that already have a release.

The workflow can also be run manually from the Actions tab. Enter an upstream
tag to build a specific older release, or leave it empty to check the latest.
The GitHub release tag is named `macos-vX.Y.Z` to distinguish it from the
upstream source tag. Each release records the exact Codeberg commit.

## Install a release

Download the latest `aarch64-apple-darwin.tar.gz` asset from this repository's
Releases page. Extract it and place `fj` somewhere on your `PATH`. The archive
also includes the upstream Apache and MIT license files.

## Operational note

GitHub may delay or drop scheduled workflow runs, and it disables schedules in
public repositories after 60 days without repository activity. Check the
Actions page periodically; if the schedule is disabled, enable it again and
run the workflow manually. For a strict hands-off guarantee, an external
scheduler can trigger the workflow through GitHub's API.

## Optional external scheduler

[cron-job.org](https://cron-job.org/) is a free, open source hosted scheduler
that can send a custom HTTP request. To use it, create a fine-grained GitHub
token limited to this repository with **Actions: write** permission. Configure
a daily job with:

- URL: `https://api.github.com/repos/Convex-Works/forgejo-cli-macos/actions/workflows/release.yml/dispatches`
- Method: `POST`
- Headers: `Authorization: Bearer YOUR_TOKEN`, `Accept: application/vnd.github+json`, and `Content-Type: application/json`
- Body: `{"ref":"main"}`

The token is stored by the scheduler, so scope it to this repository and rotate
it when it expires. Keep it in the Authorization header, never in the URL. Once
the external job is tested, the GitHub `schedule` trigger can be removed; the
workflow's `workflow_dispatch` trigger must remain.
