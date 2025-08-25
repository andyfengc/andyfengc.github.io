---
layout: post
title: Text to Speech T TTS Tips
author: Andy Feng
---
使用 TTS（Text-to-Speech）语音引擎（如 Microsoft Speech SDK 或 Azure Speech Service）进行语音合成时，有很多实用技巧可以提升语音质量、自然度和交互体验。
# 文本优化
避免太长的句子，否则语音听起来像“喘不过气”，拆分成段落或句子再合成。
使用标点（句号、逗号、问号）有助于自然断句。 
## **控制句子结构**

**结构本身影响自然度**，比如长句拆短句、逻辑连接明确、不要堆砌。
## 标点符号控制语音节奏
在 TTS 系统（尤其是微软 TTS）中，**标点符号会被用来影响断句、语调、停顿**，如果使用得当，可以让语音更自然流畅。
各种标点的作用说明：

|标点符号|含义与 TTS 行为|
|---|---|
|`。` or `.`|明确句子结束，加入适当停顿、语调下行|
|`，` or `,`|轻微停顿，用于分割短语|
|`？` or `?`|表示疑问句，语调上扬，带停顿|
|`！` or `!`|表示感叹句，语调上扬或强调|
|`…`|暗示思考或延续，会引起拉长语调|
|`；`|中等强度的停顿，常用于复杂句|
|`：`|引出内容，有提示性停顿|
|`（）`|括号内语气会变弱或变快，读法偏附加说明|
|`“”`|引号内的内容可能语调上有强调|
### 实用技巧：
- ✅ **优化断句**：不要把一整段话放在一句中，要合理使用句号和逗号切割。    
- ✅ **控制停顿节奏**：有时标点停顿不够，可以用 SSML 的 `<break time="Xms"/>` 明确控制。    
- ✅ **避免标点缺失**：没有标点的长文本会导致语音连续、难以理解，体验很差。
    
💡 **示例对比**：
不自然（无标点）：
> 你好欢迎来到语音演示现在我们开始吧

自然（添加标点）：
> 你好，欢迎来到语音演示。现在，我们开始吧！
# 使用 SSML（Speech Synthesis Markup Language）（语音合成标记语言）

几乎所有主流 TTS 都支持 SSML。它是发音控制的标准语言。
控制发音技巧一览

| 控制类型  | 技术方式                  | 是否标准 SSML   | 说明                |
| ----- | --------------------- | ----------- | ----------------- |
| 停顿    | `<break>`             | ✅ 是         | 控制停顿时间            |
| 语速    | `<prosody rate="">`   | ✅ 是         | fast / slow / xx% |
| 音调    | `<prosody pitch="">`  | ✅ 是         | 控制语调上下            |
| 音量    | `<prosody volume="">` | ✅ 是         | 控制声音大小            |
| 强调    | `<emphasis>`          | ✅ 是         | 增加朗读强度            |
| 语言切换  | `<lang>`              | ✅ 是         | 多语言支持             |
| 发音控制  | `<phoneme>`           | ✅ 是         | IPA / SAPI 音标     |
| 说话人   | `<voice>`             | ✅ 是         | 指定不同 voice        |
| 自定义词典 | Lexicon               | ❌ 不是标准 SSML | 引擎特有，但实用          |
| 情绪表达  | `<mstts:express-as>`  | ❌ 微软扩展      | 情感语调（Neural）      |
SSML 是控制语调、语速、停顿、发音等的强大工具。常用的 SSML 标签有：
- `<prosody rate="x-slow|slow|medium|fast|x-fast" volume="loud|soft|silent">`：控制语速、音量。    
- `<break time="500ms"/>`：插入停顿。    
- `<emphasis>`：增加重读。    
- `<lang xml:lang="en-US">`：切换语言。    
- `<phoneme ph="..." alphabet="ipa">`：指定发音。    
- `<voice name="...">`：指定说话人（用于 Azure 多语音）。
例如
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="zh-CN">
  <voice name="zh-CN-XiaoxiaoNeural">
    <prosody rate="medium" pitch="+0%">
      欢迎使用微软语音合成引擎。
      <break time="300ms"/>
      现在开始演示。
    </prosody>
  </voice>
</speak>
```
## 控制语速 / 音调 / 音量
不同语速适合不同应用：
- 教育类、播音：中等语速。    
- 辅助阅读（盲人等）：可适当提高语速。    
- 故事讲述：较慢语速，更有节奏。
用 `<prosody>` 标签来控制：
```xml
<prosody rate="slow" pitch="+10%" volume="loud">
    你好，欢迎来到语音合成的世界。
</prosody>
```
## 明确插入停顿
使用 `<break time="300ms"/>` 插入自定义停顿，精度到毫秒：
```xml
欢迎使用语音合成。
<break time="500ms"/>
我们现在开始。
```
## **强调词语**
使用 `<emphasis>` 强调某些关键词：
```xml
<emphasis level="strong">非常重要</emphasis> 的内容。
```
## 语言切换（多语种场景）
使用 `<lang xml:lang="en-US">...</lang>` 切换语种，配合多语音角色：
```xml
<lang xml:lang="en-US">Hello</lang>，你好。
```
## **控制说话人（Voice）**

指定特定语音角色（如不同性别、语调）：
```xml
<voice name="zh-CN-XiaoxiaoNeural">你好</voice>
<voice name="en-US-GuyNeural">Hello</voice>
```
支持将多个语言的语音在一段内容中混合（Azure 支持更佳）。
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis">
  <voice name="zh-CN-XiaoxiaoNeural">现在我将说一句英语：</voice>
  <voice name="en-US-JennyNeural">Hello, how are you?</voice>
</speak>
```
## **利用情感语音（仅限支持的引擎）**

如微软的 `<mstts:express-as>` 支持表达情绪（需要 Neural Voice）：
```xml
<mstts:express-as style="cheerful">
    今天的天气真好！
</mstts:express-as>
```
微软支持的样式有很多，例如：
- cheerful（高兴）    
- sad（悲伤）    
- angry（生气）    
- excited（激动）    
- embarrassed（尴尬）   

Eleven Labs 也用类似机制，但语义驱动更多。
## 使用自定义词典（Lexicon）
|问题|解决方案|
|---|---|
|标点无效或停顿不自然|使用逗号、句号等断句，必要时加 `<break time="300ms"/>`|
|非标准词无法正确发音|使用 `<phoneme>` 明确指定发音（支持 IPA）|
|想要更个性化控制|使用 Azure 自定义词典（Lexicon）功能|
解决非标准词/外来词/拟声词/专业词汇发音问题（如“ACHOO”）
TTS 默认使用词典 + 发音规则来发音，因此遇到非标准词时：
- 有时 **会直接拼字母读出来**（如 “A-CH-O-O”）    
- 或 **发音不准确**
### 使用 SSML 中的 `<phoneme>` 标签
你可以使用 `<phoneme>` 指定 **国际音标（IPA）** 或 **简易音素（Alphabet="sapi"）** 来告诉引擎如何发音。
示例：让 “ACHOO” 正确地发成“阿嚏！”
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-JennyNeural">
    When someone sneezes, they usually say 
    <phoneme alphabet="ipa" ph="əˈtʃuː">ACHOO</phoneme>!
  </voice>
</speak>
```
说明：
- `alphabet="ipa"` 表示使用国际音标    
- `ph="əˈtʃuː"` 是“阿嚏”的发音（可根据语言微调）

你也可以用中文的发音方式发英文词
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="zh-CN">
  <voice name="zh-CN-XiaoxiaoNeural">
    有人打喷嚏时会说 
    <phoneme alphabet="ipa" ph="ɑˈtʰu">阿嚏</phoneme>。
  </voice>
</speak>
```
#### 如何获取正确的 IPA？
你可以使用如下方法：
1. https://youglish.com/（听发音）    
2. https://www.phonemica.net/（中文）    
3. https://tophonetics.com/（将英文句子转换为 IPA）    
4. 查字典如 Oxford, Cambridge 提供的发音。
### 自定义语音词典（高级技巧）
下面是针对Azure的。
如果你使用 **Azure TTS**，可以上传自己的“发音词典”（Lexicon 文件，格式为 XML Pronunciation Lexicon Specification），用于替代系统默认发音。这对于大量术语、品牌名、缩写等特别有用。

自定义语音词典（**Custom Lexicon**）是 **Azure Speech Service** 的一项功能，用于精确控制特定词汇的发音，非常适合以下情况：
- 专业术语或品牌名（如 “ChatGPT” 不读作“查特-吉皮提”）    
- 缩写或首字母缩略词（如 “AI” 应读作 “A-I”）    
- 拟声词（如 “ACHOO”）    
- 地名、人名、外语词汇等特殊发音
#### 收费吗？
**不单独收费**，属于 Azure Speech Service 的免费功能的一部分，只要你使用语音合成功能（TTS），就可以使用 Lexicon。
不过请注意：
- 语音合成本身是**按字符计费**的（你每合成一次，按文字长度计费）。    
- 上传 Lexicon 文件不额外收费。    
- 自定义语音模型（Neural Voice）是 **另一项高级功能，是需要申请并可能收费的**。
#### 怎么制作？
在 Azure TTS（Text-to-Speech）服务中使用自定义词典（Lexicon）时，支持以下 **两种音标系统**：
1. **IPA**（International Phonetic Alphabet）
- 这是最广泛使用的国际音标系统。    
- `alphabet="ipa"` 
```xml
<lexeme>
  <grapheme>example</grapheme>
  <phoneme alphabet="ipa">ɪɡˈzæmpəl</phoneme>
</lexeme>
```
1. **SAPI**（Microsoft Speech API phonetic alphabet）
- 这是微软自家定义的发音标注系统，用于 Windows TTS 系统。    
- `alphabet="sapi"`
```xml
<lexeme>
  <grapheme>example</grapheme>
  <phoneme alphabet="sapi">ih g z ae m p ah l</phoneme>
</lexeme>
```

注意事项：
- 在 `lexicon` 根元素上，你应设置默认音标系统，例如：    
```xml
<lexicon alphabet="ipa" ... >
```
这意味着该文档中所有 `<phoneme>` 默认是 IPA，如果你单独在 `<phoneme>` 上指定了 `alphabet` 属性，也会生效。    
- Azure 不支持其他音标格式，如：    
    - **ARPAbet**（虽然常见于 CMU dictionary）        
    - **XSAMPA**
### 如何使用 Azure 的自定义语音词典（Lexicon）
[Azure AI Speech Studio](https://speech.microsoft.com/portal)

|特性|是否收费|
|---|---|
|上传 lexicon|❌ 免费|
|使用 lexicon 合成语音|✅ 按正常 TTS 计费，无额外费用|
|支持格式|PLS（XML）|
|上传方式|CLI、SDK、REST API|
|应用场景|控制术语、外语词、缩写、拟声词发音|
##### 前提准备
1. 注册 Azure 账号并启用 Speech 服务  [https://portal.azure.com](https://portal.azure.com)
2. 获取你的 Speech 资源的：    
    - **Region**（区域）        
    - **Subscription Key**（密钥）
##### 创建 Lexicon 文件
Azure 支持使用 **PLS（Pronunciation Lexicon Specification）** 格式，标准 XML 文件。
示例：lexicon.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<lexicon version="1.0"
         xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
         alphabet="ipa"
         xml:lang="en-US">
  <lexeme>
    <grapheme>ChatGPT</grapheme>
    <pronunciation>tʃæt dʒiː piː tiː</pronunciation>
  </lexeme>
  <lexeme>
    <grapheme>ACHOO</grapheme>
    <pronunciation>əˈtʃuː</pronunciation>
  </lexeme>
</lexicon>
```
- `grapheme`: 原始文本    
- `pronunciation`: IPA 发音（或可以用 `alphabet="sapi"`）    
- `xml:lang`: 设置语言，如 zh-CN, en-US

#### Web端Speech Studio 验证词典
登录 Azure portal > 创建一个 Azure AI Services resource > **Speech Studio** > audio content creation
![](images/posts/Pasted%20image%2020250807053936.png)
Select **New** > **Lexicon File**, 上传 and start authoring.

![](images/posts/Pasted%20image%2020250807061421.png)
![](images/posts/Pasted%20image%2020250807061551.png)
![](images/posts/Pasted%20image%2020250807061456.png)
![](images/posts/Pasted%20image%2020250807062008.png)
验证成功后，可以下载，上传到blob获得url，然后供调用
#### 编程上传词典到 Azure，已失效
202508. 微软似乎已经**移除关于 Lexicon 上传与管理的相关接口**。
你可以使用 Azure CLI、REST API 或 SDK 上传 Lexicon。
使用 Azure CLI：
```bash
az cognitiveservices account deployment lexicon create \
  --name YourSpeechResourceName \
  --resource-group YourResourceGroup \
  --name my-lexicon \
  --file lexicon.xml \
  --locale en-US
```
#### 获得url，为使用做准备
##### 默认lexicon放在自己azure账号下面。测试失败！
通过 Speech Studio 上传的 Lexicon 文件，其实是上传到了你的语音服务资源内部的词典列表中，不是公开的 URL 地址，所以它并不像你上传到 Blob Storage 一样可以直接访问 URL。
你不需要通过 URL 来使用，而是直接通过 Lexicon 的名称在 SSML 中引用。
示例 SSML 使用上传的 Lexicon：
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
       xmlns:mstts="http://www.w3.org/2001/mstts"
       xml:lang="en-US">
  <lexicon uri="lexicon:lexicon-en"/>
  <voice name="en-US-AriaNeural">
    <prosody rate="medium" pitch="default">
      Hello ChatGPT and ACHOO.
    </prosody>
  </voice>
</speak>
```
说明：

| 字段           | 说明                                     |
| ------------ | -------------------------------------- |
| `lexicon-en` | 是你上传 lexicon 时使用的文件名（不要加 .xml）         |
| `lexicon:`   | 表示使用的是 **本地 TTS 引擎注册的 lexicon**，不是 URL |
##### 可以设成公共存储 URI，只能用azure blob storage设成静态资源
[https://learn.microsoft.com/en-us/microsoft-copilot-studio/voice-custom-lexicon?utm_source=chatgpt.com#store-the-lexicon-file](https://learn.microsoft.com/en-us/microsoft-copilot-studio/voice-custom-lexicon?utm_source=chatgpt.com#store-the-lexicon-file)
登录 Azure 门户(https://portal.azure.com) >  **Create Resource** 
![](images/posts/Pasted%20image%2020250807072348.png)
Type "storage" in the search bar and select **Storage account**.
![](images/posts/Pasted%20image%2020250807072421.png)
![](images/posts/Pasted%20image%2020250807072505.png)
输入storage account 
-> associate the resource to an Azure subscription and resource group 
-> **Primary Service** select **Azure Blob Storage or Azure Data Lake Storage Gen 2** and turn on **Standard** performance.
-> 等待创建完成
![](images/posts/Pasted%20image%2020250807073224.png)
![](images/posts/Pasted%20image%2020250807073029.png)
![](images/posts/Pasted%20image%2020250807073411.png)
![](images/posts/Pasted%20image%2020250807073628.png)
打开resource > overview > **Capabilities** >  应该显示 “not configured.”
-> 点击enable。 You then receive primary and secondary endpoints for the website.

![](images/posts/Pasted%20image%2020250807073854.png)
![](images/posts/Pasted%20image%2020250807074116.png)

![](images/posts/Pasted%20image%2020250807074812.png)

![](images/posts/Pasted%20image%2020250807090111.png)
Data Storage > Containers > Select `$web` and upload your lexicon file
![](images/posts/Pasted%20image%2020250807090213.png)

select the file from directory and view the properties and details of the file.
-> 获得 “URL” for the file
-> Content-Type 改成 application/pls+xml
![](images/posts/Pasted%20image%2020250807090316.png)
![](images/posts/Pasted%20image%2020250807090528.png)
url类似：
https://{resourceName}.blob.core.windows./net/\$web/{lexiconFileName}
然后在 SSML 中使用公开 URI 引用词典即可，例如：
```xml
<lexicon uri="https://<your-storage-account>.blob.core.windows.net/$web/my-lexicon.xml"/>
```
~~安全考虑，levicon不支持第三方静态地址。~~
经测试，支持。

如果url无法访问，开启匿名权限。
1. 登录 Azure Portal。    
2. 打开你的存储账户（如：`myaccountname`）。    
3. 在左侧菜单中点击 **“配置（Configuration）”**。
4. 找到 **“允许 blob 公共访问（Allow blob public access）”**。    
5. 将其设置为：**启用（Enabled）**。    
6. 点击保存。
![](images/posts/Pasted%20image%2020250807090833.png)
- 回到 Azure Portal，进入你的 **存储账户**。    
- 左侧点击 **“容器（Containers）”**。    
- 找到你的容器（你上传 lexicon 的那个，可能是 `$web`，或者你创建的其他容器）。    
- 点击进入该容器。    
- 顶部点击 **“更改访问级别（Change access level）”**。    
- 弹窗中选择：   
    - ✅ **“容器（Container）”**，允许匿名读取 blob 和容器数据。        
- 点击 **“保存”**。
![](images/posts/Pasted%20image%2020250807091017.png)
如果词典没生效，有几个原因
1. 词典被首次加载后会缓存，15 分钟内不刷新。期间如果再次上传同url的词典，需要等待缓存过期才能看到更新后的效果 
2. 将原文的词改成全小写，比如achoo, 不要大写。
#### 在语音合成时启用 Lexicon
使用 REST API 或 SDK 发起语音合成时，在请求中加上 lexicon 参考：
示例（REST API 中）：
```xml
<speak version='1.0' xml:lang='en-US'
       xmlns='http://www.w3.org/2001/10/synthesis'
       xmlns:mstts='http://www.w3.org/2001/mstts'
       xmlns:lex='http://www.w3.org/2005/01/pronunciation-lexicon'>
  <lexicon uri="https://<region>.tts.speech.microsoft.com/cognitiveservices/lexicons/my-lexicon"/>
  <voice name='en-US-JennyNeural'>
    Let's talk about ChatGPT and how to say ACHOO!
  </voice>
</speak>
```
⚠️ 注意：
- `uri` 地址是你上传的 lexicon 在你资源下的访问地址。    
- 每次合成时都需要“引用词典”。# 优化语音引擎
注意事项

| 限制   | 说明                       |
| ---- | ------------------------ |
| 文件大小 | 单个 lexicon 文件不能超过 512 KB |
| 数量限制 | 每个语音资源最多 20 个 lexicon 文件 |
| 语言   | Lexicon 文件需和语音的语言一致      |
| 格式   | 支持 IPA（国际音标）和 SAPI 音素表   |
🛠️ 示例用 C# SDK 语音合成并使用 Lexicon
```csharp
var config = SpeechConfig.FromSubscription("YourKey", "YourRegion");

// 添加 lexicon URL
config.SetProperty("SpeechServiceResponse_Lexicons", "https://<your-region>.tts.speech.microsoft.com/cognitiveservices/lexicons/my-lexicon");

using var synthesizer = new SpeechSynthesizer(config);
var result = await synthesizer.SpeakTextAsync("Let's talk about ChatGPT and ACHOO!");
```

#### 其他
##### 我是用api来调用微软语音api的合成语音的，可否在所有调用中都加上lexicon 和自定义的引用词典？ 如果voice里面的内容其实并没有用到自定义词典，会有问题吗？
**可以**在所有 API 调用中统一添加 Lexicon（词典）
你完全可以在所有合成语音的请求中都**默认附加**自定义词典，无需每次判断内容是否真的用了词典里的词。
📌 **微软官方支持这样的做法**，理由如下：

|项目|描述|
|---|---|
|✅ 安全|加载 lexicon 是非破坏性的。它只是扩展了 TTS 引擎可识别的发音库|
|✅ 性能影响极小|Lexicon 文件较小（KB 级），系统会缓存，不会显著影响速度|
|✅ 灵活性|即使当前内容没用上词典，也不会出错、不会异常|
|✅ 可维护性|每次请求都加载 lexicon，可避免遗漏词典导致发音错误|
示例（REST API 请求中附带 lexicon）
```http
POST https://<region>.tts.speech.microsoft.com/cognitiveservices/v1
Ocp-Apim-Subscription-Key: YOUR_KEY
Content-Type: application/ssml+xml
X-Microsoft-OutputFormat: audio-16khz-32kbitrate-mono-mp3

<speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis'
       xmlns:lex='http://www.w3.org/2005/01/pronunciation-lexicon'
       xml:lang='en-US'>
  <lexicon uri='https://<region>.tts.speech.microsoft.com/cognitiveservices/lexicons/my-lexicon'/>
  <voice name='en-US-JennyNeural'>
    Hello, this is a test of the speech engine.
  </voice>
</speak>
```
即使 `my-lexicon` 中的词 **没有在这次文本中出现**，也完全 ✅ 没有问题。
⚠️ 注意事项

|项目|注意点|
|---|---|
|Lexicon 文件必须与语音语言一致|如果你用的是 `zh-CN-XiaoxiaoNeural`，词典必须是 `zh-CN` 的|
|不能写错 URI|错误的 lexicon URI 会导致整个请求失败（HTTP 4xx/5xx）|
|数量限制|每个语音资源最多上传 20 个词典（不影响调用，但要管理好）|
Bonus：如果你希望“只在需要时”加载词典？
你也可以在你的文本分析逻辑中预先判断是否包含特定关键词，然后动态选择是否加入 `<lexicon>` 标签。但这往往会增加复杂性，而收益不大。
##### 自定义词典能不能混合中文和英文？也就是grapheme写不同语言版本，但是 pronunciation用标准的发音？ 
比如
```xml
<lexeme> <grapheme>这里写中文或英文</grapheme> <pronunciation>tʃæt dʒiː piː tiː</pronunciation> </lexeme>
```
在 `<grapheme>` 中写中英文任意词，而 `<pronunciation>` 提供对应标准 IPA 发音——**是可以的，但需满足一定条件。**
> 是的，**微软语音词典（Lexicon）支持中文和英文混合**，只要满足语言、发音格式等技术要求，你完全可以用中文或英文写 `grapheme`，并为其提供标准的 `<pronunciation>`（IPA 或 SAPI 音素）。

示例 1：中文词条 + 英文发音
如果你希望中文“聊天GPT”发音为“Chat G P T”：
```xml
<lexeme>
  <grapheme>聊天GPT</grapheme>
  <pronunciation>tʃæt dʒiː piː tiː</pronunciation>
</lexeme>
```
示例 2：英文词条 + 中文发音（让 "Achoo" 发作 “阿嚏”）
```xml
<lexeme>
  <grapheme>ACHOO</grapheme>
  <pronunciation>ɑˈtʰu</pronunciation>
</lexeme>
```
示例 3：中英文混写词条（比如“AI技术”）
```xml
<lexeme>
  <grapheme>AI技术</grapheme>
  <pronunciation>eɪ aɪ tɕi˥˩ ʃu˥˩</pronunciation> <!-- 混合英文+中文IPA -->
</lexeme>
```
注意这个例子是 **极限用法**，可能不被所有语音引擎完全支持（尤其是混合IPA语言符号）。你可能需要拆开成多个 `lexeme` 来分别处理中英文段落。

关键点：语言必须一致
每个 Lexicon 文件（即词典 XML）必须声明**一个统一语言（xml:lang）**，如：
```xml
<lexicon ... xml:lang="zh-CN">
```
这意味着：
- 你只能用 **一个 Lexicon 文件** 来服务 **一种语音语言**。    
- 想同时服务 `zh-CN` 和 `en-US` 两种语音？→ 需要分别上传两个 Lexicon 文件。

💡 技巧推荐
✔️ 方案 A：**“中文音标”读英文单词**
如果你希望“中文语音”读出英文缩写（比如“AI”），你可以创建中文 lexicon：
```xml
<lexicon xml:lang="zh-CN" ...>
  <lexeme>
    <grapheme>AI</grapheme>
    <pronunciation>eɪ aɪ</pronunciation>
  </lexeme>
</lexicon>
```
并在合成时用中文语音合成器，比如 `zh-CN-XiaoxiaoNeural`，它会读得非常接近自然英文。

不推荐：不同语言强行混入同一个文件
比如你用 `xml:lang="zh-CN"`，但 `<grapheme>` 是英文，且 `<pronunciation>` 用英文IPA，这种做法在**Azure TTS 中通常可行**，但不保证长期支持，建议按语言分词典管理。

总结

|问题|答案|
|---|---|
|Grapheme 可以是中文或英文吗？|✅ 可以|
|Pronunciation 可以是 IPA 或 SAPI 音标吗？|✅ 支持|
|同一个词典可以中英文混用吗？|✅ 可以混写词条，但整个词典的 `xml:lang` 必须统一|
|不同语言词典怎么办？|❗ 分别上传不同 `xml:lang` 的 Lexicon 文件|
|会报错吗？|只要语言标注正确，不会报错；混语言 IPA 要小心|

## **使用 Neural Voice**
相比传统 TTS，Neural Voice 使用深度学习合成，效果接近人类。
- 语调、节奏更自然。    
- 可以使用自定义语音模型（付费功能）。
# 其他
## **Audio Format 设置**
支持输出多种格式，如 `audio-16khz-32kbitrate-mono-mp3`、`riff-16khz-16bit-mono-pcm`，可根据设备要求选用。
## 语音开发

| 项目    | 建议                   |
| ----- | -------------------- |
| 特殊词   | 使用 `<phoneme>` 指定发音  |
| 多次播放  | 缓存音频，避免重复合成          |
| 合成速度慢 | Azure 使用并发队列或本地合成加速  |
| 文件存储  | 使用 WAV 或 MP3 保存，方便回放 |

# Azure 
# FAQ
## Windows 本地更换语音
Windows 默认语音可能比较单一，你可以安装更多微软语音包：
- 设置 → 时间和语言 → 语言 → 添加语言 → 下载语音。    
- 常见高质量语音如：`Microsoft Xiaoxiao Desktop - Chinese (Mainland)`。
你可以通过代码列出可用语音：
```powershell
Add-Type –AssemblyName System.Speech
$synth = New-Object System.Speech.Synthesis.SpeechSynthesizer
$synth.GetInstalledVoices() | ForEach-Object {
    $_.VoiceInfo.Name
}
```
## 什么是IPA?
IPA，全称 **International Phonetic Alphabet（国际音标）**，是一套专门为**表示世界上所有语言的语音**而设计的标准音标系统。
它由 **国际语音学学会（International Phonetic Association）** 制定，旨在提供一种统一的、准确的方式来记录发音。
- 一种**精确表示语言发音**的音标系统。    
- 每个符号对应一个**特定的语音**（音素），不受拼写或语言差异的影响。    
- 常用于：    
    - 语言学研究        
    - 外语教学（如英语教材中的发音标注）        
    - 语音合成（TTS）和语音识别系统
例子

| 单词      | 拼写      | IPA       | 发音近似   |
| ------- | ------- | --------- | ------ |
| cat     | cat     | /kæt/     | “卡特”   |
| machine | machine | /məˈʃiːn/ | “么-希因” |
| thought | thought | /θɔːt/    | “索特”   |
| ACHOO   | achoo   | /əˈtʃuː/  | 阿-啾    |
### IPA 的组成：
IPA 包括多种类别的音标，主要分为：
✅ 辅音音标（Consonants）：
如 /p/, /b/, /t/, /d/, /k/, /g/, /f/, /v/, /s/, /z/, /ʃ/, /ʒ/, /tʃ/, /dʒ/, /θ/, /ð/, /h/, /m/, /n/, /ŋ/, /l/, /r/, /j/, /w/
✅ 元音音标（Vowels）：
如 /iː/, /ɪ/, /e/, /æ/, /ʌ/, /ə/, /ɑː/, /ɒ/, /ɔː/, /ʊ/, /uː/, /eɪ/, /aɪ/, /ɔɪ/, /aʊ/, /əʊ/, /ɪə/, /eə/, /ʊə/
✅ 特殊符号：
- 重音标记：`ˈ` 表示主重音，如 `/əˈtʃuː/    
- 次重音：`ˌ`    
- 长音：`ː`，表示元音延长，如 `/uː/`
在 Azure TTS 的自定义词典中使用 IPA，可以精准控制某些词语的发音：
```xml
<lexeme>
  <grapheme>ChatGPT</grapheme>
  <phoneme>tʃæt dʒiː piː tiː</phoneme>
</lexeme>
```
## 有没有免费的自定义语音词典下载?
目前网上公开可用的 **免费自定义语音词典（Lexicon）下载资源非常少**，主要原因是：
1. **Lexicon 文件具有语音引擎依赖性**（如微软用 PLS 格式，语音模型要匹配）    
2. **发音设计具有语言/地区/词汇特定性**，很难通用    
3. **版权限制**：一些专业词典（如医学、品牌名、AI术语）受限于许可使用 
不过，有以下几类思路和资源，你可以用于构建或参考自己的自定义词典：

|方式|是否免费|推荐程度|
|---|---|---|
|自定义生成|✅ 免费|⭐⭐⭐⭐⭐|
|CMU 词典|✅ 免费|⭐⭐⭐⭐|
|开源多语词典|✅ 免费|⭐⭐⭐|
|GitHub 词典仓库|✅ 免费|⭐⭐|
|微软官方示例|✅ 免费|⭐（很小）|
### ChatGPT 自动生成（最实用）

根据常见缩写、拟声词、品牌名、中英混合词，**批量生成标准的 Lexicon（PLS）文件**，免费、高质量、定制。
```xml
<lexeme>
  <grapheme>AI</grapheme>
  <pronunciation>eɪ aɪ</pronunciation>
</lexeme>
<lexeme>
  <grapheme>OpenAI</grapheme>
  <pronunciation>ˌoʊpən ˈeɪˈaɪ</pronunciation>
</lexeme>
<lexeme>
  <grapheme>你好GPT</grapheme>
  <pronunciation>ni˥˩ xaʊ˧ tʃiː piː tiː</pronunciation>
</lexeme>
```
发给chatgpt一份“词条清单”，可以自动生成符合格式的完整词典。

你只需给我以下内容：
- 词条列表（中英文混合都行）    
- 是否指定发音（如不指定，我可以查词典或推测）    
- 输出语言（zh-CN、en-US 等）    
我可以：
- 🚀 自动生成合法的 Lexicon（PLS）    
- ⚙️ 分语言整理成多个文件    
- 💡 提供可直接上传 Azure 的版本
### 开源 IPA 词典（可转换成 Lexicon）
| 资源                                                                         | 类型     | 下载地址 / 方法              |
| -------------------------------------------------------------------------- | ------ | ---------------------- |
| [CMU Pronouncing Dictionary](http://www.speech.cs.cmu.edu/cgi-bin/cmudict) | 英文     | 免费，格式为 ARPAbet，可转为 IPA |
| [CC-CEDICT](https://www.mdbg.net/chinese/dictionary?page=cedict)           | 中文词典   | 提供拼音，但无 IPA，可转         |
| [UniDict](https://github.com/unimorph/ud-lexicons)                         | 多语种    | 支持超过 100 种语言           |
| [IPA Chart Examples](https://www.ipachart.com/)                            | IPA 参考 | 非词典，但可用于设计发音           |
| [Wiktionary dump](https://dumps.wikimedia.org/)                            | 开放词条   | 包含部分 IPA 发音，可提取        |
你可以写脚本将这些词条转为 PLS 格式 lexicon。
### 微软官方示例词典（文档中提供）
微软在其 官方 Lexicon 文档 中有一个简短的示例 XML 文件可参考（但不是完整大词库）：
```
<?xml version="1.0" encoding="utf-8"?>
<lexicon version="1.0"
         xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
         alphabet="ipa"
         xml:lang="en-US">
  <lexeme>
    <grapheme>Contoso</grapheme>
    <phoneme>kənˈtoʊsoʊ</phoneme>
  </lexeme>
</lexicon>
```
### GitHub 社区资源（小众但有用）
- 搜索关键词：`lexicon file`, `custom pronunciation xml`, `plsdict`, `TTS lexicon`    
示例仓库：
- [`Azure-TTS-Lexicon`](https://github.com/lilfire/azure-tts-lexicon)：基础词条模板    
- [`ipa-dict`](https://github.com/open-dict-data/ipa-dict)：包含数万条英文 IPA 发音
# References 

**Pronunciation with SSML（发音控制与 SSML）**：介绍如何使用 `<phoneme>` 元素和 **Custom Lexicon** 来提升发音精准度。[Microsoft Learn](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/speech-synthesis-markup-pronunciation?utm_source=chatgpt.com)
**Create custom lexicons（创建自定义词典）**：通过 Azure Copilot Studio（Speech Studio）创建、保存和使用 Lexicon 文件的指南，包括如何上传及引用它们。[Microsoft Learn](https://learn.microsoft.com/en-us/microsoft-copilot-studio/voice-custom-lexicon?utm_source=chatgpt.com)
[Create custom lexicons](https://learn.microsoft.com/en-us/microsoft-copilot-studio/voice-custom-lexicon?utm_source=chatgpt.com)
[Pronunciation with SSML](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/speech-synthesis-markup-pronunciation#custom-lexicon-examples)
[Text to speech REST API](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/rest-text-to-speech?tabs=streaming#get-a-list-of-voices)