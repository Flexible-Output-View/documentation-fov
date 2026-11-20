# Continuous Integration

> **Last Updated:** November 13, 2026

To ensure the code quality and cross-platform reliability of the FOV software (`obs-studio-fov`), we use several GitHub Actions workflows and code analysis tools.

This document presents the Continuous Integration (CI) setup used by our repository.

## Workflow Overview

| Workflow | Trigger(s) | Runner | Purpose |
| --- | --- | --- | --- |
| **Commit Lint** | Push (excl. `ga-ignore-*`) | GitHub-hosted (`ubuntu-latest`) | Enforces commit message standards |
| **Linux CI & Analysis** | Push, Pull Request | Self-hosted (`linux`, `x64`, `ubuntu`) & GitHub-hosted | Builds Linux binaries, runs Cppcheck & CodeChecker |
| **Windows & macOS Builds** | Push | Self-hosted (`windows`, `x64`) & GitHub-hosted (`macos-15`) | Compiles multi-platform release binaries |
| **Flatpak Build** | Push (`dev`, `master`, `flatpak`, tags) | Container (`flathub-infra`) on Ubuntu | Packages the Linux application as a Flatpak bundle |
| **End-to-End Integration Test** | Push (`master`, `dev`), Pull Request (`dev`, `master`, `dependabot/**`) | GitHub-hosted (`ubuntu-latest`) | Validates streaming workflow with `web-fov` |

---

## Commit & Code Quality

### Commit Lint

[![Commit Lint - OBS-FOV](https://github.com/Flexible-Output-View/obs-studio-fov/actions/workflows/fov-commits.yml/badge.svg)](https://github.com/Flexible-Output-View/obs-studio-fov/actions/workflows/fov-commits.yml)

Verifies that every commit message adheres to our formatting guidelines.

* **Triggers:** Every push, ignoring branches prefixed with `ga-ignore-*`.
* **Runner:** GitHub-hosted (`ubuntu-latest`).
* **Target:** Checks compliance via `./.github/scripts/FOV/commit_lint.sh`.
* **Results:**
  * **Success:** Commit messages follow the required format.
  * **Failure:** Commit messages violate the standard and the job fails.


### Static Analysis

[![FOV Linux CI & Analysis](https://github.com/Flexible-Output-View/obs-studio-fov/actions/workflows/fov-build-scan-linux.yml/badge.svg)](https://github.com/Flexible-Output-View/obs-studio-fov/actions/workflows/fov-build-scan-linux.yml)

#### CodeChecker (clang-tidy)

Performs a comprehensive project analysis using CodeChecker combined with `clang-tidy`.

* **Triggers:** Pull requests targeting the `dev` or `master` branches (or pushes to `dev`/`master`).
* **Runner:** Self-hosted VM (`[self-hosted, linux, x64, ubuntu]`).
* **Results:** Uploads security and code quality findings directly to the **Security** tab of the GitHub repository via SARIF upload.

#### Cppcheck

Analyzes C/C++ source code using `cppcheck` to detect potential bugs, memory issues, and code smells.

* **Triggers:** Runs as part of the Linux CI & Analysis pipeline on pushes and pull requests.
* **Runner:** GitHub-hosted (`ubuntu-24.04`) using artifacts from the Linux build step.
* **Results:** Inline warnings and error notices are published to the GitHub pull request view. Full execution logs are uploaded as a `cppcheck` artifact.

---

## Compilation & Packaging

### Cross-Platform Builds (Windows, macOS, Linux)

[![FOV Windows & macOS Builds](https://github.com/Flexible-Output-View/obs-studio-fov/actions/workflows/fov-build-win-mac.yml/badge.svg)](https://github.com/Flexible-Output-View/obs-studio-fov/actions/workflows/fov-build-win-mac.yml)


Compiles the project across Linux, Windows, and macOS to verify multi-platform support.

* **Triggers:** Every push.
* **Runners:**
  * **Linux:** Self-hosted VM (`[self-hosted, linux, x64, ubuntu]`) building debug binaries and producing build artifacts.
  * **Windows:** Self-hosted VM (`[self-hosted, windows, x64]`) building Release configuration, zipping archives, and optionally uploading to GitHub Releases if tagged (`v*`).
  * **macOS:** GitHub-hosted `macos-15` runner building Release configuration, packaging a DMG installer, and optionally uploading to GitHub Releases if tagged.


* **Results:** Compiled binaries, zips, and DMG packages are uploaded as GitHub workflow artifacts.

### Flatpak Packaging

[![FOV Flatpak Build](https://github.com/Flexible-Output-View/obs-studio-fov/actions/workflows/fov-build-flatpak.yml/badge.svg)](https://github.com/Flexible-Output-View/obs-studio-fov/actions/workflows/fov-build-flatpak.yml)

Creates a Linux Flatpak package for deployment and application testing.

* **Triggers:** Pushes to `dev`, `master`, `flatpak`, and any tags (`*`).
* **Runner:** GitHub-hosted runner running inside the Flathub flatpak-builder container image.
* **Results:** Generates a `FOV-linux-x64-release.flatpak` bundle uploaded as an artifact, and uploads to GitHub Releases on tag pushes.

---

## Integration Testing

### End-to-End Integration Test

[![FOV Integration Test](https://github.com/Flexible-Output-View/obs-studio-fov/actions/workflows/fov-integration-test.yml/badge.svg)](https://github.com/Flexible-Output-View/obs-studio-fov/actions/workflows/fov-integration-test.yml)

Executes a full end-to-end integration test with the FOV web stack.

* **Triggers:** Pushes to `master` and `dev`, and pull requests targeting `dev`, `master`, or dependabot branches.
* **Runner:** GitHub-hosted (`ubuntu-latest`) with a 90-minute timeout.
* **Steps:**
  * Clones `obs-studio-fov` and clones the `web-fov` repository (dev branch).
  * Prepares test environment dependencies (Xvfb, PulseAudio sink, FFmpeg, Python).
  * Deploys `web-fov` locally using Docker Compose.
  * Installs OBS software prerequisites and compiles `obs-studio-fov` in Release mode with the special integration test script/plugin.
  * Starts OBS headlessly inside an Xvfb virtual display server to stream via the test stream key.
  * Verifies via the `web-fov` API script that the stream is active and running correctly.


* **Results:** Uploads the generated logs (`docker-web-stack.log`, `obs-stdout.log`, setup and integration test logs) as GitHub artifacts (`integration-test-logs`).
