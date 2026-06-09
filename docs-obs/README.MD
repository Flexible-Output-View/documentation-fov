# OBS Studio for FOV

Welcome to the documentation for the **OBS Studio for FOV** fork. This document details how the custom multi-track ecosystem operates under the hood, how the media pipelines are organized, and how to configure the platform for active streaming.

## High-Level Architecture Overview

Traditional OBS Studio collapses all active layers, captures, and audio inputs down into a single flattened canvas and one composite audio master track. The FOV variant alters this behavior by maintaining complete structural isolation of visual and auditory inputs from capture all the way to presentation in the Web GUI.

* **Video Capture Track Isolation:** Every distinct active video source inside the current OBS scene is dynamically allocated its own private frame view context and an isolated hardware encoder instance.
* **Audio Source Tracking (1:1 Isolation):** Unlike standard OBS which mixes multiple inputs together onto shared master tracks, `FOVSystem` intercepts audio at the source layer. Each active audio source is automatically hijacked and assigned its own exclusive `mixer_id` track mask. This prevents audio bleed and locks out human error by disabling custom user mixer modifications in the OBS UI.
* **Unified Container Transport:** All parallel encoded video and audio streams are packaged sequentially into a singular MPEG-TS (MPEG Transport Stream) before being pushed over the network.

![OBS FOV Architecture](./OBS-FOV-Arch.jpg)

## Technical Pipeline Breakdown

### Video Architecture
When a video source transitions to an active state, `FOVSystem` provisions a dedicated `VideoTrack` node:
1.  **View Context Allocation:** An internal `obs_view_t` is mapped to the source, ensuring raw frame allocation occurs independently of the primary OBS program canvas.
2.  **Resolution Alignment:** Frame dimensions are processed through an aspect alignment macro: `OUT_ALIGN(dimension, 16)`. This guarantees compatibility with rigorous hardware macroblock requirements.
3.  **Muxer Binding:** Encoders are appended to an `obs_encoder_group_t` tracking node and indexed continuously using `obs_output_set_video_encoder2`.

### Audio Architecture
To ensure zero configuration effort and bulletproof stream isolation, audio tracks bypass global mixer matrices entirely:
1.  **Automated Mixer Hijacking:** When an audio source is registered, `FOVSystem` overrides its bitmask using `obs_source_set_audio_mixers(source, 1 << mixer_id)`. This forces the source onto a single exclusive pipeline and clears it from all other tracks.
2.  **Hardware UI Enforcement:** By programmatically assigning and managing these bits at runtime, the platform ignores any changes made in the OBS "Advanced Audio Properties" layout, preserving track isolation.
3.  **Index Sequencing:** Within the MPEG-TS multiplexer loop, audio track indexing begins exactly where video track array iteration terminates, ensuring flawless track allocation inside the transport stream:

$$\text{Transport Stream Track ID} = \text{Video Track Count} + \text{Source Track Index}$$


## Core Class Reference: `FOVSystem`

The engine relies on `FOVSystem` to arbitrate scene modifications, monitor input states, and reconstruct the active encoder layout on the fly.

- `void initSystem(obs_output_t *muxerOutput, obs_data_t *vSettings, obs_data_t *aSettings)`
Configures and initializes the system state. Binds the structural output pipeline to the `ffmpeg-mpegts` muxer reference and locks in current initialization profiles.

- `void syncSources()`
The runtime management loop. Executed automatically to track context mutations:
* Enumerates all global sources utilizing `obs_enum_sources`.
* Checks filtering flags (`OBS_SOURCE_VIDEO` and `OBS_SOURCE_AUDIO`).
* Automatically registers active audio sources, binds them programmatically to a unique available `mixer_id`, and provisions an encoder for that isolated slot.
* Discards elements that have transitioned to inactive statuses, releases stale encoders, and appends new tracks to the multiplex group.

- `void updateEncoderGroup()`
Performs critical cleanup of the output container mappings. It detaches all existing audio and video paths from the active muxer reference, registers the modified tracks under a clean unified `obs_encoder_group_t`, and remaps container tracks dynamically to clear stream PIDs.


## Operational Guardrails & Constraints

Because multi-track encoding introduces significant resource and architectural constraints compared to basic vanilla streaming, keep the following rules in mind:

> [!WARNING]
> Hardware Resource Constraints
> * **Encoder Thresholds:** Every independent track requires its own active encoder pipeline. **Always utilize hardware encoding primitives (NVENC, AMF, or QuickSync)**. Attempting to run multiple concurrent streams on software engines (such as standard `x264`) will cause instantaneous CPU exhaustion and massive frame drops.
> * **Bitrate Apportionment:** Your target streaming bitrate configuration is applied universally to **each individual track**. If your output bitrate is set to $2500\text{ kbps}$ and your scene contains 4 video layers, your total bandwidth allocation scales linearly up to $10000\text{ kbps}$ upload overhead (excluding audio tracks). Adjust configurations to accommodate network constraints.

> [!NOTE]
> Scene Management Limitations
> * **Topology Locks:** The dynamic multi-track multiplexer layout is locked the moment transmission begins. **Adding new audio inputs or modifying video sources while a stream is actively live is strictly blocked.** Build out all scene structures completely before initiating your broadcast.
> * **Fixed Mixers:** User modifications inside the OBS Advanced Audio Properties grid will be overwritten or ignored. Audio sources are mapped on a strict 1:1 basis automatically based on their addition sequence.

## End-User Quick Start & Setup Guide

### Prerequisites
Ensure you have an operational target infrastructure set up via [web-fov](https://github.com/Flexible-Output-View/web-fov) or navigate directly to an open public node dashboard such as [https://fovapp.live](https://fovapp.live).

### Configuring the Platform Service
1.  Launch the custom built **OBS Studio for FOV** application binary.
2.  Open the application configuration directory by choosing **Settings** from the main dashboard control interface.
3.  Select the **Stream / Service** navigation tab on the left margin.
4.  Set the primary service type flag to **FOV - Multitrack**.
5.  Populate your infrastructure target parameters:
    * **URL**: Specify your hosting target platform API endpoint (e.g., `https://api.fovapp.live`).
    * **Stream Key**: Insert your authentication token string (or assign your identification handle during active development intervals).
6.  Apply settings, return to the workspace console interface, and click **Start Streaming**.

### Operational Behavior for Audio Tracks
Because track isolation is completely automated by `FOVSystem`:
* You do **not** need to manually configure tracks or checkboxes inside the **Advanced Audio Properties** window.
* Every audio source visible and active within your current scene automatically spawns its own independent stream track.
