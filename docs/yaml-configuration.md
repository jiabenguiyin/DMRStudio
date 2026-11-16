# DMR Studio YAML 配置详解（兼容 DanmakuRender）

[项目主页](../README.md) · [English](yaml-configuration.en.md) · [新手教程](quick-start.md) · [平台兼容性](platforms.md)

本手册基于 **DMR Studio v1.0 源码** 的配置加载器、桌面配置编辑器、任务生成器、旧配置导入器和录制/渲染/上传引擎编写，并参考 [DanmakuRender v5 官方使用指南](https://github.com/SmallPeaches/DanmakuRender/blob/v5/docs/usage.md) 和 [上游配置样例](https://github.com/SmallPeaches/DanmakuRender/tree/v5/configs)。本文是重新整理的 Studio 使用说明，**不等同于“DanmakuRender 全部配置直接兼容”**。随后的版本请以实际程序为准。

## 1. 工作原理：GUI、YAML、引擎怎样配合？

DMR Studio 的图形界面负责管理任务、编辑 YAML、保存配置与启停状态；实际录制、ASS 弹幕生成、FFmpeg 渲染、投稿和文件清理由底层任务引擎执行。

1. **全局配置** <code>global.yml</code>：全局默认值、工具路径、渲染/上传参数、任务文件扫描目录、状态库。
2. **任务配置** <code>DMR-任务名称.yml</code>：每个直播间或视频下载任务的 URL、输出位置和覆盖参数。<code>common_event_args</code>、<code>download_args</code> 为必需映射。
3. **启停状态**：Studio 将启用任务放在 <code>configs/</code>，暂停任务放在 <code>disabled_configs/</code>；两类任务还会被图形界面和 SQLite 记录，不要只通过拖动文件来替代 UI 管理。
4. **后台引擎**：通过 <code>dmr_engine_args.dynamic_config_path</code> 加载任务，分发给 downloader、render、uploader、cleaner。外部修改可能经过轮询才被引擎发现，并非每个字段瞬时热更新。

**文件到底保存在哪里？** Windows 安装版通常以 <code>%LOCALAPPDATA%/DMRStudio/</code> 为数据根目录；便携模式或自定义设置可能改变位置。最稳妥的入口是程序内「排期与配置 → 编辑 global.yml / 编辑 YAML」。不要往 Program Files 安装目录盲目复制配置。

### 真实的覆盖顺序

1. 内置 <code>default.yml</code>；
2. 用户 <code>global.yml</code>；
3. 对应任务类型的全局 <code>download_args.live</code> 或 <code>download_args.videos</code>；
4. 全局顶层 <code>dm_args</code>（Studio 扩展，会合并为有效下载参数）；
5. 任务内 <code>download_args</code>；
6. 任务顶层 <code>dm_args</code>（Studio 扩展，优先级更高）；
7. 按 <code>auto_render / auto_transcode / auto_upload / auto_clean</code> 决定是否载入任务自己的 <code>render_args / upload_args / clean_args</code>。

因此改了 GUI 的弹幕样式却看不到效果时，应同时检查**全局与任务顶层 dm_args、任务 download_args、任务是否已经重新加载**，不要只看一种配置。

## 2. DanmakuRender 原版与 Studio 的差异

| 项目 | DanmakuRender v5 常见写法 | DMR Studio |
|---|---|---|
| 直播任务 | <code>DMR-xxx.yml</code> + <code>common_event_args</code> / <code>download_args</code> | 基本兼容 |
| 直播弹幕参数 | 放在 <code>download_args</code> | 同时支持顶层 <code>dm_args</code>，供 Studio GUI 编辑和覆盖 |
| 任务目录 | 全局 <code>dmr_engine_args.config_path</code> | 使用 <code>dmr_engine_args.dynamic_config_path</code>；运行时目录由 Studio 管理 |
| 直播/视频任务类型 | <code>live</code>、<code>videos</code>，原版还有 <code>virtual</code> | 当前 Studio 创建/导入只接收 <code>live</code> 或 <code>videos</code> |
| 上传到多个账号 | 上游任务可使用列表、组合视频类型 | 底层存在部分列表处理逻辑，但 **Studio 文件导入器会拒绝多目标上传列表**，不要当作可直接导入格式 |
| XML 弹幕自动转换 | 原版新文档介绍 <code>render_args.dmrender.dmconvert</code> | 当前 DMR Studio 的渲染路径未确认具备同样的自动转换，**不能照搬宣称支持** |
| 弹幕模板 | 上游介绍 <code>dm_template.danmaku</code> 等 | Studio 默认模板还包含 <code>dm_template.ass_text</code>，两种机制用途不同 |
| WebUI 服务设置 | 上游可见 <code>webservice_kernel_args.host/port</code> | Studio 桌面服务采用自身的访问控制，不要复制上游开放监听及示例登录密码 |
| 默认下播等待 | 上游全局模板为 120 分钟 | Studio 当前内置模板为 15 分钟；具体任务可覆盖 |
| 默认视频编码 | 上游可用 <code>h264_nvenc</code> | Studio GUI 会按实际机器选择编码器；不要在无 NVIDIA 的电脑上硬套 NVENC |

**特别注意：** Studio 的内部默认模板与「桌面端生成的 global.yml」也不完全一样。例如桌面端生成的 YouTube 投稿隐私默认值为 <code>private</code>；不要从引擎内部默认 YAML 推断用户当前真实配置。

## 3. 先复制这些安全示例

- [通用直播录播任务（推荐起点）](yaml-examples/DMR-example-live.yml)：仅录视频和弹幕，不渲染、不上传、不清理。
- [Studio 专用弹幕参数覆盖](yaml-examples/DMR-example-studio-danmaku.yml)：演示顶层 <code>dm_args</code>，适合修改字体、透明度和过滤规则。
- [B站投稿配置示例](yaml-examples/DMR-example-bilibili-upload.yml)：保留投稿参数，但**自动上传默认关闭**，确认账号和版权后手动开启。
- [B站视频下载任务](yaml-examples/DMR-example-videos.yml)：<code>dltype: videos</code>，和直播监控不是同一种任务。

这些配置是**结构示例**，并非已对示例房间进行直播录制实测。请更换 URL、目录、账号，并先备份现有 YAML。导入后先保持任务暂停，检查输出结果，再开启渲染或投稿。

### 最小直播任务

~~~yaml
common_event_args:
  auto_render: false
  auto_upload: false
  auto_clean: false
  auto_transcode: false

download_args:
  dltype: live
  url: 'https://live.bilibili.com/123456'
  output_dir: './直播回放'
  engine: auto
  segment: 3600
  video: true
  danmaku: true
~~~

上面仅作语法示范；房间号 <code>123456</code> 不代表真实在线。YAML 仅用空格缩进，不要用 Tab。<code>true/false</code> 是布尔值，<code>~</code> 是空值，列表写成 <code>[a, b]</code>。Windows 文件目录推荐 <code>'D:/Recordings'</code>，不要意外触发反斜杠转义。

## 4. common_event_args：四个自动工作流开关

| 参数 | 含义 | 初次测试 |
|---|---|---|
| <code>auto_render</code> | 每段录像完成后自动执行弹幕版渲染 | <code>false</code> |
| <code>auto_upload</code> | 按 <code>upload_args</code> 将合格文件提交上传任务 | <code>false</code> |
| <code>auto_clean</code> | 上传成功后依 <code>clean_args</code> 清理相应产物 | <code>false</code> |
| <code>auto_transcode</code> | 对原始录像额外执行转码 | <code>false</code> |

**容易误会：** 在当前引擎的任务事件链里，自动清理主要由**上传完成**触发。不能把 <code>auto_clean: true</code> 理解成“纯本地录播结束就一定自动清理”；投稿失败时也不应触发成功后的清理。

## 5. download_args：直播录制参数

| 参数 | 说明 | 例子 |
|---|---|---|
| <code>dltype</code> | 下载类型 | <code>live</code> 或 <code>videos</code> |
| <code>url</code> | 直播间链接 | 实际可访问的直播间 URL |
| <code>output_dir</code> | 输出目录 | <code>'D:/Recordings'</code> |
| <code>output_name</code> | 文件名模板 | <code>'{STREAMER.NAME}-{CTIME.YEAR}'</code> |
| <code>engine</code> | 录制引擎 | <code>auto</code>、<code>ffmpeg</code>、<code>streamgears</code>、<code>streamlink</code>，以环境支持为准 |
| <code>segment</code> | 录像分段时长，秒 | <code>3600</code>（一小时） |
| <code>output_format</code> | 录像容器格式 | <code>flv</code> |
| <code>danmaku</code> | 是否采集弹幕 | <code>true</code>（需平台接口支持） |
| <code>video</code> | 是否录制视频流 | <code>true</code> |
| <code>stop_wait_time</code> | 下播后的等待分钟数，用来合并短暂断播 | Studio 内置参考为 <code>15</code> |

文件名可使用上游的替换变量，例如 <code>{STREAMER.NAME}</code>、<code>{TITLE}</code>、<code>{CTIME.YEAR}</code>，但不要包含 Windows 文件名不允许的字符。具体能够替换的变量取决于任务场景。

### stream_option 与高级参数

| 字段 | 说明 |
|---|---|
| <code>stream_option.stream_cdn</code> | 指定或限制 CDN，默认由引擎选取 |
| <code>stream_option.stream_type</code> | 某些平台的流格式/编码偏好 |
| <code>stream_option.bili_watch_cookies</code> | B站观看态 Cookie 文件路径 |
| <code>stream_option.douyu_watch_cookies</code> | 斗鱼观看态 Cookie 文件路径 |
| <code>advanced_video_args.default_resolution</code> | 无法探测时的默认分辨率 |
| <code>advanced_video_args.start_check_interval</code> | 检查开播的时间间隔，秒 |
| <code>advanced_video_args.stop_check_interval</code> | 下播状态检测间隔，秒 |
| <code>advanced_video_args.restart_interval</code> | 重试策略，如 <code>[0, 10, 60]</code> |
| <code>advanced_video_args.min_video_size</code> | 过小录像的过滤阈值，MB |
| <code>advanced_video_args.min_video_duration</code> | 过短录像的过滤阈值，秒 |
| <code>advanced_video_args.streamlink_quality</code> | Streamlink 的画质参数 |
| <code>advanced_video_args.ffmpeg_stream_args</code> | FFmpeg 输入参数列表 |
| <code>advanced_video_args.ffmpeg_output_args</code> | FFmpeg 输出参数列表 |
| <code>advanced_video_args.streamlink_extra_args</code> | Streamlink 额外参数列表 |
| <code>advanced_video_args.group_id</code> | 高级分组标识，需验证使用场景 |

这类高级取流参数只有在实际下载引擎使用它们时才会生效。过滤阈值可能导致特别短/特别小的视频被忽略或删除；不要随意抬高。

## 6. 弹幕参数：两种写法不要混淆

**原版常用写法**：直接放在任务的 <code>download_args</code> 下面，如 <code>download_args.opacity</code>。

**Studio 推荐写法**：在任务顶层单独设置 <code>dm_args</code>，程序启动任务前会合并到有效 <code>download_args</code>：

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
    keywords: ['需要屏蔽的词']
    username: []
    max_length: 0
~~~

| 参数 | 作用 |
|---|---|
| <code>dm_format</code> | 当前录制的弹幕格式一般为 <code>ass</code> |
| <code>font</code>、<code>fontsize</code> | 字体与字号 |
| <code>dmrate</code> | 弹幕显示区占屏比例 |
| <code>margin_h</code>、<code>margin_w</code> | 弹幕纵向/横向间距（不同数值范围有不同单位解释） |
| <code>dst</code> | 顶部偏移 |
| <code>dmduration</code> | 单条滚动弹幕持续时间，秒 |
| <code>opacity</code> | 不透明度，通常 0–1 |
| <code>auto_fontsize</code> | 按视频分辨率缩放字号 |
| <code>outlinecolor</code>、<code>outlinesize</code> | 描边颜色和宽度 |
| <code>dm_filter.keywords</code> | 包含关键词时过滤 |
| <code>dm_filter.username</code> | 按用户名过滤 |
| <code>dm_filter.max_length</code> | 超过该长度过滤；0 不限制 |

可选高级弹幕项（按实际平台支持）：<code>dm_stream_option.douyin_dm_cookies</code>、<code>advanced_dm_args.dm_delay_fixed</code>、<code>advanced_dm_args.dm_auto_restart</code>、<code>advanced_dm_args.dm_extra_inputs</code>、<code>advanced_dm_args.dm_file_min_time</code>。若需特殊模板，注意 <code>dm_template.ass_text</code> 与 <code>dm_template.danmaku</code> 分别影响 ASS 写入或消息预处理，不能直接当成相同字段。

## 7. render_args：弹幕渲染与转码

~~~yaml
render_args:
  dmrender:
    format: mp4
    vencoder: libx264
    vencoder_args: ['-b:v', '8M']
    aencoder: aac
    aencoder_args: ['-b:a', '320K']
    hwaccel_args: []
    output_resize: ~
  transcode:
    format: mp4
    vencoder: libx264
    vencoder_args: ['-b:v', '8M']
    aencoder: aac
    aencoder_args: ['-b:a', '320K']
    hwaccel_args: []
    output_resize: ~
~~~

| 字段 | 作用 |
|---|---|
| <code>render_kernel_args.nrenders</code> | 全局渲染任务并行数量 |
| <code>render_args.dmrender.output_dir / output_name</code> | 弹幕版输出路径和命名 |
| <code>render_args.dmrender.format</code> | 输出封装格式，常见 mp4 |
| <code>render_args.dmrender.vencoder / aencoder</code> | 视频/音频编码器 |
| <code>render_args.dmrender.vencoder_args / aencoder_args</code> | 编码器命令参数列表 |
| <code>render_args.dmrender.hwaccel_args</code> | FFmpeg 硬件加速输入参数 |
| <code>render_args.dmrender.output_resize</code> | 输出缩放，可为比例或目标分辨率 |
| <code>render_args.dmrender.advanced_render_args.filter_complex</code> | 高级 FFmpeg 滤镜覆盖，使用前应了解转义和路径规则 |
| <code>render_args.transcode.*</code> | 原片转码的对应设置 |

需要 NVIDIA NVENC 等硬件编码时，先在 Studio「系统设置」验证当前机器的可用编码器。CPU 兼容示例中的 <code>libx264</code> 仅用于展示；选择编码器会影响速度、体积与 GPU 占用。

**ASS 与 XML 区别：** 当前 Studio 的弹幕渲染主要依赖现有弹幕文件与 FFmpeg 字幕滤镜。上游新文档提供的 XML→ASS 转换配置及外部依赖，不应视作 当前 DMR Studio 已完整支持；要处理外部 XML，先验证实际转换路径。

## 8. upload_args：B站 / YouTube / 自定义上传

任务级上传常用格式：

~~~yaml
common_event_args:
  auto_upload: false    # 确认账号、版权和审核规则后才开启

upload_args:
  src_video:
    target: bilibili
    account: 'REPLACE_WITH_LOCAL_ACCOUNT'
    realtime: false
    retry: 3
    min_length: 120
    title: '[{STREAMER.NAME}/直播回放] {TITLE}'
    tag: '直播回放'
~~~

- <code>src_video</code>：原视频；<code>dm_video</code>：带弹幕视频；组合键与多上传目标属于高级场景。
- <code>target</code> 指定目标，例如 <code>bilibili</code> 或 <code>youtube</code>。对应全局 <code>upload_args.bilibili</code> / <code>upload_args.youtube</code> 提供默认值。
- B站常见参数还有 <code>copyright</code>、<code>source</code>、<code>tid</code>、<code>cover</code>、<code>desc</code>、<code>dynamic</code>、<code>limit</code>、<code>realtime</code>、<code>min_length</code> 等。
- YouTube 常见参数有 <code>account</code>、<code>privacy</code>、<code>category</code>、<code>concat_video</code>、<code>retry_resume</code> 等，实际账号授权与配额受 Google API 限制。
- 原版 DanmakuRender 的多目标列表虽然在底层部分逻辑中仍有处理，但 **Studio 的“导入旧配置”流程明确拒绝列表式多目标上传**，需要拆分成可导入任务，不要依赖“保存成功就能运行”的假象。
- 上传前确认自己有权录制、加工、发布直播内容；B站自制/转载标志和来源应按真实情况填写。**不要在公共仓库保存登录 Cookie、OAuth 文件或 API 密钥。**

## 9. clean_args：移动、复制、删除（谨慎）

<code>clean_args</code> 的键代表文件类型（如 <code>src_video</code> / <code>dm_video</code> / <code>all</code>），每种类型下面可用 <code>method: move</code>、<code>copy</code>、<code>delete</code> 或 <code>custom</code>（自定义命令有额外风险）。字段包括 <code>dest</code>、<code>delay</code>（秒）、<code>w_srcfile</code>、<code>w_srcpre</code>。

**先关闭 auto_clean。** 当前引擎在成功收到上传完成事件后才执行自动清理流程，上传失败会阻止该次成功后清理；若想单纯整理本地录像，请不要把自动清理当作通用文件定时清理功能。<code>delete</code> 可能不可恢复，务必先用虚拟或备份数据验证。

## 10. 全局引擎与状态恢复设置

| 字段 | 说明 |
|---|---|
| <code>executable_tools_path.ffmpeg / ffprobe / biliup</code> | 实际执行文件路径，Studio 可能同步为安装内对应工具 |
| <code>dmr_engine_args.enabled_plugins</code> | 启用的下载、渲染、上传、清理等引擎插件 |
| <code>dmr_engine_args.dynamic_config</code> | 是否动态监视任务目录 |
| <code>dmr_engine_args.dynamic_config_path</code> | Studio 受管的启用任务目录；不建议人工更改 |
| <code>render_kernel_args.nrenders</code> | 渲染并行数 |
| <code>uploader_kernel_args.nuploaders</code> | 上传并行数 |
| <code>state.db_path</code> | 录制会话/产物/上传组状态 SQLite 数据库位置 |
| <code>recovery.resume</code> | 引擎恢复未完成任务的策略开关 |
| <code>recovery.on_interrupted_file</code> 等 | 中断文件处置策略及大小/时间阈值，属高级设置 |

Studio 运行时会维护工具路径、<code>dynamic_config_path</code> 和 <code>state.db_path</code> 等受管路径。**不要将从别的电脑复制来的整个 global.yml 覆盖当前文件**；如果要导入，先用 Studio 的预览/导入工具，并保留备份。

## 11. 旧 DanmakuRender YAML 导入的实际限制

Studio 提供「导入原版配置」与 YAML 编辑器，两者用途不同：

- 原版文件名需是 <code>global.yml</code> 或 <code>DMR-*.yml</code>；任务类型只能是 <code>live</code>、<code>videos</code>。
- 文件导入预览要求 UTF-8，单个文件不超过 **2 MB**，一批最多 **50** 个文件和 **8 MB**。
- 同名冲突可选择跳过或替换；正式导入前需要先**停止录制引擎和试运行**。导入过程中会对已有文件创建备份，默认导入新任务为暂停状态。
- 导入器会拒绝列表式多目标上传；其他上游高级字段可能虽然能通过基本 YAML 语法检查，却未必被 Studio GUI 完整展示或写回。
- GUI「常用默认值」只暴露部分字段，复杂配置建议用「完整 global.yml」编辑；源码中的相关测试专门覆盖保留自定义字段、注释和导入任务的未知字段，但任何迁移仍应备份、校验。
- **“YAML 检查格式通过”只能说明结构基本有效，不保证直播平台、FFmpeg 编码器、投稿凭据都可用。**

详细使用路径：[新手教程](quick-start.md) · [平台适配范围](platforms.md) · [DanmakuRender 原版使用指南](https://github.com/SmallPeaches/DanmakuRender/blob/v5/docs/usage.md)。本指南不复制上游完整配置文件，避免把未支持的选项误当成本版功能。
