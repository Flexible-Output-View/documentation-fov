# Start a stream

Welcome! This guide will walk you through setting up and broadcasting your first stream using FOV.

## Prerequisites

Before you begin, ensure you have the FOV software [installed and running](./installation.md).

## Technical Considerations & Operational Constraints

Using FOV introduces a fundamentally different architectural model compared to standard OBS streaming by routing each source as an independent track. Because multi-track encoding imposes strict resource, structural, and network demands, keep the following guidelines in mind before going live:

> [!IMPORTANT]
> **Bandwidth Multiplication, Encoders & Codecs**
> * **Bandwidth Scaling:** Each source is transmitted as an independent video or audio track, resulting in significantly higher bandwidth demands than a regular OBS setup. Your target bitrate configuration applies to **every individual track**. If your output bitrate is set to **2500 kbps** and your scene contains 4 video layers, your total bandwidth allocation scales linearly up to **10,000 kbps** upload overhead (excluding audio). Plan your network capacity accordingly.
>
> * **CPU Load & Hardware Encoding:** Your encoder settings apply universally to *every* track. **Hardware-accelerated encoding (NVENC, AMF, or QuickSync) is strongly recommended** to handle the multi-track workload efficiently. While software-based encoders (`x264`) can be used, running multiple concurrent streams places a heavy burden on your processor and may lead to high CPU usage or frame drops.
>
> **Supported Codecs**
>  * **Video:** H.264 (`h264`), H.265 (`h265`)
>  * **Audio:** AAC (`aac`), Opus (`opus`)

> [!WARNING]
> **Single-Scene & Layout Lock**
> * FOV operates strictly within a single-scene setup. **Scene switching, as well as adding, removing, or configuring sources, is disabled while streaming.** Finalize all scene structures completely before starting your broadcast.

> [!NOTE]
> **Automated Track Isolation & Audio Rules**
> * **Automated Tracking:** Track isolation is fully automated by `FOVSystem`. You do **not** need to manually configure tracks or checkboxes inside the **Advanced Audio Properties** window; any source currently visible and active in your active scene automatically spawns its own independent stream track.
>
> * **Fixed Audio Mapping:** Manual adjustments inside the **Advanced Audio Properties** panel are automatically overwritten or ignored. Audio sources map on a strict 1:1 basis based on their order of addition.


## Quickstart

Follow these steps to configure your broadcast:

### 1. Configure the Platform Service

1. Launch the application binary.
2. Open **Settings** from the main dashboard control interface and select the **Stream** tab on the left margin.
3. Set the service type dropdown to `FOV - Multitrack`.

![alt text](image.png)

### 2. Enter Infrastructure Target Parameters

Populate your connection details:
* **Server URL:** Specify your target platform API endpoint. Use `https://api.fovapp.live` for the public service, or enter your custom backend API URL if you are self-hosting (see [the developer documentation](../developer_docs.md)).
* **Stream Key:** Insert your authentication token string.

![alt text](image-1.png)

### 3. Configure Your Scene

Add multiple audio and video sources to your current single active scene just like you would in regular OBS.

![alt text](image-2.png)

### 4. Go Live and Watch the Stream

1. Click **Start Streaming** in the workspace console interface.
2. Depending on your configuration, your interactive multi-track stream will be available on your self-hosted instance or on [https://fovapp.live](https://fovapp.live).

![alt text](image-3.png)

You can now watch your stream and enjoy the unique functionalities provided by FOV, such as resizing and moving video tracks around dynamically.

![alt text](image-5.png)