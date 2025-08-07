---
layout: post
title: .NET Community Toolkit
author: Andy Feng
---
# Introduction
.NET Community Toolkit（简称 **Community Toolkit** 或 **Toolkit**）是微软和社区联合开发的一个开源库集合，2021年左右基于以前各类微软辅助库的基础上推出。旨在为 .NET 应用开发者提供一组高效、易用且现代化的工具和辅助库，提升开发效率和代码质量。

它主要聚焦于：

- **MVVM 模式支持**（主要针对 WPF、WinUI、UWP 等桌面与现代应用）    
- **性能优化**（如高性能集合和辅助类）    
- **简化常见任务**（如事件处理、消息传递、数据绑定等）
## 发展历程：

| 时间        | 事件/版本                              | 说明                                        |
| --------- | ---------------------------------- | ----------------------------------------- |
| 2016-2018 | **Windows Community Toolkit**      | 针对 UWP 的社区工具库，微软主导，包含控件、MVVM、辅助方法等。       |
| 2019-2020 | 工具包逐渐成熟，支持多种UWP特性                  | 增加更多控件和扩展，社区贡献活跃。                         |
| 2021年     | **重构成 .NET Community Toolkit**     | 重命名并开始支持 WinUI 3 和更广泛的 .NET 平台（包括 WPF）。   |
| 2021-2023 | 模块化拆分，MVVM Toolkit 独立              | MVVM Toolkit 模块脱离主工具包，成为独立的 NuGet 包，简洁轻量。 |
| 2023-2024 | 支持 .NET 6 / 7 / MAUI，加入更多性能优化和扩展集合 | 持续更新，增强性能，支持跨平台（WinUI、WPF、MAUI）。          |
| 2025年+    | 持续迭代，聚焦开发者体验和跨平台支持                 | 预计将增强与 Blazor 等新兴框架集成，强化开发者工具链。           |
## 为什么使用MVVC Toolkit？

|问题（过去的 MVVM）|MVVM Toolkit 的解决方案|
|---|---|
|要手写 `INotifyPropertyChanged`|✅ 用 `[ObservableProperty]` 自动生成|
|要写一堆 `RelayCommand`|✅ 用 `[RelayCommand]` 自动生成命令|
|属性变更还要手动 `OnPropertyChanged()`|✅ 自动生成通知逻辑|
|手写命令类重复代码多|✅ 一个属性 + 属性名 = 自动生成命令|
|没有统一风格|✅ 微软官方风格，轻量、现代、跨平台|
# Install
`.NET Community Toolkit` 是一组轻量级、模块化的 NuGet 包，涵盖了 MVVM、性能增强、通知机制等多个方面。其中，**MVVM Toolkit** 是最核心、最常用的部分。
NuGet 安装核心包 
```
CommunityToolkit.Mvvm
```
 ``
or 
```
dotnet add package CommunityToolkit.Mvvm
```
在项目中启用代码生成（optional）
在 .csproj 文件中添加：
```xml
<PropertyGroup>
    <EnableMvvmToolkit>true</EnableMvvmToolkit>
</PropertyGroup>
```

这是最常用、最核心的 MVVM 模块。其他模块（如 Collections、Diagnostics）可以根据需要添加。

| 模块名称                               | 功能简介                                            |
| ---------------------------------- | ----------------------------------------------- |
| `CommunityToolkit.Mvvm`            | MVVM 支持：`ObservableObject`, `RelayCommand`, 消息等 |
| `CommunityToolkit.Collections`     | 高性能集合，支持异步分页、线程安全集合等                            |
| `CommunityToolkit.Diagnostics`     | 更智能的调试辅助，如 `Guard`                              |
| `CommunityToolkit.HighPerformance` | 针对 Span/Memory 的性能优化方法                          |
| `CommunityToolkit.WinUI`           | 针对 WinUI 的 UI 扩展和控件（适用于 WinUI 项目）               |
MVVM Toolkit 核心功能

|功能|用法例子|
|---|---|
|自动实现属性通知|`[ObservableProperty] string name;` → 自动生成属性和通知|
|自动生成命令|`[RelayCommand] void Save()` → 自动生成 `SaveCommand`|
|异步命令支持|`[RelayCommand] async Task LoadAsync()`|
|属性变化通知关系|`[NotifyPropertyChangedFor(nameof(IsValid))]`|
|ViewModel 基类|`ObservableObject`, `ObservableRecipient`, `ObservableValidator`|
|消息通信（解耦）|`IMessenger`, `WeakReferenceMessenger`|
## 代码生成器Source Generator
**代码生成器的核心目的**

> **自动生成样板代码，提升开发效率、减少重复劳动，并保持代码一致性与可维护性。**
> 让你专注于业务逻辑，不必手动重复写 PropertyChanged、Command 等繁琐样板代码 —— 前提是：你要“显式”让它知道要生成谁。

在 `CommunityToolkit.Mvvm` 中的用途：

| 功能                       | 原来怎么写                       | 用了代码生成器怎么写                    |
| ------------------------ | --------------------------- | ----------------------------- |
| `INotifyPropertyChanged` | 手写事件和 `OnPropertyChanged()` | 用 `[ObservableProperty]` 自动生成 |
| 命令（Command）              | 手写 `RelayCommand`，绑定事件      | 用 `[RelayCommand]` 自动生成       |
| 属性更名、同步字段                | 手动处理 backing field          | 自动生成 public 属性和同步逻辑           |
| 支持 MVVM 模式               | 自己写一堆样板 ViewModel           | 直接标记属性，精简 ViewModel           |
请注意！因为 Source Generator 只会对「**在编译时被显式引用的代码**」进行生成。  
如果你的类是通过反射动态实例化的，代码中 **没有直接 `new 模板()` 或 `typeof(模板)。生成器就以为这个类“没有被使用”，所以不处理它。
这时可以随便调用一下，让编译器也能看到类型引用。比如：
```csharp
// 可以放在 Program.cs、App.xaml.cs 等任意地方
_ = typeof(Error1QuestionPicture1CorrectPictureCellphoneTemplate);

//可以更直白的写
var dummy = new Error1QuestionPicture1CorrectPictureCellphoneTemplate(null);

```
这就足以让生成器运行并生成 `Error1QuestionPicture1CorrectPictureCellphoneTemplate.g.cs`。
你只需要**让编译器知道这个类存在，且需要参与编译**，就会触发 MVVM Toolkit 的 Source Generator。

# How to use
典型
```csharp
using CommunityToolkit.Mvvm.ComponentModel;
using CommunityToolkit.Mvvm.Input;

[INotifyPropertyChanged]
public partial class MyViewModel : MyCustomBaseViewModel
{
    [ObservableProperty]
    private string userName;

    [RelayCommand]
    private void SayHello()
    {
        MessageBox.Show($"Hi, {UserName}");
    }
}
```
## ViewModel 继承 `ObservableObject`
记住
`[ObservableProperty]` 是作用在字段（field）上，而不是属性（property）上！
`partial class`    
显示引用、使用该类，这样编译器内source generator能探测到这个类被使用，从而构建。
```csharp
using CommunityToolkit.Mvvm.ComponentModel;

public partial class MyViewModel : ObservableObject
{
}
```
等同与
```csharp
[INotifyPropertyChanged]
public partial class MyViewModel
{
}
```
## 无参同步命令（用 `[RelayCommand]`）
```csharp
[RelayCommand]
private void SayHello()
{
    MessageBox.Show("Hello!");
}
```
会自动生成 `public IRelayCommand SayHelloCommand`，XAML 直接绑定即可。
```xml
<Button Content="Say Hello" Command="{Binding SayHelloCommand}" />
```
无参异步命令（`AsyncRelayCommand`）
```csharp
[RelayCommand]
private async Task LoadDataAsync()
{
    await Task.Delay(1000); // 模拟加载
    MessageBox.Show("Data loaded");
}
```
会自动生成 `LoadDataAsyncCommand`，并在运行时管理按钮启用状态。
```xml
<Button Content="Load" Command="{Binding LoadDataAsyncCommand}" />
```
## 有参数同步命令（自动生成）
```csharp
[RelayCommand]
private void ShowMessage(string msg)
{
    MessageBox.Show(msg);
}
```
```xml
<Button Content="Hi" Command="{Binding ShowMessageCommand}" CommandParameter="Hi there!" />
```
## 有参数异步命令（自动生成）

```csharp
[RelayCommand]
private async Task FetchAsync(int id)
{
    await Task.Delay(500);
    MessageBox.Show($"Fetched ID: {id}");
}
```
```xml
<Button Content="Fetch" Command="{Binding FetchAsyncCommand}" CommandParameter="42" />
```
## 不用特性 `[RelayCommand]`，手动初始化命令
```csharp
public IRelayCommand SayHiCommand { get; }

public MyViewModel()
{
    SayHiCommand = new RelayCommand(() => MessageBox.Show("Hi!"));
}
```
或异步版本：
```csharp
public IAsyncRelayCommand LoadDataCommand { get; }

public MyViewModel()
{
    LoadDataCommand = new AsyncRelayCommand(LoadDataAsync);
}

private async Task LoadDataAsync()
{
    await Task.Delay(1000);
}
```
## 支持 CanExecute（控制按钮是否启用）
```csharp
private bool canSubmit = false;

[ObservableProperty]
private string name;

[RelayCommand(CanExecute = nameof(CanSubmit))]
private void Submit()
{
    MessageBox.Show($"Submitted: {Name}");
}

private bool CanSubmit()
{
    return !string.IsNullOrWhiteSpace(Name);
}
```
当 `Name` 改变时，`SubmitCommand` 会自动判断是否启用。
## 记住命名规则（自动生成命令名）

| 方法名               | 生成的命令名                 |
| ----------------- | ---------------------- |
| `SayHello()`      | `SayHelloCommand`      |
| `LoadDataAsync()` | `LoadDataAsyncCommand` |
| `Fetch(id)`       | `FetchCommand`         |

# 实例
一个简单的 WPF MVVM 例子，功能是一个文本输入框和一个按钮，点击按钮后弹出输入内容的消息框。
- 一个 TextBox 双向绑定 `UserInput` 属性    
- 一个按钮绑定 `ShowMessageCommand`    
- 点击按钮，弹出消息框显示当前 `UserInput` 内容
## **传统写法**：没有用 .NET Community Toolkit，自己实现 `INotifyPropertyChanged` 和 `ICommand`。
ViewModel.cs

```csharp
using System;
using System.ComponentModel;
using System.Windows.Input;

public class MainViewModel : INotifyPropertyChanged
{
    private string _userInput;

    public string UserInput
    {
        get => _userInput;
        set
        {
            if (_userInput != value)
            {
                _userInput = value;
                OnPropertyChanged(nameof(UserInput));
            }
        }
    }

    public ICommand ShowMessageCommand { get; }

    public MainViewModel()
    {
        ShowMessageCommand = new RelayCommand(ShowMessage);
    }

    private void ShowMessage()
    {
        System.Windows.MessageBox.Show($"你输入了: {UserInput}");
    }

    public event PropertyChangedEventHandler? PropertyChanged;
    protected void OnPropertyChanged(string propertyName) =>
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
}

// ICommand 实现
public class RelayCommand : ICommand
{
    private readonly Action _execute;
    private readonly Func<bool>? _canExecute;

    public RelayCommand(Action execute, Func<bool>? canExecute = null)
    {
        _execute = execute ?? throw new ArgumentNullException(nameof(execute));
        _canExecute = canExecute;
    }

    public bool CanExecute(object? parameter) => _canExecute?.Invoke() ?? true;

    public void Execute(object? parameter) => _execute();

    public event EventHandler? CanExecuteChanged;

    public void RaiseCanExecuteChanged() =>
        CanExecuteChanged?.Invoke(this, EventArgs.Empty);
}
```
MainWindow.xaml (View)
```xml
<Window x:Class="WpfApp.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="传统写法" Height="150" Width="300">
    <StackPanel Margin="20">
        <TextBox Text="{Binding UserInput, UpdateSourceTrigger=PropertyChanged}" />
        <Button Content="显示消息" Command="{Binding ShowMessageCommand}" Margin="0,10,0,0"/>
    </StackPanel>
</Window>
```
MainWindow.xaml.cs
```csharp
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
        DataContext = new MainViewModel();
    }
}
```
## **使用 Community Toolkit**：用它自带的 `ObservableObject` 和 `RelayCommand`，代码简洁很多。

ViewModel.cs
```csharp
using CommunityToolkit.Mvvm.ComponentModel;
using CommunityToolkit.Mvvm.Input;
using System.Windows;

public partial class MainViewModel : ObservableObject
{
    [ObservableProperty]
    private string userInput;

    public MainViewModel()
    {
        ShowMessageCommand = new RelayCommand(() =>
        {
            MessageBox.Show($"你输入了: {UserInput}");
        });
    }

    public RelayCommand ShowMessageCommand { get; }
}
```
注意：
- 用 `[ObservableProperty]` 特性自动生成 `UserInput` 属性和通知代码    
- 直接用 `RelayCommand`，不用自己写 ICommand 实现

MainWindow.xaml (View) 不变
```xml
<Window x:Class="WpfApp.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="传统写法" Height="150" Width="300">
    <StackPanel Margin="20">
        <TextBox Text="{Binding UserInput, UpdateSourceTrigger=PropertyChanged}" />
        <Button Content="显示消息" Command="{Binding ShowMessageCommand}" Margin="0,10,0,0"/>
    </StackPanel>
</Window>
```
MainWindow.xaml.cs 不变
```csharp
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
        DataContext = new MainViewModel();
    }
}
```
对比

|点|传统写法|使用 Toolkit|
|---|---|---|
|代码量|较多，需要手动写 `INotifyPropertyChanged` 和 `ICommand` 实现|非常简洁，属性和命令通过特性和基类自动生成|
|可维护性|代码重复、易错，特别是属性通知|结构清晰，减少样板代码|
|学习曲线|理解接口细节较多|更易上手，专注业务逻辑|
|性能|好，但写法繁琐|利用 source generators 优化性能|
|社区和微软支持|需要自己维护和测试|官方支持，持续更新，社区活跃|
## RelayCommand
是的，`CommunityToolkit.Mvvm` 提供了**几种类型的 Command（命令）类**，包括支持同步和异步的版本，满足你不同场景的需求。
Toolkit 提供的命令类型

| 类名                     | 是否异步 | 是否支持泛型参数 | 常用场景说明          |
| ---------------------- | ---- | -------- | --------------- |
| `RelayCommand`         | ❌ 否  | ✅ 是      | 普通命令（按钮点击等）     |
| `AsyncRelayCommand`    | ✅ 是  | ✅ 是      | 异步任务（加载数据、调用网络） |
| `RelayCommand<T>`      | ❌ 否  | ✅ 是      | 带参数的普通命令        |
| `AsyncRelayCommand<T>` | ✅ 是  | ✅ 是      | 带参数的异步命令        |
### 使用示例

同步命令：`RelayCommand`
```csharp
public IRelayCommand GreetCommand { get; }

public MyViewModel()
{
    GreetCommand = new RelayCommand(() => MessageBox.Show("Hello!"));
}
```
异步命令：`AsyncRelayCommand`
```csharp
public IAsyncRelayCommand LoadDataCommand { get; }

public MyViewModel()
{
    LoadDataCommand = new AsyncRelayCommand(LoadDataAsync);
}

private async Task LoadDataAsync()
{
    await Task.Delay(1000); // 模拟异步操作
}
```
异步带参数命令：`AsyncRelayCommand<T>`
```csharp
public IAsyncRelayCommand<string> ShowMessageCommand { get; }

public MyViewModel()
{
    ShowMessageCommand = new AsyncRelayCommand<string>(async msg =>
    {
        await Task.Delay(500);
        MessageBox.Show(msg);
    });
}
```
使用 `[RelayCommand]` 自动生成命令
```csharp
[RelayCommand]
private void SayHi()
{
    MessageBox.Show("Hi");
}

[RelayCommand]
private async Task LoadDataAsync()
{
    await Task.Delay(1000);
}
```
- 上面两个方法会自动生成 `SayHiCommand` 和 `LoadDataAsyncCommand`。    
- 命名遵循：方法名 + `Command`
# FAQ
## 我想修改现有的结构，现在用的是原生的写法。我可以只修改model吗，不修改command部分？
你完全可以 **只修改 Model / ViewModel 的属性声明部分，先不动命令（Command）部分**。这是 `CommunityToolkit.Mvvm` 的一大优势 —— **模块化、渐进式引入**。
假设你现在的 ViewModel 是这样写的（原生方式）：
```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    private string name;
    public string Name
    {
        get => name;
        set
        {
            if (name != value)
            {
                name = value;
                OnPropertyChanged(nameof(Name));
            }
        }
    }

    public event PropertyChangedEventHandler PropertyChanged;
    protected void OnPropertyChanged(string propertyName) =>
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
}
```
你可以只改为：
```csharp
using CommunityToolkit.Mvvm.ComponentModel;

public partial class PersonViewModel : ObservableObject
{
    [ObservableProperty]
    private string name;
}
```
这就完成了属性的迁移，`INotifyPropertyChanged`、事件触发器、Setter 都自动生成了，View 正常绑定依然有效。
## 使用toolkit的attribute，顺序有要求吗？比如[ObservableProperty] 一定要写在最上面吗？
 **顺序没有严格要求，属性顺序可以任意写**
C# 编译器对 attribute 顺序是**无关紧要的**，只要你所有 attribute 都写在字段上（**不是属性上**），`CommunityToolkit.Mvvm` 的源生成器就能识别并正确处理。
建议的顺序（为了**可读性和一致性**）：
不是语法要求，而是**风格推荐**：

| 推荐顺序                           | 理由              |
| ------------------------------ | --------------- |
| `[ObservableProperty]`         | 放最上，表明这个字段会生成属性 |
| `[Display]`、`[Browsable]` 等元数据 | 放下面，修饰生成的属性行为   |
| 自定义属性                          | 放在最下面，有效分层      |
唯一要注意的是：

> 所有属性必须标在 **字段** 上，不能写在自动属性或方法上，否则源生成器不会生效。
```csharp
// ✅ 正确：字段
[ObservableProperty]
private string name;

// ❌ 错误：完整属性
[ObservableProperty]
public string Name { get; set; }  // ❌ 编译错误
```

# References 
[.NET Community Toolkit source code github](https://github.com/CommunityToolkit/dotnet)
[# Windows Community Toolkit Documentation](https://learn.microsoft.com/en-us/windows/communitytoolkit/)