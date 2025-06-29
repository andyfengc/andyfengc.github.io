---
layout: post
title: Markdown Tutorial
author: Andy Feng
---
# Introduction #
Markdown 是一种轻量级标记语言，由 John Gruber 于 2004 年创建，旨在实现"易读易写"的纯文本格式编写，同时可以转换成有效的 HTML。
markdown让我们专注于文章内容，而不是关注排版
Markdown注重文本内容、精简格式化标签的理念，因此文本特效神马的，能不用就尽量不用，这些统统扔给CSS去解决，我们只关注文本内容 
# Markdown 基础语法
## 标题
```
# Headline 1
## Headline 2
### Headline 3
#### Headline 4
##### Headline 5
###### Headline 6
```
## 段落与换行
- 段落：空一行分隔段落。在两段之间加一行空行，Markdown就会为文本分段。
- 换行：行尾加两个空格或使用 `<br>` 标签
```
这是第一段（后面有两个空格）  
这是强制换行

这是新段落
```
## 强调
```
*斜体* 或 _斜体_
**粗体** 或 __粗体__
***粗斜体*** 或 ___粗斜体___
~~删除线~~
```
*斜体* 或 _斜体_
**粗体** 或 __粗体__
***粗斜体*** 或 ___粗斜体___
~~删除线~~
## 列表
无序列表 - 星号*、加号+和减号-来表示无序列表
```
- 项目1
- 项目2
  - 子项目（缩进2空格或1个tab）
* 也可以使用星号
+ 或加号
```
- 项目1
- 项目2
  - 子项目（缩进2空格或1个tab）
* 也可以使用星号
+ 或加号

有序列表 - 数字加一个英文句点
```
1. 第一项
2. 第二项
3. 第三项
```
1. 第一项
2. 第二项
3. 第三项
## 链接与图片
```
[链接文字](URL "可选标题")

![图片替代文字](图片URL "可选标题")
```
### 链接 
Markdown支持行内和参考两种形式的链接语法，两种都是使用中括号来把文字转成链接。
1. 行内形式是中括号包围文字，后面紧跟圆括号包围的链接，其代码如下所示：
	This is an [example link](http://example.com/).
2. 参考形式的链接可以在原文中为链接定义一个名称，然后你可以在文章的其他地方定义该链接的内容。
	以下代码用于定义链接名称，语法格式为：[链接文本][链接名称]
用<>包括的URL或邮箱地址会被自动转换成为超链接
<[http://example.com/](http://example.com/)>
<user@example.com>
### 图片
图片的语法格式和链接类似，也分为行内形式和参考形式。
行内形式语法格式为：
```
![alt text](URL title)
```
alt text和title可以选择性加入。实例代码如下：
```
![额，图片不能显示了！](Markdown_basic_files/Markdown_basic_1_0.png"正弦和余弦")
```

参考形式分为两部分：声明图片链接名称和定义图片链接。
其中，声明图片链接语法格式为
```
![alt text][id]
```
定义图片链接内容的语法格式为
```
[id]:URL "title"
```
## 代码
行内代码：`` `代码` ``

代码块：
```语言名称
多行代码
```
### **代码块语言名称** 列表
**主流编程语言**
 
|语言名称|别名/扩展名|示例|
|---|---|---|
|`python`|`py`|Python 代码|
|`javascript`|`js`|JavaScript 代码|
|`typescript`|`ts`|TypeScript 代码|
|`java`|-|Java 代码|
|`c`|-|C 语言代码|
|`cpp`|`c++`|C++ 代码|
|`csharp`|`cs`|C# 代码|
|`go`|`golang`|Go 代码|
|`ruby`|`rb`|Ruby 代码|
|`rust`|`rs`|Rust 代码|
|`swift`|-|Swift 代码|
|`kotlin`|`kt`|Kotlin 代码|
|`php`|-|PHP 代码|
**脚本与标记语言**

|语言名称|别名/扩展名|示例|
|---|---|---|
|`bash`|`sh`, `zsh`|Shell 脚本|
|`sql`|-|SQL 查询|
|`html`|-|HTML 标记|
|`css`|-|CSS 样式|
|`xml`|-|XML 数据|
|`json`|-|JSON 数据|
|`yaml`|`yml`|YAML 配置|
|`markdown`|`md`|Markdown 文本|
**数据科学与工具**

|语言名称|别名/扩展名|示例|
|---|---|---|
|`r`|-|R 语言代码|
|`julia`|`jl`|Julia 代码|
|`matlab`|-|MATLAB 代码|
|`dockerfile`|`docker`|Dockerfile|
|`makefile`|`mk`|Makefile|
**模板与配置**

| 语言名称     | 别名/扩展名       | 示例        |
| -------- | ------------ | --------- |
| `ini`    | -            | INI 配置文件  |
| `toml`   | -            | TOML 配置   |
| `nginx`  | -            | Nginx 配置  |
| `apache` | `apacheconf` | Apache 配置 |
**特殊格式**

| 语言名称        | 别名/扩展名   | 示例          |
| ----------- | -------- | ----------- |
| `diff`      | -        | Git Diff 输出 |
| `regex`     | `regexp` | 正则表达式       |
| `latex`     | `tex`    | LaTeX 公式    |
| `mermaid`   | -        | Mermaid 图表  |
| `plaintext` | `text`   | 纯文本（无高亮）    |
## 引用
Markdown使用email的区块引用方式，即右尖括号>跟后面的引用内容
```
> 这是引用内容
>> 嵌套引用
```
> 这是引用内容
>> 嵌套引用
## 分割线
```
---
或
***
或
___
```
---
或
***
或
___
## 表格
```
| 左对齐 | 居中对齐 | 右对齐 |
|:-------|:-------:|-------:|
| 单元格 | 单元格  | 单元格 |
| 单元格 | 单元格  | 单元格 |
```

| 左对齐 | 居中对齐 | 右对齐 |
| :-- | :--: | --: |
| 单元格 | 单元格  | 单元格 |
| 单元格 | 单元格  | 单元格 |
## 任务列表
```
- [x] 已完成任务
- [ ] 未完成任务
```
- [x] 已完成任务
- [ ] 未完成任务
# 扩展语法（CommonMark/GFM）
## 脚注
```
这是一个脚注示例[^note]

[^note]: 这是脚注内容
```
这是一个脚注示例[^note]

[^note]: 这是脚注内容
## 定义列表
```
术语1
: 定义1

术语2
: 定义2
```
## 高亮
```
==高亮文本==
```
==高亮文本==
## 上标和下标
```
H~2~O 和 x^2^
```
# Markdown 高级技巧
## 内容目录
```
[TOC]  # 多数工具支持自动生成
```
[TOC]  # 多数工具支持自动生成
## 注释
```
<!-- 这是注释，不会显示 -->
```
## 内嵌 HTML
Markdown基本语法不支持任意更改颜色的功能。Markdown的理念是——注重文本本身，特效神马的交给CSS搞定。当然了，既然这货支持HTML嵌套，我们可以利用HTML标记实现更改颜色的需求
```
<div style="color:red;">
  这是 <span style="font-weight:bold;">HTML</span> 内容
</div>
```
<div style="color:red;">
  这是 <span style="font-weight:bold;">HTML</span> 内容
</div>
```
<font color='red'>Red Color</font>
<font color='blue'>Blue Color</font>
<font color='green'>Green Color</font>
<font color='yellow'>Yellow Color</font>
<font color='pink'>Pink Color</font>
<font color='purple'>Purple Color</font>
<font color='orange'>Orange Color</font>
```
<font color='red'>Red Color</font>
<font color='blue'>Blue Color</font>
<font color='green'>Green Color</font>
<font color='yellow'>Yellow Color</font>
<font color='pink'>Pink Color</font>
<font color='purple'>Purple Color</font>
<font color='orange'>Orange Color</font>
换字号、字体也可以用HTML轻松实现了。以下代码可显示换字号、字体功能。
```
<font size='-2'>Small Size</font>
Normal Size
<font size='+2'>Big Size</font>
<font size='+2' face='Times'>Time New Roman</font>
```
<font size='-2'>Small Size</font>
Normal Size
<font size='+2'>Big Size</font>
<font size='+2' face='Times'>Time New Roman</font>
嵌入这些格式化字体的HTML标签后，整个文本显得臃肿了不少。这有悖于Markdown注重文本内容、精简格式化标签的理念，因此文本特效神马的，能不用就尽量不用，这些统统扔给CSS去解决，我们只关注文本内容。
## 数学公式 (LaTeX)
```
行内公式: $E=mc^2$
块级公式:
$$
\int_a^b f(x)dx
$$
```
行内公式: $E=mc^2$
块级公式:
$$
\int_a^b f(x)dx
$$

# Markdown 工具Editors
**Markdown 编辑器对比**

| 工具名称         | 特点               | 优点                     | 缺点             | 收费情况      | 平台支持            |
| ------------ | ---------------- | ---------------------- | -------------- | --------- | --------------- |
| **Typora**   | 所见即所得，实时预览       | 界面简洁，支持LaTeX/图表        | 正式版需付费         | $15 一次性购买 | Win/macOS/Linux |
| **VS Code**  | 代码编辑器+Markdown插件 | 免费，插件生态丰富，Git集成        | 需要配置插件         | 完全免费      | 跨平台             |
| **Obsidian** | 本地知识管理，双向链接      | 支持Mermaid/LaTeX，插件扩展性强 | 移动端同步需付费       | 免费（付费同步）  | 跨平台             |
| **Notion**   | 在线协作，数据库集成       | 多平台同步，模板丰富             | 高级功能需付费，离线功能有限 | 免费+付费计划   | Web/桌面/移动       |
| **Zettlr**   | 开源学术写作工具         | 支持Citeproc引用，PDF导出     | 界面较复杂          | 完全免费      | Win/macOS/Linux |
| **MarkText** | 开源替代Typora       | 轻量级，支持表格编辑             | 更新频率低          | 完全免费      | Win/macOS/Linux |
**Markdown 转换工具对比**

|工具名称|特点|优点|缺点|收费情况|输入/输出格式|
|---|---|---|---|---|---|
|**Pandoc**|万能文档转换器|支持50+格式转换，学术写作友好|命令行操作门槛高|完全免费|MD↔HTML/PDF/Word/ePub等|
|**Marked 2**|macOS预览增强工具|实时预览，支持自定义CSS|仅限macOS|$14.99|MD→HTML/PDF|
|**GitBook**|文档出版平台|自动化排版，团队协作|云服务依赖性强|免费+付费计划|MD→网站/PDF/ePub|
|**MkDocs**|静态站点生成器|主题丰富，适合技术文档|需要Python环境|完全免费|MD→静态网站|
|**Docsify**|实时文档生成|零配置，直接渲染MD为网页|SEO支持较弱|完全免费|
**Markdown 在线服务对比**

|服务名称|特点|优点|缺点|收费情况|特色功能|
|---|---|---|---|---|---|
|**StackEdit**|在线实时编辑器|支持GitHub同步，导出格式丰富|广告较多|免费+付费去广告|离线模式|
|**Dillinger**|简洁在线编辑器|实时预览，支持导入导出|功能较基础|完全免费|直接导出PDF|
|**GitHub**|代码托管平台原生支持|版本控制集成，GFM语法扩展|编辑体验较简单|完全免费|Issue/Wiki支持|
|**Notion**|一体化工作区|数据库+文档联动，移动端优秀|国内访问不稳定|免费+付费计划|多维表格集成|
|**HackMD**|协作笔记平台|实时多人编辑，支持LaTeX|中文支持一般|免费+付费计划|课堂/会议协作|
**快速选择指南**

1. **个人写作**：    
    - 离线优先：Typora（付费）或 MarkText（免费）        
    - 知识管理：Obsidian（免费）        
2. **技术团队**：    
    - 文档站点：MkDocs（免费） + GitHub Pages        
    - 协作编辑：HackMD（免费基础版）        
3. **格式转换**：    
    - 简单转换：Marked 2（macOS）        
    - 复杂需求：Pandoc（全平台）        
4. **在线应急**：    
    - 基础编辑：StackEdit        
    - 即时分享：Dillinger
# Markdown 最佳实践
1. **文件命名**: 使用小写字母、连字符分隔，如 `getting-started.md`    
2. **行长限制**: 建议每行不超过80个字符（代码块除外）    
3. **一致性**: 项目内保持相同的 Markdown 风格    
4. **版本控制**: 与 Git 完美配合    
5. **文档结构**: 合理使用标题层级
# Obsidian

快捷键

| 快捷键            | 功能    |
| -------------- | ----- |
| `Ctrl/Cmd + B` | 加粗    |
| `Ctrl/Cmd + I` | 斜体    |
| `Ctrl/Cmd + L  | 插入链接  |
| `Ctrl/Cmd + D` | 删除当前行 |
**导航与视图**

| 快捷键                    | 功能        |
| ---------------------- | --------- |
| `Ctrl/Cmd + O`         | 快速切换笔记    |
| `Ctrl/Cmd + F`         | 当前笔记内搜索   |
| `Ctrl/Cmd + Shift + F` | 全局搜索      |
| Ctrl/Cmd + E           | 切换编辑/预览模式 |

# FAQ
## Markdown 的局限性
1. 复杂布局支持有限    
2. 不同实现有语法差异    
3. 缺少标准化样式控制    
4. 表格编辑体验较差
## Markdown显示video
用html
```html
<a href="https://cl.ly/3b0S25113Z2i" target="_blank"><img src="https://cl.ly/0W0N1G0W362r/Image%202018-01-30%20at%209.58.21%20PM.png" alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>

[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/YOUTUBE_VIDEO_ID_HERE/0.jpg)](http://www.youtube.com/watch?v=YOUTUBE_VIDEO_ID_HERE)

<iframe width="420" height="315" src="http://www.youtube.com/embed/dQw4w9WgXcQ" frameborder="0" allowfullscreen></iframe>
```
# References #

https://zhuanlan.zhihu.com/p/428519519

https://publish.obsidian.md/chinesehelp/01+2021%E6%96%B0%E6%95%99%E7%A8%8B/2021%E5%B9%B4%E6%96%B0%E6%95%99%E7%A8%8B

https://publish.obsidian.md/chinesehelp/01+2021%E6%96%B0%E6%95%99%E7%A8%8B/obsidian%E6%96%B0%E6%89%8B%E4%B8%8D%E5%AE%8C%E5%85%A8%E6%8C%87%E5%8D%97+by+windily

https://pkmer.cn/Pkmer-Docs/10-obsidian/obsidian%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8obsidian%E7%AC%94%E8%AE%B0-%E4%B8%80%E6%AD%A5%E4%B8%80%E6%AD%A5%E7%9A%84%E6%8C%87%E5%8D%97/

https://pkmer.cn/Pkmer-Docs/02-%E7%9F%A5%E8%AF%86%E7%AE%A1%E7%90%86%E5%9F%BA%E7%A1%80/markdown/obsidian%E6%89%A9%E5%B1%95%E8%AF%AD%E6%B3%95/

https://sspai.com/post/67476

