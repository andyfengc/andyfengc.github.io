# Introduction
你可以把 WinForms 控件嵌入到 WPF 里，也可以反过来把 WPF 控件嵌入到 Windows Forms。
# **在 WPF 中加载 Windows Forms 控件的方法**

WPF 使用 `WindowsFormsHost` 来承载 WinForms 控件。
例如，把 WinForms 的“字幕渲染器 / 视频播放器控件”嵌入 WPF：
✔ 在 WinForms 内使用 libass / mpv / FFmpeg 播放器
✔ 然后把这个 WinForms 控件通过 WindowsFormsHost 放到 WPF
实现在 WPF 中支持复杂字幕渲染（ASS）。

你可以放任何 WinForms 控件：
✔ WinForms UserControl  
✔ Panel  
✔ DataGridView  
✔ PictureBox  
✔ WebBrowser  
✔ MediaPlayer  
✔ 你自己做的 WinForms 控件
**强化兼容性：WinForms 控件会被当作一个 AIR 层（Hwnd），WPF 无法覆盖它（反之亦然）。**
## 引用程序集
在你的 WPF 项目里添加引用：
- **WindowsFormsIntegration**    
- **System.Windows.Forms**
（.NET Framework 项目默认包含这些；.NET Core 3+/ .NET 5-8 也支持 WinForms interop）
## XAML 加载 Windows Forms 控件
```xml
<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:wf="clr-namespace:System.Windows.Forms;assembly=System.Windows.Forms"
        xmlns:wfi="clr-namespace:System.Windows.Forms.Integration;assembly=WindowsFormsIntegration"
        Title="MainWindow" Height="450" Width="800">

    <Grid>
        <wfi:WindowsFormsHost Name="host">
            <wf:Button Text="WinForms Button" />
        </wfi:WindowsFormsHost>
    </Grid>
</Window>
```
下面加载一个panel，（mpvPanel 是 WinForms Panel）
```xml
<WindowsFormsHost Name="winFormsHost">
    <panel:MPVPanel x:Name="mpvPanel"/>
</WindowsFormsHost>
```
然后C# 中初始化 mpv
```csharp
var mpv = new MpvPlayer(mpvPanel.Handle);
mpv.LoadFile("video.mp4");
mpv.Command("sub-add", "subtitle.ass");

var mpv = new MpvPlayer();
mpv.Initialize(videoHost.Handle); // WindowsFormsHost.Handle
mpv.LoadFile(videoFilePath);
mpv.LoadSubtitles(subtitleFilePath);

```
## 代码方式添加 WinForms 控件
```csharp
using System.Windows;
using System.Windows.Forms;
using System.Windows.Forms.Integration;
using SubtitleEditNamespace; // DLL 里的命名空间

public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();

        var host = new WindowsFormsHost();
        var winformButton = new Button();
        winformButton.Text = "WinForms Button";

        host.Child = winformButton;
        this.Content = host;
        
        var subtitleControl = new SubtitleEditControl(); // 你的控件类
		winFormsHost.Child = subtitleControl;

    }
}
```
# Winforms 加载WPF
ElementHost
# FAQ
## 注意
### ✔ 1. WinForms 控件是 GDI+，WPF 是 DirectX
混合使用时：
- WinForms 控件总是显示在 WPF 控件之上（Z-order 不能跨层）    
- 透明效果受限    
- 某些情况下会有闪烁 / 性能问题   
WinForms 控件是 Hwnd，WPF 控件无法覆盖它
例如：
- Popup    
- ComboBox 下拉    
- Tooltip  
这些都不能显示在 WinForms 控件上面。
DPI、缩放兼容性会更难 特别是高 DPI 屏幕。
### ✔ 2. 最好嵌入“非透明、矩形”的 WinForms 控件
如：
- WindowsFormsHost 承载 MediaPlayer    
- WinForms 的自定义控件    
- 图像控件、面板控件等   
### ✔ 3. 可以托管复杂控件（如 WindowsForms 的 WebBrowser、视频播放器）


### 3. WinForms 控件不会自动支持 WPF 资源、样式

需要手动设置字体、颜色等。
