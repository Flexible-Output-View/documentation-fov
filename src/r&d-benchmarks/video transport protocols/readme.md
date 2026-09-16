# Benchmark: Video Stream Transport Protocols

> Date: December 27th, 2025

## Introduction
While recording video files remains relatively simple, sending real-time video streams must adhere to much stricter bandwidth and latency constraints than writing to local storage, while supporting a wide range of devices.

Today, there are numerous protocols for sending video streams, each with advantages and disadvantages that we will explore here according to our use case.

## Fragmentation vs. Streaming
To fully understand the comparison that follows, we must explain the difference between the concept of fragmentation and transport streaming.

### Fragmentation (Segmented HTTP Streaming)
The concept of fragmentation consists of dividing a video file into several segments that are transported separately by the transport protocol. The receiving player must handle receiving individual fragments and recombining them or playing them sequentially.

The most common example is the HLS protocol, in which a video is initially fragmented, and the fragments are then indexed in a video playlist file (.m3u8) to let the client know the URLs of the fragments it needs to download.

This method is very widespread on the web, as it is generally well supported by all browsers.

This method has the advantage of being simple to implement, but it offers much higher latency than streaming transport methods.

### Streaming (Packet-based Streaming)
Packet-based streaming methods rely on a simple principle: sending data in real-time to one or more targets.

This technique sends a continuous data stream without fragmentation, using a connection method and a network socket, while keeping an active session/connection. This greatly reduces latency.

Since this technique offers real-time latency, the quality of the stream heavily depends on the network quality between the sender and the receiver.

Sending real-time video streams generally requires the use of a video container suited for transport: for example, .ts (MPEG-TS Transport Stream) for TV and SRT, or sending raw packets through a socket...

This technique is widely used for video broadcasting (for example, live television or video conferencing).

This method is generally flexible regarding the desired network quality of service (reliable with TCP or unreliable with UDP) depending on the chosen protocol.

## Application Use Cases
In our project, we need to set up two different types of transmission.

### OBS to Backend Stream
This is the stream sent by OBS (the capture software) to our platform.

The stream is a 1-to-1 connection; the protocol must be reliable and capable of transporting multiple synchronized video tracks.

#### Selection Criteria (sorted by importance)
1. Support for multiple synchronized streams/tracks
2. Authentication and security
3. Reliability / Quality of service
4. Latency

### Backend to Browsers Stream
The video stream received by our platform must be broadcast to all users wishing to watch this live stream.

The stream will be 1-to-many, adapted for broadcasting to web browsers.

#### Selection Criteria (sorted by importance)
1. Supported by popular browsers
2. Reliability
3. Latency

## Comparative Table

| | RTMP | HLS | SRT | RIST | DASH | WebRTC |
|---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Type | Stream | Segments | Stream | Stream | Segments | Stream (Peer-To-Peer) |
| Low-level Protocol | TCP | HTTP/TCP | UDP | UDP | HTTP/TCP | SRTP/UDP |
| Latency | Low | High | Real-time | Real-time | Medium | Real-time |
| Reliability | Medium (TCP) | High (HTTP, cache, buffering) | High (ARQ) | Very High (ARQ + Bonding) | High (HTTP, cache, buffering) | Variable |
| Browser Support | ❌ | ✅ | ❌ | ❌ | ✅ | ✅ |
| OBS Support | ✅ | ✅ | ✅ | ✅ | ❌ | ✅ |
| Multi-video Track Compatible | ❌ | Non-native simultaneous playback | ✅ | ✅ | Non-native simultaneous playback | ✅ |
| Multi-video Track Synchronization | - | Workaround | Native | Native | Workaround | Native |
| Codec Restriction | H.264/AAC | Browser support | None | None | Browser support | VP8, VP9, H.264, Opus |
| Transport Container Format | FLV | MPEG-TS or FMP4 | MPEG-TS | MPEG-TS | M4S or WEBM | Raw RTP packets |
| Open Protocol | ❌ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Authentication | URL / Stream Key | Tokens / Cookies | Passphrase / StreamID | DTLS / Certificates | Tokens / Cookies | Certificate exchange |
| Security | RTMPS | HTTPS | AES | AES | HTTPS | Mandatory (DTLS/SRTP) |

## Conclusion
Given the project constraints and the established comparison table, we chose to use the SRT protocol to send a multi-track stream to our backend and then redistribute it to viewers using the HLS protocol.

The choice of HLS is due to browser limitations, as they support very few streaming protocols.
WebRTC, although a strong contender, would likely present scalability issues, while DASH is much less widespread.

## Consulted Resources
- https://getstream.io/blog/protocol-comparison/
- https://srtminiserver.com/tpost/g0p2vmk331-low-latency-amp-real-time-streaming-srt
- https://medium.com/@contact_45426/rist-vs-srt-a-comprehensive-comparison-53b20b22464b
- https://medium.com/@n20/hls-rtmp-dash-webrtc-and-more-a-simple-guide-to-streaming-protocols-98cbabcd599f
- https://static.vsf.tv/activity_groups/RIST_poster_for_VidTrans2018Feb25.pdf
- https://www.vmix.com/download/srt_alliance_deployment_guide.pdf
- https://medium.com/@psantana5_/guide-running-rtmp-hls-with-docker-and-ffmpeg-769c3f14462e
- https://ressources.camexia.org/diffusion-dun-flux-video-en-direct-sur-le-web/
