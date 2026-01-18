# Introduction
**FFMpegCore** 是一个用于 .NET/.NET Core 的 FFmpeg/FFprobe 封装库（wrapper），把 FFmpeg 的功能以 C# API 的形式暴露出来，方便在 C# 应用中进行视频/音频分析、转码、裁剪、截图、流处理等操作。它支持同步与异步调用并兼容 .NET Standard，因此能用在 .NET Framework、.NET Core、甚至 MAUI 等平台上（只要部署好 ffmpeg/ffprobe 二进制）。
> FFMpegCore 本身是一个 C# 库，但它只是一个**壳（Wrapper）**。它的核心依赖是 **FFmpeg 和 FFprobe 的可执行文件**。
## 主要功能（你能用它做什么）
- 运行 FFmpeg 命令（封装输入/输出、参数构建、管道/流）。    
- 使用 FFprobe 提取媒体元信息（时长、分辨率、流信息、比特率等）。    
- 视频转码、分辨率/帧率调整、码率控制、音频格式转换。    
- 裁剪、剪切、拼接（concat）、抽帧/导出缩略图、合并音轨/静音等常见操作。    
- 支持从 URL、流或本地文件作为输入，支持把输出写入文件或 stdout（管道）。    
- 有若干扩展包（例如与 SkiaSharp 集成的扩展）可用来做图像处理。[GitHub](https://github.com/rosenbjerg/FFMpegCore?utm_source=chatgpt.com)
# Install
```
dotnet add package FFMpegCore
```
**必须准备 ffmpeg / ffprobe 二进制**（库不会包含这些二进制）。
- 在 Windows 可用 `winget` 或手动下载；macOS 用 `brew install ffmpeg`；Linux 用包管理器（apt、dnf 等）。示例：`winget install ffmpeg` / `brew install ffmpeg` / `apt install ffmpeg`。
## FFmpeg/FFprobe 依赖
必须确保 `ffmpeg.exe` 和 `ffprobe.exe` 存在并可被程序访问。有三种常见方法：

1. **系统 PATH (推荐用于生产环境):** 将 FFmpeg 的 `bin` 目录添加到系统的环境变量 `PATH` 中。
    
2. **默认配置 (最简单):** 将 `ffmpeg.exe` 和 `ffprobe.exe` 文件拷贝到您的**项目输出目录**（例如 `bin/Debug/net8.0/`）。
    
3. **手动配置 (用于特定环境):** 在代码启动时，手动指定它们的路径。
```csharp
using FFMpegCore;
GlobalFFOptions.Configure(options => {
    options.BinaryFolder = @"C:\path\to\ffmpeg\bin";
    // 或者 options.RootDirectory = ...
});
// 如果您想使用FFMpegCore内置的下载器（仅Windows）： 
// FFMpegCore.FFMpegDownloader.DownloadFFMpeg("/path/to/save/binaries");
```
# How to use
所有 FFmpeg 的操作都是通过 `FFMpegArguments` 类构建的。
对于简单的转换或截图，FFMpegCore 提供了流畅的Helper链式 API。
```csharp
// 简单的视频格式转换 (MP4 -> AVI)
await FFMpeg.ConvertAsync("input.mp4", "output.avi");

// 提取缩略图 (在第10秒)
await FFMpeg.Snapshot(new FileInfo("input.mp4"), new FileInfo("thumbnail.jpg"), 
                      new Size(300, 200), TimeSpan.FromSeconds(10));
```

对于复杂工作流，需要使用 `OutputToFile` 方法，并通过 `WithArguments` 注入自定义参数，特别是 `filter_complex`。
- **从输入文件开始：** 使用 `FromFileInput` 或 `FromInputs`。    
- **调用 `OutputToFile`：** 开始定义输出。    
- **使用 `options.WithArguments`：** 注入原始的 FFmpeg 命令行参数。
```csharp
using FFMpegCore;
using FFMpegCore.Arguments;
using System.IO;

public class FfmpegCoreExecutor
{
    /// <summary>
    /// 统一执行包含复杂滤镜的 FFmpeg 命令。
    /// </summary>
    public async Task<bool> ExecuteFilterComplex(
        string mainVideoPath, 
        string watermarkPath, 
        string filterComplexString, 
        string outputFilePath,
        string finalVideoStreamLabel = "[final_v]",
        string finalAudioStreamMap = "0:a")
    {
        // 1. 定义所有输入文件
        var input1 = new FileInfo(mainVideoPath);
        var input2 = new FileInfo(watermarkPath);
        
        // 2. 构建 FFmpeg 命令行参数
        var args = FFMpegArguments
            .FromFileInput(input1)          // [0:v] (主视频)
            .AddFileInput(input2)           // [1:v] (水印图片)
            
            // 3. 配置输出文件和参数
            .OutputToFile(outputFilePath, true, options => options
                
                // 核心：使用 WithArguments 注入原始参数
                .WithArguments(cmdArgs => 
                {
                    // 注入 filter_complex 字符串
                    cmdArgs.AppendText($"-filter_complex \"{filterComplexString}\"");
                    
                    // 映射滤镜图的输出视频流
                    cmdArgs.AppendText($"-map \"{finalVideoStreamLabel}\""); 
                    
                    // 映射音频流
                    cmdArgs.AppendText($"-map {finalAudioStreamMap}"); 

                    // 标准编码参数
                    cmdArgs.Append("-c:v libx264 -crf 23 -preset veryfast");
                    cmdArgs.Append("-c:a aac -b:a 192k");
                })
            );
        
        // 4. 执行命令并等待结果
        return await args.ProcessAsynchronously();
    }
}

/* // 示例调用：
string filterString = "[0:v][1:v]overlay=10:10[final_v]"; 
// 这里的 [final_v] 必须与 ExecuteFilterComplex 方法中的 finalVideoStreamLabel 匹配
await new FfmpegCoreExecutor().ExecuteFilterComplex("video.mp4", "logo.png", filterString, "out.mp4");
*/
```
FFMpegCore 的简洁 API WithArguments() 是重要接口。 C# 工作流层只需要专注于：
收集所有输入文件路径。
构建最终的、完整的 filter_complex 字符串。
将 输入文件列表、滤镜字符串 和 输出文件路径 传入 FFMpegArguments 并执行。
## 简单转码（同步）
```csharp
using FFMpegCore;

FFMpegArguments
    .FromFileInput("input.mp4")
    .OutputToFile("output.mp4", true, options => options
        .WithVideoCodec("libx264")
        .WithConstantRateFactor(23)
        .WithAudioCodec("aac"))
    .ProcessSynchronously();
```
## **异步转码**
```csharp
await FFMpegArguments
    .FromFileInput("input.mp4")
    .OutputToFile("output.mp4", true, options => options
    .WithVideoCodec("libx264")
    .WithAudioCodec("aac"))
    .ProcessAsynchronously();
```
## 用 FFProbe 获取媒体信息
```csharp
using FFMpegCore;
using FFMpegCore.Pipes;

var info = await FFProbe.AnalyseAsync("input.mp4");
Console.WriteLine($"Duration: {info.Duration}, Video streams: {info.VideoStreams.Count}");
```
（获取流信息、解码器名、分辨率、比特率都很方便）
```csharp
    private readonly VideoFilterContext _ctx = new();

    public VideoWorkflow(string inputVideo)
    {
        _ctx.InputVideo = inputVideo;
        // 用 ffprobe 获取视频尺寸
        var info = FFProbe.Analyse(inputVideo);
        _ctx.VideoWidth = info.PrimaryVideoStream.Width;
        _ctx.VideoHeight = info.PrimaryVideoStream.Height;
    }
```
```csharp
using FFMpegCore;
using FFMpegCore.Exceptions;
using FFMpegCore.Enums;

public class MediaInfoService
{
    public async Task GetVideoDetails(string filePath)
    {
        try
        {
            // 获取媒体文件信息
            var mediaInfo = await FFProbe.AnalyseAsync(filePath);

            Console.WriteLine($"文件名: {mediaInfo.Path}");
            Console.WriteLine($"时长: {mediaInfo.Duration.TotalSeconds:F2} 秒");
            Console.WriteLine($"宽度: {mediaInfo.PrimaryVideoStream.Width} px");
            Console.WriteLine($"高度: {mediaInfo.PrimaryVideoStream.Height} px");
            Console.WriteLine($"编码器: {mediaInfo.PrimaryVideoStream.CodecName}");
            
            // 您可以使用这些信息来计算滤镜中的动态参数（例如水印的缩放比例）
        }
        catch (FFMpegException ex)
        {
            Console.WriteLine($"FFprobe分析失败: {ex.Message}");
        }
    }
}```
## 把视频每隔 X 秒截一帧作为缩略图
```csharp
FFMpegArguments
    .FromFileInput("input.mp4")
    .OutputToFile("thumb-%03d.jpg", false, opts => opts
        .WithCustomArgument("-vf fps=1/20")) // 每20秒一帧
    .ProcessSynchronously();
```
## 从 URL 拉流并转为本地文件（流处理）
```csharp
FFMpegArguments
    .FromUrlInput("https://example.com/stream.m3u8")
    .OutputToFile("local.mp4")
    .ProcessSynchronously();
```
库支持 `WithCustomArgument(...)` 来传入任意 FFmpeg 参数，适合把复杂命令封装成 C# 代码。[C# Corner](https://www.c-sharpcorner.com/article/converting-video-with-ffmpegcore/?utm_source=chatgpt.com)
# Advanced
## 高级集成：日志和错误处理
FFMpegCore 默认会捕获 FFmpeg 进程的 `stdout` 和 `stderr`。

- **执行失败：** 抛出 `FFMpegException`。您可以通过 `try-catch` 结构来处理，这比 PowerShell 脚本中的错误处理更符合 C# 习惯。
    
- **进度跟踪：** FFMpegCore 允许您传递一个 `IProgress<FFMpegCore.Enums.ProgressData>` 实例来实时获取 FFmpeg 的进度（已处理帧数、速度等）。
```csharp
using FFMpegCore;
using FFMpegCore.Enums;
using System.Diagnostics;

public class ProgressReporter : IProgress<ProgressData>
{
    public void Report(ProgressData value)
    {
        // Debug.WriteLine($"进度: {value.Percent:F2}% - 帧数: {value.Frame}");
        // 可以在这里更新UI或记录日志
    }
}

public async Task ExecuteWithProgress(FFMpegArgumentProcessor args)
{
    var reporter = new ProgressReporter();
    
    // ProcessAsynchronously 重载方法接受 IProgress 接口
    await args.ProcessAsynchronously(true, reporter);
}```
# FAQ
## 与其它 C# FFmpeg 库对比

Xabe.FFmpeg：另一个流行的 .NET 封装，API 风格不同，有其活跃社区。

FFmpeg.Autogen / FFmpeg.AutoGen：提供更低层的 C API 绑定，适合需要深入控制 FFmpeg 内部的场景（复杂但更灵活）。

选择原则：如果你想用“封装良好、快速构建命令、常见转码/截图/分析操作”，FFMpegCore 是不错的选择；需要底层控制则看 AutoGen 类库。
