# Project FOV: Personalize Your Streaming Experience

> [!TIP]
> **Looking for the complete, interactive documentation?**
>
> Visit our official documentation at: [https://flexible-output-view.github.io/documentation-fov](https://flexible-output-view.github.io/documentation-fov)

---

## What is FOV?

> [!Note]
> **In Short**
> **FOV (Flexible Output View)** is a next-generation streaming solution designed to give viewers ultimate control over their viewing experience.


Have you ever watched a live stream where the music was too loud, the webcam was blocking the game, or the chat interface was in the way? Traditionally, viewers are stuck with whatever layout the streamer chooses.

**FOV** solves this by providing a multi-track streaming solution. Instead of receiving one flat video, viewers receive separate, **synchronized layers** (such as the game feed, webcam, and individual audio tracks) that they can independently control and arrange.

## Key Features for Viewers
Our platform delivers a modern streaming experience equipped with advanced interactive capabilities:

*   🎛️ **Custom Layouts:** Move, resize, or hide individual elements like the streamer's camera or chat window.
*   🔊 **Audio Mixing:** Adjust the volume of the game, background music, and the streamer's voice independently.
*   💾 **Personalized Settings:** Save your favorite layouts so you never have to reconfigure them manually.
*   🔄 **One-Click Reset:** Instantly return to the streamer's default layout whenever needed.

## How It Works
Traditional streaming software flattens every element into a single video stream before broadcasting. FOV reimagines this pipeline:

1. 🎬 **Broadcasting:** Using a custom version of OBS Studio, the streamer transmits multiple isolated video and audio tracks simultaneously.
2. 🌐 **Delivery:** The FOV Backend ingests these tracks, synchronizes them dynamically, and converts them into optimized browser-ready streams.
3. 👀 **Viewing:** The FOV Web Platform receives these separate layers, empowering the viewer's browser to render and arrange them in real-time.

## The FOV Ecosystem
The project is built on three main pillars:

*   **Custom OBS Studio:** A modified version of the world's most popular streaming software that handles "Multi-Track" isolation.
*   **The Backend API:** The "brain" of the operation that manages users, categories, and the complex media pipelines required for multi-track delivery.
*   **The Web Platform:** An intuitive website where spectators discover content and interact with the personalized video player.

## Documentation
This repository contains the entire documentation for the FOV project.