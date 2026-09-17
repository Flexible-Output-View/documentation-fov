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
* **[Development & Branching Strategy](#development--branching-strategy)**
  * [Git Branch Rules](#git-branch-rules)
  * [Commit Message Format](#commit-message-format)
  * [Pull Requests](#pull-requests)
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

### Feature Approvals
* New features **must be approved** by the FOV core team before any code is written or pull request is opened.
* Before opening a PR for a new feature, you must create a **GitHub issue** or start a thread in **GitHub Discussions** to propose and vet the idea.

### Quality Standards & Right to Reject
* The FOV core team holds ultimate responsibility for the project's direction, stability, and code health.
* The team retains the **right to reject any contribution** that does not align with our project standards, code quality benchmarks, or process requirements.

---

## Development & Branching Strategy

### Git Branch Rules
Our repository structure relies on two primary long-lived branches alongside feature branches:
* **`dev` (Integration Branch):** All active development happens here. **Every pull request must target and merge into `dev`.**
* **`main` / `master` (Production Branch):** Stable, production-ready code. This branch **only** receives commits by merging `dev` into it. Direct commits or PRs into `main`/`master` are strictly prohibited.

#### Feature Branches & Cleanup
* Create dedicated Git branches for every feature or bug fix (ideally using GitHub's "Create a branch" button directly from your assigned issue).
* **Cleanup:** Merged branches **must be deleted** once the pull request is closed.

### Commits
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

### Pull Requests
Pull requests (PRs) are **mandatory** for introducing changes into `dev`.

#### PR Requirements & Review Policy
* **Mandatory Review:** **All PRs must be reviewed and approved by at least one member of the team** before merging.
* **Manual & Local Testing:** The FOV team tests every pull request manually and locally to ensure our quality standards are met.
* **CI Validation:** The automated CI pipeline must complete successfully.
* **Association:** Every PR must explicitly reference the issue or branch it addresses.

---

## Governance & Community Guidelines

* **Disagreements:** Any disagreements or conflicts within the community will be handled and resolved by **majority decision**.
* **Constructive Collaboration:** We value respectful, community-driven development and encourage open discussions via GitHub channels.

---

## Frequently Asked Questions (Q&A)

* **What is fovapp.live for?**
  * [fovapp.live](https://fovapp.live) is provided primarily as a **demo environment** to showcase the project capabilities. It does not possess the server capacity to operate as a fully-featured, Twitch-sized production platform. Users must use it responsibly, respect applicable laws, and avoid abusive or unauthorized behavior ("don't do anything stupid with it").
* **What is the context of this project?**
  * This project is developed as part of an **EPITECH EIP (Innovative Projects Unit)** curriculum for **Promo 2027, Toulouse**.
* **Where should I report bugs or request features?**
  * Please open an issue on the relevant repository or head over to our [GitHub Discussions](https://github.com/orgs/Flexible-Output-View/discussions) page.
* **Can I use / deploy FOV?**
  * Yes! You are free to use, fork, modify, and deploy FOV, provided you adhere to the project's licenses: our web stack is licensed under **MIT**, and the OBS fork (`obs-studio-fov`) is licensed under **GPLv2** (meaning any distributed modifications or forks must also remain open-source under GPLv2).