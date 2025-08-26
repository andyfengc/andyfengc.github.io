---
layout: post
title: ToastNotifications Tutorial
author: Andy Feng
---
# Introduction .NET9 无法使用

**ToastNotifications** 是一个专为 .NET WPF 应用设计的轻量级库，用于在桌面端显示 **临时通知消息**（类似Windows系统Toast通知）。其核心特点是：

- **非侵入式**：通知从屏幕边缘弹出，不打断用户操作。    
- **可定制**：支持位置、动画、样式和交互。    
- **低耦合**：无需复杂依赖，直接集成到现有项目。
ToastNotifications 核心对象是 `Notifier` 类，你需要创建一个 `Notifier` 实例，指定通知位置、生命周期等，然后使用它发送通知。

## 应用场景
|场景|示例|推荐配置|
|---|---|---|
|**操作反馈**|文件保存成功/失败提示|`ShowSuccess()`/`ShowError()`|
|**后台任务完成**|下载完成、数据处理结束|`ShowAsync()` + 自动关闭|
|**用户确认**|删除确认、二次验证|带交互按钮的通知|
|**实时监控**|服务器状态报警|持久化通知（需手动关闭）|
# Install
## install
###  Install via nuget:
```
Install-Package ToastNotifications
Install-Package ToastNotifications.Messages
```
![](/images/posts/Pasted%20image%2020250720002738.png)
## Import ToastNotifications.Messages theme in App.xaml
```xml
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <ResourceDictionary Source="pack://application:,,,/ToastNotifications.Messages;component/Themes/Default.xaml" />
        </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
</Application.Resources>
```
## 基本用法 
创建通知接口 `INotificationService`
```csharp
public interface INotificationService
{
    void ShowSuccess(string message);
    void ShowError(string message);
    void ShowWarning(string message);
    void ShowInformation(string message);
}
```
### 加入DI
实现
```csharp
    public class ToastNotificationService : INotificationService
    {
        private readonly ToastNotifier _notifier;

        public ToastNotificationService(ToastNotifier notifier)
        {
            _notifier = notifier;
        }

        public void ShowSuccess(string message) => _notifier.ShowSuccess(message);
        public void ShowError(string message) => _notifier.ShowError(message);

        public void ShowWarning(string message) =>_notifier.ShowWarning(message);
        public void ShowInformation(string message)
        {
            throw new NotImplementedException();
        }
    }
```
**App.xaml.cs**
```csharp
private void ConfigureServices(IServiceCollection services)
    {
        // 注册ToastNotifier为单例（推荐）INoticationService
        services.AddSingleton<INoticationService>(provider => 
        {
            var notifier = new ToastNotifier(cfg =>
            {
                cfg.Position = NotificationPosition.BottomRight;
                cfg.MaxItems = 3;
            });
            return notifier;
        });

        // 注册主窗口和其他服务
        services.AddTransient<MainWindow>();
        services.AddTransient<MainViewModel>();
    }
```
### 其他配置细节
创建对象，初始化配置
```csharp
// 初始化配置
var notifier = new ToastNotifier(cfg =>
{
    cfg.Position = NotificationPosition.BottomRight; // 位置（Top/Bottom/Left/Right）
    cfg.MaxItems = 3;                                // 最大同时显示数
    cfg.AnimationType = AnimationType.Fade;          // 动画类型（Fade/Slide）
    cfg.DisplayTime = TimeSpan.FromSeconds(5);       // 显示时长
});
```
或直接Create Notifier instance
```csharp
using ToastNotifications;
using ToastNotifications.Lifetime;
using ToastNotifications.Position;
/* * */
Notifier notifier = new Notifier(cfg =>
{
    cfg.PositionProvider = new WindowPositionProvider(
        parentWindow: Application.Current.MainWindow,
        corner: Corner.TopRight,
        offsetX: 10,  
        offsetY: 10);

    cfg.LifetimeSupervisor = new TimeAndCountBasedLifetimeSupervisor(
        notificationLifetime: TimeSpan.FromSeconds(3),
        maximumNotificationCount: MaximumNotificationCount.FromCount(5));

    cfg.Dispatcher = Application.Current.Dispatcher;
});
```
**交互支持**
```csharp
// 带按钮的通知
notifier.Show("确认删除？", 
    confirmationButtons: new[] { "确定", "取消" },
    onClick: buttonText => 
    {
        if (buttonText == "确定") DeleteFile();
    });
```
### 集成
view 集成
```xml
<!-- MainWindow.xaml -->
<Window ...
        xmlns:toast="clr-namespace:ToastNotifications;assembly=ToastNotifications">
        <!-- 通知容器 -->
        <toast:ToastNotifier x:Name="Notifier" Position="BottomRight"/>
</Window>
```
mvvm集成
```csharp
// 在ViewModel中通过服务调用
public class MainViewModel
{
    private readonly INoticationService _notifier;
    
    public MainViewModel(INoticationService notifier)
    {
        _notifier = notifier;
        SaveCommand = new RelayCommand(() => _notifier.ShowSuccess("保存成功"));
    }
}
```
## 建议用法

实现通知服务 `NotificationService`
```csharp
using ToastNotifications;
using ToastNotifications.Messages;
using ToastNotifications.Lifetime;
using ToastNotifications.Position;

public class NotificationService : INotificationService, IDisposable
{
    private readonly Notifier _notifier;

    public NotificationService()
    {
        _notifier = new Notifier(cfg =>
        {
            cfg.PositionProvider = new WindowPositionProvider(
                parentWindow: Application.Current.MainWindow, // 确保主窗口已加载
                corner: Corner.TopRight,
                offsetX: 10,
                offsetY: 10);

            cfg.LifetimeSupervisor = new TimeAndCountBasedLifetimeSupervisor(
                notificationLifetime: TimeSpan.FromSeconds(3),
                maximumNotificationCount: MaximumNotificationCount.FromCount(5));

            cfg.Dispatcher = Application.Current.Dispatcher;
        });
    }

    public void ShowSuccess(string message) => _notifier.ShowSuccess(message);
    public void ShowError(string message) => _notifier.ShowError(message);
    public void ShowWarning(string message) => _notifier.ShowWarning(message);
    public void ShowInformation(string message) => _notifier.ShowInformation(message);

    public void Dispose() => _notifier.Dispose();
}
```
### 在 DI 容器中注册服务
```csharp
public partial class App : Application
{
    public static IServiceProvider Services { get; private set; }

    protected override void OnStartup(StartupEventArgs e)
    {
        var services = new ServiceCollection();

        // 注册你的其他服务
        services.AddSingleton<INotificationService, NotificationService>();

        Services = services.BuildServiceProvider();

        base.OnStartup(e);
    }
}
```
### 集成
View 中创建 ViewModel 并注入notification service
```csharp
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
        var notificationService = App.Services.GetRequiredService<INotificationService>();
        DataContext = new MainViewModel(notificationService);
    }
}
```
view model
```csharp
public class MainViewModel
{
    private readonly INotificationService _notificationService;

    public MainViewModel(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }

    public void Submit()
    {
        _notificationService.ShowSuccess("保存成功！");
    }
}

```
## Use provided messages
**通知类型**

|方法|效果|示例代码|
|---|---|---|
|`ShowSuccess`|绿色成功通知（带✓图标）|`notifier.ShowSuccess("保存成功")`|
|`ShowError`|红色错误通知（带×图标）|`notifier.ShowError("文件读取失败")`|
|`ShowInformation`|蓝色信息通知（带i图标）|`notifier.ShowInfo("新版本可用")`|
|`ShowWarning`|黄色警告通知（带!图标）|`notifier.ShowWarning("磁盘空间不足")`|
```csharp
using ToastNotifications.Messages;
/* * */
notifier.ShowInformation(message);
notifier.ShowSuccess(message);
notifier.ShowWarning(message);
notifier.ShowError(message);
```


## 清理资源（可选）
Dispose notifier when it's no longer needed
当窗口关闭时，记得调用 `_notifier.Dispose()` 避免资源泄露：
```csharp
public void Dispose()
{
    _notifier.Dispose();
}
```
可放在 `MainWindow.OnClosed` 或 `App.OnExit` 里调用。
如果你用的是 `Application.Current.MainWindow`，确保在主窗体关闭时清理 `Notifier`：
```csharp
protected override void OnExit(ExitEventArgs e)
{
    (App.Services.GetService<INotificationService>() as IDisposable)?.Dispose();
    base.OnExit(e);
}
```
# 高级配置
## **自定义UI模板**
```xml
<!-- 定义自定义通知样式 -->
<DataTemplate x:Key="CustomToastTemplate">
    <Border Background="#FF444444" CornerRadius="5">
        <TextBlock Text="{Binding Message}" Foreground="White" Margin="10"/>
    </Border>
</DataTemplate>
```
使用模版
```csharp
// 应用模板
notifier.UseCustomTemplate((Notification notification) => 
    Application.Current.FindResource("CustomToastTemplate"));
```
## **队列控制**
```csharp
// 清空所有通知
notifier.ClearAll();

// 暂停/恢复显示
notifier.Pause();
notifier.Resume();
```
## 异步通知
```csharp
await notifier.ShowAsync("后台处理完成");
```
# FAQ

|问题|解决方案|
|---|---|
|通知被其他窗口遮挡|设置`Topmost="True"`或使用`Windowless`模式|
|高DPI显示模糊|在App.xaml中添加`<Application.Resources><Style TargetType="Notification" BasedOn="{StaticResource {x:Type Notification}}"/></Application.Resources>`|
|MVVM下无法直接绑定|通过`IToastNotifier`接口注入服务|

# References 
[ToastNotifications Github](https://github.com/rafallopatka/ToastNotifications)