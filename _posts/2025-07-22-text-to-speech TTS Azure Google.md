---
layout: post
title: Text to Speech T TTS Tutorial
author: Andy Feng
---
# Azure 的文本转语音服务 
Azure 的文本转语音（Text to Speech）服务是 Microsoft Azure 认知服务（Cognitive Services）中的一部分，隶属于 **Azure Speech 服务**。它可以将文本内容转换为自然、接近人类的语音，适用于各种应用场景，如语音助手、视频配音、无障碍阅读等。
## 主要特点

1. **高自然度的语音（Neural TTS）**

- 基于深度神经网络的语音合成技术（Neural TTS），支持 **逼真自然的语音效果**。
    
- 提供了超过 **400 多种语音、140 多种语言和方言**。
    
- 支持 **语音风格（Style）** 和 **情感（Emotion）** 控制，例如新闻、客服、播报、欢快、悲伤等语气。
    

2. **自定义语音（Custom Neural Voice）**

- 可上传自己录制的语音素材，训练专属的 AI 声音。
    
- 适用于品牌化语音、虚拟人物等场景。
    
- 注意：**必须通过 Microsoft 的审批**，以防止滥用（如伪造语音）。
    

3. **SSML 支持（语音合成标记语言）**

- 支持 SSML 标准，可对语音的 **语速、语调、停顿、音量、发音方式** 等进行细致控制。
    

4. **语音合成输出格式**

- 输出格式包括 **PCM、MP3、OGG、WAV 等**，适配各种平台和设备。
    
- 可用于实时播放，也可保存为音频文件。
## 开发接入方式

### 1. **REST API**

- 发送 HTTP 请求即可实现语音合成，适合跨平台开发。
    

### 2. **SDK 支持（Azure Speech SDK）**

- 提供 **C#, Java, Python, JavaScript 等多语言 SDK**。
    
- 支持流式合成、语音事件监听、播放控制等功能。
    

### 3. **CLI 和门户测试**

- 可以在 Azure Portal 中在线测试语音合成。
    
- 也可以用 Azure CLI 管理语音资源。
## 计费方式
定价模型分为：

- **标准语音（Standard TTS）**：按字符计费，较便宜。
    
- **神经语音（Neural TTS）**：语音更自然，价格略高。
    
- **自定义神经语音（Custom Neural Voice）**：需审批，训练和使用费用分开计费。

## 使用方法
✅ 使用场景举例

| 场景        | 描述             |
| --------- | -------------- |
| 视频配音      | 把字幕或剧本转成高质量语音  |
| 虚拟客服      | 自动语音应答，结合语音识别  |
| 无障碍辅助阅读   | 为视力障碍者朗读网页或文档  |
| 教育 & 培训   | 合成教学内容         |
| 物联网设备语音播报 | 如语音闹钟、导航仪、智能音箱 |

安装官方sdk
```c#
Microsoft.CognitiveServices.Speech
```
![](/images/posts/Pasted%20image%2020250722014538.png)

示例代码（C#）
```csharp
var config = SpeechConfig.FromSubscription("YourAzureKey", "YourRegion");
config.SpeechSynthesisVoiceName = "zh-CN-XiaoxiaoNeural";

using var synthesizer = new SpeechSynthesizer(config);
var result = await synthesizer.SpeakTextAsync("你好，欢迎使用 Azure 文本转语音服务！");

```
## SSML

| 参数           | 说明     | 推荐写法及单位                  | 取值范围 / 说明                               | 备注                          |
| ------------ | ------ | ------------------------ | --------------------------------------- | --------------------------- |
| **rate**     | 语速     | 浮点倍率，如 `1.0`             | 0.5 ~ 2.0 （0.5 超慢，2.0 超快）               | 推荐倍率，不建议用百分比字符串             |
| **volume**   | 音量     | 浮点倍率，如 `1.0`             | 0.0 ~ 2.0 （0 静音，2 最大增强）                 | 支持字符串值（default, x-loud）但不推荐 |
| **pitch**    | 音调     | Hz，如 `+50Hz`、`-30Hz`     | -500Hz ~ +500Hz                         | 以 Hz 计，整数，0 为默认             |
| **break**    | 句中停顿时间 | 秒数字符串，如 `0.5s`           | 最小 0.001s，最大约 10s                       | 也支持毫秒，如 `500ms`             |
| **emphasis** | 语气强调   | 字符串，如 `strong`           | `strong`, `moderate`, `reduced`         | 无标签即无强调                     |
| **style**    | 说话风格   | 字符串，如 `chat`             | 依voice支持，如 `general`, `chat`, `angry` 等 | 可选，未设置默认普通风格                |
| **role**     | 角色     | 字符串，如 `youngAdultFemale` | 依voice支持，如 `girl`, `seniorMale`         | 可选                          |
[https://learn.microsoft.com/en-us/azure/ai-services/speech-service/speech-synthesis-markup-voice](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/speech-synthesis-markup-voice)

test: 
[https://www.text-to-speech.cn/](https://www.text-to-speech.cn/)
# Google 的text to speech 服务
Google 的 Text-to-Speech（文本转语音）服务是 **Google Cloud Text-to-Speech API**，是 Google Cloud Platform（GCP）提供的 AI 服务之一。它可以将文字内容合成成自然语音，广泛用于语音助手、IVR 系统、视频配音、教育、可访问性等场景。
## 核心特点

1. **自然逼真的声音（WaveNet 模型）**

- 使用 **DeepMind 提出的 WaveNet 模型**，语音自然度高于传统 TTS 技术。
    
- 支持 **超过 400 多种声音**，**50 多种语言和变体**（包括中、英、日、韩、西班牙等）。
    
- 可选择 Standard（标准）和 WaveNet（高质量）语音：
    
    - `Standard`：传统的语音模型，价格低。
        
    - `WaveNet`：高自然度语音，语调、节奏更接近人类。
        

2. **灵活的语音控制**

- 支持 **SSML（Speech Synthesis Markup Language）**，控制语音速度、音量、音高、停顿、发音方式等。
    
- 可选择不同的语音角色、性别、口音。
    

3. **实时与离线支持**

- 支持将文本实时合成为音频流，或直接导出为 `.mp3`, `.ogg`, `.wav` 等格式音频文件。
    
4. **语音调优功能**

- `Speaking Rate`（语速）
    
- `Pitch`（音高）
    
- `Volume Gain`（音量增益）
    
- `Audio Profile`（设备优化）：如电话、扬声器、耳机、车载等设备优化播放效果。
## 接入方式

### 1. **REST API**

- 通过 HTTPS 请求调用，支持 JSON 请求体和 Base64 编码的音频响应。
    

### 2. **Google Cloud Client Libraries**

- 提供官方 SDK（支持 Node.js、Python、Java、C#、Go 等），可快速集成。
    

### 3. **Cloud Console 在线测试**

- 在 GCP 控制台可直接测试不同语言和声音的效果。
## 定价（2025 ）

|类型|计费单位|价格（美元）/ 百万字符|
|---|---|---|
|Standard TTS|每百万字符|$4.00 起|
|WaveNet TTS|每百万字符|$16.00 起|
## 使用方法
使用场景

|应用领域|描述|
|---|---|
|智能客服|自动语音回答用户提问|
|视频自动配音|自动将字幕转为高质量语音|
|阅读辅助工具|辅助视力障碍者阅读文本|
|语言学习|多语言、多口音训练|
|IoT设备语音播报|如智能门铃、智能车载导航|
示例代码（Python）
```python
from google.cloud import texttospeech

client = texttospeech.TextToSpeechClient()

input_text = texttospeech.SynthesisInput(text="你好，欢迎使用 Google 文字转语音服务。")

voice = texttospeech.VoiceSelectionParams(
    language_code="zh-CN", name="zh-CN-Wavenet-A"
)

audio_config = texttospeech.AudioConfig(
    audio_encoding=texttospeech.AudioEncoding.MP3
)

response = client.synthesize_speech(
    input=input_text, voice=voice, audio_config=audio_config
)

with open("output.mp3", "wb") as out:
    out.write(response.audio_content)

```
# FAQ
## 比较一下google，azure的服务
🔍 Azure vs Google Cloud 对比总表（2025 年版）

|比较项 ✅|**Azure**|**Google Cloud**|
|---|---|---|
|**是否支持 C# 调用 API**|✔️ 官方提供 [Azure Speech SDK for C#](https://learn.microsoft.com/en-us/dotnet/api/overview/azure/cognitiveservices.speech)|✔️ 提供 REST API，可通过 [gRPC + C#](https://cloud.google.com/speech-to-text/docs/reference/libraries) 调用|
|**上手难易程度**|⭐⭐ 较易，Portal 配置 + SDK 支持中文文档；免费资源需升级订阅才能继续使用|⭐⭐⭐ 更简洁，免费层无需升级订阅，API 密钥开箱即用，开发者体验好|
|**是否支持文字转语音（TTS）**|✔️ 支持 Neural/Standard/Custom Neural，多语种、多风格|✔️ 支持 Standard/WaveNet 多语种、多口音|
|**是否支持语音转文字（STT）/字幕生成**|✔️ 支持语音识别（含时间戳），可生成字幕（SRT/VTT）|✔️ 支持语音识别，返回每词时间戳，易于生成字幕|
|**是否有长期免费额度？**|✔️ 有，**每月 5,000,000 字符 TTS**；STT 每月 5 小时|✔️ 有，**每月 1,000,000 字符 TTS（WaveNet + Standard）**；STT 每月 60 分钟|
|**免费期多久？**|30 天 $200 美元试用 + 部分服务“永久免费”层，但需升级订阅才能继续用|90 天 $300 美元试用 + **“长期免费层”无需升级订阅也能用**|
|**收费账号计费方式**|✅ 3 类计费：Standard / Neural / Custom Neural（按**字符数**计费）|✅ 2 类计费：Standard / WaveNet（按**字符数**计费）|
|**计费标准（2025）**|- Standard：$4 / 百万字符- Neural：$16 / 百万字符- Custom Neural：更高，需申请|- Standard：$4 / 百万字符- WaveNet：$16 / 百万字符|

## 是否支持训练个人声音
### Google Cloud Text-to-Speech 不支持公众用户自定义训练个人声音
也就是说，你不能像 Azure 的 Custom Neural Voice 那样，用自己的声音训练出一个专属语音模型。

Google 的限制原因：

1. **安全与伦理**：语音克隆存在滥用风险（如假新闻、诈骗等），Google 对自定义声音保持非常严格的限制。
    
2. **WaveNet 模型由 DeepMind 提供**，不开放训练接口，仅允许使用其预训练的声音模型。
    
3. **企业级定制服务**（如通过 Google Cloud 与 Google DeepMind 合作的项目）可能支持定制声音，但这通常需要：
    
    - 高额度的付费；
        
    - 法律合规审核；
        
    - 与 Google 团队签署特殊协议；
        
    - 并非对开发者或中小企业开放。
替代方案
如果你确实需要训练属于你自己的语音克隆模型，以下是一些可选平台：

|平台|是否支持自定义语音|备注|
|---|---|---|
|**Microsoft Azure**|✅ 支持 Custom Neural Voice|需申请，审核严格，适合正规企业|
|**ElevenLabs**|✅ 支持快速语音克隆|在线上传几分钟语音即可合成|
|**Resemble.ai**|✅ 支持语音克隆和情感合成|有免费试用，适合小规模实验|
|**iSpeech**|✅ 支持定制语音|企业级，价格高|
|**OpenTTS / Coqui TTS**|✅ 可本地训练|开源项目，需 GPU 和数据集|
### 推荐方案
如果你打算：

- 做**高质量短视频配音**或**数字人项目**，推荐用 Azure + 申请 Custom Neural Voice。
    
- 快速测试“用自己声音读脚本”的效果，ElevenLabs 或 Resemble.ai 是更轻便的选择。
    
- 完全本地部署（不上传到云端），考虑用 Coqui TTS 自己训练。
如你有音频样本，AI可以帮你判断是否适合训练，或者生成一份申请 Azure Custom Voice 的计划书草稿，也可以帮你写好训练数据格式、生成文本对齐、设计脚本等。如果你有目标平台，AI可以具体建议怎么实现。
## 是否有免费额度
## 免费额度一览

| 项目        | Azure TTS           | Google TTS                      |
| --------- | ------------------- | ------------------------------- |
| 免费字符配额    | 5,000,000/月（Neural） | 1,000,000/月（Standard + WaveNet） |
| 免费时间限制    | 一年（绑定免费账户）          | 永久（长期免费层）                       |
| 账号注册是否送钱  | ✔️ $200（30 天）       | ✔️ $300（90 天）                   |
| 语音质量（免费层） | Neural（高）           | WaveNet（高）                      |
## 有没有生成字幕的功能
是的，**Google Cloud** 和 **Azure** 都提供了 **语音识别（Speech to Text）** 服务，可以将语音内容识别为文字，从而生成**字幕（transcript）**。不过要注意：

> ✅ “文本转语音（TTS）” 是生成配音，  
> ✅ “语音转文本（STT）” 才是生成字幕。

你需要的是 **Speech-to-Text（STT）功能** 来 **生成字幕**。
### 1. **Google Cloud Speech-to-Text**

🎯 支持功能：

- 实时或离线音频转写
    
- **返回每个字词的时间戳**（用于字幕）
    
- 支持多语言、多口音（如普通话、粤语、英语、美音、英音等）
    
- 支持自动断句、自动标点、分段识别
    
 示例：生成字幕 JSON（含时间戳）

```json
{
  "alternatives": [
    {
      "transcript": "你好，欢迎来到字幕测试。",
      "words": [
        {
          "startTime": "0.500s",
          "endTime": "1.000s",
          "word": "你好"
        },
        {
          "startTime": "1.000s",
          "endTime": "2.500s",
          "word": "欢迎"
        }
      ]
    }
  ]
}

```
你可以将其导出为 **SRT、VTT 或 ASS 字幕文件**。
### **Azure Speech-to-Text**

🎯 支持功能：

- 离线音频识别或实时流识别
    
- 支持 **词级别时间戳**
    
- 支持 **自动分段输出（适合字幕）**
    
- 支持多语种、多口音
    
- 提供 **Conversation Transcription（对话转录）** 和 **字幕格式导出**
    

🧪 返回结果示例（可转 SRT）：

Azure STT 返回的是 JSON 结构，你可以提取以下字段来生成字幕：

```json
{
  "DisplayText": "你好，欢迎使用 Azure。",
  "Offset": 3000000,
  "Duration": 12000000
}

```
- Offset 是开始时间（单位 100ns）    
- Duration 是持续时间
    
可以自行转成标准 SRT 格式。
## 微软TTS对于免费用户的限制
**微软 Azure 的文本转语音（TTS）免费用户** 在使用时有**长度限制和配额限制**，具体取决于你使用的是 **免费试用订阅（Free Tier）** 还是 **始终免费（Always Free）** 服务。

| **限制类型**     | **免费试用（前12个月）**                                                | **永久免费（Always Free）**   |
| ------------ | -------------------------------------------------------------- | ----------------------- |
| **每月免费字符数**  | 0.5M（神经语音）  <br>5M（标准语音）                                       | 50K（仅标准语音）              |
| **支持的语音类型**  | ✅ 神经语音（如 `en-US-JennyNeural`）  <br>✅ 标准语音（如 `en-US-Guy24kRUS`） | ❌ 神经语音（需付费）  <br>✅ 标准语音 |
| **单次请求最长音频** | ≤10 分钟                                                         | ≤10 分钟                  |
| **请求频率限制**   | 每分钟 ≤200 次 API 调用                                              | 每分钟 ≤200 次 API 调用       |
| **音频输出格式**   | MP3、WAV、OGG、AAC 等                                              | MP3、WAV、OGG、AAC 等       |
| **是否需信用卡**   | ❌ 无需（但注册时可选绑定）                                                 | ❌ 无需                    |
| **超出限制后的费用** | 神经语音：$16/百万字符  <br>标准语音：$4/百万字符                                | 标准语音：$4/百万字符            |
### 10 分钟限制下的字数估算
考虑到语速影响，下表再砍掉25%，保守一点。

| **语音类型** | **语言** | **字数（10分钟）** | **字符数（Azure计费）** |
| -------- | ------ | ------------ | ---------------- |
| **神经语音** | 英文     | ~1,500 词     | ~30,000 字符       |
| **神经语音** | 中文     | ~3,000 字     | ~30,000 字符       |
| **标准语音** | 英文     | ~2,000 词     | ~20,000 字符       |
| **标准语音** | 中文     | ~4,000 字     | ~20,000 字符       |
### 10分钟的讲话，英文，中文平均各讲多少个字符，多少个单词，多少句子？
英文（English）
- **平均语速**：130–160 单词/分钟（一般演讲）    
- **10分钟总词数**：约 1,300 – 1,600 words    
- **平均句长**：约 15–20 个词/句    
- **句子数**：约 80 – 110 句    
- **平均字符数**：每单词约 5 个字母 + 空格，粗略计算每词 6 字符    
- **总字符数**：约 7,800 – 9,600 字符（包括空格）

中文（Chinese）
- **平均语速**：260–300 字/分钟（含停顿）    
- **10分钟总字数**：约 2,600 – 3,000 汉字    
- **平均句长**：约 10–15 字/句    
- **句子数**：约 200 – 300 句    
- **总字符数**：与总字数基本一致：约 2,600 – 3,000 字符
10分钟的讲话，总结对比表

|项目|英文估算|中文估算|
|---|---|---|
|单词/汉字数|1,300 – 1,600 words|2,600 – 3,000 汉字|
|句子数|80 – 110|200 – 300|
|字符数|7,800 – 9,600 chars|2,600 – 3,000 chars|

怎么知道用的是标准语音，还是神经语音

|**判断方式**|**神经语音（Neural TTS）**|**标准语音（Standard TTS）**|
|---|---|---|
|**语音名称**|以 `Neural` 结尾（如 `YunxiNeural`）|无 `Neural`（如 `Yunyang`）|
|**Speech Studio 分类**|标注为 "Neural"|标注为 "Standard"|
|**API/SDK 参数**|必须指定 `Neural` 语音名|使用普通语音名|
|**音质与功能**|自然、支持情感|机械、无情感|
|**价格**|$16/百万字符|$4/百万字符|
如何绕过限制？
如果免费额度不够，你可以：
✅ 拆分长文本（每次请求 <10 分钟，然后拼接 MP3）
✅ 使用标准语音（非神经）（更节省字符）
✅ 升级到付费订阅（按量计费，$16/百万字符神经语音）

### 如何查看剩余免费额度？
你可以在 **Azure 门户** 查看使用情况：
1. 登录 [Azure 门户](https://portal.azure.com/)    
2. 进入 **"Cognitive Services"** → **你的 Speech 资源**    
3. 在 **"Metrics"（指标）** 或 **"Quotas"（配额）** 查看已用字符数
## 怎么本地生成字幕？
如果你是先从文本转语音（TTS）生成配音，然后想反向生成字幕（例如给配音生成时间轴）

这可以通过以下方式实现：
1. 配音文本已经有结构（每句配音对一个字幕）
2. 用 FFmpeg 或 TTS 的时长估算每段语音的时间（或用语音对齐工具如 Gentle、aeneas）
3. 生成 SRT/VTT 格式字幕

### 实现步骤详解：
**1. 你已经有配音文本结构**
即：你不是从人说话中提取文字（STT），而是你一开始就有：
```
第1句：你好，欢迎来到我们的频道。
第2句：今天我们讲讲冷知识：猫为什么晚上很活跃？
第3句：快来猜猜正确答案是哪一个吧！
```
这些是你传给 TTS 的原始文本，每一段对应一段配音。
你只需要为每句语音生成正确的“起止时间” → 自动生成字幕。

**2. 获取每段语音的起止时间（时间轴）的方法**
你需要知道每句配音在最终合成音频中的 **起始时间 + 时长**。

方式 A：估算（TTS 合成后估算时间）
- 使用 Azure 或 Google TTS 的 **合成结果时长**（通常可以获取总音频时长） 
- 每句语音可以按字数或句子比例估算时间（如总 3 句，合成音频 9.6 秒）
```text
每句配音所占时间 ≈ 总时长 ×（该句字符数 / 总字符数）
```
✅ 优点：实现简单  
⚠️ 缺点：精度不高，特别是长句、短句混排时

方式 B：使用 **自动语音对齐工具（推荐）**
这些工具可以将音频与原始文本“对齐”，自动输出字幕时间戳：

| 工具         | 说明                                 |
| ---------- | ---------------------------------- |
| **Gentle** | 基于 Kaldi 的自动对齐工具（英文效果极佳）           |
| **aeneas** | 多语言支持的强大对齐工具，支持 SRT/VTT 导出         |
| **MFA**    | Montreal Forced Aligner，适用于多种语音学任务 |
你只需要提供：
- `配音音频（mp3/wav）`    
- `原始文本（utf-8纯文本）`  
它就会输出 `.srt` 字幕文件，每句话时间精准同步。

**3. 生成标准字幕格式（.srt / .vtt）**
工具会自动生成类似这样的内容：
```lua
1
00:00:00,000 --> 00:00:02,500
你好，欢迎来到我们的频道。

2
00:00:02,501 --> 00:00:06,000
今天我们讲讲冷知识：猫为什么晚上很活跃？

3
00:00:06,001 --> 00:00:08,000
快来猜猜正确答案是哪一个吧！
```

### 示例流程（使用 aeneas）

1. 安装：
    `pip install aeneas`

2. 准备音频 + 文本
    `your_audio.mp3 your_text.txt`

3. 执行命令（生成 SRT）
```bash
python -m aeneas.tools.execute_task \
  your_audio.mp3 \
  your_text.txt \
  "task_language=zh|is_text_type=plain|os_task_file_format=srt" \
  your_output.srt

```
### 结论：推荐方案总结

| 方法        | 精度    | 难度  | 适用场景        |
| --------- | ----- | --- | ----------- |
| 字符比例估算    | 低     | ⭐   | 快速估计，无需对齐工具 |
| aeneas    | 高     | ⭐⭐  | 多语言视频生成     |
| Gentle    | 高（英文） | ⭐⭐  | 英文配音视频      |
| Azure STT | 中     | ⭐⭐  | 有语音无文字的反向识别 |
## Azure 常用视频配音语音清单（Neural TTS）
[Microsoft TTS Demo 页面](https://speech.microsoft.com/portal/voicegallery)

| VoiceName               | 语言/区域     | 性别  | 支持风格（Style）                                          | 推荐用途           |
| ----------------------- | --------- | --- | ---------------------------------------------------- | -------------- |
| `zh-CN-XiaoxiaoNeural`  | 中文普通话     | 女   | chat, cheerfulness, narration, customer-service      | 标准中文配音、解说、情绪语气 |
| `zh-CN-YunjianNeural`   | 中文普通话     | 男   | newscast, customer-service, assistant                | 男声讲解、旁白、客服语音   |
| `zh-CN-XiaochenNeural`  | 中文普通话     | 女   | news, chat, serious                                  | 数字人女主播风格、冷知识   |
| `zh-CN-XiaohanNeural`   | 中文普通话     | 女   | chat                                                 | 轻松视频、生活方式配音    |
| `zh-CN-YunyangNeural`   | 中文普通话     | 男   | newscast                                             | 严肃旁白、历史解说      |
| `zh-HK-HiuGaaiNeural`   | 粤语（香港）    | 女   | default                                              | 粤语短视频、本地特色     |
| `zh-TW-HsiaoChenNeural` | 中文（台湾）    | 女   | default                                              | 台湾风格视频、娱乐八卦    |
| `en-US-JennyNeural`     | 英文（美音）    | 女   | newscast, assistant, cheerful, empathetic, narration | 国际通用女声，官方配音感   |
| `en-US-GuyNeural`       | 英文（美音）    | 男   | assistant, newscast, angry, excited, sad, friendly   | 动感视频、游戏旁白      |
| `en-US-DavisNeural`     | 英文（美音）    | 男   | chat, assistant                                      | 轻松男声、AI 教学     |
| `en-GB-SoniaNeural`     | 英文（英音）    | 女   | chat                                                 | 高级感、纪录片风格      |
| `ja-JP-NanamiNeural`    | 日语        | 女   | chat, customer-service                               | 二次元风格、日系内容     |
| `ko-KR-SunHiNeural`     | 韩语        | 女   | chat                                                 | 韩语娱乐、美妆相关      |
| `fr-FR-DeniseNeural`    | 法语        | 女   | cheerful                                             | 法语教学、美食内容      |
| `es-ES-ElviraNeural`    | 西班牙语（西班牙） | 女   | cheerful, chat                                       | 西语娱乐类、拉美市场内容   |
| en-US-JaneNeural        |           |     |                                                      |                |
| en-US-CoraNeural        |           |     |                                                      |                |
🧑‍🏫 英文中老年教授风格（严肃、清晰、有智慧感）

| VoiceName             | 性别  | 区域  | 风格支持                | 推荐用途            |
| --------------------- | --- | --- | ------------------- | --------------- |
| `en-US-GuyNeural`     | 男   | 美音  | newscast, assistant | 稳重专业，适合讲解、历史、逻辑 |
| **en-US-DavisNeural** | 男   | 美音  | assistant, chat     | 声音柔和但有沉稳感，适合教学类 |
| `en-GB-RyanNeural`    | 男   | 英音  | newscast            | 英伦口音教授风，适合纪录片风格 |
| `en-US-TonyNeural`    | 男   | 美音  | assistant           | 成熟感中带亲和，类似友好导师  |
| `en-US-BrandonNeural` | 男   | 美音  | default             | 有点年长感，适合慢节奏讲述类  |
### 英文少年 / 10–15 岁男孩风格

> 注意：Azure 没有标明“age=child”的官方分类，但某些语音接近儿童或少年角色，以下是实测相似度较高的：

| VoiceName                 | 性别  | 区域  | 风格支持                                 | 推荐用途              |
| ------------------------- | --- | --- | ------------------------------------ | ----------------- |
| `en-US-AIGenerate1Neural` | 男   | 美音  | chat                                 | 微微稚嫩，像青少年，适合童声问答类 |
| `en-US-AndrewNeural`      | 男   | 美音  | assistant                            | 偏少年感，有青春但不幼稚      |
| **en-US-BrianNeural**     | 男   | 美音  | newscast                             | 少年广播风，有“好学生”感觉    |
| `en-US-EricNeural`        | 男   | 美音  | default                              | 声线较细，适合青少年角色      |
| tony                      |     | 美音  | ==cheerful==,<br>excited,<br>hopeful |                   |
| jason                     |     | 美音  | cheerful,<br>excited,<br>hopeful     |                   |
|                           |     |     |                                      |                   |

🎯 适合做“童声解说”、“青少年答题”、“趣味讲堂”、“迷你剧场”等内容。

### 适合讲历史故事的微软 TTS 声音推荐

#### 中文（普通话）

| 名称                           | 样式支持               | 特点分析                      | 适合风格                        |
| ---------------------------- | ------------------ | ------------------------- | --------------------------- |
| **zh-CN-YunxiNeural**（云希）    | 多种情感样式（含“叙述者”）     | 男声，音色沉稳、清晰，节奏自然，非常适合讲述类内容 | 正剧风、史诗叙述                    |
| **zh-CN-XiaomoNeural**（晓墨）   | 支持“愉快”“叙述者”等样式     | 女声，温柔知性，语速适中，适合温情讲述类历史故事  | 女性旁白、抒情历史                   |
| **zh-CN-YunjianNeural**（云健）  | 支持“新闻播报”“叙述者” 男/中年 | 男声，音质干净，富有播报感，适合正式历史事件解说  | 历史纪录片风格。低沉浑厚，带权威感，语速平稳如史官叙事 |
| **zh-CN-XiaoxiaoNeural**（晓晓） | 多样式支持（含“叙述者”）      | 微微软标准女声，适合温和清晰的历史介绍       | 教育类历史讲述                     |
| **晓辰（女**                     | 女/青年               | 清亮婉转，擅长情感起伏，适合演绎人物对话      | 民间故事、情感历史                   |
| **晓悠（女）**                    | 女/中年               | 温和知性，带有学者气质，停顿节奏精准        | 文化解说、文物历史                   |
| **云扬（男）**                    | 男/青年               | 朝气蓬勃，语速较快，适合年轻化叙事         | 历史趣闻、少儿历史                   |
| **晓墨（女）**                    | 女/老年               | 略带沙哑的"说书人"音色，自带沧桑感        | 传统评书、传奇故事                   |
|                              |                    |                           |                             |
- 云健（中）↔ Davis（英） （权威感配对）    
- 晓悠（中）↔ Jenny（英） （学者型配对）

| 场景          | 中文配音                                          | 英文配音                                                                  |
| ----------- | --------------------------------------------- | --------------------------------------------------------------------- |
| 正史类（帝王将相）   | 云希 + `narration`<br>云健（主叙） + 晓辰（人物对话）/ 晓墨（引文） | Guy + `narration-professional`<br>Davis（主叙） + Andrew（引述史料）            |
| 女性视角（后宫、佳人） | 晓墨 + `affectionate`                           | Jenny + `narration-relaxed`<br>Aria/Jenny（主） + Amber（细节描写）/Andrew（史料） |
| 纪录片风格       | 云健 + `newscast`                               | Aria + `documentary-narration`                                        |
| 教育类，文化类     | 晓晓 + 默认<br>晓悠（学者视角） + 云扬（补充提问                 | Davis + 默认                                                            |
| 儿童故事        | 晓辰（中）                                         | Amber（英）                                                              |
#### 英文（美式英语）

| 名称                    | 样式支持                           | 特点分析                                            | 适合风格                |
| --------------------- | ------------------------------ | ----------------------------------------------- | ------------------- |
| **en-US-GuyNeural**   | 支持“narration-professional”叙述样式 | 男声，沉稳低音，有历史纪录片旁白感                               | 史诗/战争类历史故事          |
| **en-US-JennyNeural** | 支持“narration-relaxed”样式 女/中年   | 女声，自然、亲切，语音细腻，适合轻柔的历史叙述。中性知性声线，语速适中，专业名词发音清晰    | 女性视角、温情历史。 文化比较、艺术史 |
| **en-US-AriaNeural**  | 多种情感样式（含narration）女/青年         | 女声，中性偏亲切，语速适中，表达清晰。 优雅的BBC播音腔，元音饱满，适合英式历史叙事\|\| | 通用历史解说。欧洲史、古典文学     |
| **en-US-DavisNeural** | 男/中年 标准男声，音色厚实，清晰有力            | 虽然无专属narration样式，但基础素质优秀。美式新闻主播声线，权威感强，重音处理突出   | 正史讲解、课堂内容。战争史、政治史   |
| **Amber (Female)**    | 女/青年                           | 柔和亲切，带故事讲述的呼吸感                                  | 民间传说、人物传记           |
| **Andrew (Male)**     | 男/老年                           | 沙哑低沉的"老教授"音色，适合怀旧叙事                             | 口述史、回忆录             |
|                       |                                |                                                 |                     |

## 🔍 建议搭配使用的样式标签（Style）

微软 TTS 支持 **Style + Role** 标签（SSML语法），建议为历史故事配音时使用：
- `style="narration"` （叙述类）    
- `style="documentary-narration"` （纪录片叙述，英文可用）    
- `style="newscast"` （官方播报感，适合正式历史事件）    
- `style="affectionate"` / `style="calm"`（柔情版本，适合回忆体）
### 我的选择
#### 英文男
 
| 语音名称            | 历史故事                                       | 脑洞故事                | 儿童故事                                  | 爱情故事                           | 声音特点 & 适用理由                                       |
| --------------- | ------------------------------------------ | ------------------- | ------------------------------------- | ------------------------------ | ------------------------------------------------- |
| **GuyNeural**   |                                            | friendly            | chat, cheerful, polite                | hopeful, friendly<br>speed 0.9 | 语音自然亲切，情感丰富，故事感强，适合多种故事类型。<br>温暖细腻型（适合温馨浪漫）       |
| **DavisNeural** | Default                                    | chat,               | chat, cheerful, ==friendly==          |                                | 稳重温和，细腻感情，适合悬疑、细节丰富的历史及脑洞故事。<br>深情成熟型（适合催泪或深刻情感）  |
| Davis Multi     | Default,<br>==Funny==, ==Empathetic king== | ==empathetic==      |                                       | Empathetic                     |                                                   |
| **JasonNeural** |                                            | friendly            | chat, cheerful,<br>excited            | friendly,                      | 年轻活泼，适合轻松幽默的脑洞故事和儿童故事，历史故事较轻松。<br>轻松甜蜜型（适合青春喜剧爱情） |
| **TonyNeural**  | friendly, ==hopeful读中文名字==                 | Default, friendly   | chat, polite,<br>cheerful,<br>excited | hopeful,                       | 成熟沉稳，有磁性，适合悬疑、权威感强的历史和脑洞故事。<br>戏剧浓烈型（适合跌宕爱情）      |
| Andrew          | Default读中文名字                               | ==Default==         |                                       |                                |                                                   |
| Andrew Multi    | Default                                    | Empathetic          |                                       | Empathetic speed 0.9           |                                                   |
| Derek mullti    |                                            | Default, Empathetic |                                       | default                        | 偏正统                                               |
|                 |                                            |                     |                                       |                                |                                                   |

- **GuyNeural** 是最“全能”的声音，适合故事感强、情感丰富的讲述。    
- **DavisNeural** 偏温情悬疑和细腻叙事。    
- **JasonNeural** 活力轻松，适合幽默风格。    
- **TonyNeural** 稳重成熟，适合庄重权威类内容。
#### 英文女
 
| 语音名称            | 爱情故事                             | 纯情温柔                 | 儿童故事                                                                                                               | 历史故事                 | 技术               | 备注                      |
| --------------- | -------------------------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------ | -------------------- | ---------------- | ----------------------- |
| **AriaNeural**  | empathetic, polite, hopeful      | Whisper<br>speed -15 | chat, cheerful, polite, narration - professional, newcast casual, friendly<br>speed 0.9<br>pitch -5<br>emotion 1.5 | empathetic           |                  | 明亮富有感情，适合情感丰富的故事。声音略尖   |
| **JessaNeural** | empathetic, polite, sad          |                      | chat, cheerful                                                                                                     |                      |                  | 温柔细腻，适合浪漫和温馨故事          |
| **JennyNeural** |                                  | Whisper<br>speed -15 | chat, ==cheerful==<br>speed 0.9<br>pitch -5<br>emotion 1.5                                                         | chat,<br>pitch -5    | chat<br>pitch +5 | 亲切自然，情感均衡，适合大众化故事       |
| **CoraNeural**  | default<br>2 strong              |                      | chat, cheerful                                                                                                     |                      |                  | 柔和亲和，适合儿童故事和温情爱情故事      |
| Cora Multi      |                                  |                      |                                                                                                                    | default,<br>pitch -5 |                  |                         |
| **JaneNeural**  | empathetic, polite, hopeful      |                      | chat, cheerful, ==friendly==                                                                                       |                      |                  | 语音清晰明亮，适合温情和积极向上的故事     |
| **SaraNeural**  | ==friendly==                     |                      | ==friendly, cheerful==, chat,<br>speed 1.15                                                                        |                      |                  | 温暖柔和，情感细腻，适合细腻爱情故事和儿童故事 |
| phoebe multi    | ==Empathetic==<br>==1.5 strong== |                      |                                                                                                                    |                      |                  |                         |
| Serena Multi中性  | Empathetic<br>1.5 strong         |                      |                                                                                                                    |                      |                  |                         |
| Nancy Mullti    |                                  |                      | Funny                                                                                                              |                      |                  |                         |

#### 中文男
 
| 语音名称              | 历史故事                                                                                                                          | 脑洞故事                             | 儿童故事           | 爱情故事                 | 声音特点与适用理由                                           |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------- | -------------------------------- | -------------- | -------------------- | --------------------------------------------------- |
| **YunfengNeural** | Default, ==Serious==                                                                                                          | Default, cheerful,               | chat, cheerful |                      | 沉稳亲和，情感丰富，适合多场景多风格。                                 |
| **YunjianNeural** | newscast, <br>polite, empathetic, <br>==speed 1.25 narration==<br><br>==帝王第一人称，感情强度最大, documentary narrator, <br>speed 1.15== | Default, ==Narration - relaxed== | chat, cheerful | sad                  | 音质自然清晰，适合多场景情感表达。成熟浪漫型（适合深情旁白）                      |
| **YunzeNeural**   | newscast, <br>polite, empathetic<br>==old senior, <br>感情强度最大, narrator/calm, speed -5 关羽==                                    | Default<br>==Calm==              | chat, cheerful | sad                  | 声音温暖，富感染力，适合多种故事风格。沉静感伤型（适合遗憾/回忆类）                  |
| **YunyangNeural** | newscast, <br>polite<br>==narrator==                                                                                          |                                  | chat, cheerful |                      | 语调自然流畅，适合轻松和正式场景。<br>阳光温暖型（适合甜蜜浪漫），正式，播音腔，有磁性，不够生活化 |
| **YunyeNeural**   | newscast, <br>polite<br>==calm/serious==                                                                                      | ==cheerful==,                    | cheerful       | sad                  | 音质柔和亲切，适合温情和儿童故事。<br>轻松治愈型（适合温馨日常）.更适合解说            |
| **YunxiNeural**   | newscast, <br>Narration - relaxed                                                                                             | ==Narration - relaxed,==         | cheerful       | Narration-relax,<br> | 声音富有表现力，适合多样情感需求。<br>青春纯爱型（适合校园/初恋）。过于大陆货           |

简要说明：
- **YunfengNeural**：全能型，声音沉稳自然，情感丰富，适合所有三类故事。    
- **KangkangNeural**：声音更年轻活泼，脑洞和儿童故事表现佳，历史故事适合轻松叙述。    
- **HuihuiNeural**：声音温和，适合细腻和情感丰富的历史与儿童故事。    
- **HaoranNeural**：较沉稳有权威感，适合严肃历史和悬疑脑洞，儿童故事使用较活泼styles。

推荐Styles简介：
- **chat**：口语化，亲切自然，适合讲故事。    
- **cheerful**：欢快明朗，适合儿童和轻松故事。    
- **newscast**：正式、权威，适合历史故事。    
- **empathetic**：富有情感，适合情感细腻的故事。    
- **polite**：礼貌温和，适合温馨故事和儿童故事。

#### 中文女
 
| 语音名称               | 讲爱情故事                           | 纯情温柔                         | 儿童故事                               | 历史故事                                                                          | 声音特点与适用理由                 |
| ------------------ | ------------------------------- | ---------------------------- | ---------------------------------- | ----------------------------------------------------------------------------- | ------------------------- |
| **XiaoxiaoNeural** | polite, hopeful                 | ==Whisper, speed 0.8 (-20)== | ==default==,chat, cheerful, polite |                                                                               | 甜美亲切，声音年轻，适合甜美温馨的爱情及儿童故事。 |
| xiaoxiao multi     | ==empathetic 1.5<br>speed 1.1== |                              |                                    |                                                                               |                           |
| **XiaomoNeural**   | empathetic, polite, hopeful     |                              | default, cheerful, polite          | newscast,<br>calm,<br>                                                        | 声音柔和细腻，情感丰富，适合温馨浪漫和儿童故事。  |
| **XiaoyouNeural**  | empathetic, polite              |                              | chat, cheerful                     |                                                                               | 童声，亲切自然，适合大众化爱情和儿童故事。     |
| xiaoshuang         |                                 |                              | chat                               |                                                                               | 童声                        |
| xiaoyan            |                                 |                              | default                            |                                                                               | 生活化                       |
| xiaoqiu            |                                 |                              |                                    | default                                                                       | 中年女性，略严肃                  |
| xiaozhen           |                                 |                              |                                    | 皇家<br>default<br>==serious==,<br>pitch -8<br><br>无奈<br>==sad==<br>pitch<br>-5 |                           |
| xiaoyi             |                                 |                              | ==cheerful==                       |                                                                               |                           |
 
# References 

[Google Speech-to-Text](https://cloud.google.com/speech-to-text/docs?hl=zh-cn)
[Azure Speech to text](https://learn.microsoft.com/zh-cn/azure/ai-services/speech-service/index-speech-to-text)