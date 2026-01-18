---
layout: post
title: Prism tutorial
author: Andy Feng
---
# Introduction
[Prism](https://prismlibrary.com/)是一个WPF开源框架，企业级 WPF MVVM 应用框架，旨在帮助开发者设计和构建松耦合、易维护、可测试的复合型应用程序（Composite Applications）。
https://github.com/PrismLibrary/Prism
传统的 WPF 开发如果处理不当，业务逻辑、UI 代码和数据访问会高度耦合，导致：
- **代码臃肿：** `MainWindow.xaml.cs` 变得极其庞大。    
- **难以测试：** UI 和逻辑混在一起，无法进行单元测试。    
- **协作困难：** 多个开发者很难同时修改同一个界面。
- View / ViewModel 手动绑定
- 页面切换全靠 `ContentControl + if/else
- ViewModel 之间互相引用
- 模块拆分困难
- 项目越写越“团成一坨”
## 问题
#### 1. 界面解构（Region Management）
- **痛点：** 在标准 WPF 中，如果你想在主窗体切换页面，通常需要手动控制 `Visibility` 或在 `MainViewModel` 里写一堆 `switch` 逻辑。    
- **Prism 方案：** 提出 **Region（区域）** 概念。主窗体只负责定义“坑”，任何模块都可以申请把自己的 View 填进这个“坑”。这让主窗体完全不需要知道子页面的存在。    
#### 2. 团队协作与并行开发（Modularity）
- **痛点：** 几十个 View 堆在一个项目中，多个人修改同一个项目文件经常导致 Git 冲突。    
- **Prism 方案：** **Module（模块化）**。你可以将不同的功能拆分成独立的工程（DLL）。开发 A 模块的人甚至不需要打开 B 模块的代码，通过配置即可在运行时组装。   
#### 3. 跨层级/跨模块通信（Event Aggregator）
- **痛点：** 两个完全没关系的 View 需要传消息，传统的做法是逐级传递回调或使用全局单例，导致代码耦合严重。
- **Prism 方案：** **事件聚合器**。像“广播站”一样，发送者只管发，订阅者只管收，彼此互不引用。
#### 4. 复杂的导航与生命周期（Navigation）
- **痛点：** 页面跳转时传参、权限拦截（如：未保存退出提示）在原生 WPF 中很难优雅实现。    
- **Prism 方案：** 完整的导航框架。支持 `OnNavigatedTo`（进入）、`OnNavigatedFrom`（离开）以及导航拦截接口。

Prism是应用架构框架，通过模块化 + 解耦解决了这些问题。
- **高度解耦：** 代码结构非常清晰。    
- **团队协作：** 适合大型项目，多人开发互不干扰。
- **易于测试：** 逻辑全部在 ViewModel 中，方便编写单元测试。    
- **社区强大：** 文档齐全，是 WPF 领域的工业标准。
# Prism 的基础结构
Prism =  Shell + Region + Navigation + Module + DI + MVVM 规范 + 解耦通信

Prism 的核心思想
1️⃣ **依赖注入（DI）**  
2️⃣ **ViewModel First**  
3️⃣ **Region（区域）**  
4️⃣ **模块化（Module）**  
5️⃣ **事件聚合器（EventAggregator）**

```
App
 |
 +--> Container (DI)
 |       |
 |       +--> Services
 |
 +--> ModuleCatalog
 |       |
 |       +--> Modules
 |
 +--> Shell
         |
         +--> Regions
                 |
                 +--> Views
                         |
                         +--> ViewModels
                                 |
                                 +--> BindableBase
                                 |
                                 +--> EventAggregator
                                 |
                                 +--> Commands
```
Prism 能解决的问题

| Prism 功能                                         | 对你有用吗？                           |
| ------------------------------------------------ | -------------------------------- |
| **Region / Navigation**                          | ✅ 可以统一管理 content 区切换，切换时自动调用生命周期 |
| **INavigationAware / IConfirmNavigationRequest** | ✅ 触发保存、确认离开等逻辑可以集中管理             |
| **模块化（模块加载/插件）**                                 | ✅ 长期维护 / 复杂功能扩展很方便               |
| **事件聚合（EventAggregator）**                        | ✅ 各模块通讯更干净                       |
| **依赖注入**                                         | ✅ 你已经用 DI，Prism 兼容               |
| **动态模板 / DataTemplateSelector**                  | ❌ Prism 不直接帮你管理模板内部保存逻辑          |

## Prism 应用入口
```
+------------------------------------------------+
|                    App.xaml                    |
|  (PrismApplication / PrismApplicationBase)     |
+------------------------+-----------------------+
                         |
                         v
                CreateShell()
                         |
                         v
+------------------------------------------------+
|                   Shell                        |
|            (MainWindow / RootView)             |
|                                                |
|  +------------------------------------------+  |
|  |  Region: MainRegion                      |  |
|  |  Region: NavigationRegion                |  |
|  |  Region: DialogRegion                    |  |
|  +------------------------------------------+  |
+------------------------------------------------+
```
Prism 接管了 App.xaml.cs 的启动流程
```csharp
public partial class App : PrismApplication
{
    protected override Window CreateShell()
    {
        return Container.Resolve<MainWindow>();
    }

    protected override void RegisterTypes(IContainerRegistry containerRegistry)
    {
    }
}
```
## Shell（外壳）Shell = 主窗口
```xml
<Window x:Class="MyApp.Views.MainWindow"
        xmlns:prism="http://prismlibrary.com/">
    <Grid>
        <ContentControl prism:RegionManager.RegionName="MainRegion"/>
    </Grid>
</Window>
```
Shell 里通常只放：
- 菜单    
- 布局    
- Region
## Region：Prism 的页面切换核心。
```
+-----------------+        +----------------------+
|  RegionManager  | -----> |   Region             |
|  (IRegionMgr)   |        |  (IRegion)           |
+-----------------+        +----------+-----------+
                                      |
                                      v
                          +----------------------+
                          |      View            |
                          |  (UserView, etc.)    |
                          +----------+-----------+
                                     |
                                     v
                          +----------------------+
                          |   ViewModel          |
                          | (DataContext)        |
                          +----------------------+
```
Prism 引入了 **Region** 的概念。你可以在主界面定义一个“区域”（Region），比如叫 `ContentRegion`。
- 你不需要在代码里写 `mainGrid.Children.Add(new UserControl())`。    
- 你只需要告诉 Prism：“请把 `OrderView` 导航到 `ContentRegion` 中”。

导航流程
```
RequestNavigate()
      |
      v
RegionManager
      |
      v
  Region
      |
      v
 Inject View into Shell Region
```
Region 是一个“可被动态填充 View 的占位符
```xml
<ContentControl prism:RegionManager.RegionName="MainRegion"/>
```
没有 Region 的切换方式（传统）
```csharp
CurrentView = new UserListView();
```
问题：
- 强耦合    
- 难维护    
- 不可模块化
Prism 的切换方式
```csharp
_regionManager.RequestNavigate(
    "MainRegion",
    "UserListView"
);
```
View 注册
```csharp
containerRegistry.RegisterForNavigation<UserListView>();
```
## navigation导航生命周期
```
              +-------------------------+
              |     INavigationAware    |
              +-------------------------+
              | + OnNavigatedTo()       |
              | + OnNavigatedFrom()     |
              | + IsNavigationTarget()  |
              +-------------------------+

              +---------------------------------+
              | IConfirmNavigationRequest       |
              +---------------------------------+
              | + ConfirmNavigationRequest()    |
              +---------------------------------+

```
**导航系统**。支持类似网页的导航（前进、后退、传参），并支持导航生命周期拦截（如：离开页面前提示保存）。

典型使用
```
ViewModel
   |
   +-- implements INavigationAware
   |
   +-- implements IConfirmNavigationRequest
```

ViewModel 实现接口
```csharp
public class UserEditViewModel : INavigationAware
{
    public void OnNavigatedTo(NavigationContext navigationContext)
    {
    }

    public void OnNavigatedFrom(NavigationContext navigationContext)
    {
        // 👉 切走时保存
    }

    public bool IsNavigationTarget(NavigationContext navigationContext)
    {
        return true;
    }
}
```
可以用来实现 切换 View 自动保存

## 模块化（Module）
允许你将应用程序拆分成多个独立的 **Module（模块）**（通常是不同的 DLL）。每个模块交给不同的开发者。
```
+----------------+
|   IModule      |
+----------------+
| RegisterTypes  |
| OnInitialized  |
+--------+-------+
         |
         v
+------------------------+
|  UserModule            |
|  ReportModule          |
|  SettingsModule        |
+------------------------+
```
一个模块=一个功能单元。
例如：
```
App
 ├─ Shell
 ├─ User（用户管理）
	├─ Views
	├─ ViewModels
	├─ UserModule.cs
 ├─ UserModule.cs
 ├─ Order（订单管理）
 ├─ Report（报表）
 ├─ Settings（设置）
 ├─ ModuleA
```
模块声明
```csharp
public class UserModule : IModule
{
    public void RegisterTypes(IContainerRegistry containerRegistry)
    {
        containerRegistry.RegisterForNavigation<UserListView>();
    }

    public void OnInitialized(IContainerProvider containerProvider)
    {
    }
}
```
注册模块
```csharp
protected override void ConfigureModuleCatalog(IModuleCatalog moduleCatalog)
{
    moduleCatalog.AddModule<UserModule>();
}
```
## EventAggregator：ViewModel 解耦通信
```
+----------------------+
|   EventAggregator    |
+----------------------+
          |
          v
+----------------------+
|   PubSubEvent<T>     |
+----------------------+
   ^              ^
   |              |
Publisher      Subscriber
```
**事件聚合器**。允许不相关的组件之间通信（发布/订阅模式）。比如：点击左侧列表，右侧详情页自动刷新，而两者互不引用。
解决view model之间通信的问题。

典型流程
```
Publish(Event)
      |
      v
EventAggregator
      |
      v
Notify Subscribers
```

定义event
```csharp
public class UserSavedEvent : PubSubEvent<int> { }
```
发布
```csharp
_eventAggregator
    .GetEvent<UserSavedEvent>()
    .Publish(userId);
```
订阅
```csharp
_eventAggregator
    .GetEvent<UserSavedEvent>()
    .Subscribe(OnUserSaved);
```
## Dialog Service
**对话框服务**。让你以 MVVM 的方式弹出模态窗口，而不需要在 ViewModel 里写任何 UI 相关的代码。
```
+-----------------------+
|   IDialogService     |
+-----------------------+
            |
            v
+-----------------------+
|   DialogWindow       |
+-----------------------+
            |
            v
+-----------------------+
|  DialogViewModel     |
|  (IDialogAware)     |
+-----------------------+
```
## MVVM支持
- **`BindableBase`**：一个实现了 `INotifyPropertyChanged` 的基类，让你写属性通知时非常简洁。
- **`DelegateCommand`**：强大的命令绑定工具，支持 UI 交互逻辑的编写。

Command 体系
```
+--------------------+
|   ICommand         |
+--------------------+
          ^
          |
+--------------------+
|  DelegateCommand   |
+--------------------+
| Execute()          |
| CanExecute()       |
+--------------------+
```
## 依赖注入（DI）
```
+------------------------+
|   IContainerRegistry   |
+------------------------+
| Register<T>()          |
| RegisterSingleton<T>() |
+------------+-----------+
             |
             v
+--------------------------+
|  DI Container            |
|  (DryIoc / Autofac)     |
+--------------------------+
             |
             v
+--------------------------+
|   ViewModel / Service   |
+--------------------------+
```
# Install
way1，注意extension已经多年没有维护，不推荐
Visual Studio > 扩展 (Extensions) ->  "Prism Template Pack" > 重启 VS
或者直接下载 https://marketplace.visualstudio.com/items?itemName=BrianLagunas.PrismTemplatePack
![](images/posts/20251219.jpeg)
新建项目时搜索 **"Prism"**，选择 **Prism Blank App (WPF)**。
![](images/posts/20251219-2.jpeg)
way2 如果你是在现有的项目里安装，nuget：
```
Prism.DryIoc
Prism.Core
Prism.Wpf
```

这会自动安装 Prism.Wpf 和 Prism.Core。
![](images/posts/20251219-1.jpeg)
安装完成后，你会得到这些关键类
- `PrismApplication`    
- `BindableBase    
- `DelegateCommand`
- `IRegionManager`
- `IModule`

改造启动项目 App.xaml / App.xaml.cs（核心步骤）
App.xaml
```xml
<Application x:Class="WinPrism.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:WinPrism"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
         
    </Application.Resources>
</Application>
```
不再指定 StartupUri, 修改Application名字，添加prism, 改成
```xml
<prism:PrismApplication x:Class="WinPrism.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:WinPrism"
             xmlns:prism="http://prismlibrary.com/">
    <prism:PrismApplication.Resources>
         
    </prism:PrismApplication.Resources>
</prism:PrismApplication>
```

App.xaml.cs
```csharp
using Prism.DryIoc;
using Prism.Ioc;
using System.Windows;

public partial class App : PrismApplication
{
    protected override Window CreateShell()
    {
        return Container.Resolve<MainWindow>();
    }

    protected override void RegisterTypes(IContainerRegistry containerRegistry)
    {
        // 注册服务
        containerRegistry.Register<IUserService, UserService>();
    }
}
```

创建 Shell（主窗口）> MainWindow.xaml
```xml
<Window x:Class="WinPrism.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WinPrism"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800"
        xmlns:prism="http://prismlibrary.com/"
        prism:ViewModelLocator.AutoWireViewModel="True"
        >
    <StackPanel VerticalAlignment="Center"
                HorizontalAlignment="Center"
                Width="300" >

        <TextBlock Text="{Binding Title}"
                   FontSize="24"
                   HorizontalAlignment="Center"/>

        <TextBlock Text="{Binding UserName}"
                   FontSize="16"
                   HorizontalAlignment="Center"/>

        <Button Content="Load User"
                Command="{Binding LoadUserCommand}"
                Height="35"/>
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
    }
}
```

与MainWindow同目录下创建MainWindowViewModel
```csharp
    public class MainWindowViewModel : BindableBase
    {
        private readonly IUserService _userService;

        // 构造函数注入（DI）
        public MainWindowViewModel(IUserService userService)
        {
            _userService = userService;

            LoadUserCommand = new DelegateCommand(OnLoadUser);

            Title = "Hello Prism";
        }

        // 标题
        private string _title;
        public string Title
        {
            get => _title;
            set => SetProperty(ref _title, value);
        }

        // 用户名
        private string _userName;
        public string UserName
        {
            get => _userName;
            set => SetProperty(ref _userName, value);
        }

        // 命令
        public DelegateCommand LoadUserCommand { get; }

        private void OnLoadUser()
        {
            UserName = _userService.GetUserName();
        }
    }
```

运行效果：
![](images/posts/20251219-3.jpeg)

创建第一个 View + ViewModel
```xml
<UserControl x:Class="Demo.Views.HomeView"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">

    <StackPanel>
        <TextBlock Text="Hello Prism!" FontSize="24"/>
    </StackPanel>
</UserControl>
```

```csharp
public class HomeViewModel : BindableBase
{}
```

ViewModel（HomeViewModel.cs）
```csharp
using Prism.Mvvm;

public class HomeViewModel : BindableBase
{
}
```

把 View 注入 Region. 在 `RegisterTypes` 中注册 View：
```csharp
protected override void RegisterTypes(IContainerRegistry containerRegistry)
{
    containerRegistry.RegisterForNavigation<HomeView>();
}
```

导航到 View
```csharp
public class MainWindowViewModel
{
    private readonly IRegionManager _regionManager;

    public MainWindowViewModel(IRegionManager regionManager)
    {
        _regionManager = regionManager;
        _regionManager.RequestNavigate("MainRegion", "HomeView");
    }
}
```
# FAQ

# References 
[