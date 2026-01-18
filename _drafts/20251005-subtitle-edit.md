# Introduction
Subtitle Edit 是 C# (.NET) 开发的开源项目，源代码托管在 GitHub 上：
🔗 https://github.com/SubtitleEdit/subtitleedit

框架：.NET 6（部分旧版本是 .NET Framework 4.8）
主项目文件：SubtitleEdit.sln
![](images/posts/20251005.jpeg)
# 源码结构
| 文件夹 / 项目                 | 作用                                                                    | 类型                 |
| ------------------------ | --------------------------------------------------------------------- | ------------------ |
| **libse**                | 🔥 **核心库（Library）**，这里包含了几乎所有逻辑代码：字幕解析、格式转换、时间轴、OCR、波形分析等。是你要使用的主要部分。 | .NET Class Library |
| **ui**                   | Subtitle Edit 的图形界面（WinUI/WPF/WinForms）项目。依赖 `libse`。运行整个程序时用它启动。     | WinForms / UI 程序   |
| **Tests**                | 单元测试项目。验证 `libse` 的逻辑正确性。                                             | xUnit/NUnit 测试项目   |
| **UpdateAssemblyInfo**   | 内部构建工具项目，用来自动更新程序集版本号。                                                | 小工具                |
| **UpdateLanguageFiles**  | 内部工具，用来批量更新语言文件（.xml 或 .resx）。                                        | 小工具                |
| **UpdateResourceScript** | 更新资源脚本、图标、语言包等的辅助工具。                                                  | 小工具                |
## libse 项目总体说明

libse = Subtitle Edit 的 核心逻辑库
包含几乎所有非 UI 的功能：
字幕格式解析、文件读写、翻译、语音识别、卡拉OK渲染、波形、音频分析、语法检查、机器翻译接口等。

它输出的是一个 .dll，由 ui 项目（Subtitle Edit 主程序）调用。
你可以独立引用 libse 来构建自己的应用。

| 文件夹                     | 功能说明                                                                                          | 开发说明，是否重要                                                                                                                                          |
| :---------------------- | :-------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------- |
| **AudioToText**         | 音频转文字（语音识别）模块。内含 Whisper、Azure、Vosk 等语音引擎的封装。                                                 | ✅ 如果要自动对齐歌词，用得上。<br>用于生成字幕文本或同步歌词。<br>若要自动从音乐提取歌词时间轴，可用此模块（语音识别）                                                                                   |
| **AutoTranslate**       | 自动翻译模块，支持 DeepL、Google Translate、ChatGPT、Microsoft Translator。                                | ❌仅用于字幕自动翻译。                                                                                                                                        |
| **BluRaySup**           | Blu-ray `.sup` 字幕图像格式解析与提取。                                                                   | ❌用于导入蓝光光盘字幕。                                                                                                                                       |
| **CDG**                 | CD+G（卡拉OK碟机专用）.cdg字幕图像格式支持。（Compact Disc + Graphics）<br>                                      | ⚙️ 仅特殊卡拉OK格式时使用。分析或输出 CDG 卡拉OK字幕。<br>用于研究卡拉OK效果（如果要模仿 CDG 卡拉OK 动画逻辑）<br>可借鉴其逐字绘制逻辑                                                                 |
| **Cea608 / Cea708**     | 电视字幕标准解析（Closed Caption - CC，美国电视标准）                                                          | ❌主要用于字幕流提取和分析。                                                                                                                                     |
| **Common**              | 各种通用工具类、时间轴换算、文本处理、颜色、字体、正则表达式、文件操作等。                                                         | ✅ 非常重要，是底层依赖。<br>包含时间码、段落、字幕模型、文本处理、波形、OCR 基类等。<br>提供基础对象，如 `Subtitle`, `Paragraph`, `TimeCode`, `Style`<br>所有字幕格式都基于这些对象                          |
| **ContainerFormats**    | 封装格式（如 MKV、MP4、TS）内嵌字幕轨提取/导入逻辑                                                                | ❌让 SE 能从视频容器文件中提取内嵌字幕。                                                                                                                             |
| **DetectEncoding**      | 自动检测文本编码（UTF-8, ANSI, UTF-16 等）。                                                              | ✅导入字幕文件时判断是 UTF-8 / ANSI / Unicode 等。                                                                                                              |
| **Dictionaries**        | 拼写检查词典管理。                                                                                     | ❌拼写、语法检查辅助。                                                                                                                                        |
| **Enums**               | 各种全局枚举类型定义。                                                                                   | ✅各模块共享的枚举值。                                                                                                                                        |
| **Forms**               | 与字幕编辑器逻辑耦合的部分窗体逻辑代码（虽然是“Forms”，但仍是非UI业务层逻辑）。<br>⛔ 注意：不是 UI 界面，而是对话框逻辑、表单数据类（部分仍为 WinForms 代码） | ⚙️ 可选<br>这里可能有一些旧的窗体残留逻辑。                                                                                                                          |
| **Grammar**             | 语法检查器模块（Grammar checker）。                                                                     | ❌类似 Grammarly 的基本语法校正。                                                                                                                             |
| **Http**                | 网络请求封装，用于翻译、OCR、语音API等的HTTP请求。                                                                | ⚙️可选<br>包含网络请求与响应解析。                                                                                                                               |
| **Interfaces**          | 各功能模块的接口定义（ITranslator, IAudioToText, ISubtitleFormat 等）。<br><br>                             | 用于解耦核心逻辑与具体实现。<br>✅ 很关键：如果要自定义字幕格式或翻译引擎，用这里的接口。ISubtitleFormat, IAudioToTextEngine                                                                 |
| **NetflixQualityCheck** | Netflix字幕质量检测工具模块<br>Netflix QC 工具（检测字幕时间间隔、长度、字符等）。                                          | ❌用于格式规范验证（时长、位置、换行规则）                                                                                                                              |
| **Properties**          | 程序内部属性与资源引用。<br>AssemblyInfo与资源定义                                                             | ⚙️可选<br>标准 .NET 元信息。                                                                                                                               |
| **Resources**           | 内部资源文件，如内置字体、语言数据。内部图标、文本资源                                                                   | ❌一般不需要动。                                                                                                                                           |
| **Settings**            | 用户配置与全局设置逻辑。程序设置、偏好保存逻辑<br>                                                                   | ⚙️JSON/XML 设置存取。<br>如果你要保存特效配置或模板样式                                                                                                                |
| **SpellCheck**          | 拼写检查逻辑。                                                                                       | ⚙️用于字幕校对。                                                                                                                                          |
| **SubtitleFormats**     | 🎯 **最重要模块！** 包含所有字幕格式解析与输出逻辑（SRT、ASS、LRC、VTT、TTML, STL等）。                                    | ✅✅✅<br>负责 `.ass`、`.srt`、`.lrc` 等格式的读写<br>例如读取 `.lrc`、生成 `.ass`、控制对齐和时间轴。<br>主要类如 `AdvancedSubStationAlpha`, `SubRip`, `Lyrics3`, `SubStationAlpha` |
| **TextToSpeech**        | 文本转语音模块（TTS）。                                                                                 | ⚙️ 如果想配合AI配音使用。可生成音频预览或辅助功能。                                                                                                                       |
| **Translate**           | 翻译逻辑（AutoTranslate的具体实现）。AutoTranslate 之外的主要模块                                                | ❌支持不同翻译后端。                                                                                                                                         |
| **VobSub**              | DVD VobSub `.sub/.idx` 图像字幕支持。                                                                | ❌导入DVD字幕。                                                                                                                                          |
# 二次开发
Subtitle Edit 源码整体结构大致如下：
```swift
SubtitleEdit.sln
├── libse/                      ← 🔹 Subtitle Edit 的核心逻辑库（最重要）
│   ├── SubtitleFormats/        ← 各种字幕格式解析与导出
│   ├── AudioToText/            ← Whisper / Vosk / DeepSpeech 接口
│   ├── Translate/              ← Google / Azure / DeepL 翻译封装
│   ├── Common/, Enums/, Interfaces/
│   ├── Settings/, Resources/
│   └── SpellCheck/, OCR/, Waveform/
│
├── ui/                         ← Windows 界面（WinForms）
│   ├── Forms/
│   ├── Controls/
│   └── ...
│
├── Tests/                      ← 单元测试
├── UpdateLanguageFiles/        ← 语言更新脚本
├── UpdateResourceScript/
└── UpdateAssemblyInfo/
```
**重点开发方向在 `libse/` 文件夹**，这里包含所有核心功能，不依赖 UI，可以直接二次开发。
## 添加subtitle libse到.net 9项目
.NET 9.0开始是完全跨平台，但se还是windows only的程序。
未来想跨平台 → 🔧 可考虑用 SkiaSharp 替代 System.Drawing（官方推荐方案）。
为了加入se项目，需要让你的项目也编译成 Windows 桌面类型。
打开libse的 .csproj
```xml
<Project Sdk="Microsoft.NET.Sdk">

	<PropertyGroup>
		<TargetFrameworks>net48;netstandard2.1</TargetFrameworks>
```
 改成
```xml
<Project Sdk="Microsoft.NET.Sdk">

	<PropertyGroup>
		<TargetFrameworks>net9.0-windows</TargetFrameworks>
		<UseWindowsForms>true</UseWindowsForms>
```
可选：右键你的项目 → “管理 NuGet 包” → 搜索并安装：System.Drawing.Common

如果碰到：Version conflict detected for System.Drawing.Common.
升级 libse 的 System.Drawing.Common > 打开 libse.csproj
```
<PackageReference Include="System.Drawing.Common" />
```
改成
```
	<ItemGroup>
		<PackageReference Include="System.Drawing.Common" Version="9.0.4" />
		...
	</ItemGroup>
```
## 主要类
| 模块                | 主要功能                                   | 是否依赖 WinForms        | 可否跨平台            |
| ----------------- | -------------------------------------- | -------------------- | ---------------- |
| `SubtitleFormats` | 解析/导出各种字幕格式（ASS, SRT, LRC, VTT, STL 等） | ❌                    | ✅                |
| `Translate`       | 翻译模块（内置 Google、DeepL 等）                | ❌                    | ✅                |
| `AudioToText`     | 语音识别（Whisper、Vosk）                     | ❌                    | ✅（部分）            |
| `Waveform`        | 音频波形生成                                 | ⚠️ 依赖 System.Drawing | ❌（需改 成SkiaSharp） |
| `OCR`             | OCR 文字识别（Tesseract）                    | ✅ 外部依赖               | ✅（需配置）           |
| `SpellCheck`      | 拼写检查                                   | ❌                    | ✅                |
| `Common`          | 通用工具函数（时间轴、颜色、文本工具等）                   | ❌                    | ✅                |
常见的二次开发方向（及可直接调用的类）

|开发方向|可用类/命名空间|示例功能|
|---|---|---|
|🎵 **歌词转字幕**|`Nikse.SubtitleEdit.Core.SubtitleFormats.LRC`|从歌词时间戳生成 ASS/SRT|
|✍️ **字幕格式转换**|`SubtitleFormats` 下的全部类|`.srt` ↔ `.ass` ↔ `.vtt` ↔ `.lrc`|
|🎨 **卡拉OK特效渲染**|`Nikse.SubtitleEdit.Core.Common`, `ColorUtilities`, `HtmlUtil`|可以配合 SkiaSharp 绘制特效文字|
|🧠 **自动对齐/分割字幕**|`Nikse.SubtitleEdit.Core.Forms.Timing`, `AdjustDisplayTime`|自动调节显示时间|
|🔊 **音频转字幕（语音识别）**|`AudioToText.Whisper` 或 `AudioToText.Vosk`|自动生成字幕文件|
|🌎 **自动翻译**|`Translate.GoogleTranslate`, `Translate.DeepLTranslate`|一键多语言字幕|
|🧾 **字幕合并/校对**|`Nikse.SubtitleEdit.Core.Common.SubtitleMergeHelper`|合并多段字幕|
|📈 **分析与统计**|`Nikse.SubtitleEdit.Core.Common.SubtitleStats`|字幕时长/词频/字符分布分析|
## 功能需求
1. lrc转ass。
2. 修改ass字幕文字格式，比如分辨率，字体，文本大小，位置，颜色，outline.
3. 给ass字幕的每句Dialogue，切好词，根据起止时间的歌曲音波逐词计算每个词的时间，逐词添加卡拉ok动画 \k。或者，切好词后，按起止时间均匀计算。
4. 读取ass字幕每句Dialogue，添加其他动画，比如fade in/fade out, move(), t()。
5. 按顺序读取每句Dialogue，解析时间，计算时间，重新计算坐标y，保存修改ass。
整体开发流程图（逻辑层）
```less
[输入 LRC 文件]
        |
        v
[LRC -> Subtitle 对象]
        |
        v
[导出 ASS -> AdvancedSubStationAlpha 类]
        |
        v
[修改 ASS 样式 (分辨率/字体/大小/位置/颜色/Outline)]
        |
        v
[逐句/逐词处理 Dialogue]
        |   \
        |    -> 根据音波或均匀切词时间，添加 \k
        |
        -> 添加其他动画标签 (\fad, \move, \t)
        |
        v
[重新计算坐标 Y 或其他样式]
        |
        v
[保存 ASS 文件]
```

# FAQ
## 英文按词、中文按字切分
加入卡拉OK的标签\k，需要对文本进行切词，我希望英文按单词，中文按字符。subtitle edit有没有内置切词模块？

- **Subtitle Edit 并没有内置“切词”模块**（不论中英文）。需自写算法
    
- 它对“逐词”卡拉OK的支持，主要是依赖用户**手动分割**或通过**导入已有的逐词时间戳文件**（如 `.lrc`、`.srt` 中包含逐词时间）。
    
- 在源码中，`KaraokeWordSplit` 只是做**基于时间戳**的分割，而不是自然语言层面的 tokenization。
## 自动按宽度换行 (`\N`)
我的歌词文本，会将字符放到很大，如果长的话，需要根据视频宽度，自动插入\N进行换行。subtitle edit能不能自动加入换行？

- **Subtitle Edit 没有内置“自动根据视频宽度插入 `\N`”的功能。无内置宽度测量，需自己测量宽度后自己插入 `\N`
    
- 它可以**手动换行**（用户编辑时按 Shift+Enter），也可以按字符数分割（例如自动换行 30 字符后）。
    
- 但**不会自动根据屏幕宽度和字体大小测量**并换行。
## 修改 `[Styles]` 样式（字体、大小、颜色等）
subtitle edit是否运行快速对ass文件里面的[styles]部分的样式，进行方便的修改？比如字体，文本大小，位置，颜色，outline等。

✅ SE 支持良好：
Subtitle Edit 内部有完整的数据结构和 UI 支持, SE 内部 API 封装完善。

核心类：AssaStyle / AdvancedSubStationAlpha parser。
```CSHARP
var styles = subtitle.Header.Replace("[V4+ Styles]", ""); 
// 或更干净地通过 SubtitleFormat.AssAStyle 类操作
```
既可以直接重写 `[V4+ Styles]` 段，也可以设置每个style
在 `Subtitle.Styles` 集合中（或通过 `AdvancedSubStationAlpha.GetStyles()`），可以直接修改：
- `Fontname`    
- `Fontsize`    
- `PrimaryColour`    
- `OutlineColour`    
- `Outline`    
- `Bold`, `Italic`    
- `Alignment`（对应 `\an` 参数）    
- `MarginL / MarginR / MarginV`（位置）
修改后再调用：
```CSHARP
new AdvancedSubStationAlpha().Save(subtitle, "output.ass", Encoding.UTF8);
```
## 批量行操作与动画注入
subtitle edit是否封装了对于dialogue每行的操作，能方便快速插入动画。我自己写动画效果，我只需要能方便找到位置，并保存到ass文件，subtitle edit这方面有封装吗？

SE 已经封装了非常好的行级访问接口，对每行 `Dialogue` 的封装和动画插入
`Subtitle.Paragraphs` 是所有字幕行的集合，每个 `Paragraph` 对象包含：
```csharp
public TimeCode StartTime;
public TimeCode EndTime;
public string Text;
public int Number;
```
你可以直接遍历：
```csharp
foreach (var p in subtitle.Paragraphs)
{
    p.Text = "{\\move(660,1470,540,1470,0,200)}" + p.Text;
}
```
然后 `new AdvancedSubStationAlpha().Save()` 输出即可。
动画自定义方式：
- `\move()`、`\t()`、`\fade()`、`\pos()` 全部是字符串插入操作（SE 不会阻止 ASS tag）。    
- SE **不会修改或清理** `{\xxx}` 内的标签。    
- 所以你可以轻松批量插入自定义动画逻辑。

示例：
```csharp
foreach (var p in subtitle.Paragraphs)
{
    var enter = "{\\move(700,1470,540,1470,0,200)\\t(0,200,\\fscx110\\fscy110)}";
    var exit  = "{\\t(" + (p.Duration.TotalMilliseconds-200) + "," + p.Duration.TotalMilliseconds + ",\\alpha&HFF&)}";
    p.Text = enter + p.Text + exit;
}
```
输出文件在 Aegisub、FFmpeg、mpv 中可直接播放。

```
(1) 淡入淡出
{\fad(500,500)}Hello

参数 (入,出) 单位毫秒

适合整行歌词淡入淡出

(2) 缩放
{\fscx100\fscy100\t(0,500,\fscx150\fscy150)}Hello


\fscx / \fscy 分别控制水平/垂直缩放

\t(start,end,标签) 表示从 start 到 end 时间执行渐变动画

(3) 移动
{\move(100,200,300,200)}Hello


参数 (x1,y1,x2,y2) 控制字幕从 (x1,y1) 移动到 (x2,y2)

可以结合 \t 做平滑移动：

{\t(0,1000,\move(100,200,300,200))}Hello

(4) 颜色渐变
{\c&HFF0000&}Hello
{\t(0,1000,\c&H00FF00&)}Hello


\c&HBBGGRR& 格式：蓝-绿-红（Hex）

结合 \t 可以渐变动画
 
(5) 逐字动画

基于 LRC 的时间戳生成 \k 标签：

{\k50}H{\k50}e{\k50}l{\k50}l{\k50}o


每个字显示 0.5 秒 (50*0.01s)


可和 \fad、\fscx/fscy、\c 结合

将 LRC 时间戳拆分为每个字 → 转换为 ASS 的 \k 标签

用 \t 做逐字缩放/颜色渐变，增强动态效果


(6) 旋转 / 翻转
{\frz360\t(0,500)}Hello
{} 内的标签顺序无严格要求，但建议逻辑清晰：淡入淡出 → 移动 → 颜色 → 缩放 → 渐变动画


我希望做这样的效果。
显示所有歌词，每次10行。
根据时间进度，把每行的颜色变成黄色。
加在default处。

```