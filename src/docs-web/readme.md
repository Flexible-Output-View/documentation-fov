# FOV Web Documentation

Welcome to the documentation for the **FOV Web Stack**. The web platform is composed of two main components:
- **Frontend:** An Angular-based user interface and custom multi-stream player.
- **Backend:** A Node.js + Express server managing stream metadata, user authentication, and routing.

---

## Documentation Sections

* **[Frontend Documentation](./docs-frontend/index.html)**
  Guides, setup instructions, and component references for the Angular frontend application.

* **[Backend Documentation](./docs-backend/index.html)**
  Comprehensive documentation for the Node.js backend, covering architecture, API testing, deployment, security, and developer guidelines.

* **[Continuous Integration](./continuous_integration.md)**
  Overview of the CI/CD pipelines, workflows, and automated checks used for the web repository.

---

## Web Platform Architecture Overview

### Core Technologies

* **Frontend:**
  * **Angular Framework:** Utilized for its robust component architecture, team familiarity, and strong support for managing complex, interactive user interfaces.
  * **Custom Video Player:** Handles multi-stream layouts, stream synchronization, and interactive layout customization.

* **Backend:**
  * **Node.js & Express:** Powers the core API, user authentication systems, and stream metadata handling (operating similarly to standard broadcasting platforms like Twitch).
  * **Database:** Postgres used for persistent data storage.

### Infrastructure & Requirements
* **Development Setup:** Service isolation achieved via Docker containers.
* **Viewer Requirements:** Modern web browsers (Chrome, Firefox, Edge) for smooth multi-stream layout rendering and low-latency playback.
