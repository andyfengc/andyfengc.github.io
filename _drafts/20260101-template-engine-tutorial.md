# Introduction
| 特性 / 引擎              | Scriban | Handlebars.Net | Razor | Mustache/DotLiquid | SmartFormat.NET |
| -------------------- | ------- | -------------- | ----- | ------------------ | --------------- |
| 免费                   | ✅       | ✅              | ✅     | ✅                  | ✅               |
| 安全（无任意 C# 执行）        | ✅       | ✅              | ❌     | ✅                  | ✅               |
| 变量替换                 | ✅       | ✅              | ✅     | ✅                  | ✅               |
| 条件语句                 | ✅       | ✅              | ✅     | ⚠️                 | ✅               |
| 循环                   | ✅       | ✅              | ✅     | ⚠️                 | ✅               |
| 嵌套模板                 | ✅       | ✅              | ✅     | ✅                  | ⚠️              |
| JSON / Dictionary 驱动 | ⭐⭐⭐⭐    | ⭐⭐⭐            | ⭐⭐    | ⭐⭐                 | ⭐⭐⭐             |
| 易学程度                 | ⭐⭐⭐⭐    | ⭐⭐⭐            | ⭐     | ⭐⭐⭐                | ⭐⭐⭐             |
| 扩展能力（自定义函数）          | ✅       | ✅（helpers）     | ✅     | ❌                  | ✅               |
| 适合 AI Prompt / 文案    | ✅       | ⭐⭐⭐            | ⭐⭐    | ⭐⭐                 | ✅               |
| HTML 输出友好            | ⭐       | ⭐              | ✅     | ⭐                  | ⭐               |
top 3

| 场景 / 指标                | Scriban | Handlebars.Net | SmartFormat.NET |
| ---------------------- | ------- | -------------- | --------------- |
| AI 文案 / Prompt         | ⭐⭐⭐⭐    | ⭐⭐⭐            | ⭐⭐⭐⭐            |
| 安全性                    | ⭐⭐⭐⭐    | ⭐⭐⭐⭐           | ⭐⭐⭐⭐            |
| JSON / Dictionary 自然绑定 | ⭐⭐⭐⭐    | ⭐⭐⭐            | ⭐⭐⭐             |
| 条件 / 循环支持              | ✅       | ✅              | ✅               |
| 扩展性（自定义函数）             | ✅       | ✅              | ✅               |
| 学习成本                   | ⭐⭐⭐⭐    | ⭐⭐⭐            | ⭐⭐⭐⭐            |
| 模板复杂度上限                | 高       | 中              | 中               |
| 最适合用户可修改模板             | ✅       | ⚠️             | ✅               |
# SmartFormat.NET
最接近c#的插值语法。
能直接识别你 JSON 对象中的属性名，并自动替换。
例子：
假设你从 JSON 中读取了数据并存入一个 Dictionary 或 Dynamic 对象：
```csharp
using SmartFormat;

// 1. 你的模版（支持换行，建议存入数据库的 TEXT 字段）
string template = @"你是一个{channel_direction}频道的编剧，
请根据以下主题和提纲生成完整视频文案：

主题：{topic}
提纲：{outline}";

// 2. 模拟从 UI 获取的用户输入数据
var data = new Dictionary<string, string> {
    { "channel_direction", "年轻美女英语金曲" },
    { "topic", "2024最火英文歌" },
    { "outline", "1. 开场热舞\n2. 歌曲串烧\n3. 互动点赞" }
};

// 3. 一行代码完成替换
string result = Smart.Format(template, data);

Console.WriteLine(result);
```
# Scriban
如果你的需求变复杂了，比如：“如果提纲为空，就不显示提纲这一行”，原生替换做不到，这时候用 Scriban：
```csharp
using Scriban;

var template = Template.Parse(@"
你是一个{{ channel_direction }}频道的编剧。
主题：{{ topic }}
{{ if outline }}提纲：{{ outline }}{{ end }}
");

var result = template.Render(new { 
    channel_direction = "英语", 
    topic = "Hello", 
    outline = "" // 为空时，上面的 if 会自动隐藏提纲行
});
```
由于你有 `fields` 的元数据配置（JSON），你可以设计一个 **TemplateManager** 服务，将数据验证与替换结合起来：
推荐的 C# 处理流程：
1. **解析元数据**：读取 JSON 配置，生成前端 UI 界面（输入框等）。    
2. **收集输入**：用户填写后，得到一个 `Dictionary<string, object>`。    
3. **校验**：根据 JSON 里的 `"required": true` 遍历字典，确保所有必填项都有值。    
4. **渲染**：调用 `Smart.Format` 或 `Scriban` 将字典数据塞入模版。

## 安装

# Handlebars.Net
