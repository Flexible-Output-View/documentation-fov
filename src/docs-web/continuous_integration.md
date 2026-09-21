# Continuous Integration

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
