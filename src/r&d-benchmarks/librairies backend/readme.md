# Comparison of SRT / FFmpeg Approaches for Supporting Multiple Video Tracks

> Date: January 4th, 2026

We are considering several approaches to building a Node.js backend that supports multiple video tracks from live streams over SRT. Below is a comparison of the options and their capabilities.

## Comparison Table

| Option | What It Is | SRT Support | Media Capability (multiple video/audio tracks) | Level of Control | Ease of Use | Typical Use Case |
|--------|-------------|-------------|------------------------------------------------|------------------|-------------|------------------|
| `@eyevinn/srt` | Node.js bindings for the SRT protocol (transport only) | Yes (transport layer only) | No (does not demux or decode) | Low–Medium | Medium | We use this to receive raw SRT packets and pass them to a media processor |
| FFmpeg CLI (`child_process.spawn`) | Running the FFmpeg binary as an external process | Yes if FFmpeg is compiled with SRT support | Yes (full demux, decode, filters, remux, multiple tracks) | Medium | Easy–Medium | We use this to handle actual media processing including multiple video and audio tracks |
| Direct FFmpeg bindings (`node-av`) | Native bindings to FFmpeg’s C API via N-API | Yes (via FFmpeg libraries) | Yes (full mux/demux, decode, filters) | High | Hard | We use this when we want in-process access to FFmpeg features |
| Other FFmpeg binding libs (`avcpp` / node-ffmpeg) | Wrappers around FFmpeg C APIs with Node stream interfaces | Yes (via FFmpeg libraries) | Yes (full mux/demux, decode, filters) | High | Hard | We use this if we want a stream-like API with direct access to video/audio data |
| Old libav / legacy bindings | Older fork or outdated bindings | Limited / outdated | Partial | High | Hard | Generally not recommended due to lack of modern support |

## Detailed Explanation

### `@eyevinn/srt`

This option provides Node.js bindings for the **SRT transport protocol** itself. It handles accepting SRT connections and emitting raw packet data to JavaScript. It **does not include media parsing** (no demux, decode, or handling of video/audio tracks). To handle multiple video tracks we must combine it with a media processing library such as FFmpeg CLI or a binding that understands codecs and containers.

### FFmpeg CLI (`child_process.spawn`)

This approach runs the external **FFmpeg binary** as a subprocess from Node.js. When FFmpeg is compiled with SRT support it can **listen on an SRT endpoint**, demux the incoming container format, and handle all packed streams. FFmpeg can map all video and audio tracks from the input (for example using `-map 0:v` for all video tracks and `-map 0:a` for audio), then remux, transcode, or convert to other streaming formats as needed. FFmpeg’s CLI handles the full media stack including codecs, filters, and output formats, making it suitable for supporting multiple tracks out of the box.

### Direct FFmpeg Bindings (`node-av`)

Direct FFmpeg bindings like `node-av` provide **native access to FFmpeg’s C API** inside the Node.js process via N-API. This allows in-process control of demuxing, decoding, encoding, and track handling without launching a separate process. It supports all of FFmpeg’s media capabilities (including handling multiple tracks) and offers both low-level and higher-level abstractions. Because it operates inside the same process, we can build custom pipelines and handle frames or packets directly rather than through command-line arguments.

### Other FFmpeg Bindings (`avcpp` / node-ffmpeg)

Other bindings provide a C++ wrapper around FFmpeg’s native interfaces and expose them as Node.js stream APIs. These allow demuxing and processing of multiple tracks and can expose video and audio data as Node streams. They are similar in capability to `node-av` but differ in the API design (e.g., Readable/Writable interfaces).

### Old libav / Legacy Bindings

Older libav projects and outdated bindings are generally not well maintained and lack modern codec/protocol support, including up-to-date SRT and stream handling. Because of this, they are not recommended when building a backend that must support modern streaming workflows.

## Notes on Multiple Video Tracks

Supporting multiple video tracks is a function of the **media processing layer** rather than the transport layer. SRT itself only transports packets and does not interpret how many tracks are inside a container. Tools that demux and process the container format (FFmpeg CLI or direct bindings) are what enable handling multiple video tracks. Using FFmpeg’s stream mapping options you can include all video and audio streams present in the input.