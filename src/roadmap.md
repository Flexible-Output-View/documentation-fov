# Project Roadmap & Overview

> Updated: September 24th, 2026

## Introduction

Welcome to the public roadmap for the Flexible Output View (FOV) project. This document outlines our core objectives, ongoing development pillars, and high-level milestones as we elevate the platform to a production-grade, professional ecosystem.

Our current core features are fully operational, including starting and stopping streams, navigating the web interface to view streams, dynamically positioning and resizing video elements, and independently adjusting audio track volumes.

## 1. Development Pillars & Objectives

To achieve a professional standard of quality and performance, our development efforts are structured around several key pillars:

* **Stability & Performance:**
  * Minimize inter-track latency to ensure precise audio and video synchronization.
  * Resolve bugs and reinforce the overall stability of both the desktop application and the web platform.
  * Implement regular performance benchmarks.

* **Social Features & Authentication:**
  * Develop a comprehensive user authentication and account management system.
  * Build interactive and social features, including real-time chat and live statistics.

* **Ergonomics, Accessibility, and Mobile Support:**
  * Ensure full compliance with web accessibility standards (**WCAG**).
  * Optimize layouts for mobile devices, particularly for the multi-stream viewing experience.
  * Add user convenience features such as fullscreen mode and default stream layouts.
  * Refine the desktop client (OBS Studio fork) interface for seamless synergy with the web platform.

* **Documentation & Community:**
  * Provide extensive, comprehensive technical and user documentation.
  * Foster an open environment that encourages external community contributions and involvement.

* **Software Quality & Security:**
  * Expand automated testing suites (regression, validation, integration) and optimize the continuous integration (CI) pipeline.
  * Rigorously validate user inputs and maintain a strong focus on security.


## 2. Public Roadmap Timeline

### 🔴 High Priority — September to October 2026
* **API & Data:** Complete the replacement of all remaining mock data with the production API.
* **Player UX:** Automatically persist and restore user-selected stream layouts.
* **Player UX:** Optimize client performance to minimize stream load times.
* **Responsive Design:** Ensure global site responsiveness, focusing heavily on mobile views for the multi-stream player.
* **Documentation:** Clean up repository README files and establish a centralized, publicly accessible documentation hub.
* **Desktop Client (OBS):** Implement source selection capabilities to easily show or hide specific output streams.
* **R&D / Prototyping:** Explore and test cutting-edge technologies (such as Media Over QUIC) to address technical bottlenecks.
  * Open to contributions

### 🟡 Medium Priority — October to December 2026
* **Accounts & Sessions:** Implement user registration and login workflows.
* **Social Features:** Deploy a comprehensive follower system.
* **Navigation:** Dynamically link categories to active live streams.
* **Navigation:** Introduce user profile pages and dedicated user stream spaces.
* **Quality & Accessibility:** Finalize frontend accessibility compliance for primary user journeys.
* **Social Features:** Release real-time live chat functionality.
* **Security:** Vulnerability audit across our project.
  * Open to contributions (see [#63](https://github.com/Flexible-Output-View/web-fov/issues/63))

### 🟢 Low Priority — January / February 2027
* **Advanced Social Features:** Roll out advanced live statistics and deep social interactions.
* **Recommendation Engine:** Implement personalized content discovery algorithms ("You might also like...").
* **Industrialization:** Expand automated frontend/backend testing coverage and thoroughly document the codebase.
  * Open to contributions (see [#62](https://github.com/Flexible-Output-View/web-fov/issues/62) and [#61](https://github.com/Flexible-Output-View/web-fov/issues/61))
* **Milestone Review:** Conduct final performance benchmarks and polish all technical and user-facing documentation.
* **Language Localization:** Translate the website and FOV Software UI.
  * Open to contributions (see [#64](https://github.com/Flexible-Output-View/web-fov/issues/64) and [#28](https://github.com/Flexible-Output-View/obs-studio-fov/issues/28))
