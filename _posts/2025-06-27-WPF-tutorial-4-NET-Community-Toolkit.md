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
NuGet 安装 `CommunityToolkit.Mvvm`
or 
dotnet add package CommunityToolkit.Mvvm
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
# How to use
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
# FAQ


# References 
[.NET Community Toolkit source code github](https://github.com/CommunityToolkit/dotnet)
[# Windows Community Toolkit Documentation](https://learn.microsoft.com/en-us/windows/communitytoolkit/)