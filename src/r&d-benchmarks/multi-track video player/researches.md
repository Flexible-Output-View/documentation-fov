# Research: Client-Side Synchronized Multi-Track Player

> Date: December 20th, 2025

## Introduction

As part of our project, we need to allow viewers to simultaneously view multiple video streams from the streamer (gameplay, webcam, etc.) while maintaining perfect synchronization between these streams.

This document details our research and implementation approach for a client-side multi-track player (web browser) capable of playing multiple HLS streams in a synchronized manner.

## Objectives

- Simultaneous playback of multiple video/audio streams
- Synchronization between tracks with a maximum deviation of 200ms
- Dynamic addition and removal of tracks during playback
- Interface allowing the viewer to customize the layout (drag, resize, z-order)
- Independent audio mixing per track

## Technical Stack Used

| Component | Technology |
|---|---|
| Frontend framework | Angular |
| HLS Player | hls.js |
| Language | TypeScript |
| Styling | SCSS |

## Benchmark: Web Player Technology Choice

| Evaluation Criterion | Native HTML5 (`<video>`) | dash.js (MPEG-DASH) | hls.js (Apple HLS) 🏆 |
| :--- | :--- | :--- | :--- |
| **Protocol Used** | HLS (if supported natively) | DASH | HLS |
| **Buffer Control (MSE)** | ❌ Very limited | ✅ Excellent | ✅ Excellent |
| **Maintaining Manual Sync** | ❌ Impossible with precision | ✅ Possible | ✅ Possible and documented |
| **Cross-browser Compatibility** | 🟠 Good (especially Safari/iOS) | 🟡 Depends on Media Source Extensions | 🟢 Excellent (native fallback on iOS) |
| **Integration Complexity** | 🟢 Very easy | 🟠 Complex | 🟡 Moderate |
| **Consistency with FOV Backend** | ❌ Lack of control | ❌ Requires changing FFmpeg output | 🟢 Perfect (Backend already in HLS) |

**Benchmark Conclusion:** 
The **Native HTML5** approach was quickly dismissed because it is very limited. Between **dash.js** and **hls.js**, our choice fell on **hls.js**. In addition to integrating perfectly with our FFmpeg pipeline (which generates HLS), its API offers total control over fragments (MSE), network error management, and latency, which is essential to keep our streams below the 200ms drift threshold.

## Initial Experiments

### Creating Multi-Track Test Files with FFmpeg

For our tests, we first created an MP4 file containing multiple video and audio tracks using FFmpeg:

    ffmpeg -i video1.mp4 -i video2.mp4 \
      -map 0:v -map 0:a -map 1:v -map 1:a \
      -c copy multipiste.mp4

### Verifying the Created File

    ffprobe -hide_banner -i multipiste.mp4

#### Result

    Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'multipiste.mp4':
      Duration: 00:10:11.20, bitrate: 908 kb/s
      Stream #0:0: Video: h264, 640x360, 25.74 fps
      Stream #0:1: Audio: aac, 44100 Hz, stereo
      Stream #0:2: Video: h264, 640x360, 23.98 fps
      Stream #0:3: Audio: aac, 44100 Hz, stereo

## Conversion to HLS for Streaming

### Converting Each Track into a Separate HLS Stream

    #!/bin/bash

    INPUT="$1"
    OUTPUT_DIR="./hls_out"

    # Extraction and conversion of each track
    ffmpeg -y -i "$INPUT" -map 0:v:0 -map 0:a:0 -c:v copy -c:a aac \
      -hls_time 2 -hls_list_size 0 -f hls "$OUTPUT_DIR/first.m3u8"

    ffmpeg -y -i "$INPUT" -map 0:v:1 -map 0:a:1 -c:v copy -c:a aac \
      -hls_time 2 -hls_list_size 0 -f hls "$OUTPUT_DIR/second.m3u8"

This method generates .m3u8 files and .ts segments for each track, allowing independent playback while preserving original timestamps.

## Player Architecture

### Data Structure

    interface Track {
      index: number;
      name: string;
      videoUrl: string;
      hasAudio: boolean;
    }

    interface VideoWrapper {
      playerId: string;
      track: Track;
      x: number;
      y: number;
      width: number;
      height: number;
      hls: Hls | null;
      videoElement: HTMLVideoElement | null;
      visible: boolean;
      zIndex: number;
      volume: number;
    }

### Synchronization Mechanism

The player uses a master track system that serves as a time reference for all other tracks:

    private syncAllToMaster() {
      const master = this.videoWrappers[0];
      if (!master?.videoElement) return;

      const masterTime = master.videoElement.currentTime;

      this.videoWrappers.forEach((w, i) => {
        if (i === 0 || !w.videoElement) return;

        const drift = w.videoElement.currentTime - masterTime;
        const absDrift = Math.abs(drift);

        // Record stats
        this.syncStats.set(w.track.name, drift * 1000);

        // Correction if drift > 150ms
        if (absDrift > 0.15) {
          w.videoElement.currentTime = masterTime;
        }
      });
    }

Synchronization monitoring is performed every 500ms:

    startSyncMonitoring() {
      this.syncInterval = setInterval(() => this.syncAllToMaster(), 500);
    }

## Performance Tests

### Test Conditions

| Parameter | Value |
|---|---|
| Number of video streams | 8 |
| Number of audio streams | 8 |
| Total simultaneous streams | 16 |
| Resolution per stream | 640x360 |
| Test duration | 10+ minutes |
| Simulated network conditions | Slow 4G (Chrome DevTools) |

### Results

| Metric | Result |
|---|---|
| Maximum observed drift | < 200ms ✅ |
| Average drift | ~50-80ms |
| Sync corrections required | Extremely rare |
| Lag or stuttering | None |
| Audio/video desynchronization | None |

#### Synchronization Monitoring Screenshot

    ┌─────────────────────────────────────┐
    │ Sync Monitor                        │
    ├─────────────────────────────────────┤
    │ first_1        +12ms                │
    │ second_2       -45ms                │
    │ gameplay_3     +23ms                │
    │ webcam_4       -18ms                │
    │ handcam_5      +67ms                │
    │ screen_6       -34ms                │
    │ overlay_7      +8ms                 │
    │                                     │
    │ Status : Synced (67ms max)          │
    └─────────────────────────────────────┘

(Example data)

### Testing Under Degraded Network Conditions

We used Chrome DevTools throttling tools to simulate different network conditions:

| Network Condition | Max Drift | Behavior |
|---|---|---|
| No limitation | < 50ms | Excellent |
| Fast 4G | < 100ms | Very Good |
| Slow 4G | < 200ms | Acceptable ✅ |
| Offline → Online | ~500ms then resync | Recovery OK |

## Implemented Features

### Dynamic Track Management

Tracks can be added or removed during playback without interrupting other streams:

    addTrack(templateName: string) {
      const track = this.availableTracks.find(t => t.name === templateName);
      if (!track) return;

      // Create wrapper with automatic positioning
      const newWrapper: VideoWrapper = {
        playerId: `player_${track.name}_${Date.now()}`,
        track,
        x: 20,
        y: 20 + (this.videoWrappers.length - 1) * 20,
        width: 300,
        height: 169,
        // ...
      };

      this.videoWrappers.push(newWrapper);
      this.initHlsForWrapper(newWrapper);
    }

    removeTrack(wrapper: VideoWrapper) {
      if (wrapper.hls) wrapper.hls.destroy();
      this.videoWrappers = this.videoWrappers.filter(w => w !== wrapper);
      
      // Resync if the master was deleted
      if (wasMaster) {
        this.syncStats.clear();
        this.syncAllToMaster();
      }
    }

### Customization Interface

The viewer can customize the layout in edit mode:

- **Drag & Drop:** Move streams within the viewing area
- **Resize:** Resize each stream (16:9 ratio preserved)
- **Z-Order:** Modify the stacking order of streams
- **Visibility:** Hide/show each stream
- **Reset:** Return to default layout

    @HostListener('window:pointermove', ['$event'])
    onPointerMove(event: PointerEvent) {
      if (this.activeDragWrapper) {
        // Movement constrained within the stage area
        let newX = Math.max(0, Math.min(newX, maxX));
        let newY = Math.max(0, Math.min(newY, maxY));
        this.activeDragWrapper.x = newX;
        this.activeDragWrapper.y = newY;
      } else if (this.activeResizeWrapper) {
        // Resizing with 16:9 ratio
        const newW = Math.max(150, this.initialW + dx);
        this.activeResizeWrapper.width = newW;
        this.activeResizeWrapper.height = newW / (16/9);
      }
    }

(Later, the 16/9 ratio will be replaced by the original stream ratio)

### Audio Mixing

Each track has its own volume control:

    setVolume(wrapper: VideoWrapper, event: Event) {
      const volume = parseFloat((event.target as HTMLInputElement).value);
      wrapper.volume = volume;
      if (wrapper.videoElement) {
        wrapper.videoElement.volume = volume;
        wrapper.videoElement.muted = (volume === 0);
      }
    }

## Encountered Issues and Solutions

### Issue 1: Dynamic Creation of DOM Elements

**Problem:** Video elements created dynamically with `document.createElement` did not work correctly with the resizing system.

**Solution:** Using Angular bindings with `*ngFor` on a track array, and managing positions/dimensions via bound properties:

    <div *ngFor="let wrapper of videoWrappers"
         [style.left.px]="wrapper.x"
         [style.top.px]="wrapper.y"
         [style.width.px]="wrapper.width"
         [style.height.px]="wrapper.height">
      <video [id]="'videoElement_' + wrapper.track.index"></video>
    </div>

### Issue 2: Initial Size of the Main Stream

**Problem:** Using `width: 100%` caused position calculation issues during dragging.

**Solution:** Calculating the size in pixels at creation time:

    const stage = document.getElementById('stageArea');
    const stageW = stage ? stage.offsetWidth : 800;
    const stageH = stage ? stage.offsetHeight : 450;

    wrapper.width = isFirst ? stageW : 300;
    wrapper.height = isFirst ? stageH : 169;

### Issue 3: Synchronization After Master Deletion

**Problem:** After deleting the master track, other tracks kept their relative drift to the old master.

**Solution:** Resetting stats and immediate resync when changing master:

    if (wasMaster) {
      this.syncStats.clear();
      this.maxDrift = 0;
      this.syncAllToMaster();
    }

## Conclusion

Our implementation demonstrates that it is possible to simultaneously play 8 video streams + 8 audio streams (16 total streams) in a web browser with synchronization maintained below 200ms, even under degraded network conditions (slow 4G).

Key success factors:

- **hls.js:** Robust library for HLS playback in the browser
- **Sync polling:** Verification and correction every 500ms
- **Tolerance threshold:** Correction only if drift > 150ms (avoids unnecessary corrections)
- **Single master:** A single reference track for synchronization

## Next Steps

- Integration with the backend to receive HLS URLs dynamically
- Saving custom layouts per user
- Fullscreen mode
- Reconnection management in case of network interruption

## Resources

- hls.js - JavaScript HLS client
- Angular Documentation
- FFmpeg HLS Muxer
- MDN - HTMLMediaElement
