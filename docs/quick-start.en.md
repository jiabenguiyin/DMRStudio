# DMR Studio Quick Start — Automatic Livestream Recording on Windows

[Project home](../README.en.md) · [Platform compatibility](platforms.en.md) · [简体中文](quick-start.md)

For **DMR Studio v1.0 (Build 18)** on Windows. Both **English** and Simplified Chinese are supported. Follow the labels in your installed version if the interface changes.

## 1. Install and launch

1. Download the Windows installer from the official [GitHub Releases](https://github.com/jiabenguiyin/DMRStudio/releases).
2. Install DMR Studio, launch it, and use **Quick Start** for the introductory walkthrough.
3. The **Home** page shows the runtime console, recording engine, and task status.
4. Choose **System Settings → Interface Language → English**, then **Save Settings** if you need to change the language.

If SmartScreen displays an unknown-publisher warning, verify the release source before running the file.

## 2. Create a livestream recording task

1. Open **Recording Tasks → New Task** (or **Create Task**).
2. Fill in **Task Name** and paste a **Stream URL**.
3. Example livestream URL formats (illustrative IDs only, **not verified active streams**):
   - Bilibili: `https://live.bilibili.com/123456`
   - Douyin: `https://live.douyin.com/123456`
   - Huya: `https://www.huya.com/123456`
   - Douyu: `https://www.douyu.com/123456`
4. Set an **Output Directory** with enough free disk space and write permission.
5. Enable **Record video**. Enable **Record danmaku** only if live-chat capture is needed and available.
6. For your first test, keep **Render danmaku version after recording** off and choose **Upload to → Do not upload**. Add rendering or upload after basic recording succeeds.
7. Save the task.

Platform-specific stream and danmaku adapters are present for Bilibili, Douyin, Huya, Douyu, and others. That does **not** guarantee continuous compatibility with every stream. See [supported platforms](platforms.en.md).

## 3. Start the engine and confirm output

1. On **Home** or **Recording Tasks**, click **Start Engine**.
2. Check whether the task is enabled and the streamer is live. Confirm the recording state.
3. If the stream is offline, the task may wait for a later monitoring cycle.
4. When a stream is live, inspect the output directory for an **actual video file**. Check logs for any errors.
5. For long recordings, use **Advanced Settings → Segment length (seconds)**. Segmented recordings are not automatically identified highlight clips.

## 4. Danmaku rendering and uploads

- **Record danmaku** captures chat only when supported by the platform interface.
- **Danmaku Rendering** provides controls for font size, opacity, scroll speed, and display placement.
- **Render Center** lists render queues, candidate videos, errors, and upload results.
- **Upload to** offers **Do not upload / Bilibili / YouTube**. Set up account authorization under **Upload Accounts** first. Upload rights, platform rules, API restrictions, and quotas may apply.

## 5. Troubleshooting

| Problem | Check first |
|---|---|
| Task created, but no recording | Engine started, task enabled, streamer live, valid URL? |
| Cannot load room information | URL accessible in a browser, network and platform access restrictions? |
| Platform marked unverified | Treat as experimental, not guaranteed support |
| Video is saved without danmaku | Danmaku enabled and currently available for the platform? |
| No automatic render | Auto-render enabled, source and danmaku files present, errors in Render Center? |
| Upload failed | Account authorization, rights, moderation rules, quotas |

For help, open an [Issue](https://github.com/jiabenguiyin/DMRStudio/issues) with your Windows version, DMR Studio version, reproduction steps and sanitized logs. **Never publish cookies, access tokens, keys, or private account details.**

## Livestream clips vs automatic highlight detection

DMR Studio is for **livestream recording, segmented video files, danmaku capture, rendering, and optional uploads**. These features can supply footage for editing livestream clips. They do **not** mean that Build 18 automatically detects highlights and creates short clips.

See also: [platform compatibility](platforms.en.md) · [real English interface screenshots](../README.en.md#screenshots).
