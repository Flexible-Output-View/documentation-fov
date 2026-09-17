# User Guide


## Web Application

## Software

### End-User Quick Start & Setup Guide

#### Prerequisites
Ensure you have an operational target infrastructure set up via [web-fov](https://github.com/Flexible-Output-View/web-fov) or navigate directly to an open public node dashboard such as [https://fovapp.live](https://fovapp.live).

#### Configuring the Platform Service
1.  Launch the custom built **OBS Studio for FOV** application binary.
2.  Open the application configuration directory by choosing **Settings** from the main dashboard control interface.
3.  Select the **Stream / Service** navigation tab on the left margin.
4.  Set the primary service type flag to **FOV - Multitrack**.
5.  Populate your infrastructure target parameters:
    * **URL**: Specify your hosting target platform API endpoint (e.g., `https://api.fovapp.live`).
    * **Stream Key**: Insert your authentication token string (or assign your identification handle during active development intervals).
6.  Apply settings, return to the workspace console interface, and click **Start Streaming**.

#### Operational Behavior for Audio Tracks
Because track isolation is completely automated by `FOVSystem`:
* You do **not** need to manually configure tracks or checkboxes inside the **Advanced Audio Properties** window.
* Every audio source visible and active within your current scene automatically spawns its own independent stream track.

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
