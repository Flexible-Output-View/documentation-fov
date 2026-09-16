# Deployment Architecture

> Date: April 5th, 2026

The infrastructure relies on a clear separation between code management, deployment automation, and containerized service execution on an EPITECH virtual machine. The entire setup is secured and optimized by a proxy and CDN layer.

## Lifecycle and CI/CD (GitHub)
The workflow starts on **GitHub**.
* **Trigger**: An action is initiated upon a `push` to the `master` branch.
* **GitHub Actions Runner**: A runner is installed directly on the **Virtual Machine** to update the stack (Docker Compose) to rebuild and update the deployment.

## Server Infrastructure (Virtual Machine)
The virtual machine hosts the entire application via **Docker**. The architecture is divided into two distinct Docker Compose groups for isolation:

### Entry Point and Proxy (Docker Compose - Nginx Proxy Manager)
* **Nginx Proxy Manager**: This container acts as the single entry point for HTTP/HTTPS traffic. It manages the routing of requests coming from Cloudflare to the appropriate internal services (Frontend or Backend) and handles SSL termination.

### Application Stack (Docker Compose - Services)
This group contains the core of the platform, segmented into three units:
* **Frontend (Angular & Nginx)**: The client application developed with Angular is served by a dedicated Nginx instance. It communicates with the proxy for user exposure.
* **Backend (Node.js & FFmpeg)**:
    * The **Node.js** server handles business logic and APIs.
    * **FFmpeg** is integrated to receive the SRT stream from OBS and process the video streams to convert them into HLS.
* **MySQL**: MySQL database in a container

## Network Flow and Protocols
The architecture supports two types of incoming/outgoing flows:

* **Web Traffic (Frontend / API)**:
    * The user's browser (Firefox, etc.) accesses the platform via **Cloudflare**, which provides DDoS protection and caching.
    * Requests are then forwarded to the **Nginx Proxy Manager** on the VM.
* **Contribution Flow (SRT Stream)**:
    * The streaming software (**OBS**) sends the multi-track video stream directly to the **Backend** via the **SRT** protocol.
    * This stream bypasses the standard HTTP proxy to benefit from the low latency and reliability of SRT transport, allowing the FFmpeg module to process data in real time.

## Deployment Environment
The GitHub Actions workflow used relies on GitHub deployment environments for the environment variables necessary for deployment.

![GitHub Environment](<Github Environment.png>)

### Architecture Diagram and Configurations
![Deployment Architecture](<Deploy Architecture.jpg>)

## Domains
- The website is deployed on [fovapp.live](https://fovapp.live)
- The backend API (to configure in OBS) is deployed on [api.fovapp.live](https://api.fovapp.live)
- The entry point for SRT streams is at [ingest.fovapp.live](https://ingest.fovapp.live)

## Summary of Technologies Used
| Component | Technology | Role |
| :--- | :--- | :--- |
| **Source** | GitHub | Deployment via GitHub actions |
| **CDN / Security** | Cloudflare | Protection and network acceleration |
| **Orchestration** | Docker Compose | Container management |
| **Routing** | Nginx Proxy Manager | Reverse proxy and SSL management |
| **Video Processing** | FFmpeg | Multi-track stream manipulation |
| **Video Transport** | SRT | Protocol for transmitting video streams to the backend |