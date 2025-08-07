---
layout: post
title: Text to srt Tutorial
author: Andy Feng
---
# Introduction
以下是主流字幕文件格式的对比表

| **格式**      | **扩展名**   | **特点**                         | **适用场景**            | **主流程度**   |
| ----------- | --------- | ------------------------------ | ------------------- | ---------- |
| **SRT**     | .srt      | 纯文本、时间码+字幕行，简单易编辑。             | 通用（影视、短视频）          | ★★★★★（最主流） |
| **VTT**     | .vtt      | WebVTT格式，类似SRT但支持HTML标签、样式和注释。 | 网页（HTML5视频、YouTube） | ★★★★☆      |
| **ASS/SSA** | .ass/.ssa | 高级字幕格式，支持字体、颜色、位置、特效（如卡拉OK）。   | 动画、特效字幕（Aegisub编辑）  | ★★★☆☆      |
| **TTML**    | .ttml     | XML格式，支持复杂样式和时序，兼容性广。          | 流媒体（Netflix、Hulu）   | ★★★☆☆      |
| **SCC**     | .scc      | 封闭字幕格式，专为电视广播设计，二进制编码。         | 广播电视                | ★★☆☆☆      |
| **STL**     | .stl      | 欧洲广播标准，支持多语言和字体控制。             | 专业影视后期              | ★★☆☆☆      |
| **IDX+SUB** | .idx/.sub | 图形字幕（非文本），基于图像存储。              | DVD光盘               | ★★☆☆☆      |
| **LRC**     | .lrc      | 歌词同步格式，仅需时间戳+文本，无结束时间。         | 音乐播放器（如QQ音乐）        | ★★★☆☆      |

你已经有了“配音文本”和“配音 mp3”，现在你想为它 **生成带时间轴的字幕（SRT）文件**。这是非常常见的流程，比如自动生成卡拉 OK 字幕或视频解说字幕。
## ✅ 前提

- ✅ 文本是你自己控制的（例如分段脚本、句子级）    
- ✅ mp3 是由这些文本合成的（比如用 Azure TTS）    
- ✅ 每一段文本 → 对应一段语音 → 需要字幕时间轴

可选方法：

|方法|原理|优点|缺点|
|---|---|---|---|
|✅ **基于文本切分估算时间**|根据文本顺序 & 音频时长做线性分段|快速、简单、适合 TTS|不够精确|
|✅ **使用 `aeneas` 对齐工具**|用音频 + 文本对齐得到精准时间轴|高精度支持多语种|配置略复杂，需要 Python|
|✅ **用 FFmpeg 估算音频时长**|结合文本数和音频长度做分段|无需依赖外部组件|精度取决于文本切法|
# 基于文本切分估算：

由于是你生成的语音，**每句配音一段文本**，可以直接采用 **“按句等分 + 估算时间”** 来生成 `.srt` 字幕
Step 1：准备文本列表
```csharp
var lines = new List<string>
{
    "First sentence of your voice-over.",
    "Second sentence for subtitle.",
    "Third and final sentence."
};
```

Step 2：获取 MP3 音频时长
使用 `NAudio`、`TagLib#` 等库获取时长（以秒为单位）：
```csharp
var file = TagLib.File.Create("output/merged.mp3");
double duration = file.Properties.Duration.TotalSeconds;
```
Step 3：计算每段持续时间 & 生成 SRT 字幕
```csharp
var builder = new StringBuilder();
int index = 1;
double chunkDuration = duration / lines.Count;
double currentStart = 0;

foreach (var line in lines)
{
    var start = TimeSpan.FromSeconds(currentStart);
    var end = TimeSpan.FromSeconds(currentStart + chunkDuration);

    builder.AppendLine(index.ToString());
    builder.AppendLine($"{FormatSrtTime(start)} --> {FormatSrtTime(end)}");
    builder.AppendLine(line);
    builder.AppendLine();

    currentStart += chunkDuration;
    index++;
}

File.WriteAllText("output/captions.srt", builder.ToString());

string FormatSrtTime(TimeSpan time) =>
    time.ToString(@"hh\:mm\:ss\,fff");
```
示例输出（captions.srt）
```sql
1
00:00:00,000 --> 00:00:06,500
First sentence of your voice-over.

2
00:00:06,500 --> 00:00:13,000
Second sentence for subtitle.

3
00:00:13,000 --> 00:00:20,120
Third and final sentence.
```
# 使用 `aeneas` 做精准对齐

.....todo


# aeneas


# References 

