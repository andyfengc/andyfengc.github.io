# Introduction
Whisper 是 OpenAI 开发的一个 **自动语音识别（ASR, Automatic Speech Recognition）模型**，可以把音频中的语音内容自动转写成文字，也就是生成字幕的核心技术。它非常适合用来做视频或音频的字幕生成。
- **多语言支持**：支持多种语言，包括中文、英文、法语、日语等。    
- **准确率高**：相比传统开源 ASR 模型，它对噪音和口音有一定鲁棒性。    
- **模型大小可选**：有多种规模（tiny, base, small, medium, large），性能与速度可平衡选择。
- **开源**：你可以在本地部署，也可以通过 Python 调用。
## Whisper 用途
- **生成字幕**：视频或音频的语音内容转文字。    
- **语音转写**：会议录音、访谈、讲座等。    
- **语音翻译**：部分模型支持直接将语音翻译成英文或其他语言文本。
## 模型比较
**Whisper 模型对比表**，包括各个版本区别、系统要求、中文支持情况

| 模型版本       | 参数量 & 体积        | 准确率 | GPU 显存 | CPU推理速度                               | 对系统要求            | 中文支持     | 适用场景              | 备注                        |
| ---------- | --------------- | --- | ------ | ------------------------------------- | ---------------- | -------- | ----------------- | ------------------------- |
| **tiny**   | 39M 参数，<75MB    | 最低  | <1GB   | 可流畅运行                                 | CPU 可用，GPU 可选    | 支持，但准确率低 | 实时转写、快速测试         |                           |
| **base**   | 74M 参数，<142MB   | 较低  | <1GB   | 可用，稍慢                                 | CPU 可用，GPU 可选    | 支持，识别一般  | 快速批量转写、轻量应用       | 8g内存，4g显存，i7<br>6分钟视频约3m  |
| **small**  | 244M 参数，约466MB  | 中等  | 1~2GB  | 可运行，但很慢                               | CPU 可用，但建议 GPU   | 支持，准确率中等 | 小型项目、实时字幕         | 8g内存，4g显存，i7<br>6分钟视频约7m  |
| **medium** | 769M 参数，约1.55GB | 高   | 3~4GB  | 可运行，但速度非常慢（几倍甚至几十倍于 GPU）              | GPU 推荐，CPU 较慢    | 中文准确率较高  | 专业字幕、会议转写、播客转写    | 8g内存，4g显存，i7<br>6分钟视频约9m  |
| **large**  | 1550M 参数，约2.9GB | 最高  | ~7GB   | CPU 可运行，但会极度慢，几乎不实用；1GB 显存的 GPU 根本跑不动 | GPU 强烈推荐，CPU 非常慢 | 中文准确率最好  | 高精度转写、学术、长音频、商业字幕 | 8g内存，4g显存，i7<br>6分钟视频约20m |
说明：
1. **参数量与体积**：模型越大参数越多，对音频理解能力更强，但需要更多内存和显存。    
2. **准确率**：大模型识别能力最强，尤其是复杂场景和中文环境。    
3. **推理速度**：tiny/base 速度快，适合实时或批量处理；large 最慢，适合追求高精度。    
4. **系统要求**：    
    - **CPU**：tiny/base 可用，medium/large 很慢。        
    - **GPU**：medium/large 推荐 NVIDIA GPU（CUDA 支持），可以显著加快处理速度。        
5. **中文支持**：所有模型都支持中文，越大模型中文识别越准确。    
6. **适用场景**：    
    - tiny/base：快速、轻量、测试或实时场景。        
    - small/medium：中等精度需求，专业转写。        
    - large：高精度、重要视频字幕或学术内容。

| 模型     | GPU 显存需求 | CPU 推理情况 |
| ------ | -------- | -------- |
| tiny   |          |          |
| base   |          |          |
| small  |          | CPU      |
| medium |          | CPU      |
| large  |          |          |
# Subtitle Edit使用
下载subtitle editor https://www.nikse.dk/subtitleedit 或 https://github.com/SubtitleEdit/subtitleedit/releases
![](/images/posts/20250828-whisper-subtitle-1.jpeg)
说明：
- Subtitle Edit 支持直接调用 Faster-Whisper 模型，不需要你手动安装 Python。    
- 模型会在第一次运行时自动下载，也可以手动下载放到指定目录。    
- 中文准确率推荐：`large-v2`（需要显存 >4GB，CPU 也能跑但会慢）。
## 使用步骤
在 Subtitle Edit 菜单 → video`视频` → Audio to text(whisper) `生成字幕（音频转文本）    
![](/images/posts/20250828-whisper-subtitle-6.jpeg)
下载引擎：![](/images/posts/20250828-whisper-subtitle.jpeg)
下载模型：
![](/images/posts/20250828-whisper-subtitle-8.jpeg)
![](/images/posts/20250828-whisper-subtitle-3.jpeg)
-在弹窗里选择：    
    - **引擎**：`Faster-Whisper`        
    - **模型**：根据电脑配置选，越高越好，202508 `large-v3`        
    - **语言**：强制设为 `zh`（中文），不要选 auto。        
点击Generate > 开始生成 > 速度取决于 CPU/GPU 性能。
![](/images/posts/20250828-whisper-subtitle-4.jpeg)
转换完成，或部分转换 > 保存字幕为 `.srt`
也可以导出为纯文本 File → Export → Plain Text
![](/images/posts/20250828-whisper-subtitle-7.jpeg)
弹出窗口中可选择：    
- **只导出字幕文字**        
- **是否保留空行**（一般保留便于段落分隔）
导出的文件就是**纯文本字幕**，没有时间戳，适合直接阅读或做文字稿。
如果你只想导出**部分字幕**，可以先在 Subtitle Edit 中选中需要的段落，再导出。
![](/images/posts/20250828-whisper-subtitle-10.jpeg)
![](/images/posts/20250828-whisper-subtitle-9.jpeg)
# potplayer 使用

![](/images/posts/20250828-whisper-subtitle-5.jpeg)
有多个引擎选项：
- **whisper.cpp CPU** → 纯 CPU 推理（最慢，但兼容性最好）；    
- **whisper.cpp BLAS** → 利用 CPU 的 BLAS 库加速（比纯 CPU 快一些）；    
- **whisper.cpp Vulkan** → 用显卡 Vulkan API 加速；    
- **whisper.cpp CUDA** → NVIDIA GPU CUDA 加速（最快之一）；    
- **Whisper-Faster / Faster-Whisper-XXL** → 基于 CTranslate2 / GGML 的更快实现（推荐）。

推荐：**Faster-Whisper-XXL** 或 **CUDA**（你有 NVIDIA 显卡的话）
# python使用
## whisper示例
```bash
# 安装 Whisper
pip install openai-whisper

import whisper

# 加载模型（大小可选：tiny, base, small, medium, large）
model = whisper.load_model("medium")

# 转写音频
result = model.transcribe("audio.mp3", language="zh")  # 指定中文

# 输出文字
print(result["text"])
```
- `audio.mp3` 可以是任何音频格式（mp3、wav、m4a）。    
- `result["text"]` 就是识别出来的字幕文字。
- 可以配合 `Faster-Whisper`（一个 Whisper 的加速版本，GPU 或 CPU 都比官方快很多），大幅提升字幕生成速度。    
- 如果视频是中文内容，先指定 `language="zh"`，可以避免模型误识别为英文。
直接输出纯文本文件
```python
import whisper

model = whisper.load_model("small")
result = model.transcribe("audio.mp3", language="zh")

# result["text"] 本身就是纯文本
with open("subtitle.txt", "w", encoding="utf-8") as f:
    f.write(result["text"])
```
## Faster-Whisper
Faster-Whisper 是 **Whisper 的加速版本**，由社区开发，目的是在 **CPU 或 GPU 上比官方 Whisper 更快**、同时保持接近原版的识别准确率。它适合电脑配置一般、想快速生成字幕的人使用。

| 特点               | 说明                                             |
| ---------------- | ---------------------------------------------- |
| **速度快**          | CPU 或低显存 GPU 上速度比官方 Whisper 快 3~5 倍（GPU 上速度更快） |
| **多平台**          | 支持 Windows、Linux、macOS                         |
| **节省显存**         | 对 GPU 显存需求低，可以在 2~4GB 显存上运行中等模型                |
| **接口兼容 Whisper** | API 接近官方 Whisper，基本可以直接替换调用                    |
| **支持中文**         | 和官方 Whisper 一样，中文识别效果随模型大小提升                   |
安装fast whisper
```python
pip install faster-whisper
```
可选安装 GPU 版本（需要 CUDA 支持）：
```python
pip install faster-whisper[gpu]
```
使用
```python
from faster_whisper import WhisperModel

# 加载模型，选择大小，可选 tiny/base/small/medium/large
model_size = "small"
model = WhisperModel(model_size, device="cpu")  # CPU 或 "cuda" GPU

# 转写音频
audio_path = "audio.mp3"
segments, info = model.transcribe(audio_path, language="zh")  # 指定中文

# 输出文字
for segment in segments:
    print(f"[{segment.start:.2f}s -> {segment.end:.2f}s] {segment.text}")
```
说明：
- `segments` 是带 **时间戳**的转写结果，可以直接生成 SRT 字幕文件。    
- `device="cpu"` 表示用 CPU；如果有 GPU 可以写 `"cuda"`。    
- 支持 **语言指定**，`language="zh"` 对中文识别更准确。
### 输出 SRT 字幕示例
```python
with open("output.srt", "w", encoding="utf-8") as f:
    for i, segment in enumerate(segments, 1):
        start = segment.start
        end = segment.end
        text = segment.text.strip()
        f.write(f"{i}\n")
        f.write(f"{start:.3f} --> {end:.3f}\n")
        f.write(f"{text}\n\n")
```
生成的 `output.srt` 可以直接导入 PotPlayer、剪映或任何视频编辑软件。
### 直接输出纯文本文件
**字幕文件不一定非得带时间戳**。常见字幕格式里，像 `.srt` 和 `.ass` 默认都是带时间戳的，但你完全可以只保存纯文本
如果你用 **Whisper / Faster-Whisper** 得到的是分段结果 `segments`，只取文本部分写入文件即可：
```python
from faster_whisper import WhisperModel

model = WhisperModel("small", device="cpu")
segments, info = model.transcribe("audio.mp3", language="zh")

# 只保存纯文本
with open("subtitle.txt", "w", encoding="utf-8") as f:
    for segment in segments:
        f.write(segment.text.strip() + "\n")
```
生成的 `subtitle.txt` 就只有每段文字，没有时间戳。

## 开发步骤 

### 选择合适的模型

- Whisper 有不同大小的模型：`tiny`、`base`、`small`、`medium`、`large`。    
- **推荐至少用 `medium` 或 `large`**，中文识别准确率明显高于 `tiny` / `base`。    
- 如果显卡显存够，最好直接用 **Faster-Whisper Large-v2 (中文准确率最好)**。

### 下载模型
https://huggingface.co/ggerganov/whisper.cpp
https://huggingface.co/Systran/faster-whisper-large-v2
### 使用 Faster-Whisper 替代 Whisper.cpp
Faster-Whisper 基于 CTranslate2，推理速度和准确度在中文上都更好。
PotPlayer 里如果能选 Faster-Whisper-XXL，就直接用这个。
### 设置正确的语言
- **语言选项**里，手动指定为 **中文 (zh)**，避免自动检测。    
- Whisper 默认会先检测语言，如果视频里有中英混杂，容易误判成英文，导致乱码或拼音。
### 提取音频
用 **ffmpeg** 提取
```bash
ffmpeg -i input.mp4 -ar 16000 -ac 1 audio.wav
```
### 降噪与前处理
中文音频里常有：环境噪声、口语化、省略字。
视频音频嘈杂 → 先用 `ffmpeg -af arnndn` 或 Audacity 降噪。
- 先用 **音频降噪工具**（比如 Audacity、ffmpeg 的 `arnndn` 滤波器）预处理。    
- 保持 16kHz 单声道（Whisper 的最佳输入格式）。
### 后处理优化
Whisper 输出往往缺少标点、分句不理想。可以用：
- **Subtitle Edit** → 菜单“工具” → “修复标点/大写” → 自动断句、加标点。    
- 或者用 NLP 模型做中文断句 + 标点恢复。