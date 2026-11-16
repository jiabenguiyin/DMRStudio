# DMR Studio YAML Configuration and DanmakuRender Compatibility

[English README](../README.en.md) · [简体中文](yaml-configuration.md) · [Quick start](quick-start.en.md) · [Supported platforms](platforms.en.md)

This guide is grounded in **DMR Studio v1.0 / Build 18** configuration-loader, task-generation, YAML editor, import, and recording-engine code. It is informed by the [DanmakuRender v5 upstream user guide](https://github.com/SmallPeaches/DanmakuRender/blob/v5/docs/usage.md), but **Studio is not guaranteed to support every field or feature in the latest upstream release**.

## 1. YAML files and the execution pipeline

DMR Studio uses a Windows GUI plus YAML configuration files, with recording and processing handled by the underlying task engine.

- <code>global.yml</code>: global recording defaults, FFmpeg/tools, render/upload defaults, state storage, and engine settings.
- <code>DMR-task-name.yml</code>: one recording or video-download task. The mappings <code>common_event_args</code> and <code>download_args</code> are required.
- Active task files normally live under <code>configs/</code>, while paused tasks live under <code>disabled_configs/</code>. The desktop application also tracks task metadata and state in SQLite.
- Windows desktop data normally resides under <code>%LOCALAPPDATA%/DMRStudio/</code> unless portable mode or settings override that location. Prefer **Scheduling & Config → Edit global.yml / Edit YAML** rather than writing to Program Files.

The engine reads global defaults, merges the relevant task type, and dispatches jobs to the downloader, renderer, uploader, and cleaner. A changed task may need to be reloaded before it affects an active recording.

### Merge order for effective recording settings

1. Built-in <code>default.yml</code>.
2. User <code>global.yml</code>.
3. Global <code>download_args.live</code> or <code>download_args.videos</code>.
4. Global top-level <code>dm_args</code> (Studio-specific overlay).
5. Task <code>download_args</code>.
6. Task top-level <code>dm_args</code> (higher-priority Studio overlay).
7. Optional <code>render_args</code>, <code>upload_args</code>, and <code>clean_args</code> according to the <code>common_event_args</code> switches.

In particular, Studio's GUI-generated top-level <code>dm_args</code> is merged into the effective downloader configuration; upstream DanmakuRender commonly places these fields directly inside <code>download_args</code>.

## 2. Key differences from upstream DanmakuRender

| Area | Upstream DanmakuRender | DMR Studio Build 18 |
|---|---|---|
| Basic task structure | <code>common_event_args</code> and <code>download_args</code> | Generally compatible |
| Danmaku style | Usually under task <code>download_args</code> | Also supports top-level <code>dm_args</code> |
| Config directory | <code>dmr_engine_args.config_path</code> | Uses managed <code>dmr_engine_args.dynamic_config_path</code> |
| Task types | Includes <code>live</code>, <code>videos</code>, and newer <code>virtual</code> support | Studio creation and legacy import accept <code>live</code> and <code>videos</code>, not virtual |
| Multiple upload targets | Upstream task lists / combined types | Some engine-level handling exists, but Studio's legacy importer rejects list-valued upload targets |
| XML-to-ASS | Documented by upstream via <code>render_args.dmrender.dmconvert</code> | Automatic XML conversion is **not confirmed in Studio Build 18** |
| Web service | Upstream has host, port, and login YAML | Studio manages its own desktop access controls; do not paste upstream server defaults |
| Offline grace period | Upstream global example 120 minutes | Studio built-in default 15 minutes |

The GUI-generated <code>global.yml</code> is also different from the raw engine-default file: its YouTube privacy default is <code>private</code>. **Inspect your active settings; do not treat a library fallback as your product's current configuration.**

## 3. Ready-to-edit YAML examples

- [Basic livestream recording](yaml-examples/DMR-example-live.yml): safe starting point with rendering, uploading and cleanup disabled; uses shared DanmakuRender-style keys.
- [Studio-specific danmaku overrides](yaml-examples/DMR-example-studio-danmaku.yml): shows top-level <code>dm_args</code> and ASS styling.
- [Bilibili upload template](yaml-examples/DMR-example-bilibili-upload.yml): explicit upload target, but **auto-upload remains disabled** until credentials and publishing rights are verified.
- [Video-download task](yaml-examples/DMR-example-videos.yml): demonstrates <code>dltype: videos</code>, not live stream recording.

Replace sample URLs, output directories, and account placeholders. Example room IDs are illustrative and **not verified live streams**.

~~~yaml
common_event_args:
  auto_render: false
  auto_upload: false
  auto_clean: false
  auto_transcode: false

download_args:
  dltype: live
  url: 'https://live.bilibili.com/123456'
  output_dir: './recordings'
  engine: auto
  segment: 3600
  video: true
  danmaku: true
~~~

Use spaces, not tabs. YAML <code>true</code>/<code>false</code> are booleans, <code>~</code> means null, and <code>[]</code> is an empty list. On Windows, single-quoted forward-slash paths such as <code>'D:/Recordings'</code> are easier to edit safely.

## 4. Task lifecycle switches

| Key | Meaning | Recommended first test |
|---|---|---|
| <code>common_event_args.auto_render</code> | Render a danmaku version after each completed segment | <code>false</code> |
| <code>common_event_args.auto_upload</code> | Enqueue uploads configured in <code>upload_args</code> | <code>false</code> |
| <code>common_event_args.auto_clean</code> | Run cleanup rules after successful uploads | <code>false</code> |
| <code>common_event_args.auto_transcode</code> | Additional conversion of source video | <code>false</code> |

**Important:** In this engine, automatic cleanup is triggered by successful upload completion events, **not** simply because recording stopped. Upload errors do not trigger the successful-upload cleanup path.

## 5. download_args: live recording

| Key | Explanation |
|---|---|
| <code>dltype</code> | <code>live</code> or <code>videos</code> |
| <code>url</code> | Actual stream-room URL |
| <code>output_dir / output_name</code> | Output folder / filename template |
| <code>engine</code> | <code>auto</code>, <code>ffmpeg</code>, <code>streamgears</code>, <code>streamlink</code>, depending on environment |
| <code>segment</code> | Recording segment length in seconds (typical: <code>3600</code>) |
| <code>output_format</code> | Stream file container (typical: <code>flv</code>) |
| <code>video / danmaku</code> | Record video and supported live chat, respectively |
| <code>stop_wait_time</code> | Minutes to treat a temporary disconnect as part of the same session; Studio's internal default is 15 |

**Stream selection / advanced controls:** <code>stream_option.stream_cdn</code>, <code>stream_option.stream_type</code>, <code>stream_option.bili_watch_cookies</code>, <code>stream_option.douyu_watch_cookies</code>, plus <code>advanced_video_args.default_resolution</code>, <code>start_check_interval</code>, <code>stop_check_interval</code>, <code>restart_interval</code>, <code>min_video_size</code>, <code>min_video_duration</code>, <code>streamlink_quality</code>, <code>ffmpeg_stream_args</code>, and <code>ffmpeg_output_args</code>.

These are engine-specific options; copying advanced FFmpeg arguments from another platform or encoder is not guaranteed to work. Filters for short/small recordings may discard files.

## 6. dm_args: ASS danmaku capture and display

Upstream style keys often live directly under <code>download_args</code>. In Studio, the following **top-level** block is supported, and task-level overrides take precedence:

~~~yaml
dm_args:
  dm_format: ass
  font: 'Microsoft YaHei'
  fontsize: 36
  dmrate: 0.4
  margin_h: 6
  margin_w: 12
  dst: 20
  dmduration: 16
  opacity: 0.8
  auto_fontsize: true
  outlinecolor: '000000'
  outlinesize: 1.0
  dm_filter:
    keywords: ['example-word']
    username: []
    max_length: 0
~~~

- <code>fontsize</code>, <code>font</code>, <code>outlinecolor</code>, <code>outlinesize</code>: typography.
- <code>dmrate</code>, <code>margin_h</code>, <code>margin_w</code>, <code>dst</code>: placement and density.
- <code>dmduration</code>, <code>opacity</code>, <code>auto_fontsize</code>: duration, alpha, resolution scaling.
- <code>dm_filter.keywords / username / max_length</code>: live chat filtering.

Advanced fields may include <code>dm_stream_option.douyin_dm_cookies</code> and <code>advanced_dm_args.dm_delay_fixed</code>, <code>dm_auto_restart</code>, <code>dm_extra_inputs</code>, <code>dm_file_min_time</code>. Upstream <code>dm_template.danmaku</code> and Studio's <code>dm_template.ass_text</code> affect different stages; do not assume they are interchangeable.

## 7. render_args: danmaku rendering and transcode

The render pipeline uses FFmpeg and recorded ASS files. Main settings:

| Key | Explanation |
|---|---|
| <code>render_kernel_args.nrenders</code> | Concurrent render worker count |
| <code>render_args.dmrender.output_dir / output_name</code> | Danmaku-video output destination and filename |
| <code>render_args.dmrender.format</code> | Output container, usually mp4 |
| <code>render_args.dmrender.vencoder / aencoder</code> | Video and audio codecs |
| <code>render_args.dmrender.vencoder_args / aencoder_args</code> | FFmpeg codec arguments |
| <code>render_args.dmrender.hwaccel_args</code> | Hardware decoding arguments |
| <code>render_args.dmrender.output_resize</code> | Resize ratio or target resolution |
| <code>render_args.dmrender.advanced_render_args.filter_complex</code> | Advanced video filter override |
| <code>render_args.transcode.*</code> | Similar settings for source-video transcode |

Choose an encoder actually available on your hardware (for instance, <code>libx264</code> on CPU). Do not assume NVIDIA NVENC is present on every Windows computer. Upstream XML danmaku conversion settings should not be advertised as confirmed Studio Build 18 features.

## 8. upload_args and clean_args

<code>upload_args</code> is a mapping of output type (<code>src_video</code> or <code>dm_video</code>) to a target-specific configuration. The output type is **not** the same as the destination: set <code>target: bilibili</code> or <code>target: youtube</code> on the upload rule.

Typical Bilibili keys include <code>account</code>, <code>cookies</code>, <code>retry</code>, <code>realtime</code>, <code>min_length</code>, <code>title</code>, <code>desc</code>, <code>tag</code>, <code>tid</code>, <code>copyright</code>, <code>source</code>, and <code>cover</code>. YouTube keys include <code>account</code>, <code>privacy</code>, <code>category</code>, <code>concat_video</code>, <code>retry_resume</code>, and related Google API authentication settings.

**Do not enable uploads before confirming account authorization and publishing rights.** Never commit login cookies, OAuth credentials or API secrets to a public repository. The Studio legacy importer explicitly rejects multi-target upload lists even though some underlying engine paths still process list-like data.

<code>clean_args</code> supports file-type rules with <code>method: move</code>, <code>copy</code>, <code>delete</code>, or <code>custom</code>. Other fields include <code>dest</code>, <code>delay</code>, <code>w_srcfile</code>, and <code>w_srcpre</code>. Cleanup may be destructive; keep <code>auto_clean: false</code> until tested.

## 9. Global engine, storage and recovery settings

| Key | Purpose |
|---|---|
| <code>executable_tools_path.ffmpeg / ffprobe / biliup</code> | Tool binaries, managed by the desktop application when necessary |
| <code>dmr_engine_args.enabled_plugins</code> | Downloader, renderer, uploader and cleaner components |
| <code>dmr_engine_args.dynamic_config / dynamic_config_path</code> | Dynamic task-file discovery and managed path |
| <code>state.db_path</code> | SQLite state for sessions, artifacts and upload groups |
| <code>recovery.resume</code> | Whether to resume interrupted work |
| <code>recovery.on_interrupted_file</code> and related thresholds | Advanced recovery policies |

Studio maintains selected paths inside <code>global.yml</code>. **Do not replace the entire Studio global config with an upstream config file**, especially one exposing an upstream WebUI host, credentials or outdated filesystem paths.

## 10. Importing old DanmakuRender YAML safely

The **Import Existing Configs** flow accepts only <code>global.yml</code> and <code>DMR-*.yml</code> (or their <code>.yaml</code> counterparts). Task types must be <code>live</code> or <code>videos</code>. It requires UTF-8 content, enforces **up to 2 MB per file**, **8 MB per batch**, and **1–50 files per batch**.

- Preview the import; choose whether to skip or replace colliding names.
- Stop the main engine and dry-run worker before committing an import.
- Imported tasks are paused by default unless explicitly enabled.
- The import flow backs up existing files; set the source directory when migrating relative paths.
- Multi-target upload lists are rejected by this import path.
- The global editor preserves many advanced keys and comments, but **YAML syntax passing validation is not proof that a stream, encoder, uploader, or platform is operational**.

For troubleshooting, see [Windows Quick Start](quick-start.en.md) and [Supported Platforms](platforms.en.md). This guide intentionally does not reproduce every upstream YAML field verbatim; configuration details are scoped to the current Studio implementation.
