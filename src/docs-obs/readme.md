# OBS Studio for FOV

Welcome to the documentation for the **OBS Studio for FOV** fork.

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

## Doxygen documentation
