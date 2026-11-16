# Supported Platforms — DMR Studio Livestream Recorder

[English README](../README.en.md) · [Quick start](quick-start.en.md) · [简体中文](platforms.md)

This document describes platform integration in **DMR Studio v1.0 (Build 18)**, a Windows **livestream recorder** with **automatic stream recording**, **danmaku recording**, and **danmaku rendering** features.

**Having an adapter does not mean every platform or live room has been verified.** Third-party APIs, login challenges, and networking conditions can change.

## Livestream and danmaku integrations

| Platform | Stream API adapter | Danmaku / live chat adapter | Notes |
|---|---|---|---|
| **Bilibili** | Present | Present | Confirm on a real accessible stream |
| **Douyin** | Present | Present | Platform validation may change |
| **Huya** | Present | Present | Confirm current room access |
| **Douyu** | Present | Present | Confirm current room access |
| **Twitch** | Present | Present | Regional networking and API restrictions may apply |
| **NetEase CC** | Legacy code present | Legacy code present | Application UI marks its live service as discontinued; do not consider it a currently functioning source |

Other URLs may use generic parsing or be marked **Unverified**; that does not imply universal site compatibility.

## Livestream recording vs video download vs video upload

- **Livestream recording:** Capture a live stream according to its state and the task settings.
- **Danmaku recording / rendering:** Save supported live chat and render it with a video according to your settings.
- **Video downloads:** Different from live recording. The task UI documents Bilibili video danmaku download; other video sources may have different limitations.
- **Uploads:** The task interface provides **Do not upload, Bilibili, and YouTube**. **YouTube uploading is not a claim of YouTube livestream recording support.**

For first-time setup, see [How to record a livestream on Windows](quick-start.en.md). Verify a local video file first, then enable chat recording, rendering or optional uploads. Report compatibility issues through [GitHub Issues](https://github.com/jiabenguiyin/DMRStudio/issues) without exposing login cookies or tokens.
