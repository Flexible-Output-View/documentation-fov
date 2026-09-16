## Architectural Proposal: Integrating MediaMTX for SRT Multiplexing

> Date: February 27th, 2026

Currently, our backend relies on FFmpeg to ingest SRT streams directly. Because FFmpeg's SRT listener locks the network port, scaling requires dynamically assigning a new port for every incoming stream. Integrating MediaMTX—a high-performance, zero-dependency real-time media server—acts as a dedicated ingest layer, resolving this limitation while offering significant operational advantages for our infrastructure.

---

### I. Strategic Benefits (Pros)

* **Unified Ingest Endpoint (Port Multiplexing):** MediaMTX natively supports SRT multiplexing. This allows all our broadcasters to stream to a single, static UDP port (e.g., `9999`). MediaMTX routes the incoming traffic internally based on the `streamid` parameter. This eliminates the need to expose and manage broad port ranges in our Docker configuration or firewall.
* **Native Protocol Translation & Resource Optimization:** MediaMTX automatically translates incoming SRT streams into HLS, RTSP, and WebRTC with zero configuration. If our platform does not require custom FFmpeg processing (such as transcoding to multiple resolutions like 720p or 480p), we can offload HLS generation entirely to MediaMTX. This would drastically reduce the CPU overhead on our servers.
* **Enhanced Stream Resilience:** Designed specifically for real-time media delivery, MediaMTX handles network jitter, packet loss, and broadcaster reconnections much more gracefully than our raw FFmpeg listener process.
* **Built-in API Management:** MediaMTX features an HTTP API that allows us to query active sessions, monitor bandwidth, and terminate streams, reducing the amount of manual process management required in our Node.js backend.

### II. Trade-offs and Considerations (Cons)

* **Increased Infrastructure Complexity:** Introducing a dedicated media server adds a new component to our stack. Our architecture will evolve from a standalone Node.js application to a multi-service environment (Node.js backend + Database + MediaMTX).
* **Container Orchestration Requirements:** A standard `docker run` command will no longer suffice. We will need to implement a `docker-compose.yml` file to orchestrate the backend and the MediaMTX containers, ensuring they can communicate over a shared Docker network.
* **Internal Network Overhead:** If we choose to retain FFmpeg for custom transcoding, the video data must travel a slightly longer path: `Broadcaster -> MediaMTX -> FFmpeg -> HLS Files`. While this internal routing only adds a few milliseconds of latency, it is an extra hop in our pipeline.

---

### III. Required Architectural Changes

To implement this solution, the following modifications to our project are necessary:

#### 1. Infrastructure Expansion (Docker Compose)

We will need to transition from our current `Dockerfile` implementation to a `docker-compose.yml` setup. This file will define two services:

* Our existing Node.js backend.
* The `bluenviron/mediamtx` image, configured to expose the single SRT ingest port (e.g., `8890`) to the public, alongside its API and internal HTTP ports.

#### 2. Backend Refactoring (`mediaServer.mjs`)

The logic within `backend/src/mediaServer.mjs` will need to be updated based on how much we want to rely on MediaMTX:

* **Path A (Retain FFmpeg for Transcoding):** Our backend will no longer spawn FFmpeg as a *listener*. Instead, when a user registers a stream, our backend will instruct FFmpeg to run as a *caller*, pulling the internal RTSP or SRT feed directly from the MediaMTX container (e.g., `rtsp://mediamtx:8554/<streamId>`) and processing it into HLS segments.
* **Path B (Deprecate FFmpeg - Recommended for simple pass-through):** We can remove the `child_process` spawning logic entirely. The `/ffmpeg/register` endpoint will simply allocate a `streamId`, instruct the user to stream to the global MediaMTX port, and return the anticipated HLS playback URL (which MediaMTX will generate automatically).
