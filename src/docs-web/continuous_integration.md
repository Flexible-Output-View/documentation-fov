# Continuous Integration & Deployment (CI/CD)

> **Last Updated:** November 13, 2026

To maintain code quality and automate releases for the FOV web stack (`web-fov`), we use GitHub Actions workflows and automated analysis tools. This document outlines the triggers, environments, and expected outcomes for each workflow in our repository.

## Workflow Overview

| Workflow | Trigger(s) | Runner | Purpose |
| :--- | :--- | :--- | :--- |
| **Commit Lint** | Push (excl. `ga-ignore-*`) | GitHub-hosted | Enforces commit message standards via script |
| **Backend Tests & Lint** | Push (`backend/**`), PR (`main`, `dev`) | GitHub-hosted | Runs ESLint, unit/integration tests, & coverage |
| **Angular Tests** | Push (`fov-angular/**`), PR (`main`, `dev`) | GitHub-hosted | Runs frontend Angular test suite & coverage |
| **Integration Test** | Push (`master`, `dev`), PR (`master`, `dev`, `dependabot/**`) | GitHub-hosted (Ubuntu) | End-to-end streaming & stack validation with OBS fork |
| **Build & Push Images** | Push (`main`, `dev`), Tags (`v*`) | GitHub-hosted | Builds & publishes Docker images to GHCR |
| **Development Deployment** | Push to `dev` | Self-hosted (`deploy`) | Deploys stack to internal testing server |
| **Production Deployment** | Push to `main` | Self-hosted (`public`, `master`) | Deploys stack to public production |
| **Frontend Accessibility** | Push to `main` | GitHub-hosted | Audits WCAG compliance & opens issues |

---

## Code Quality & Unit Testing

### Commit Lint

[![Commit Lint - WEB-FOV](https://github.com/Flexible-Output-View/web-fov/actions/workflows/fov-commits.yml/badge.svg)](https://github.com/Flexible-Output-View/web-fov/actions/workflows/fov-commits.yml)

Verifies that every commit message adheres to the repository's formatting guidelines.

- **Triggers:** Pushes across all branches except those prefixed with `ga-ignore-*`.
- **Runner:** GitHub-hosted (`ubuntu-latest`).
- **Target:** Executes `commit_lint.sh`.
- **Results:**
  - **Success:** All commit messages follow the required format.
  - **Failure:** One or more commit messages violate the standard, failing the check.

### Backend Tests & Lint

[![Backend Tests & Lint](https://github.com/Flexible-Output-View/web-fov/actions/workflows/backend-tests.yml/badge.svg)](https://github.com/Flexible-Output-View/web-fov/actions/workflows/backend-tests.yml)

Executes backend database integration tests, unit tests, code coverage, and ESLint static code analysis.

- **Triggers:** Pushes affecting `backend/**` (or workflow file) and Pull Requests targeting `main` or `dev`.
- **Runner:** GitHub-hosted (`ubuntu-latest`) with a PostgreSQL service container.
- **Results:** Reports test metrics, uploads coverage reports to Codecov and GitHub artifacts, and runs the linter.

### Angular Tests

[![Angular Tests](https://github.com/Flexible-Output-View/web-fov/actions/workflows/angular-tests.yml/badge.svg)](https://github.com/Flexible-Output-View/web-fov/actions/workflows/angular-tests.yml)

Executes the full Angular frontend test suite using Headless Chrome with code coverage tracking.

- **Triggers:** Pushes affecting `fov-angular/**` (or workflow file) and Pull Requests targeting `main` or `dev`.
- **Runner:** GitHub-hosted (`ubuntu-latest`).
- **Results:** Reports test outcomes, uploads coverage reports to Codecov and artifacts.

---

## End-to-End Integration

### Integration Test

[![FOV Integration Test](https://github.com/Flexible-Output-View/web-fov/actions/workflows/fov-integration-test.yml/badge.svg)](https://github.com/Flexible-Output-View/web-fov/actions/workflows/fov-integration-test.yml)

Runs a full-stack integration test simulating a live streaming workflow between the web platform and our compiled custom OBS fork (`obs-studio-fov`).

- **Triggers:** Pushes to `master` and `dev`, plus Pull Requests targeting `master`, `dev`, `bump-dependencies`, or `dependabot/**`.
- **Runner:** GitHub-hosted (`ubuntu-latest`) with a 90-minute timeout.
- **Workflow Steps:**
  1. Sets up Docker and checks out `web-fov` with submodules, cloning `obs-studio-fov` (dev branch).
  2. Installs testing dependencies (Xvfb, PulseAudio, FFmpeg, Python dependencies).
  3. Launches the `web-fov` Docker compose stack.
  4. Configures stream settings and creates a test user via python scripts (`integration_setup_stream.py`).
  5. Compiles `obs-studio-fov` and runs it headlessly inside an Xvfb virtual display server to stream via the generated key.
  6. Validates stream availability and metadata via `integration_test.py`.
- **Results:** Uploads comprehensive logs (`docker-web-stack.log`, `obs-stdout.log`, setup/test logs) as workflow artifacts.

---

## Build & Deployment

### Build & Push Images

[![Build & Push images](https://github.com/Flexible-Output-View/web-fov/actions/workflows/build-images.yml/badge.svg)](https://github.com/Flexible-Output-View/web-fov/actions/workflows/build-images.yml)

Builds and publishes Docker container images (`fov-backend` and `fov-frontend`) to [GitHub Packages (GHCR)](https://github.com/orgs/Flexible-Output-View/packages?repo_name=web-fov).

- **Triggers:** Pushes to `main` or `dev`, and version tags (`v*`).
- **Runner:** GitHub-hosted (`ubuntu-latest`).
- **Target:** Authenticates with GHCR and pushes images tagged with the matching branch or tag reference (`${{ github.ref_name }}`).
- **Results:**
  - **Success:** Container images are successfully built and published.
  - **Failure:** Build errors or registry authentication failures fail the job.

### Development Deployment

[![Deploy Developpement - WEB-FOV](https://github.com/Flexible-Output-View/web-fov/actions/workflows/fov-deploy.yml/badge.svg)](https://github.com/Flexible-Output-View/web-fov/actions/workflows/fov-deploy.yml)

Deploys the latest `dev` branch changes to the internal self-hosted deployment server.

- **Triggers:** Every push to `dev`.
- **Runner:** Self-hosted runner (`[self-hosted, linux, x64, deploy]`).
- **Results:** Cleans previous containers/images, copies updated repository files, and spins up the Docker compose stack.

### Production Deployment

[![Deploy Public - WEB-FOV](https://github.com/Flexible-Output-View/web-fov/actions/workflows/fov-deploy-public.yml/badge.svg)](https://github.com/Flexible-Output-View/web-fov/actions/workflows/fov-deploy-public.yml)

Deploys the release website (`main` branch) to the public self-hosted production environment.

- **Triggers:** Every push to `main`.
- **Runner:** Self-hosted public runner (`[self-hosted, linux, x64, public, master]`).
- **Results:** Ensures the `nginx-proxy-network` Docker network exists, stops previous containers, cleans images, and runs `docker compose` using both `docker-compose.yml` and `docker-compose.prod.yml` override files.

---

## Post-Deployment Audits

### Frontend Accessibility Check

[![FOV Frontend Accessibility](https://github.com/Flexible-Output-View/web-fov/actions/workflows/fov-frontend-accessibility.yml/badge.svg)](https://github.com/Flexible-Output-View/web-fov/actions/workflows/fov-frontend-accessibility.yml)

Runs an automated accessibility scan against the frontend application URLs.

- **Triggers:** Every push to `main`.
- **Runner:** GitHub-hosted (`ubuntu-latest`).
- **Results:** Spins up the web stack via Docker and runs the GitHub Accessibility Scanner action against local endpoints (`/`, `/login`, `/followed`, `/discover`, `/how-does-it-work`), grouping issues by rule and opening them automatically in the repository issue tracker.
