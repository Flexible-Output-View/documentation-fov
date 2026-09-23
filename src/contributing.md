# Contribution Guidelines & Project Organization

Welcome to the contribution guidelines! This document outlines the standards, workflows, team structure, governance, and repositories for the Flexible Output View (FOV) ecosystem.

### Scope

This document applies to all organization repositories and to every team member or contributor working on the project.

---

## Table of Contents

* **[Team & Organization](#team--organization)**
  * [Core Team](#core-team)
  * [GitHub Organization & Repositories](#github-organization--repositories)
  * [Community, Discussions & Feedback](#community-discussions--feedback)
  * [GitHub Project Board](#github-project-board)
* **[Releases](#releases)**
* **[Contribution & Feature Policy](#contribution--feature-policy)**
  * [What Will and Won't Be Accepted](#what-will-and-wont-be-accepted)
* **[Development & Branching Strategy](#development--branching-strategy)**
  * [Git Branch Rules](#git-branch-rules)
  * [Coding Conventions](#coding-conventions)
  * [Commit Message Format](#commit-message-format)
  * [Pull Requests & How to Submit a Change](#pull-requests--how-to-submit-a-change)
  * [Response Time & Review SLA](#response-time--review-sla)
* **[Governance & Community Guidelines](#governance--community-guidelines)**
* **[Frequently Asked Questions (Q&A)](#frequently-asked-questions-qa)**

---

## Team & Organization

### Core Team
FOV is developed and maintained by a 3-person core team:
* **Lucas Loustalot** ([@LucasLoustalot](https://github.com/LucasLoustalot))
* **Raphael Scandella** ([@RaphxelS](https://github.com/RaphxelS))
* **Samy NASSET** ([@Slymoz](https://github.com/Slymoz))

### GitHub Organization & Repositories
All project code and documentation are hosted under the official [Flexible Output View GitHub Organization](https://github.com/Flexible-Output-View). The project is structured across three primary repositories:

1. **[`obs-studio-fov`](https://github.com/Flexible-Output-View/obs-studio-fov)**
   * **Language:** C
   * **License:** GPLv2
   * **Description:** A custom fork of `obsproject/obs-studio` built specifically to add robust multi-track functionality and source isolation for the FOV ecosystem.
2. **[`web-fov`](https://github.com/Flexible-Output-View/web-fov)**
   * **Language:** JavaScript
   * **License:** MIT
   * **Description:** Repository hosting the Flexible Output View web stack and platform infrastructure.
3. **[`documentation-fov`](https://github.com/Flexible-Output-View/documentation-fov)**
   * **Description:** Public documentation repository containing user guides, developer docs, and `mdBook` source files.

### Community, Discussions & Feedback
* **GitHub Discussions:** We actively use [GitHub Discussions](https://github.com/orgs/Flexible-Output-View/discussions) to collect user feedback, discuss architectural decisions, answer questions, and interact with the community.
* **Building & Feedback:** We strongly encourage users to share their feedback, exchange thoughts, and build alternative or complementary software solutions utilizing our platform.

### GitHub Project Board
The main internal GitHub project board is currently private (managed under the Epitech EIP framework) and accessible exclusively to the FOV team.

> [!NOTE]
> We plan to release a public mini GitHub project with a global roadmap to the public soon. Keeping the internal project board up-to-date remains **mandatory** for all active team contributors.

---

## Releases

The FOV core team periodically creates tagged GitHub releases containing stable software packages.
* **Trigger Criteria:** A new release is triggered when sufficient improvements, bug fixes, and features have been accumulated and merged into `master` to deliver a significantly enhanced experience to users.

---

## Contribution & Feature Policy

### Feature Submissions & Workflow
* Contributors are free to develop features or fixes and open a pull request directly whenever they are ready.
* **Feature Requests & Questions:** You can discuss potential feature requests or ask questions directly to the team via [GitHub Discussions](https://github.com/orgs/Flexible-Output-View/discussions) before or during implementation.
* The FOV core team begins evaluating and reviewing contributions starting from the moment a pull request is opened.

### What Will and Won't Be Accepted
* **What Will Be Accepted:**
  * Well-tested bug fixes and performance improvements.
  * Clear, modular features that align with the core architectural goals of the FOV ecosystem.
  * Documentation updates with accurate, clear explanations.
  * Contributions that successfully pass automated CI checks and peer reviews.
* **What Won't Be Accepted:**
  * Out-of-scope architectural rewrites or features introduced without prior discussion or issue alignment.
  * Code lacking proper documentation, formatting, or test coverage.
  * Contributions failing automated continuous integration pipelines or introducing unresolved security/stability risks.

---

## Development & Branching Strategy

### Git Branch Rules
Our repository structure relies on two primary long-lived branches alongside feature branches:
* **`dev` (Integration Branch):** All active development happens here. **Every pull request must target and merge into `dev`.**
* **`main` / `master` (Production Branch):** Stable, production-ready code. This branch **only** receives commits by merging `dev` into it. Direct commits or PRs into `main`/`master` are strictly prohibited.

#### Feature Branches & Cleanup
* Create dedicated Git branches for every feature or bug fix (ideally using GitHub's "Create a branch" button directly from your assigned issue).
* **Cleanup:** Merged branches **must be deleted** once the pull request is closed.

### Coding Conventions
To maintain a clean and readable codebase across repositories, please adhere to the following baseline conventions:
* **Language Standards:** Follow established language-specific styles (e.g., standard JavaScript style guidelines for `web-fov`, and clean C practices for `obs-studio-fov`).
* **Automatic Formatting:** Contributors must use automatic code formatting tools prior to submission:
  * Use **`clang-format`** for all `obs-studio-fov` C code.
  * Use **`eslint` or `prettier`** for the frontend and backend codebase in `web-fov`.
* **Comments & Clarity:** Write self-documenting code and add clear comments for complex logic or business logic blocks.
  * Use Doxygen comments for `obs-studio-fov`.

### Commit Message Format
Commit messages must follow a clean, standardized format:

```text
PREFIX: Short description
(Optional detailed description)
```

Where **PREFIX** is:
* `ADD`: Adding a new feature.
* `UPDATE`: Updating an existing feature.
* `FIX`: Fixing a bug.
* `RM`: Removing a functionality or file.
* `DOC`: Adding or updating documentation.
* `REFACT`: Refactoring code without altering functionality.
* `TEST`: Adding or updating tests.
* `MERGE`: Merging a branch via pull request.

### Pull Requests & How to Submit a Change
Pull requests (PRs) are **mandatory** for introducing changes into `dev`.

#### Step-by-Step Submission Workflow:
1. **Fork & Branch:** Create a dedicated feature branch off `dev`.
2. **Implement & Test:** Code your changes following project conventions, apply formatters, and perform local testing.
3. **Commit:** Commit your changes using the standardized commit message format.
4. **Open PR:** Submit a Pull Request targeting the `dev` branch, referencing any related issues or discussions.

#### PR Requirements & Review Policy
* **Mandatory Review:** **All PRs must be reviewed and approved by at least one member of the team** before merging.
* **Manual & Local Testing:** The FOV team tests every pull request manually and locally to ensure our quality standards are met.
* **CI Validation:** The automated CI pipeline must complete successfully.

### Response Time & Review SLA
* As this project is developed as part of our 5th-year Epitech curriculum, the core team is primarily active and working on the repositories during **Thursdays and Fridays**.
* Review feedback, issue triage, and PR merges are concentrated around these project days. We appreciate your patience outside of these windows!

---

## Governance & Community Guidelines

* **Disagreements:** Any disagreements or conflicts within the community will be handled and resolved by **majority decision**.
* **Constructive Collaboration:** We value respectful, community-driven development and encourage open discussions via GitHub channels.
