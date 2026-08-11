# OmniRadio

**One device. Many ways to listen, communicate, and explore radio.**

OmniRadio is a **multi-source, multi-target radio and network-audio platform** built around the ESP32-S3. It brings broadcast reception, internet audio, podcasts, audiobooks, local files, microphone input, and voice communication into one physical device with one consistent interface, while keeping audio sources separate from their destinations.

The project is under active development. Public documentation is available, but public firmware downloads and installation images are not yet published.

[Read the OmniRadio documentation](https://uandy24.github.io/omniradio-public/)

## Available documentation

These sections are already published and checked against the current firmware implementation:

- **[Getting Started](https://uandy24.github.io/omniradio-public/start/what-is-omniradio/)** — requirements, preparation, first boot, connected-hardware configuration, Wi-Fi, portal access, and physical navigation
- **[Project overview](https://uandy24.github.io/omniradio-public/project/goals/)** — goals and non-goals, philosophy, differences, architecture, and current status
- **[Core concepts](https://uandy24.github.io/omniradio-public/concepts/terminology/)** — terminology, modes, pages, sources, targets, Canvas/UI, and the embedded portal
- **[FM Radio](https://uandy24.github.io/omniradio-public/modes/fm/)** — SI4732 requirements, all FM pages, regional ranges, tuning, scanning, presets, signal quality, audio processing, RDS, and persistent settings
- **[AM Radio](https://uandy24.github.io/omniradio-public/modes/am/)** — LW, MW, and SW entries, all AM pages, EiBi schedules, antenna controls, receiver settings, audio processing, and persistence
- **[SSB Radio](https://uandy24.github.io/omniradio-public/modes/ssb/)** — amateur-band entries, SI4732 patch lifecycle, LSB/USB/CW, BFO, receiver bandwidth, scanning, presets, and analog audio requirements
- **[Internet Radio](https://uandy24.github.io/omniradio-public/modes/internet-radio/)** — Icecast-compatible streams, [Radio Browser](https://www.radio-browser.info/) discovery, favorites, ICY metadata, formats, TLS, and reconnect behavior
- **[Podcasts](https://uandy24.github.io/omniradio-public/modes/podcasts/)** — Apple catalog discovery, publisher RSS feeds, subscriptions, bookmarks, and resumable episodes
- **[Audiobooks](https://uandy24.github.io/omniradio-public/modes/audiobooks/)** — [LibriVox](https://librivox.org/) catalog browsing, chapters, bookmarks, playback position, and persistence
- **[Files](https://uandy24.github.io/omniradio-public/modes/files/)** — SD-card audio, folders, queues, playlists, recordings, metadata, and USB storage access
- **[Microphone](https://uandy24.github.io/omniradio-public/modes/microphone/)** — live I²S input, level and clipping indication, safe monitoring, target routing, and voice recording
- **[File recording](https://uandy24.github.io/omniradio-public/features/file-recording/)** — cross-mode capture controls, supported sources, MP3 and ID3 rules, SD-card lifecycle, and error handling

Public firmware downloads, flashing, upgrades, and recovery remain intentionally hidden until the ESP32-S3 N8R8 release profile is validated.

## The main idea

OmniRadio is not a collection of separate ESP32 demos placed behind one menu. Every capability participates in the same product architecture:

- A **mode** represents the current listening, communication, or configuration context.
- A **page** presents one focused view of a mode on the physical display.
- A **source** produces audio or live media state.
- A **target** determines where active audio is delivered.
- The **canvas** and reusable UI elements render the device interface.
- The embedded **portal** handles configuration and management through a browser.

This separation lets the firmware grow without giving every feature its own unrelated navigation, audio output, persistence, and configuration system.

“Multi-source, multi-target” describes the architecture and available routing choices. It does not claim that every source can play simultaneously to every target; compatibility depends on codecs, transports, hardware, and the active mode.

## Capabilities

OmniRadio is designed to combine:

- FM broadcast reception with tuning, scanning, presets, signal information, and RDS
- AM and shortwave reception with presets, scanning, signal information, and EiBi data
- SSB reception with USB/LSB, BFO, tuning step, and bandwidth control
- Internet radio, including direct HTTP and HTTPS streams
- Podcast search, feeds, favorites, bookmarks, and playback
- LibriVox audiobook discovery, chapters, bookmarks, and playback position
- Local audio files, folders, playlists, metadata, and USB storage
- Microphone input, level and clipping monitoring, routing to compatible targets, and voice recording
- Walkie-Talkie communication through Mumble, M17, Zello, and SIP2SIP
- Local speakers and network-oriented audio destinations
- A multilingual physical UI and embedded configuration portal

Actual capabilities depend on the hardware connected and enabled in the device configuration.

## Modes

| Mode | Purpose | Main requirements |
| --- | --- | --- |
| FM Radio | FM reception, RDS, presets, scanning, and signal analysis | SI4732 receiver and radio front end |
| AM Radio | AM and shortwave reception, presets, scanning, and EiBi browsing | SI4732 receiver and radio front end |
| SSB Radio | USB/LSB reception with BFO and bandwidth control | SI4732 with SSB support |
| Internet Radio | Direct network streams, browsing, metadata, and favorites | Wi-Fi and audio output |
| Podcasts | Search, feeds, episodes, bookmarks, and playback | Wi-Fi and audio output |
| Audiobooks | LibriVox discovery, sections, bookmarks, and playback | Wi-Fi and audio output |
| Files | Local files, folders, playlists, and removable storage | Supported storage and audio output |
| Microphone | Audio input, level monitoring, and routing | Microphone and compatible audio input hardware |
| Walkie-Talkie | Mumble, M17, Zello, and SIP2SIP communication | Wi-Fi and compatible audio input/output |
| Settings | Device, Wi-Fi, portal, region, system, reset, and version control | Physical display and input |

Mumble, M17, Zello, and SIP2SIP are protocol implementations inside the Walkie-Talkie mode. They are documented as part of that mode rather than as top-level product concepts.

## Audio targets

Audio production and audio output are deliberately separate. The current target implementations are:

| Target | Description |
| --- | --- |
| Local Audio | Sends PCM audio to the configured local I2S path, codec, amplifiers, and speakers |
| DLNA | Discovers a renderer and sends the active source through a device-hosted network stream |
| HTTP Stream | Exposes active audio as a stream to compatible clients on the local network |
| FM Audio | Sends digital audio to an SI4713 FM transmitter, with optional local speaker behavior |

Target availability depends on connected hardware, Wi-Fi state, and the active source.

## Two device interfaces

The physical display and controls are the primary everyday interface. They are intended for direct operation without requiring a phone or browser.

The embedded portal is served locally by the device. It handles operations that benefit from a larger screen and keyboard:

- Wi-Fi setup
- Connected-hardware configuration
- Mode-specific profiles
- Configuration files
- Language files
- Playlists and bookmarks
- User storage
- System status and power operations

This public website is separate from the embedded portal. It documents the product but does not control a device.

## Hardware-aware design

The first public hardware target is planned around an **ESP32-S3 N8R8**: 8 MB flash and 8 MB octal PSRAM.

Depending on the assembly, OmniRadio can use:

- ST7789 display
- Rotary encoder and buttons
- SI4732 broadcast receiver
- TLV320ADC3101 and other configured audio-path components
- I2S audio output
- Left and right speaker amplifiers
- Microphone input
- SI4713 FM transmitter
- Battery monitoring
- USB or removable file storage

Hardware is treated as configuration. Features should become available only when their required devices and buses are configured successfully.

## How OmniRadio is different

- It is organized as one product rather than several unrelated applications.
- Physical controls, navigation, notifications, persistence, and audio routing are shared.
- Sources do not hard-code their output destinations.
- Hardware variation is expected instead of hidden in one fixed schematic.
- The physical UI remains central; the portal complements it.
- Public HTTPS uses the maintained ESP-IDF CA bundle and hostname validation rather than service-specific certificate pinning.
- Private device configuration stays on the device.
- Firmware source and development artifacts remain separate from the public documentation repository.

## Configuration and storage

OmniRadio uses several persistence layers for different responsibilities:

- **NVS** for compact settings, protected values, and device-generated local TLS material
- **LittleFS** for structured configuration, profiles, bookmarks, playlists, and language resources
- **External storage** for user media and recordings where supported
- **Firmware defaults** for first boot and recovery

The embedded portal exposes only supported configuration paths and formats instead of unrestricted access to the internal filesystem.

## Security model

- The device generates and stores its own HTTPS certificate for the local portal.
- Outgoing public HTTPS connections use the standard ESP-IDF public CA bundle.
- Hostname verification remains enabled for secure connections.
- Private Mumble servers can use a user-uploaded CA file stored in LittleFS.
- Leaf and intermediate certificates for individual internet services are not pinned in firmware.
- Saved secret fields are redacted in portal responses and protected in persistent configuration where implemented.

## Repository layout

This public repository intentionally contains only:

- The generated documentation site in the `gh-pages` branch
- This project description in the `main` branch

Firmware source is maintained in a separate private repository. Public firmware distribution will be enabled only after the supported hardware profile and installation process are validated.

## Documentation status

The documentation is being expanded directly from the firmware implementation.

Completed:

1. [Getting Started before public firmware](https://uandy24.github.io/omniradio-public/start/what-is-omniradio/)
2. [Overview and terminology](https://uandy24.github.io/omniradio-public/concepts/terminology/)
3. [FM Radio](https://uandy24.github.io/omniradio-public/modes/fm/)
4. [AM Radio](https://uandy24.github.io/omniradio-public/modes/am/)
5. [SSB Radio](https://uandy24.github.io/omniradio-public/modes/ssb/)
6. [Internet Radio](https://uandy24.github.io/omniradio-public/modes/internet-radio/)
7. [Podcasts](https://uandy24.github.io/omniradio-public/modes/podcasts/)
8. [Audiobooks](https://uandy24.github.io/omniradio-public/modes/audiobooks/)
9. [Files](https://uandy24.github.io/omniradio-public/modes/files/)
10. [Microphone](https://uandy24.github.io/omniradio-public/modes/microphone/)
11. [File recording](https://uandy24.github.io/omniradio-public/features/file-recording/)

Next planned sections:

1. Walkie-Talkie and its protocol implementations
2. Settings and audio targets
3. Physical UI, Canvas, and portal
4. Battery power, battery-presence detection, charging, low-battery behavior, and energy management
5. Connected hardware and complete configuration reference
6. Wi-Fi, TLS, storage, and troubleshooting
7. Installation and firmware distribution after N8R8 validation

See the [public documentation](https://uandy24.github.io/omniradio-public/) for the material available now.
