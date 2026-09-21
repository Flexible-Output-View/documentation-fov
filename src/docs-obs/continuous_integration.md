# Continuous Integration

To ensure the code quality of the FOV software, we use several GitHub Actions workflows and code analysis tools.

This document presents the Continuous Integration (CI) setup used by our repository fork.

## Commit Lint

Verifies that every commit message adheres to our formatting guidelines.

* **Triggers:** Runs on every push across all branches.
* **Runner:** GitHub-hosted runners.
* **Target:** Checks compliance with our [commit standard](../contributing.md#Commits).
* **Results:**
  * **Success:** Commit messages follow the required format.
  * **Failure:** Commit messages violate the standard and the job fails.

## CodeChecker (clang-tidy)

Performs a comprehensive project analysis using CodeChecker combined with `clang-tidy`.

* **Triggers:** Pull requests targeting the `dev` or `master` branches.
* **Runner:** Self-hosted VM.
* **Results:** Uploads security and code quality findings directly to the **Security** tab of the GitHub repository.

## Cppcheck

Analyzes C/C++ source code using `cppcheck` to detect potential bugs, memory issues, and code smells.

* **Triggers:**
  * **On Push:** Analyzes only the files modified in the `git diff`.
  * **On Pull Request:** Analyzes all files modified within the pull request.
* **Runner:** Self-hosted VM.
* **Results:** Inline warnings and error notices are added directly to the affected files in the GitHub pull request view. Full execution logs are uploaded as GitHub artifacts.

## End-to-End Integration Test

Executes a full end-to-end integration test with the FOV web stack.

* **Triggers:** Runs on pushes to `dev` and `master`.
* **Runner:** GitHub-hosted runners.
* **Steps:**
  * Pulls `web-fov` and `obs-studio-fov`.
  * Deploys `web-fov` locally using Docker.
  * Installs OBS dependencies from `requirements.sh`.
  * Builds `obs-studio-fov` in Release mode with the special `fov-integration-test` plugin.
  * Runs the software using `xvfb`.
  * Configures a test scene with two video media sources and starts a stream to the locally deployed `web-fov` instance.
  * Verifies via the `web-fov` API that the stream is available and contains 2 video and 2 audio tracks.
* **Results:** Uploads the generated logs produced by `obs-studio-fov` and `web-fov` as GitHub artifacts.

## Cross-Platform Builds (Windows, macOS, Linux)

Compiles the project across Linux, Windows, and macOS to verify multi-platform support.

* **Triggers:**
  * **`dev` or `master` branches:** Built using the **Release** configuration.
  * **Other branches:** Built using the **Debug** configuration.
* **Runner:** Self-hosted VM.
* **Results:** Upon successful compilation, build binaries are uploaded as GitHub artifacts.

## Flatpak Packaging

Creates a Linux Flatpak package for deployment and application testing.

* **Triggers:** Runs on pushes to `dev` and `master`.
* **Runner:** Self-hosted VM.
* **Results:** Uploads the generated Flatpak file as a GitHub artifact.
