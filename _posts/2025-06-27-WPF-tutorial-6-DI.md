---
layout: post
title: Dependency Injection
author: Andy Feng
---
# Introduction
是的，**WPF 本身不内建 DI（依赖注入）框架**，但它**完全支持依赖注入**。
你可以在 App 启动时手动配置并注入服务，  再在窗口或 ViewModel 中通过构造函数注入依赖。
可以使用你喜欢的 DI 容器
- **Microsoft.Extensions.DependencyInjection**（官方推荐）    
- Autofac    
- Unity    
- DryIoc 等
# Install
Install-Package Microsoft.Extensions.DependencyInjection
在 App.xaml.cs 配置容器
```csharp
public partial class App : Application
{
    public static IServiceProvider ServiceProvider { get; private set; }

    protected override void OnStartup(StartupEventArgs e)
    {
        base.OnStartup(e);

        var serviceCollection = new ServiceCollection();
        ConfigureServices(serviceCollection);

        ServiceProvider = serviceCollection.BuildServiceProvider();

        var mainWindow = ServiceProvider.GetRequiredService<MainWindow>();
        mainWindow.Show();
    }

    private void ConfigureServices(IServiceCollection services)
    {
        // 注册窗口
        services.AddSingleton<MainWindow>();
        
        // 注册 ViewModel
        services.AddTransient<MainViewModel>();
        
        // 注册服务
        services.AddSingleton<IGreetingService, GreetingService>();
    }
}
```
使用构造函数注入依赖。示例：MainWindow.xaml.cs

```csharp
public partial class MainWindow : Window
{
    public MainWindow(MainViewModel viewModel)
    {
        InitializeComponent();
        DataContext = viewModel;
    }
}
```
服务接口和实现
```csharp
public interface IGreetingService
{
    string GetGreeting();
}

public class GreetingService : IGreetingService
{
    public string GetGreeting() => "你好，WPF with DI!";
}
```
MainViewModel.cs
```csharp
public class MainViewModel
{
    private readonly IGreetingService _greetingService;

    public string Message => _greetingService.GetGreeting();

    public MainViewModel(IGreetingService greetingService)
    {
        _greetingService = greetingService;
    }
}
```
# 配置
安装httpclient： nuget > Microsoft.Extensions.Http
![](images/posts/Pasted%20image%2020250629005449.png)


# FAQ


# References 
[.NET Community Toolkit source code github](https://github.com/CommunityToolkit/dotnet)
[# Windows Community Toolkit Documentation](https://learn.microsoft.com/en-us/windows/communitytoolkit/)