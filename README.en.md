# DMR Studio — Windows Livestream Recorder & Danmaku Recording Tool

<p align="center"><strong>Language:</strong> <a href="README.md">简体中文</a> · <a href="README.en.md">English</a></p>

<p align="center">
  <img src="branding/LOGO.png" alt="DMR Studio — Windows livestream recording and danmaku management tool" width="360">
</p>

<p align="center">
  <strong>DMR Studio</strong> is a Windows desktop application for <strong>automatic livestream recording</strong>, danmaku (live chat) capture and rendering, video processing, and recording task management.<br>
  A graphical interface helps users manage live recording without complex command-line setup.
</p>

<p align="center">
  <img alt="Windows" src="https://img.shields.io/badge/Platform-Windows-0078D6?logo=windows&logoColor=white">
  <img alt="Desktop App" src="https://img.shields.io/badge/Edition-Desktop%20App-111111">
  <img alt="Windows Installer" src="https://img.shields.io/badge/Delivery-Installer-0EA5E9">
  <img alt="Maintained" src="https://img.shields.io/badge/Maintenance-Long--Term-16A34A">
</p>

<p align="center">
  <a href="https://github.com/jiabenguiyin/DMRStudio/releases">Download for Windows</a> ·
  <a href="https://github.com/jiabenguiyin/DMRStudio/releases">Release notes</a> ·
  <a href="https://github.com/jiabenguiyin/DMRStudio/issues">Report an issue</a>
</p>

## What is DMR Studio?

If you're searching for a **livestream recorder**, **automatic live stream recording software**, **stream recording tool**, or **danmaku recorder** for Windows, DMR Studio brings recording and day-to-day management into one desktop interface.

It is designed to record live streams, manage recording jobs, save and render associated danmaku, and handle optional video uploads. Most recording and processing activities run locally on the user's computer.

**Languages:** Simplified Chinese and English. Switch between them in **System Settings → Interface Language**. You do not need a separate English installer.

## Product Demo Video

**DMR Studio 1.0 — 32-second introduction.** See the Windows livestream recording workflow, automatic recording when a stream goes live, danmaku rendering, and optional video uploads.

<p align="center">
  <a href="https://www.bilibili.com/video/BV13rWC6qEgg" title="Watch the full DMR Studio demo on Bilibili">
    <img src="docs/media/DMRStudio-v1.0-promo-preview.webp" alt="DMR Studio product demo: silent 15-second animated preview; click for the full video" width="800">
  </a>
</p>

<p align="center"><strong>▶ <a href="https://www.bilibili.com/video/BV13rWC6qEgg">Watch the full video on Bilibili (with sound)</a></strong> · <a href="https://raw.githubusercontent.com/jiabenguiyin/DMRStudio/main/docs/media/DMRStudio-v1.0-promo.mp4">Original MP4</a> · <a href="https://github.com/jiabenguiyin/DMRStudio/releases">Download for Windows</a></p>

*The preview above is a silent 15-second loop. The full promotional video contains Simplified Chinese text. The application itself supports an English interface, shown in the screenshots below. Platform availability and individual features depend on the current version and configuration.*

## Screenshots

The following images were captured from **DMR Studio v1.0 running on Windows**. These are actual application screenshots, not mockups. No live-recording tasks or upload accounts were configured in the test environment, so the queues are empty.

<p align="center">
  <a href="docs/screenshots/en/01-home.jpg"><img src="docs/screenshots/en/01-home.jpg" alt="DMR Studio dashboard for Windows livestream recording" width="860"></a>
</p>
<p align="center"><sub>Dashboard — engine controls and recording status</sub></p>

| Livestream task management | Render center |
|:---:|:---:|
| <a href="docs/screenshots/en/02-recording-tasks.jpg"><img src="docs/screenshots/en/02-recording-tasks.jpg" alt="Stream recorder task manager and live monitoring" width="420"></a> | <a href="docs/screenshots/en/03-render-center.jpg"><img src="docs/screenshots/en/03-render-center.jpg" alt="Danmaku rendering queue and upload status" width="420"></a> |
| **Recording tasks** — live monitoring and job status | **Render center** — queues, errors and upload history |
| <a href="docs/screenshots/en/04-danmaku-settings.jpg"><img src="docs/screenshots/en/04-danmaku-settings.jpg" alt="Danmaku rendering layout opacity and font size controls" width="420"></a> | <a href="docs/screenshots/en/05-system-settings.jpg"><img src="docs/screenshots/en/05-system-settings.jpg" alt="DMR Studio desktop app system and language settings" width="420"></a> |
| **Danmaku settings** — font size, opacity and display layout | **System settings** — language, theme and encoder |

*All screenshots above show the actual English-language interface of DMR Studio v1.0, captured on Windows with a fresh, empty test profile.*

## Features

- **Automatic livestream recording:** Organize and monitor recording tasks based on stream and task status.
- **Segmented recordings:** Split long live recording sessions into manageable files.
- **Danmaku capture and rendering:** Save supported live-chat data and render it with video using configured settings.
- **Recording task management:** Create, enable, pause and adjust jobs; inspect activity and status.
- **Rendering and optional uploads:** Process recordings and upload videos through configured platform accounts when enabled.
- **Windows desktop workflow:** Graphical settings, system tray access, Windows installer and upgrade support.
- **Simplified Chinese / English interface:** Choose the interface language without installing a different edition.

Available integrations, recording methods and individual features may depend on your version, stream platform and configuration.

## Supported Platforms & Guides

DMR Studio includes livestream and danmaku integration code for **Bilibili, Douyin, Huya, Douyu, and Twitch**. It is a Windows **live stream recorder** with **automatic livestream recording**, recording task management and optional danmaku rendering. Actual compatibility depends on third-party stream availability, current APIs, and the network; the presence of an adapter is not a guarantee.

- **[How to record a livestream on Windows — Quick Start](docs/quick-start.en.md)**: Download, create a live recording task, start the engine, and confirm video output.
- **[Supported platforms and compatibility](docs/platforms.en.md)**: What Bilibili, Douyin, Huya, Douyu and Twitch adapters do, and how recording differs from video downloads and optional uploads.
- Also available: [中文录播教程](docs/quick-start.md) · [中文平台说明](docs/platforms.md).

## Advanced YAML Configuration & DanmakuRender Compatibility

For recording settings, danmaku styling, FFmpeg rendering, and optional publishing, read the **[DMR Studio YAML Configuration Guide](docs/yaml-configuration.en.md)**. It explains compatibility differences from upstream DanmakuRender, configuration precedence, import limitations, and includes four safe, editable YAML samples. [中文文档](docs/yaml-configuration.md).

## Getting Started

1. Download the latest Windows installer from [GitHub Releases](https://github.com/jiabenguiyin/DMRStudio/releases).
2. Install and launch DMR Studio from the desktop, Start menu or system tray.
3. To use English, open **System Settings → Interface Language → English**.
4. Create a livestream recording task and adjust monitoring, segmented recording, danmaku, rendering and upload options as needed.
5. Check recording status in the task manager and manage output files on your PC.

If Windows SmartScreen warns about an unknown publisher, confirm the installer was downloaded from this repository's official release page. The installer may not be digitally signed.

## Frequently Asked Questions

### Is this a screen recorder or a livestream recorder?

DMR Studio focuses on livestream data recording and task management rather than only capturing pixels on your desktop. It is intended for users who want to monitor live streams and save recordings automatically.

### Does it automatically create highlight clips?

Not as a confirmed feature of this release. Segmented recording and danmaku rendering can help prepare material for **livestream clips and highlight editing**, but splitting a recording is not the same as automatically identifying highlights.

### Do I need command-line experience?

The goal is to keep common recording workflows accessible through a graphical Windows application.

### Do recordings have to be stored in the cloud?

Recording and processing primarily take place on your local computer. Uploading videos is optional and depends on task configuration.

## Releases and Feedback

The current public product version is **v1.0**. See [Releases](https://github.com/jiabenguiyin/DMRStudio/releases) for the available installer and latest release details.

For installation issues, recording failures, danmaku problems or application errors, please open an [Issue](https://github.com/jiabenguiyin/DMRStudio/issues). Include reproduction steps, software version and relevant screenshots if possible. **Never share login cookies, access tokens or account credentials publicly.**

This repository publishes product information and releases. The application source code is maintained separately.
