# Introduction
Playwright 是由 Microsoft 团队开发的现代浏览器自动化库。它能以编程方式控制浏览器（Chromium、Firefox、WebKit），用于：
- E2E（端到端）测试、UI 自动化    
- 网页爬取、数据采集    
- 自动化操作（登录、填表、导出截图/PDF）    
- 自动化生成环境下的交互式流程（例如模拟用户行为）
**特点**：
- 支持 **多浏览器（Chromium/Firefox/WebKit）**，同一套 API。    
- 支持 **多语言**：Node.js（JavaScript/TypeScript）、Python、C#、Java。    
- 内置 **智能等待（auto-wait）**，减少显式 sleep，稳定性更好。    
- 提供测试运行器（Playwright Test），包含并发、断言、报告、截图/录像/Trace。
# Install
nuget
```
dotnet add package Microsoft.Playwright
```
![](images/posts/20251109.jpeg)
编译一下项目（必须！）> 进入项目目录 > 安装浏览器驱动
```
# 这会运行一个 PowerShell 脚本来下载所有浏览器
pwsh bin/Debug/net.../playwright.ps1 install
```
![](images/posts/20251109-1.jpeg)
# How to use
## 核心概念
Browser：浏览器进程（Chromium/Firefox/WebKit）。
BrowserContext：浏览器中的隔离上下文（类似独立的浏览器配置/profile），可以创建多个 Context 以模拟不同用户。
Page：Context 下的页面（tab）。绝大多数操作都是对 Page 进行。
Locator / Selector：查找元素的方式（CSS、text、role、data-testid、XPath 等）。
Auto-wait：很多 API（click/type）会自动等待元素可用，不用再加额外的等待。
Trace：记录一次运行的细节（网络、DOM、快照），用于排查失败。
Fixtures（在 Playwright Test 中）：管理测试前置/后置状态（例如登录、数据库清理）。
## 选择器（Selector）技巧
- CSS：`page.click('button.submit')`    
- 文本：`page.click('text=Submit')` 或 `page.locator('text=Submit')`    
- Role（更稳定，accessibility）：`page.get_by_role('button', { name: 'Submit' })    
- data-testid：`page.locator('[data-testid="login-btn"]')`    
- chained locator（更稳）： `page.locator('form').locator('input[name="email"]')`
## 智能等待（auto-wait）
大多数动作会自动等待元素可见和可交互：
```node
await page.click('button#save'); // 会等待可点击
```
如果需要等待某条件：
```node
await page.waitForSelector('.result', { timeout: 5000 });
```
表单填充 / 模拟键盘
```node
await page.fill('#email', 'me@example.com');
await page.press('#password', 'Enter');
```
截图 / PDF / 视频 / 下载
```node
await page.screenshot({ path: 'shot.png', fullPage: false });
await page.pdf({ path: 'page.pdf' });

// 启用视频录制（context 层面）
const context = await browser.newContext({ recordVideo: { dir: 'videos/' } });

// 下载处理
const [download] = await Promise.all([
  page.waitForEvent('download'),
  page.click('a#download')
]);
await download.saveAs('file.zip');
```
网络拦截 / Mock 数据
```
await page.route('**/api/**', route => {
  route.fulfill({ status: 200, contentType: 'application/json', body: JSON.stringify({ ok: true }) });
});

await page.route('**/analytics/**', route => route.abort());
```
多标签页 / 多 context / 并发
不同用户使用不同 BrowserContext（隔离 cookie/localStorage）。
多 Page 用于并发任务或多窗口场景。
```
const ctx1 = await browser.newContext();
const page1 = await ctx1.newPage();

const ctx2 = await browser.newContext();
const page2 = await ctx2.newPage();
```
## 基本功能
|功能|用法|说明|
|---|---|---|
|**打开页面**|`await page.GotoAsync("https://xxx");`|访问网页|
|**输入文本**|`await page.FillAsync("input#username", "Andy");`|填充表单|
|**点击元素**|`await page.ClickAsync("button#login");`|点击按钮|
|**等待元素**|`await page.WaitForSelectorAsync("div.result");`|等待加载完成|
|**截屏**|`await page.ScreenshotAsync(new PageScreenshotOptions { Path = "1.png" });`|页面截图|
|**PDF 输出**|`await page.PdfAsync(new PagePdfOptions { Path = "file.pdf" });`|导出 PDF|
|**模拟设备**|`var context = await browser.NewContextAsync(playwright.Devices["iPhone 13"]);`|模拟移动设备|
|**多标签页**|`await context.NewPageAsync();`|同时控制多个页面|
|**抓取文本**|`var text = await page.InnerTextAsync("h1");`|获取网页内容|
# Demos
## 录制视频
```
var context = await browser.NewContextAsync(new BrowserNewContextOptions
{
    RecordVideoDir = "videos/",
    RecordVideoSize = new RecordVideoSize { Width = 1280, Height = 720 }
});
var page = await context.NewPageAsync();
await page.GotoAsync("https://example.com");
await context.CloseAsync(); // 关闭后视频文件生成
```
## 处理登录和Cookies
保存登录状态
```
await context.StorageStateAsync(new BrowserContextStorageStateOptions { Path = "auth.json" });
```
重新加载登录状态
```
var context = await browser.NewContextAsync(new BrowserNewContextOptions
{
    StorageStatePath = "auth.json"
});
```
这能让你登录一次，后续脚本自动带上 cookie。
## 调试技巧
|功能|命令|
|---|---|
|**调试模式**|`PWDEBUG=1 dotnet run`|
|**慢动作模式**|`await browser.NewContextAsync(new() { SlowMo = 500 });`|
|**日志输出**|`page.Console += (_, msg) => Console.WriteLine(msg.Text);`|
# FAQ
## C# 项目结构建议
```
PlaywrightDemo/
├─ Program.cs
├─ Pages/
│  ├─ GoogleSearchPage.cs
│  ├─ LoginPage.cs
├─ Utils/
│  ├─ BrowserHelper.cs
└─ playwright.ps1
```
# Reference
- 官方网站：  
    🔗 https://playwright.dev/dotnet
    
- NuGet 包：  
    🔗 [https://www.nuget.org/packages/Microsoft.Playwright](https://www.nuget.org/packages/Microsoft.Playwright)
    
- GitHub 项目：  
    🔗 [https://github.com/microsoft/playwright-dotnet](https://github.com/microsoft/playwright-dotnet)