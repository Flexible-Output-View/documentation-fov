# Project FOV: Personalize Your Streaming Experience

Welcome to the central documentation for **FOV (Flexible Output View)**.

## What is FOV?
Have you ever watched a live stream where the music was too loud, the webcam was blocking the game, or the chat interface was in the way? Traditionally, viewers are stuck with whatever layout the streamer chooses.

**FOV** solves this by providing a "multi-track" streaming solution. Instead of receiving one flat video, viewers receive separate "layers" (like the game, the webcam, and individual audio tracks) that they can control themselves.

## Key Features for Viewers
Our platform aims to provide a viewing experience similar to popular sites like Twitch but with advanced interactive features:
*   **Custom Layouts:** Move, resize, or hide elements like the streamer's camera or the chat window.
*   **Audio Mixing:** Adjust the volume of the game, the music, and the streamer's voice independently.
*   **Personalized Settings:** Save your favorite layouts so you don't have to reconfigure them every time you watch.
*   **One-Click Reset:** Easily return to the "default" look designed by the streamer if you get lost.

## How It Works
Traditional streaming software "flattens" every element into a single image before sending it to you. FOV changes this process:

1.  **Broadcasting:** Using a custom version of OBS (Open Broadcaster Software), the streamer sends multiple isolated video and audio tracks simultaneously.
2.  **Delivery:** The FOV Backend manages these tracks and ensures they stay synchronized as they travel across the internet.
3.  **Viewing:** The FOV Web Platform receives these separate tracks and allows the viewer's browser to arrange them in real-time based on the viewer's preferences.

## The FOV Ecosystem
The project is built on three main pillars:
*   **Custom OBS Studio:** A modified version of the world's most popular streaming software that handles "Multi-Track" isolation.
*   **The Backend API:** The "brain" of the operation that manages users, categories, and the complex media pipelines required for multi-track delivery.
*   **The Web Platform:** An intuitive website where spectators discover content and interact with the personalized video player.


## Documentation
This repository contains the entire documentation for the FOV project.
If you want to contribute, please read the pinned Github discussion of the Github organization.

