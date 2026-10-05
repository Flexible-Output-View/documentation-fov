# Continuous Integration

> **Last Updated:** October 5th, 2026

To ensure the code quality of the FOV web stack, we use several GitHub Actions workflows and code analysis tools.

This document presents the Continuous Integration (CI) setup used by our repository.

## Commit Lint

Verifies that every commit message adheres to our formatting guidelines.

* **Triggers:** Runs on every push across all branches.
* **Runner:** GitHub-hosted runners.
* **Target:** Checks compliance with our [commit standard](../contributing.md#commits).
* **Results:**
  * **Success:** Commit messages follow the required format.
  * **Failure:** Commit messages violate the standard and the job fails.

## Backend Lint

Performs a backend analysis using ESLint.

* **Triggers:** Pull requests targeting the `main` branch.
* **Runner:** GitHub-hosted runners.
* **Results:** Inline warnings and error notices are added directly to the affected files in the GitHub pull request view.

## Backend Tests

Runs all backend tests with coverage.

* **Triggers:** Pull requests targeting the `main` branch.
* **Runner:** GitHub-hosted runners.
* **Results:** Coverage report uploaded as a GitHub artifact.

## Angular Tests

Runs all frontend tests with coverage.

* **Triggers:** Pull requests targeting the `main` branch.
* **Runner:** GitHub-hosted runners.
* **Results:** Coverage report uploaded as a GitHub artifact.

## Build & Push Images

Builds and publishes Docker container images for both the backend and frontend to GitHub Packages (GHCR) as [packages](https://github.com/orgs/Flexible-Output-View/packages?repo_name=web-fov).

* **Triggers:** Pushes to `main` and `dev` branch.
* **Runner:** GitHub-hosted runners.
* **Target:** Authenticates with GHCR and builds/pushes `fov-backend` and `fov-frontend` images tagged with the matching branch/reference name (`${{ github.ref_name }}`).
* **Results:**
  * **Success:** Container images are successfully built, tagged, and published to GitHub Packages.
  * **Failure:** Build errors or authentication/push failures cause the workflow job to fail.

## Development Deployment

Deploys the web application to our internal self-hosted test server.

* **Triggers:** Runs on every push to `dev`.
* **Runner:** Self-hosted VM.
* **Results:** Reports the success state of the deployment.

## Production Deployment

Deploys the web application to the public website [https://fovapp.live](https://fovapp.live).

* **Triggers:** Runs on every push to `main`.
* **Runner:** Public-hosted VM.
* **Results:** Reports the success state of the deployment.
