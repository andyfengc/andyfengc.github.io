# Introduction
Fiddler 是一款广泛使用的网络嗅探和调试工具。
**Fiddler Classic**：适合个人开发者和小型项目，永久免费，但功能和支持有限。
**Fiddler Everywhere**：适合需要跨平台支持和高级功能的用户，提供试用和多种订阅计划。

## ## 网络嗅探工具比较（抓包 + 导出文件）

### **Wireshark**
- 免费、开源，支持 Windows。    
- 能捕获所有网络流量，并用 **过滤规则**（比如 `http && frame contains ".webp"`) 来筛选请求。 
- **不足**：Wireshark 默认不会自动把文件下载出来，需要写 Lua 脚本或者用第三方插件/后处理脚本把 `.webp` 数据流重组导出。
### **Fiddler Classic / Everywhere**
- Windows 下经典工具，能直接解密 HTTPS。    
- 支持 **AutoResponder** 脚本，你可以写规则：如果 URL 包含 `.webp`，就把响应体保存到指定目录。
### **Charles Proxy**
- 跨平台，功能和 Fiddler 类似。    
- 可以通过导出规则，把 `.webp` 文件的响应自动保存。   
- Charles 支持 **Java 插件**（因为 Charles 是 Java 程序，插件机制是基于 JVM 的）
- 插件可以拦截会话请求/响应、访问响应体，然后做复杂处理，比如保存文件、调用外部程序。   
- 官方文档里称为 **Charles External Tools / Extensions**，需要自己写 Java 代码，编译成 `.jar`，放到 Charles 的扩展目录。
- 缺点：Charles 是收费软件（试用 30 天）。

## Fiddler classic vs everywhere

| 维度     | Fiddler Classic                                            | Fiddler Everywhere                                              |
| ------ | ---------------------------------------------------------- | --------------------------------------------------------------- |
| 定位与状态  | 经典版，功能稳定，官方已不再“active development”（维护为主）                   | 现行主线产品，持续更新                                                     |
| 操作系统   | 仅 Windows                                                  | 跨平台（Windows / macOS / Linux）                                    |
| 价格/授权  | 免费                                                         | 需登录账号，提供试用/订阅；企业版支持 SSO                                         |
| 脚本与自动化 | **FiddlerScript（JScript.NET）**，可写代码、访问文件系统、做复杂自动化          | **Rules（条件+动作）**，图形化/声明式，不支持运行自定义代码或直接访问文件系统                    |
| 规则体系   | 通过脚本（`OnBeforeRequest/OnBeforeResponse` 等）实现规则             | 内置 Rules 面板（条件匹配、设置/替换头、改响应、Mock 等），支持与 Classic 相同的“字符串字面量”匹配写法 |
| 协议/现代性 | 经典 HTTP(S)/WebSocket；更新节奏慢                                 | 官方文档强调对 **HTTP/2 / TLS 1.3** 等现代协议与场景的适配                        |
| 扩展/插件  | 有 .NET 扩展模型与历史插件生态                                         | 无脚本/扩展 SDK，聚焦内置功能与工作流                                           |
| 协作与分享  | 以本地 .SAZ 为主                                                | 内置**保存、分享、协同**（会话、规则等工作流更友好）                                    |
| 界面与体验  | 传统 WinForms UI, up to 4.x，不支持.net core                     | 统一的现代 UI；内置工作区、规则、Composer、证书向导等                                |
| 适用场景   | 需要**深度定制/自动化**（如你现在的 JScript.NET 保存文件、调用自定义程序、与 WPF 进程通信等） | 需要**跨平台、团队协作、低门槛改包/Mock**、对新协议与安全栈更友好                           |
## Fiddler vs mitmproxy
| 功能          | **Fiddler**                                                                       | **mitmproxy**                                                 |
| ----------- | --------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| 开发语言        | C# 编写，.NET 生态，扩展需用 .NET 语言（C#、VB.NET 等）                                           | Python 编写，支持用 Python 写脚本扩展规则                                  |
| 跨平台         | Fiddler classic - Windows<br>Fiddler Everywhere - Windows/ macOS/Linux 版，但功能比经典版少 | Windows / macOS / Linux 全平台                                   |
| GUI 界面      | Fiddler Classic 有成熟 GUI，Fiddler Everywhere 有现代 UI                                 | 自带 TUI（命令行界面），也有 `mitmweb` 提供 Web 界面                          |
| HTTPS 支持    | 支持，通过 FiddlerRoot 证书拦截 HTTPS                                                      | 完全支持，自动生成 CA 证书，可拦截/解密 HTTPS 流量                               |
| **抓包方式**    | GUI 界面，点点鼠标就能过滤、修改请求                                                              | 交互式 CLI + Web UI，偏命令行，脚本驱动                                    |
| 读取 JSON     | 支持 JSON 高亮、树状查看，但脚本处理上较弱，需要用 .NET 扩展                                              | 内置 JSON 解析，支持流量面板中格式化 JSON 请求/响应，脚本里也能 `flow.response.json()` |
| 嗅探功能（流量捕获）  | 可捕获 HTTP(S)，WebSocket，HTTP/2 支持不完善                                                | 强大，可以捕获 HTTP(S)、WebSocket、HTTP/2、gRPC（实验性）等                   |
| **规则定制**    | 内置 FiddlerScript（C# 脚本）扩展                                                         | Python 脚本 API（功能极强大）                                          |
| **修改请求/响应** | 支持，通过 UI 或规则                                                                      | 支持，可实时拦截、修改，写 Python 逻辑更灵活                                    |
| **自动化**     | 比较弱，主要靠 FiddlerScript                                                             | 很强，可以写完整的 Python 插件/脚本做自动化                                    |
| 脚本/自动化      | 主要通过 Rules 菜单或 C# 插件，自动化能力不如 mitmproxy 灵活                                         | Python 脚本灵活强大，能写自定义代理逻辑（改包、转发、模拟 API）                         |
| 典型用户        | 测试人员、QA、Windows 用户                                                                | 开发者、爬虫工程师、安全测试人员                                              |
| **流量回放**    | 有 Replay，但较简单                                                                     | 支持 Flow 保存/回放、批量模拟请求                                          |
| 学习曲线        | 上手快（点点菜单即可）                                                                       | 稍微陡峭（需要懂 Python）                                              |
| **生态/扩展性**  | 插件相对少，主要是内置功能                                                                     | 完全开放 API，Python 扩展无限制<br>大量 Python 插件，可以像写爬虫一样操作流量            |
| **性能**      | 对日常调试足够                                                                           | 高并发更好，可做测试代理、压力测试场景                                           |
- 如果你是 **开发/测试人员** → **Fiddler 更适合**，因为 UI 直观，配置简单。    
- 如果你是 **安全研究/流量分析/需要自动化** → **mitmproxy 更强大**，灵活性高，但学习成本大。    
- HTTPS：两者都支持，但 **mitmproxy 在证书和加密层更强**，能玩更底层的 TLS。
- **如果你会写 Python**：选 **mitmproxy**，因为它扩展性强，能写脚本批量修改、分析 JSON、自动化嗅探，非常适合做爬虫、接口调试、自动化测试。    
- **如果你想要 GUI 简单点点就用**：选 **Fiddler**，特别是 Windows 用户，上手快，抓包+调试够用。
### 脚本代码对比
分别用 **mitmproxy (Python)** 和 **Fiddler (C#)** 实现一个简单功能：
**功能目标：** 拦截 HTTP(S) 请求 → 如果返回的是 JSON，就打印或修改内容。
mitmproxy 示例（Python）
```python
# 保存为 json_sniffer.py
# 运行： mitmproxy -s json_sniffer.py
from mitmproxy import http
import json

def response(flow: http.HTTPFlow):
    # 判断是否为 JSON 响应
    if "application/json" in flow.response.headers.get("content-type", ""):
        try:
            data = json.loads(flow.response.text)
            print("捕获到 JSON:", data)

            # 修改 JSON 内容示例
            data["intercepted"] = True
            flow.response.text = json.dumps(data, indent=2, ensure_ascii=False)
        except Exception as e:
            print("解析失败:", e)
```
**优点：**
- Python 脚本，代码简洁    
- JSON 读取/修改原生支持    
- 支持 HTTPS 解密（安装 CA 证书即可）

Fiddler 示例（C# - FiddlerScript）
Fiddler 本身运行在 .NET/C#，通过 **FiddlerScript** 来扩展。
```csharp
// 在 FiddlerScriptRules.cs 中修改
import System;
import System.Windows.Forms;
import Fiddler;
using System.Web.Script.Serialization;

public static RulesOption("拦截 JSON 并修改")
BindPref("fiddlerscript.rules.jsonsniffer")
var m_JSONSniffer: boolean = false;

static function OnBeforeResponse(oSession: Session) {
    if (m_JSONSniffer && oSession.oResponse.headers.ExistsAndContains("Content-Type", "application/json")) {
        var body = oSession.GetResponseBodyAsString();

        try {
            var serializer = new JavaScriptSerializer();
            var data = serializer.DeserializeObject(body) as Dictionary<String, Object>;

            // 打印 JSON
            FiddlerApplication.Log.LogString("捕获到 JSON: " + body);

            // 修改 JSON
            data["intercepted"] = true;
            var newBody = serializer.Serialize(data);

            oSession.utilSetResponseBody(newBody);
        }
        catch (e) {
            FiddlerApplication.Log.LogString("解析失败: " + e.Message);
        }
    }
}
```
# Install fiddler classic
程序目录路径
```
C:\Users\<你的用户名>\AppData\Local\Programs\Fiddler\
```
脚本目录路径
```
C:\Users\<你的用户名>\Documents\Fiddler2\Scripts
```
Fiddler Classic 默认运行在 **.NET Framework 4.x** 环境。
# 使用Fiddler classic

![](images/posts/20250831-10.jpeg)
- **AppContainer**：Windows 从 Win8 开始引入的一种安全沙箱，用于运行“沉浸式应用”（UWP 应用，比如 Microsoft Edge、商店应用）。    
- **问题**：这些 AppContainer 应用的流量不会自动被 Fiddler Classic 抓到，因为它们默认不能使用系统代理。    
- **解决方法**：Fiddler 提供一个 `WinConfig` 工具（在 Fiddler 工具栏上），可以让你勾选哪些 AppContainer 应用允许通过 Fiddler 的代理，从而实现抓包。
- - 点击工具栏上的 **WinConfig** 按钮。    
- 在弹出的窗口中勾选 **Microsoft Edge** 或者你需要抓的 UWP 应用。    
- 确认并保存 → 重新启动 Fiddler。
## 安装fiddler
![](images/posts/Pasted%20image%2020250831022722.png)

退出
![](images/posts/20250830-Fiddler-Tutorial-23.jpeg)
安装dlls，拷贝到fiddler安装目录

![](images/posts/20250830-Fiddler-Tutorial-21.jpeg)
fiddler老款现在基本没有维护。 
偶尔h会因为它截获https网络数据太狠，造成网络卡住 
如果碰到这个情况，重启fiddler，或者重启电脑就好了。

![](images/posts/20250831-7.jpeg)
## 安装证书
- **看到 CONNECT 443** = Fiddler 没解密 HTTPS    
- **解决方法** = 打开 HTTPS 解密并信任 Fiddler 根证书

options > https > 打开 decrypt https traffic
![](images/posts/20250830-Fiddler-Tutorial-13.jpeg)
安装 Fiddler 的根证书：    
- 点击 **Actions → Trust Root Certificate**        
- Windows 会提示安装，允许即可     
⚠️ 安装证书后，Fiddler 才能解密 HTTPS 流量，否则你只能看到 `CONNECT 443`，响应体无法抓取
![](images/posts/20250830-Fiddler-Tutorial-12.jpeg)
可以手动导出证书，安装到chrome等外部程序
![](images/posts/20250830-Fiddler-Tutorial-14.jpeg)
![](images/posts/20250830-Fiddler-Tutorial-22.jpeg)
- 在 Chrome 地址栏输入 `chrome://settings/security` → 搜索 “证书管理”    
- 确保 Fiddler 根证书被系统信任    
- 如果没找到，就import证书
- 需要重启 Chrome 才生效
chrome settings > security > certificates > 确认看到下面2个证书
![](images/posts/20250830-Fiddler-Tutorial-15.jpeg)
检查证书：
windows command > certmgr.msc → 受信任的根证书 → 找到 Fiddler 根证书
![](images/posts/20250831-11.jpeg)
https://example.com/
![](images/posts/20250830-Fiddler-Tutorial-16.jpeg)
![](images/posts/20250830-Fiddler-Tutorial-17.jpeg)
![](images/posts/20250830-Fiddler-Tutorial-18.jpeg)
## 安装插件

![](images/posts/20250830-Fiddler-Tutorial-20.jpeg)

![](images/posts/20250830-Fiddler-Tutorial-19.jpeg)
## 脚本Jscript.net
在 **Fiddler Classic**（Windows 桌面版，.NET WinForms）里，脚本系统用的是 **FiddlerScript**，基于 **JScript.NET**。跟 JavaScript 类似，但运行在 .NET 环境下，可以直接调用 .NET Framework 类库。

脚本文件默认名字是 **`CustomRules.js`**，且只有这一个文件。它不会直接放在安装目录，而是放在 **用户配置文件夹** 下。Fiddler Classic 会在启动时自动加载这个脚本。

**入口函数**：常用的是 `OnBeforeRequest(oSession)` 和 `OnBeforeResponse(oSession)`，分别在请求发出前、响应返回后被调用。

脚本文件会在 Fiddler 启动时编译加载，主要用来定制请求/响应的处理逻辑。
### 使用
打开 Fiddler Classic → 菜单 Rules → Customize Rules...
会打开 CustomRules.js 文件（JScript.NET 脚本）。如果文件不存在，Fiddler 会自动生成一个默认模板。
![](images/posts/20250831-8.jpeg)
把代码粘贴到 class Handlers 里面。
![](images/posts/20250831-9.jpeg)
保存文件 → 回到 Fiddler → 脚本会自动加载。

打开 Fiddler，启用 HTTPS 解密。

JScript.NET 的 DLL 引用规则
- 默认只会搜索 **GAC（全局程序集缓存）** 或 **Fiddler 根目录 / 系统 PATH 下的 DLL**。
- 如果你熟悉命令行 gacutil 可以把 DLL 安装到 GAC，这样任何 .NET 程序都能引用。（不推荐，麻烦）
- 只支持.net 4.x，不支持.net core
- Fiddler Classic 使用的是 .NET Framework 4.x，不是 .NET Core。
- 所以你需要下载 针对 .NET Framework 的 dll。如果下载的是 .NET Standard / .NET Core 版本，会报加载失败。

### 常见用法
**对象模型**：
- `oSession`：代表一次 HTTP/HTTPS 会话，包含请求和响应的所有数据。    
    - `oSession.oRequest` / `oSession.oResponse`：访问请求和响应对象        
    - `oSession.HostnameIs("xxx")`：判断请求目标主机        
    - `oSession.fullUrl`：请求完整 URL        
- `FiddlerObject`：提供一些工具方法，例如日志输出、弹窗。

能做的事
- **修改请求**：加/改/删 headers、body、URL。    
- **修改响应**：替换内容、注入脚本、模拟数据。    
- **自动化测试**：批量重放请求、加延迟、模拟慢网速。    
- **调试辅助**：日志记录、条件断点。    
- **拦截/重定向**：把请求定向到别的服务器或本地文件。

拦截并修改请求
```js
class Handlers
{
    static function OnBeforeRequest(oSession: Session) {
        // 修改 User-Agent
        if (oSession.HostnameIs("example.com")) {
            oSession.oRequest["User-Agent"] = "MyCustomUA/1.0";
        }
    }
}
```
拦截并修改响应
```js
class Handlers
{
    static function OnBeforeResponse(oSession: Session) {
        if (oSession.HostnameIs("api.test.com")) {
            // 把响应内容转成字符串
            var body = oSession.GetResponseBodyAsString();
            // 替换某些字段
            body = body.Replace("Hello", "Hi");
            // 设置回去
            oSession.utilSetResponseBody(body);
        }
    }
}
```
自动返回本地文件
```js
if (oSession.uriContains("/test.js")) {
    oSession.utilCreateResponseAndBypassServer();
    oSession.oResponse.headers.HTTPResponseStatus = "HTTP/1.1 200 OK";
    oSession.oResponse["Content-Type"] = "application/javascript";
    oSession.utilSetResponseBody(System.IO.File.ReadAllText("C:\\mytest.js"));
}
```
控制台输出
```js
FiddlerObject.log("捕获到请求：" + oSession.fullUrl);
```
过滤 Content-Type
```js
var contentType = oSession.oResponse["Content-Type"];
if (contentType == null || !contentType.ToLower().StartsWith("image/")) {
    LogToFile("不是图片，跳过: " + oSession.fullUrl);
    return;
}
```
解压缩响应
```js
oSession.utilDecodeResponse(); // 确保 responseBodyBytes 是原始图片数据
var responseBytes = oSession.responseBodyBytes;
```
**判断图片宽度**   
- 对于常规 JPG/PNG/BMP 用 `System.Drawing.Image.FromStream` 检查宽度
```js

// ==========================
// 图片处理方法
// ==========================
static function IsImageWidthLargeEnough(fileBytes: byte[]): boolean {
    // 使用 .NET 的 System.Drawing.Image 检查宽度
    try {
        var ms: MemoryStream = new MemoryStream(fileBytes);
        var img: System.Drawing.Image = System.Drawing.Image.FromStream(ms);
        var width: int = img.Width;
        ms.Close();
        img.Dispose();

        // 如果宽度大于500，则返回 true
        return width > 500;
    } catch(ex: Exception) {
        LogToFile("检查图片宽度失败: " + ex.Message);
        return false;
    }
}
static function IsImageWidthLargeEnough(fileBytes: byte[], ext: String): boolean {
    try {
        if(fileBytes == null || fileBytes.Length < 100) return false;

        var lowerExt = ext.ToLower();
        if(lowerExt == "webp") {
            // TODO: 用 WebP 库解析并获取宽度
            LogToFile("WebP 图片暂不检查宽度");
            return true; // 暂时直接保存
        } else {
            using(var ms = new MemoryStream(fileBytes)) {
                using(var img = System.Drawing.Image.FromStream(ms)) {
                    LogToFile("图片宽度: " + img.Width);
                    return img.Width > 400;
                }
            }
        }
    } catch(ex: Exception) {
        LogToFile("检查图片宽度失败: " + ex.Message);
        return false;
    }
}
```
检查 Response Body 是否为空或长度为 0
在处理图片前加判断
```js
if (oSession.responseBodyBytes == null || oSession.responseBodyBytes.Length == 0) {
    LogToFile("响应体为空，跳过 session " + oSession.id);
    return;
}
```
转换图片格式
```js
static function ConvertToJpgIfNeeded(fileBytes: byte[], ext: String): byte[] {
    try {
        // 如果已经是 jpg 或 png，则直接返回原始字节
        var lowerExt: String = ext.ToLower();
        if(lowerExt == "jpg" || lowerExt == "jpeg" || lowerExt == "png") {
            return fileBytes;
        }

        // 否则统一转换为 jpg
        var ms: MemoryStream = new MemoryStream(fileBytes);
        var img: System.Drawing.Image = System.Drawing.Image.FromStream(ms);
        ms.Close();

        var msOut: MemoryStream = new MemoryStream();
        img.Save(msOut, System.Drawing.Imaging.ImageFormat.Jpeg);
        img.Dispose();

        return msOut.ToArray();
    } catch(ex: Exception) {
        LogToFile("图片格式转换失败: " + ex.Message);
        return fileBytes; // 出错时仍返回原始数据
    }
}
```


- 对于 WebP，需要用额外库（例如 [ImageMagick.NET](https://github.com/dlemstra/Magick.NET?utm_source=chatgpt.com) 或 [WebP .NET](https://github.com/imazen/webp?utm_source=chatgpt.com))
- 选择 Q8 版本 AnyCPU，下载 `Magick.NET-Q8-AnyCPU.dll`
- FiddlerScript 是 **JScript.NET**，不是 C#，所以直接在 FiddlerScript 调用 .NET NuGet 包会很麻烦。
- 如果想用 Magick.NET，你需要写 **一个 C# DLL**，然后在 FiddlerScript 里通过 `Reflection` 或 COM 调用它。    
- 更简单的是写一个 **独立 C# 程序 / 控制台工具**，Fiddler 抓取后把文件传给这个程序处理。
### 注意
- **Fiddler Everywhere**（跨平台版，Windows/Mac/Linux）不再用 `CustomRules.js`。    
- 它的规则管理是 **可视化 UI**，在 **Rules** 面板里配置条件 + 动作。    
- 如果你要写脚本式逻辑，就不行了，Everywhere 目前不支持 JScript.NET 自定义。
- -如果你需要写 **灵活的脚本**（比如根据 URL/请求体做复杂逻辑处理），那 **Fiddler Classic** 完全能满足，不需要升级。    
- 如果只是要做 **简单的请求改写/重定向**，那 Everywhere 的 UI 规则也能搞定，反而更直观。
- Fiddler Classic 的脚本引擎是基于 **.NET Framework + JScript.NET**，和浏览器里常见的 JavaScript (ES6+) 语法有差别，比如 `let`/`const`、箭头函数都不支持。写的时候最好用旧版 JS 风格。

针对「**自动化流量嗅探 + 文件保存**」，我来分别对比 **Fiddler Classic** 和 **Fiddler Everywhere** 能力：

🔍 Fiddler Classic
- **脚本能力**：支持 `CustomRules.js`（JScript.NET），你可以写代码完成：    
    - 按规则过滤请求（只抓特定网站 + 文件类型）        
    - 自动保存响应体到硬盘        
    - 自定义命名方式        
    - 写一个开关变量 + 热键命令（QuickExec 或配合 AutoHotkey）        
- **灵活性**：几乎可以完全满足你提出的所有需求    
- **不足**：    
    - 只能在 Windows 上用        
    - 脚本需要自己写和维护（复杂逻辑需要懂点代码）
✅ 结论：**完全能实现你的需求**，而且是最佳选择。

🔍 Fiddler Everywhere
- **规则系统**：Everywhere 提供的是「**UI 配置规则**」，类似于“如果 Host=xxx 且 Content-Type=image/webp → 执行动作”。    
- **动作支持**：目前支持的动作主要是拦截、修改、重定向、阻止等，**不支持“自动保存文件到硬盘”** 这种高级动作。    
- **脚本扩展**：Everywhere 去掉了 `CustomRules.js` 脚本机制，意味着你无法写复杂的“自动保存”逻辑。    
- **跨平台**：支持 Windows / Mac / Linux，但灵活性比 Classic 差。
❌ 结论：**Everywhere 不能完全满足**，尤其是“自动保存到硬盘 + 自定义命名 + 快捷键开关”这一部分。

🎯 总结
- **你的需求必须用 Fiddler Classic**，Everywhere 不行。    
- Classic：脚本灵活，完全可定制，能满足后台嗅探 + 自动保存 + 快捷键控制。    
- Everywhere：更偏向企业/跨平台调试，不适合用来做“自动化抓取+保存”。
### FiddlerScript的坑
Fiddler 的 JScript.NET 语法确实让人抓狂：既不是 JS，又不是 C#，强类型又强制类内定义，全局变量、函数和初始化都要小心。写起来简直像在“绕弯子写代码”。
FiddlerScript 不完全等同于标准 .NET JScript，它是自己的语法。规则非常严格且狭隘，容错率低。

FiddlerScript 里要求 **静态函数或变量必须在class类里声明类型**，或者 **类的静态构造函数不能像 C# 那样放在类外面。

没有全局作用域，静态变量不能放在文件顶层，必须放在class里面

不要在类外声明全局变量或函数

所有变量必须显式声明类型（`String`、`bool`、`int`、Object 等）。

**所有函数和变量必须在 `class Handlers` 内**

不允许在类外全局声明任何变量或函数

不支持 C# 风格的静态构造函数 `static Handlers()`
```js
// not working
static Handlers() : void {
    LoadRules();
}
```

JSON 解析用 Newtonsoft.Json.dll

#### 正确做法

**所有变量声明为 `public static var`**   所有变量必须显式声明类型 
全部变量 `public static var … : Type`
    ```js
    public static var gRules:RulesConfig = null; 
    public static var AutoSaveEnabled:boolean = false; 
    public static var gCounter:int = 0;
    ```    
所有函数也必须显式指定返回类型
全部函数 `static function …(: Type): Type`
所有函数加类型声明 比如`: void`
```js
static function LoadRules(): void {
    ...
}

static function OnBeforeResponse(oSession: Session): void {
    ...
}

static function OnExecAction(sParams: String[]): boolean {
    ...
}
```
**全局变量和函数都必须在 `class Handlers` 内**
- 类外定义的全局变量或函数 Fiddler 会报错    
- 不能用 C# 风格的静态构造函数 `static Handlers() {...}`
- **类型要求严格**    
- 布尔值用 `boolean`，整数用 `int`，字符串用 `String`	
- 数组类型也必须明确 `String[]` 或 `Rule[]`
         
**把初始化函数写成静态函数**或 `Main()` / `OnBoot()`**，放在Handlers类里面
```js
static function LoadRules() 
{     ... }
```
        
初始化在 `Main()` 或 `OnBoot()` 调用
```js
static function Main() {
     Handlers.LoadRules();
     FiddlerObject.StatusText = "Rules loaded at: " + DateTime.Now; 
}
```
- `Main()` 是 **FiddlerScript 自动调用**的入口函数，每次脚本加载都会运行        
- **不要用 C# 风格静态构造函数**

脚本最前面加import引用，后面调用要写全类名 

```csharp
import System;
import System.IO;
import MyImageHelper;   // 你 DLL 里的命名空间

class Handlers
{
    // OnBeforeResponse 事件
    static function OnBeforeResponse(oSession: Session) {
        if (oSession.HostnameIs("example.com") && oSession.oResponse.headers.ExistsAndContains("Content-Type", "image")) {
            
            var bytes: byte[] = oSession.responseBodyBytes;

            // 调用你写的 C# 方法
            var jpegBytes: byte[] = ImageHelper.ConvertToJpeg(bytes, 90);

            // 替换 Fiddler 的响应内容
            oSession.responseBodyBytes = jpegBytes;
            oSession.oResponse["Content-Type"] = "image/jpeg";
        }
    }
}

```


## 开发步骤
### **准备配置文件**（你要抓的网站、类型、保存目录、命名规则）
```json
{
  "rules": [
    {
      "host": "example.com",
      "types": [".webp", ".jpeg"],
      "saveDir": "D:\\Sniffer\\Example",
      "namePattern": "{host}_{timestamp}{ext}"
    },
    {
      "host": "video.com",
      "types": [".mp4"],
      "saveDir": "D:\\Sniffer\\Video",
      "namePattern": "{host}_{counter}{ext}"
    }
  ]
}
```
### **修改 CustomRules.js**（实现：加载配置文件 + 开关变量 + 保存逻辑）
找到 Fiddler 安装目录下的 **CustomRules.js**，用编辑器打开。我们要做的：
1. 定义一个 **全局开关**（`AutoSaveEnabled`）。
2. 启动时加载配置文件（JSON）。    
3. 在 `OnBeforeResponse` 里检查规则，若匹配则保存响应体。  
Fiddler 自带的 `CustomRules.js` 文件里有很多模板方法，比如：
```js
static function OnBeforeRequest(oSession: Session) { ... } 
// 插入你的规则检查和保存逻辑
static function OnBeforeResponse(oSession: Session) { ... }
```
不需要直接全部替换掉整个 `CustomRules.js`，而是 **在现有 `CustomRules.js` 中做增量修改**，这样比较安全，避免覆盖 Fiddler 自带的其他默认规则和功能。
### **实现快捷键开关**（用 Fiddler 内置命令 + AutoHotkey 热键）
- 在 Fiddler 底部命令行（QuickExec）输入：    
    - `autosave on` → 开启嗅探        
    - `autosave off` → 关闭嗅探
        
- 如果你想用 **全局热键**，可以用 **AutoHotkey**：
```ahk
; Ctrl+Alt+S 开启
^!s::
Run, "C:\Program Files (x86)\Fiddler2\Fiddler.exe" /quietexec "autosave on"
return

; Ctrl+Alt+D 关闭
^!d::
Run, "C:\Program Files (x86)\Fiddler2\Fiddler.exe" /quietexec "autosave off"
return
```
### - **测试验证**（先抓 1 个站点的 webp，确认能自动保存，再扩展规则）
- 打开 Fiddler，启用 HTTPS 解密。
    
- 在 QuickExec 输入 `autosave on`。
    
- 浏览 `example.com` 的页面，看是否自动保存 webp/jpeg 到 `D:\Sniffer\Example`。
    
- 验证命名规则是否正确。
    
- 输入 `autosave off` 停止。
# FAQ
## 怎么捕获浏览器缓存
有一个问题。fiddler捕获了每一次chrome访问图片链接的请求，但是有时候F5刷新chrome的时候，chrome没有去下载完整图片，而是从缓存中取的图片，这样fiddler捕获到的是无效的图片文件。 如何跳过这个情况？

Fiddler 作为代理，**只会截获真正经过代理服务器的 HTTP(S) 请求**。如果 Chrome **直接从本地缓存读取**，那这次图片就不会经过网络，也就不会进入 Fiddler pipeline ——所以你在 `OnBeforeResponse` 或 `OnPeekAtResponseHeaders` 里得到的就是空或者无效数据。

常见的几种解决方法：
### 方法 1：跳过缓存请求

在 FiddlerScript (`CustomRules.js`) 中，你可以检测响应是否来自缓存。  
Fiddler 有一个标志位：
```csharp
if (oSession.oFlags["ui-cacheonly"] != null) {
    // 说明这是缓存命中，不是真正下载
    oSession["ui-hide"] = "true";  // 在会话列表中隐藏
    return;
}
```
这样可以直接 **忽略缓存命中的请求**。
### 方法 2：强制 Chrome 每次都走网络

如果你需要 Fiddler 每次都拿到完整图片，那就得告诉 Chrome 不要用缓存：
![](images/posts/20250831-12.jpeg)
1. 打开 Chrome DevTools (F12)    
2. 勾选 **Network → Disable cache (在 DevTools 打开时有效)**    
3. 或者请求 URL 后面加随机参数，比如：
`https://example.com/image.png?t=169098123`
这样浏览器会认为是新请求，必定走网络。
### 方法 3：Fiddler 全局设置

在 Fiddler Classic 菜单里：  
**Rules → Performance → Disable Caching**  
这会自动给响应加上禁止缓存的头部：
```yaml
Cache-Control: no-cache, no-store, must-revalidate
Pragma: no-cache
Expires: 0
```
这样 Chrome 无法使用本地缓存，所有请求都会走网络。
✅ 总结：
- 如果你只想**跳过无效图片** → 方法 1（检测 `ui-cacheonly`）。    
- 如果你想**确保每次都有完整图片** → 方法 2 或 方法 3（禁用缓存）。
## 怎么抓取BLOB
在浏览器中，Blob 通常是 **表示二进制数据的对象**，可能是：
- 图片、视频、音频文件    
- PDF、Excel 等文档    
- 后端接口返回的二进制数据流
网页上的 Blob URL 通常是这样的：
`blob:https://example.com/1234-5678-90ab-cdef`
**特点**：
1. 这个 URL 只是浏览器内存中的临时对象，不是真正的文件服务器路径。    
2. 刷新页面或关闭浏览器，这个 Blob URL 会失效。    
3. 不能直接右键“另存为”像普通 URL 那样下载，有时候需要额外处理。
Blob 可以下载，但不能直接用普通 URL 下载，需要通过 JS 创建 **下载链接** 或使用工具抓取数据。

这类 URL **不是实际网络请求地址**，而是浏览器内存中的对象 URL，**直接访问 blob URL 是抓不到的**。所以在 Fiddler 中，你看到的请求实际是原始资源请求（可能是 `.png`, `.jpeg` 或者 `.webp`），Blob 只是浏览器创建的临时 URL

✅ 注意事项：
1. Blob URL 本身在 Fiddler 中不可用，需要抓真实请求。    
2. `oSession.responseBodyBytes` 可以获取原始图片二进制数据。    
3. 图片类型通过 `Content-Type` 判断，方便保存对应后缀。    
4. 文件夹 `C:\Temp\` 要先存在，或者改成你想要的路径。
5. 
通过 Fiddler 捕获原始图片请求
在 `OnBeforeResponse(oSession: Session)` 中，可以抓到真实图片请求：
```JS
// FiddlerScript (CustomRules.js)
static function OnBeforeResponse(oSession: Session) {
    // 判断是不是图片
    if (oSession.oResponse.headers.ExistsAndContains("Content-Type", "image/")) {
        // 下载并保存图片
        SaveImage(oSession);
    }
}
```
下载并保存图片
Fiddler JScript.NET 可以直接用 `oSession.ResponseBody`：
```JS
static function SaveImage(oSession: Session) {
    try {
        // 获取文件扩展名
        var contentType = oSession.oResponse.headers["Content-Type"];
        var ext = "jpg";
        if (contentType == "image/png") ext = "png";
        else if (contentType == "image/jpeg") ext = "jpg";
        else if (contentType == "image/webp") ext = "webp";

        // 构建文件名
        var filename = "C:\\Temp\\" + DateTime.Now.ToString("yyyyMMdd_HHmmss") + "." + ext;

        // 保存图片
        var bytes = oSession.responseBodyBytes;
        System.IO.File.WriteAllBytes(filename, bytes);

        FiddlerApplication.Log.LogString("图片已保存: " + filename);
    }
    catch(e) {
        FiddlerApplication.Log.LogString("保存图片失败: " + e);
    }
}
```
如果要把图片转换为 Base64
```JS
var base64 = Convert.ToBase64String(oSession.responseBodyBytes);
FiddlerApplication.Log.LogString("Base64长度: " + base64.Length);
```
可以把 Base64 传给其他程序或 WPF 通知。
💡 总结流程：
1. **Blob URL 在浏览器内存** → Fiddler 捕获不到    
2. **Fiddler 捕获的是原始图片请求** → 可以在 `OnBeforeResponse` 获取 `oSession.responseBodyBytes`    
3. **保存到本地或转 Base64** → 自定义逻辑