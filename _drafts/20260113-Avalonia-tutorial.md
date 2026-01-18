# Introduction
**Avalonia 是一个跨平台的 .NET UI 框架**  
类似 WPF，但可以跑在：
- Windows    
- Linux    
- macOS  
    （甚至 Web / Mobile：进阶内容）
工作方式：
- **Skia 渲染引擎**：Avalonia 将 UI 绘制到画布上。这意味着你的应用在 Windows、Linux (包括树莓派) 和 macOS 上看起来几乎完全一样，真正实现了“像素级一致性”。    
- **XAML 驱动**：使用类似 WPF 的 XAML 语言描述界面，但功能更现代（例如支持 CSS 样式的选择器）。    
- **MVVM 模式**：官方强烈建议使用 **Model-View-ViewModel** 模式。你可以选择简单的内建实现，或者功能强大的 `ReactiveUI`。
Avalonia 最大的优势是部署灵活。2026 年，它的工具链已经非常成熟：
- **Windows/Linux/macOS**：直接发布为单文件独立程序。    
- **WebAssembly (WASM)**：可以将 C# 应用直接运行在浏览器中。    
- **移动端**：支持 iOS 和 Android。
## Avalonia vs WPF
| 对比          | WPF       | Avalonia |
| ----------- | --------- | -------- |
| XAML        | ✅         | ✅（高度相似）  |
| MVVM        | ✅         | ✅（一模一样）  |
| DataBinding | ✅         | ✅        |
| Command     | ✅         | ✅        |
| 平台          | 仅 Windows | 跨平台      |
| 底层          | DirectX   | Skia     |
命名空间不一样
```xml
xmlns="https://github.com/avaloniaui
```
Binding 更严格（这是优点）
```xml
`<TextBlock Text="{Binding Title}" />`
```
如果 `Title` 不存在 → **直接报错**（WPF 很多时候是静默失败）
Avalonia 样式是 CSS 风格的
```xml
<Style Selector="Button">
  <Setter Property="Background" Value="Blue"/>
</Style>
```
而不是 WPF 的：
```xml
<Style TargetType="Button">
```
## Avalonia XPF
**Avalonia XPF** 是一套由 Avalonia 团队开发的**商业级二进制兼容方案**。它让原本只能在 Windows 上运行的 WPF（Windows Presentation Foundation）应用程序，无需重写代码，就能直接运行在 Linux、macOS 和 Web 浏览器上。它不是让你把 WPF 代码转换成 Avalonia 代码，而是在非 Windows 平台上**重新实现了一套 WPF 的底层渲染层**。
- **无需重写**：你的 XAML 还是 WPF 的 XAML，你的 C# 代码还是调用 `System.Windows` 命名空间。    
- **二进制兼容**：它甚至可以运行一些已经编译好的 WPF 第三方控件库（如 Syncfusion, DevExpress 或 Telerik），即使你没有这些库的源代码。    
- **底层替换**：它将 WPF 原本依赖 DirectX 的底层绘图替换为 Avalonia 强大的跨平台渲染引擎（基于 Skia）。
XPF 是**商业收费**产品，面向企业客户，与开源的 Avalonia 不同。很多 WPF 企业应用依赖昂贵的第三方 UI 套件，XPF 能够兼容这些套件。
XPF 的授权费用通常很高，是为那些需要把行业级软件快速搬到 Linux 系统的公司准备的。

| **特性**   | **Avalonia UI (开源版)**    | **Avalonia XPF (商业版)** |
| -------- | ------------------------ | ---------------------- |
| **费用**   | 免费 (MIT 协议)              | 收费 (按年/按项目订阅)          |
| **代码迁移** | 需要手动将 WPF 改为 Avalonia 语法 | 几乎 0 代码修改              |
| **适合对象** | 新项目，追求极致跨平台体验            | 有大型老牌 WPF 资产的企业        |
| **控制力**  | 完全控制 UI 行为               | 继承 WPF 的所有行为           |
# Install
安装项目模版
```bash
dotnet new install Avalonia.Templates
```
![](images/posts/20260113-1.jpeg)

| **模板名称**                    | **适用场景**                      | **建议**                  |
| --------------------------- | ----------------------------- | ----------------------- |
| **Avalonia .NET App**       | 最简单的项目，不含 MVVM。               | 仅适合做非常小的脚本工具。           |
| **Avalonia .NET MVVM App**  | **标准桌面开发项目**。                 | **新手首选**，结构清晰，易于维护。     |
| **Avalonia Cross Platform** | 包含 iOS, Android, WebAssembly。 | 如果你打算做**手机端或网页版**，请选这个。 |
| **UserControl / Window**    | 辅助模板。                         | 当你项目建好后，用来快速添加新窗口。      |
此时visual studio创建新项目已经有显示:
![](images/posts/20260113-4.jpeg)
dotnet创建一个项目
```bash
dotnet new avalonia.app -n HelloAvalonia
cd HelloAvalonia
dotnet run
```
你会看到一个**跨平台窗口**弹出来
![](images/posts/20260113.jpeg)
创建 Avalonia MVVM 项目
```
dotnet new avalonia.mvvm -n Generator.UI
cd YoutubeGenerator.UI
dotnet run
```
![](images/posts/20260113-3.jpeg)
# Skia
**Skia** 是一个**跨平台 2D 图形渲染引擎**。Skia = 跨平台、高性能、GPU 加速的 2D 绘图库
## Skia 和 DirectX / OpenGL 的关系
|技术|层级|
|---|---|
|DirectX / OpenGL / Vulkan|底层 GPU API|
|**Skia**|图形抽象层|
|UI 框架|UI 逻辑|
Skia能实现真正的“跨平台一致渲染”
原生 UI 框架的问题是：

|平台|渲染|
|---|---|
|Windows|DirectX|
|macOS|CoreGraphics|
|Linux|GTK / X11|
👉 样式、字体、对齐都会不一样
**Skia 的优势：**
- 自己画    
- 像素级一致    
- 不依赖系统控件
Skia 是：
- Google 出的    
- Chrome / Android / Flutter 用的  
👉 **日均渲染几十亿设备**
## **Skia 的缺点
**Skia 的缺点不是“性能差”，而是：**它解决的是“画”，不是“UI”**。Skia 很强，但“很底层、很重、很不贴近业务”。
太底层：你得“自己画一切”
Skia 提供的是：
- 画线    
- 画字    
- 画路径    
- 画位图   
但 **没有**：
- Button    
- TextBox    
- List    
- Layout    
- Focus    
- Keyboard Navigation    
- Accessibility
Skia 本身 **不懂**：
- 鼠标    
- 触摸    
- 键盘    
- IME    
- 焦点
你必须：
- 自己做 HitTest    
- 自己做事件冒泡    
- 自己做文本输入处理   
📌 **写一个像样的文本编辑器非常痛苦**
# Advanced
- **Native AOT (加分项)：** Avalonia 对 .NET 最新的 Native AOT 支持极其出色。你可以直接编译成一个不需要安装 .NET 运行时的独立 `.exe` 或 `.app`，且无法被常规工具反编译，这对你的订阅制算法是最好的保护。
**Avalonia UI + FluentAvalonia (UI 库) + CommunityToolkit.Mvvm**
- **FluentAvalonia** 能让你获得 Uno 那样精美的 WinUI 3 控件外观。    
- **Avalonia.PropertyGrid** 解决你视频模板的反射 UI 构建。    
- **Avalonia 原生拖拽** 解决素材管理。