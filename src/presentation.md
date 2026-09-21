# Project FOV: Personalize Your Streaming Experience

> [!NOTE] In Short
> **FOV (Flexible Output View)** is a next-generation streaming solution designed to give viewers ultimate control over their viewing experience.

---

## What is FOV?
Have you ever watched a live stream where the music was too loud, the webcam was blocking the game, or the chat interface was in the way? Traditionally, viewers are stuck with whatever layout the streamer chooses.

**FOV** solves this by providing a multi-track streaming solution. Instead of receiving one flat video, viewers receive separate, **synchronized layers** (such as the game feed, webcam, and individual audio tracks) that they can independently control and arrange.


## Key Features for Viewers
Our platform delivers a modern streaming experience equipped with advanced interactive capabilities:

*   🎛️ **Custom Layouts:** Move, resize, or hide individual elements like the streamer's camera or chat window.
*   🔊 **Audio Mixing:** Adjust the volume of the game, background music, and the streamer's voice independently.
*   💾 **Personalized Settings:** Save your favorite layouts so you never have to reconfigure them manually.
*   🔄 **One-Click Reset:** Instantly return to the streamer's default layout whenever needed.

## Want to Try FOV?

Ready to dive in? Choose your path below:

*   💻 **Watching a Stream:** Experience the future of interactive viewing firsthand on our public demo instance at **[https://fovapp.live](https://fovapp.live)**.
*   📡 **Streaming to FOV:** Ready to broadcast? Follow our detailed **[User Guide](./user_guide.html)** to set up multi-track streaming.

---

## How It Works
Traditional streaming software flattens every element into a single video stream before broadcasting. FOV reimagines this pipeline:

1. 🎬 **Broadcasting:** Using a custom version of OBS Studio, the streamer transmits multiple isolated video and audio tracks simultaneously.
2. 🌐 **Delivery:** The FOV Backend ingests these tracks, synchronizes them dynamically, and converts them into optimized browser-ready streams.
3. 👀 **Viewing:** The FOV Web Platform receives these separate layers, empowering the viewer's browser to render and arrange them in real-time.

## The FOV Ecosystem
The project is built on three core pillars:

| Component | Description |
| :--- | :--- |
| **FOV Software** | A modified fork of OBS Studio, the world's leading streaming software optimized for multi-track isolation. |
| **Backend API** | The “brain” of the operation that manages users, categories, and the complex media pipelines required for multi-track delivery. |
| **Angular Frontend** | An intuitive website where spectators discover content and interact with the player. |

## Advanced Technical Architecture
*   ⚙️ **Custom OBS Integration:** Built on a modified C/C++ OBS fork featuring a custom output module that packages and transmits isolated tracks via the SRT protocol.
*   ⚡ **Backend Processing Pipeline:** Dynamically synchronizes incoming streams and converts them into segments for robust cross-browser playback.
*   📐 **Angular Web Platform:** A responsive, component-driven frontend providing a real-time interactive player for full layer customization.

## Performance Metrics & Targets
*   ⏱️ **Ultra-Low Latency:** Optimized for an inter-track synchronization delay under 50 ms and an overall viewer latency of ~5 seconds.
*   🌱 **Resource Optimization:** Implements advanced backend performance tuning to minimize CPU, memory, and bandwidth consumption.
*   🛡️ **Quality Assurance:** Backed by automated GitHub Actions CI/CD pipelines running unit tests, static code analysis, and multi-platform builds.

---

## Documentation
This repository contains the complete documentation for the FOV project.

> [!TIP] Want to contribute?
> Please check out the pinned [GitHub discussion](https://github.com/orgs/Flexible-Output-View/discussions) on our organization's page and [our contribution guidelines](./contributing.md) to get started!

## Frequently Asked Questions (Q&A)

*   ❓ **What is fovapp.live for?**
    *   [fovapp.live](https://fovapp.live) is provided primarily as a **demo environment** to showcase project capabilities. It does not have the server capacity to operate as a full-scale, Twitch-sized production platform. Please use it responsibly, respect applicable laws, and avoid abusive or unauthorized behavior.

*   🎓 **What is the context of this project?**
    *   This project is developed as part of the **EPITECH EIP (Innovative Projects Unit)** curriculum for **Promo 2027, Toulouse**.

*   🐛 **Where should I report bugs or request features?**
    *   You can open an issue on the relevant repository or head over to our [GitHub Discussions](https://github.com/orgs/Flexible-Output-View/discussions) page.

*   📦 **Can I use or deploy FOV?**
    *   Yes! You are free to use, fork, modify, and deploy FOV, provided you adhere to the project's licenses: our web stack is licensed under **MIT**, and the OBS fork (`obs-studio-fov`) is licensed under **GPLv2** (requiring any distributed modifications or forks to remain open-source under GPLv2).
