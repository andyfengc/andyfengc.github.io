---
layout: post
title: WPF Tutorial 1
author: Andy Feng
tags:
---
# Introduction
MVVM（Model-View-ViewModel）是一种常用于构建用户界面的**软件架构模式**，特别适用于支持数据绑定的框架，如 **WPF**、**Xamarin**、**WinUI**、**MAUI** 等。MVVM 通过**分离界面和逻辑**，提高了代码的可维护性、可测试性和可重用性。

![](/images/posts/Pasted%20image%2020250627002914.png)
## 历史发展
MVVM（Model-View-ViewModel）是在微软推出 WPF 时，为了解决 UI 与逻辑强耦合问题，借鉴 MVC/MVP 等模式而诞生的架构模式。
早期桌面开发：UI 和逻辑混在一起（如 WinForms）。在 WinForms / VB6 / MFC 时代：
- 所有事件处理都写在按钮点击、窗体加载等事件里    
- UI 和逻辑紧耦合，维护困难，复用性差 
- 你改 UI，可能要改一堆逻辑；你改逻辑，可能要重新布控件
```csharp
btnSave.Click += (s, e) =>
{
    var name = txtName.Text;
    SaveToDatabase(name);
    MessageBox.Show("保存成功");
};
```

MVC/MVP 出现：开始尝试**分离逻辑和 UI**

| 模式                             | 说明                                   |
| ------------------------------ | ------------------------------------ |
| **MVC**（Model-View-Controller） | 控制器处理用户输入，更新 Model 和 View（如 ASP.NET） |
| **MVP**（Model-View-Presenter）  | View 是被动的，由 Presenter 来驱动            |
这些适合 Web，但在桌面（特别是数据双向绑定）上仍然繁琐：
- Presenter 仍然需要写很多 UI 更新代码（`textBox.Text = model.Name`）

WPF 2006年发布，催生 MVVM。MVVM 是微软为配合 WPF 的数据绑定系统而设计的架构模式

| 特点                     | 原因           |
| ---------------------- | ------------ |
| WPF 支持强大的数据绑定系统        | 不再需要手动操作控件   |
| 控件属性可直接绑定 ViewModel 属性 | 需要一套更清晰的分层设计 |
MVVM 的结构 #".net"

| 层             | 职责                                    |
| ------------- | ------------------------------------- |
| **Model**     | 数据结构、业务规则                             |
| **View**      | 纯 UI，使用 XAML                          |
| **ViewModel** | 暴露属性/命令供 View 绑定，处理 UI 逻辑，**不直接操作控件** |
WPF使用内建的绑定系统来完成绑定，自动调用，自动更新

MVVM的发展（跨平台 + 框架）

| 阶段                              | 演进                                       |
| ------------------------------- | ---------------------------------------- |
| .NET MVVM 初期                    | 手写 INotifyPropertyChanged、RelayCommand   |
| MVVM Light、Caliburn.Micro、Prism | 提供简化 ViewModel 编写的工具和导航、消息通信             |
| Xamarin.Forms / MAUI            | 将 MVVM 用于跨平台移动开发                         |
| .NET Community Toolkit MVVM     | 微软官方支持的现代 MVVM 框架，轻量、现代、Source Generator |
## 我的总结, chatgpt认可
MVVM是一套框架设计模式。他的设计目标是，实现UI和ViewModel的解耦，而且还能实现两者的双向同步，提升开发效率和可维护性。

MVVM改进了MVC, MVP框架，去除了 Controller/Presenter对UI的直接操作，利用绑定系统和通知机制自动同步状态。他综合了多个设计模式，比如观察者模式（属性通知），命令模式（行为封装)，工厂模式等设计出来的。

WPF是MVVM的实现，提供了内建的一系列机制来实现这个目标。
> Binding 系统（数据绑定）
> ICommand（命令绑定）
> DependencyProperty（支持变更通知），就是写一个类，封装了某一个数据的值和操作方法，然后把这个类注册到WPF框架
> DataTemplate - 模板化的view

View通过绑定机制，将属性和事件绑定到ViewModel。
> 绑定属性：Text="{Binding Name}" → 自动连接 ViewModel 的属性。
> 绑定命令：Command="{Binding SaveCommand}" → 自动连接 ViewModel 的命令逻辑。> 

View通过命令模式解耦，在WPF框架帮助下执行业务逻辑。
> View 不再直接调用方法，而是绑定到实现了 ICommand 的对象（命令模式）。WPF 框架负责帮你在点击按钮时自动调用 Command.Execute()。

ViewModel通过delegate事件机制，将数据变化通知给WPF框架，由WPF绑定系统负责自动更新UI，实现与UI的解耦。
View不封装数据，如果一定要放数据，就设计一个view model，一定要放到view model里。View只**用于显示 UI 和响应用户交互**，所有的数据、状态、行为都交给 ViewModel 来处理。这样很容易注入mock的view model 测试ui
View不应该引用另一个view，这让 View 和 View 紧耦合，不符合 MVVM。

View知道ViewModel。但ViewModel不知道UI。
> View 通过 DataContext = new ViewModel() 明确引用 ViewModel
> ViewModel 完全不知道 UI 的存在 —— 这样就能进行 单元测试、重用、抽离逻辑

基本做法：

| 组件                         | 说明                              |
| -------------------------- | ------------------------------- |
| `EditCategoryViewModel`    | 绑定字段、命令（SaveCommand）、加载逻辑       |
| `EditCategoryView.xaml`    | 使用 `TextBox` 绑定字段，按钮绑定命令        |
| `EditCategoryView.xaml.cs` | 只负责注入依赖，传递参数，调用 ViewModel 的加载方法 |
## MVVM 的三大组成部分

| 角色            | 作用说明                                                                                                  |
| ------------- | ----------------------------------------------------------------------------------------------------- |
| **Model**     | 表示应用程序的**数据**和**业务逻辑**。通常是实体类、服务类、数据库访问、API 调用等。不依赖 UI，纯粹面向业务。                                        |
| **View**      | 表示**界面**（UI），通常是 XAML 文件（如 `MainWindow.xaml`）。通过数据**绑定**展示 ViewModel 提供的数据。不包含业务逻辑。                   |
| **ViewModel** | 是 View 和 Model 的桥梁。负责**数据转换、命令实现、通知 View 更新**。ViewModel 不依赖 View，也不知道具体哪个View，他提供供 View **绑定**的属性和命令。 |
|               |                                                                                                       |
## MVVM 的优点
✅ 清晰分离 UI 和逻辑，降低耦合  
✅ 提高测试性（可为 ViewModel 编写单元测试）  
✅ 便于多人协作（UI 和逻辑可以分工）  
✅ 支持 UI 重用和主题切换
## WPF 的三大核心特性决定了MVVM
### 1. **数据绑定（Data Binding）**
- WPF View（XAML）可直接绑定 ViewModel 的属性。    
- 不需要 Controller 手动操作 UI。    
### ✅ 2. **命令系统（ICommand）**
- WPF 提供 `ICommand` 接口，代替传统的事件处理器。    
- ViewModel 可定义命令，View 绑定即可。    
### ✅ 3. **通知机制（INotifyPropertyChanged）**
- 支持 UI 随数据变化自动更新。
# 主要概念
## 数据绑定（Data Binding） 

View 通过绑定 ViewModel 的属性（如 `Text="{Binding UserName}"`）自动显示数据。
^wpf-databinding
所谓绑定就是把自己注册到订阅列表里 
## **属性通知（INotifyPropertyChanged）**
这是一个接口，表示“我会在属性变更时通过事件通知外部”
ViewModel 实现 `INotifyPropertyChanged` 接口，属性值变更时通知 UI 自动更新。
https://github.com/microsoft/referencesource/blob/main/System/compmod/system/componentmodel/INotifyPropertyChanged.cs
```csharp
public interface INotifyPropertyChanged
    {
        /// <devdoc>
        /// </devdoc>
        event PropertyChangedEventHandler PropertyChanged;
    }
```
https://github.com/microsoft/referencesource/blob/main/System/compmod/system/componentmodel/PropertyChangedEventHandler.cs
```csharp
public delegate void PropertyChangedEventHandler(object sender, PropertyChangedEventArgs e);
```
例如：
```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    private string _name;
    public string Name {
        get => _name;
        set {
            if (_name != value) {
                _name = value;
                // 事件发生，调用内部方法进行处理
                OnPropertyChanged(nameof(Name));
            }
        }
    }
    
	// 定义一个delegate引用，还没有赋值。由外部+或-来订阅
    public event PropertyChangedEventHandler? PropertyChanged;

	// 在Windows form中，control本身包括UI和代码2部分，事件由代码处理并负责手动更新UI
	// 在wpf中，代码负责处理事件，并依次调用外部subscribers注册过的方法。代码不负责更新UI
    protected void OnPropertyChanged(string propName)
        => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propName));
}
```
WPF 的事件绑定 vs WinForms 的区别

|项目|**WPF / MVVM**|**Windows Forms**|
|---|---|---|
|数据绑定方式|通过 `INotifyPropertyChanged` 和 `Binding` 实现属性与 UI 的同步|通常需要手动设置控件值，如 `textBox1.Text = person.Name;`|
|使用 delegate 的方式|**事件用于通知绑定系统更新 UI**|**事件用于响应 UI 行为（如 Click）**|
|事件机制|`event` + `delegate` + `INotifyPropertyChanged`|`event` + `delegate` 直接用于事件回调|
|控件绑定|支持双向自动绑定，如 `{Binding Name}`|需要自己写事件处理器来同步数据|
|用途|逻辑层 ViewModel -> View 的通知机制|交互层 View -> Logic 的事件处理机制|
## 绑定系统 (Binding system)
**绑定系统（Binding System）** 是 WPF 框架内部的“监听+同步”引擎：
它负责：
- 查找 Binding 的数据源（DataContext）    
- 创建 `BindingExpression`    
- 订阅 `INotifyPropertyChanged` 事件 
- 当你调用 `OnPropertyChanged(...)` 时，自动更新 UI 控件

你在 ViewModel 里定义的这个事件：
	public event PropertyChangedEventHandler? PropertyChanged;
WPF 内部的绑定系统会做一系列事情。
包括绑定属性值，自动注册一个处理函数到`PropertyChanged`事件上（`+=`）等等

当你调用 `.Invoke()` 触发事件时，它就会运行那个处理函数，告诉系统：“这个属性值变了！”
**然后 WPF 框架就去更新 UI 了**
### 绑定系统（Binding System）是 MVVM 的核心
没有“绑定系统”，就没有 MVVM。

| 原因                | 说明                                                                   |
| ----------------- | -------------------------------------------------------------------- |
| 🔗 **解耦 UI 与逻辑**  | View 不再手动调用 ViewModel，只需绑定表达式                                        |
| 🔄 **自动同步状态**     | 属性值一变，UI 自动更新；UI 输入，数据自动同步                                           |
| 📉 **替代事件、赋值、回调** | View不需要写 `Button.Click += ...` <br>后台代码不需要写 `textBox.Text = vm.Name` |
| 🤖 **命令绑定代替逻辑控制** | 不再在 code-behind 写事件，统一用命令触发                                          |
绑定系统的核心技术包括
-  delegate  
- `ICommand`（行为命令接口）    
- `反射`（访问属性值） 绑定系统通过反射找 ViewModel 中的属性，比如 `Name`、`SaveCommand`
- `依赖属性`（DependencyProperty，View 控件层面的核心）所有 WPF 控件的属性（如 TextBox.Text）都必须是依赖属性，才能绑定和响应变化
- BindingExpression (内部对象管理源、路径、模式（OneWay/TwoWay）、更新方向等)
- **值转换器（IValueConverter）** 在绑定路径中进行格式转换
- UpdateSourceTrigger 控制何时触发数据源更新（LostFocus、PropertyChanged）

| 技术                   | 是否绑定系统核心？     | 作用                              |
| -------------------- | ------------- | ------------------------------- |
| ✅ delegate           | ✅ 是（事件通知机制核心） | 通知属性值变化（INotifyPropertyChanged） |
| ✅ 反射                 | ✅ 是           | 用反射定位属性路径<br>找到属性 getter/setter |
| ✅ DependencyProperty | ✅ 是           | 控件能被绑定的基础                       |
| ✅ ICommand           | ✅ 是           | 控件行为与逻辑解耦                       |
| ✅ BindingExpression  | ✅ 是           | 绑定表达式对象，管理绑定行为                  |
### 完整绑定触发链（双向绑定示例）
```
<TextBox Text="{Binding Name, Mode=TwoWay}" />
```

### → UI 输入数据：
1. 用户输入 TextBox    
2. 触发 DependencyProperty 变更 → 调用 `BindingExpression.UpdateSource()`    
3. 通过反射写入ViewModel的 `Name` 属性 → 同时触发ViewModel的 PropertyChanged 事件    
4. 若绑定了多个控件，会触发其它控件同步更新    
### → ViewModel 改数据：
1. 设置 `Name` 属性并触发 `PropertyChanged`    
2. 绑定系统收到事件 → 调用 `BindingExpression.UpdateTarget()`    
3. 将新值写入 TextBox
绑定系统负责维护其中的复杂关系
### 具体绑定流程
       开发者写：
`<TextBox Text="{Binding Name}" />`
              ↓
        Binding 对象（绑定描述）
              ↓
  BindingExpression（绑定执行对象）
              ↓
   绑定系统监听 ViewModel 的 Name 属性
              ↓
      ViewModel 属性发生变化
              ↓
     触发 PropertyChanged 事件
              ↓
   BindingExpression 捕捉事件并更新 UI

Binding 系统组件总览

| 组件                         | 说明                                               |
| -------------------------- | ------------------------------------------------ |
| **Binding**                | 表示绑定描述（路径、源、模式等）。配置谁绑定谁。<br>相当于快递单，描述            |
| SetBinding()               | 注册绑定关系到依赖属性。<br>相当于注册快递单                         |
| **BindingExpression**      | 表示“某一个绑定关系”的执行体。执行绑定，双向监听变更，负责更新。<br>相当于快递员，具体送货 |
| **DataBindEngine**         | 内部绑定系统的总控中心（单例），负责所有绑定操作的调度。<br>相当于物流公司          |
| **PropertyPathWorker**     | 解析绑定路径如 `"User.Address.City"`                    |
| **INotifyPropertyChanged** | ViewModel 触发属性变更事件，驱动更新。ViewModel 通知绑定系统，数据已更改   |
| PropertyChanged            | ViewModel的属性变更事件。<br>相当于电话通知（货到了）                |
| **DependencyProperty**     | 控件属性系统，用于接收绑定值，支持绑定和回调。<br>相当于收件地址（控件属性）         |

下面逐句分析
```csharp
<TextBox Text="{Binding Name}" />
```
WPF 运行时等价于执行：
```
var binding = new Binding("Name");
textBox.SetBinding(TextBox.TextProperty, binding);
```
WPF 中的 `Binding` 是一个类：`System.Windows.Data.Binding`
它表示“控件的某个属性”如何与“数据源的某个属性”建立关系。

FrameworkElement.SetBinding(...) 传入控件本身、目标依赖属性、`Binding` 描述对象
```csharp
public BindingExpressionBase SetBinding(DependencyProperty dp, BindingBase binding)
{
    return BindingOperations.SetBinding(this, dp, binding);
}
```
BindingOperations.SetBinding(...)
```csharp
public static BindingExpressionBase SetBinding(
    DependencyObject target, DependencyProperty dp, BindingBase binding)
{
    return binding.ProvideValueInternal(...); // 简化解释
}
```
这里调用了 `Binding.ProvideValue(...)`，最终会创建一个 BindingExpression对象
`BindingExpression` 是绑定关系的执行者，负责监听源、更新目标。

BindingExpression.Attach(...)
执行以下事情：
- 确定绑定源（默认是 `DataContext`）    
- 用 `PropertyPathWorker` 解析路径（比如 `User.Name`）    
- 注册到源对象的 `PropertyChanged` 事件上    
- 将源值写入目标 `DependencyProperty`

注意`BindingExpression` 实现了一个 `PropertyChanged` 监听器，负责监听属性变化
```csharp
public class BindingExpression : BindingExpressionBase
{
    private void OnPropertyChanged(object sender, PropertyChangedEventArgs e)
    {
        if (e.PropertyName == Path) {
            UpdateTarget();
        }
    }
}
```
当你在 ViewModel 中这样写：
```
_name = value;
PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name));
```
如果有属性变化，`BindingExpression` 被触发，调用 `UpdateTarget()` 把新值写到 UI 控件的 `TextProperty` 上。

UI 更新：DependencyProperty 系统
控件属性不是普通字段，而是：
```csharp
public static readonly DependencyProperty TextProperty = ...
```
这些 `DependencyProperty` 提供能力：
- 能被绑定    
- 能被监听    
- 能在值改变时通知控件刷新
### Binding 的核心类原理剖析
Binding 它只负责“描述绑定”，不负责执行。
```csharp
public class Binding : BindingBase
{
    public string Path { get; set; } // 要绑定的属性路径
    public object Source { get; set; } // 显式设置的数据源（可选）
    public BindingMode Mode { get; set; } // OneWay、TwoWay 等
    public UpdateSourceTrigger UpdateSourceTrigger { get; set; } // 更新时机
    ...
}
```
BindingExpression 是绑定系统的“执行体”，负责监听、更新、转换。
```csharp
public class BindingExpression : BindingExpressionBase
{
    private object _dataItem; // 数据源对象
    private PropertyPathWorker _pathWorker;
    public override void UpdateTarget() { ... }
    public override void UpdateSource() { ... }
}
```
### `DataBindEngine`

是绑定系统的总控中心：
- 负责调度表达式    
- 管理所有的活动绑定（弱引用方式）    
- 执行绑定验证、回调、延迟更新等任务
## 命令绑定（ICommand）
用于绑定按钮等 UI 事件到 ViewModel 中的方法。
命令绑定是一种机制，允许你在 XAML 中把按钮等控件的操作（如点击）绑定到 ViewModel 中的“命令对象”，而不需要写事件处理器。
命令绑定把解决了UI控件和逻辑的强耦合：btnSayHello += ....。把命令剥离出来写成单独类，实现了复用，而且也使得UI 和 ViewModel能分别进行测试。
### ICommand 接口定义
```csharp
public interface ICommand
{
    bool CanExecute(object? parameter);       // 是否可以执行，如按钮是否启用
    void Execute(object? parameter);          // 执行命令
    event EventHandler? CanExecuteChanged;    // 可执行状态发生变化时通知（delegate）
}

```
这就是命令模式的典型接口，三个要点：

1. **Execute**：真正执行的动作    
2. **CanExecute**：判断是否允许执行（比如表单未填完整 → 按钮禁用）    
3. **CanExecuteChanged**：通知系统重新评估 `CanExecute`（触发按钮启用/禁用变化）

它用了什么设计模式？

|名称|是否体现|原因|
|---|---|---|
|✅ 命令模式|✔|将“动作”封装为对象（如 SaveCommand），可绑定到 UI|
|✅ 委托（delegate）|✔|用事件 `CanExecuteChanged` 通知控件状态更新|
|✅ 观察者模式|✔|控件监听命令的 `CanExecuteChanged`，当命令状态变化就更新|

### 命令绑定（ICommand）的核心意义
| 功能          | 实现方式                            |
| ----------- | ------------------------------- |
| 把操作抽象成对象    | 用 ICommand 接口                   |
| 控件自动绑定并调用   | Command="{Binding SaveCommand}" |
| 控制按钮启用/禁用   | 实现 CanExecute()                 |
| 解耦 View 和逻辑 | 逻辑完全放在 ViewModel 中              |
| 支持 MVVM     | 是 WPF 支持 MVVM 的关键机制之一           |
### 流程
+---------+       +------------------+
| Button  |-----> |   ICommand       | <-------+
| (Invoker)|      | + Execute()      |         |
+---------+       | + CanExecute()   |         |
                  +------------------+         |
                             ^                 |
                     +---------------+         |
                     | RelayCommand  |         |
                     +---------------+         |
                             ^                 |
                     +----------------+        |
                     |   ViewModel    |--------+
                     +----------------+

例如
```
<Button Content="Say Hello" Command="{Binding SayHelloCommand}" />
```
> 这里，View背后绑定到了ViewModel中的SayHelloCommand属性
> View 只绑定 `Command="{Binding SaveCommand}"`
> 不需要知道 SayHelloCommand 背后执行了什么

创建Command对象
```csharp
public class RelayCommand : ICommand
{
    private readonly Action _execute;
    public RelayCommand(Action execute) => _execute = execute;
    public bool CanExecute(object? parameter) => true;
    public void Execute(object? parameter) => _execute();
    public event EventHandler? CanExecuteChanged;
}
```

ViewModel 中使用：
```csharp
public ICommand SayHelloCommand { get; }
public MyViewModel()
{
    SayHelloCommand = new RelayCommand(() => MessageBox.Show("Hello!"));
}
```
> 当你点击按钮时，WPF 会自动执行 `SayHelloCommand.Execute()`，不需要你在 `Button_Click` 里写代码。

## ICommand命令绑定 vs Data Binding数据绑定
`ICommand` 命令绑定 和 WPF 的 **数据绑定系统**  都是**通过“绑定表达式（BindingExpression）”结合在一起的**。
数据绑定只用到了delegate和观察者模式，命名绑定又加了命令模式，显得复杂了些。
命令绑定（Command="{Binding XXXCommand}"）也是使用 WPF 的数据绑定系统来连接控件与 ViewModel 的 ICommand 对象，只不过绑定目标是 ICommand 类型而不是普通属性。
WPF 框架系统在合适的时间自动调用命令绑定的 `CanExecute()` 和 `Execute()` 方法，不需要自己调用。

|类比角色|普通属性绑定|命令绑定|
|---|---|---|
|UI控件属性|`TextBox.Text`|`Button.Command`|
|数据源属性|`string Name`|`ICommand SaveCommand`|
|Binding系统|`Binding("Name")`|`Binding("SaveCommand")`|
|控件行为|自动更新 Text|自动调用 Execute()|
### 命令绑定 ≈ 数据绑定的一种特殊形式

普通数据绑定 绑的是属性
```
<TextBox Text="{Binding Name}" />
```
- 绑定目标是 `DependencyProperty`：Text    
- 绑定源是 ViewModel 的属性：Name（string）
命令绑定 绑的是行为
```
<Button Command="{Binding SaveCommand}" />
```
- 绑定目标是 `DependencyProperty`：Button.Command（类型为 `ICommand`）    
- 绑定源是 ViewModel 的属性：SaveCommand（类型为 `ICommand`）
- ✅ 本质上，`Command="{Binding SaveCommand}"` 也是一个 `BindingExpression`！
### 命令绑定的执行过程
1. XAML 中写下 `Command="{Binding SaveCommand}"`    
2. WPF 调用 `SetBinding(Button.CommandProperty, binding)    
3. 绑定系统创建 `BindingExpression`，找到了 ViewModel 的 `SaveCommand` 属性    
4. `Button.CommandProperty` ← 绑定了这个 `ICommand` 对象（比如 `RelayCommand` 实例）    
5. Button 控件内部：    
    - 在加载和状态变化时调用 `command.CanExecute()` 决定是否启用        
    - 在用户点击时调用 `command.Execute()`
## 命令绑定 vs 属性通知 

命令绑定实现了命令设计模式。属性通知实现了观察者模式

| 项目           | 命令模式（Command）              | 观察者模式（Observer）                             |
| ------------ | -------------------------- | ------------------------------------------- |
| 用于控制行为       | ✅ 是                        | ❌ 否                                         |
| 用于同步数据       | ❌ 否                        | ✅ 是                                         |
| MVVM 中的实现    | `ICommand`, `RelayCommand` | `INotifyPropertyChanged`, `PropertyChanged` |
| 控件角色         | 调用命令                       | 观察 ViewModel 数据变化                           |
| ViewModel 作用 | 暴露命令供绑定                    | 实现通知机制供绑定                                   |
2个模式用来解决不同问题

|模式|解决的问题|MVVM 中的应用|
|---|---|---|
|✅ **命令模式**(Command Pattern)|**将请求封装成对象，解耦请求发送者与执行者**解决“按钮点击时要执行什么操作”的问题|WPF 中的 `ICommand` 和 `RelayCommand`比如：`Button.Command="{Binding SaveCommand}"`控件不需要知道逻辑怎么写|
|✅ **观察者模式**(Observer Pattern)|**当一个对象状态改变时，自动通知依赖它的多个对象**解决“属性值变了，UI 如何自动更新”的问题|ViewModel 实现 `INotifyPropertyChanged`WPF 绑定系统监听这个事件比如：`OnPropertyChanged("Name")` 通知 UI 刷新绑定的 `TextBox.Text`|
换个角度

| MVVM 层           | 使用的设计模式 | 用途               |
| ---------------- | ------- | ---------------- |
| ViewModel → View | 观察者模式   | 数据变 → 通知 UI 自动更新 |
| View → ViewModel | 命令模式    | 用户操作 → 调用命令方法    |
# 支持库
| 框架/库名                                     | 发布年份  | 作者/主导           | 适用平台                    | 特点关键词                      | 当前状态               |
| ----------------------------------------- | ----- | --------------- | ----------------------- | -------------------------- | ------------------ |
| **Prism**                                 | ~2008 | 微软 → 社区         | WPF, UWP, Xamarin, MAUI | 模块化、导航、事件聚合、DI             | ✅ 活跃维护             |
| **MVVM Light**                            | ~2009 | Laurent Bugnion | WPF, UWP, Xamarin       | 轻量、Messenger、RelayCommand  | ❌ 停止维护（作者加入微软）     |
| **Caliburn.Micro**                        | ~2010 | Rob Eisenberg   | WPF, UWP                | 约定优于配置、自动绑定、Action 调用      | ⚠️ 基本停滞（维护慢）       |
| **ReactiveUI**                            | ~2010 | 社区              | 所有 .NET UI 平台           | 响应式、Rx.NET、双向绑定            | ✅ 活跃维护（偏 Rx 思维）    |
| **CommunityToolkit.Mvvm**（原 MVVM Toolkit） | 2020  | 微软官方            | WPF, WinUI, MAUI, Uno   | Source Generator、属性注解、命令注解 | ✅ 官方推荐             |
| **FreshMvvm**                             | ~2016 | Michael Ridland | Xamarin.Forms           | Page 自动注入、导航集成             | ⚠️ Xamarin 退场，维护有限 |
| **Catel**                                 | ~2012 | 社区              | WPF, Xamarin            | MVVM + DI + Validation     | ⚠️ 小众，维护减缓         |
| **Template10**                            | ~2015 | Jerry Nixon（微软） | UWP                     | UWP 快速开发框架                 | ❌ 已弃用（微软也不再推荐）     |
| **Stylet**                                | ~2017 | 个人/小团队          | WPF                     | 类似 Caliburn.Micro，精简、可测试   | ⚠️ 小众，文档少          |
Prism vs ReactiveUI vs MVVM Toolkit 对比表

| 比较维度                     | **Prism**                         | **ReactiveUI**                                    | **MVVM Toolkit** (CommunityToolkit.Mvvm)     |
| ------------------------ | --------------------------------- | ------------------------------------------------- | -------------------------------------------- |
| 🔰 发布年份                  | ~2008                             | ~2010                                             | 2020（.NET 统一平台后）                             |
| 🧠 核心理念                  | 企业级模块化、导航、DI、事件聚合                 | 响应式编程、数据流驱动                                       | 极简、自动生成样板代码                                  |
| 🧩 是否完整框架                | ✅ 是，功能齐全                          | ❌ 否，偏工具库（基于 Rx.NET）                               | ❌ 否，仅提供属性和命令的精简实现                            |
| 💡 是否官方支持                | 最初微软，后转社区维护                       | ❌ 社区主导                                            | ✅ 微软官方维护                                     |
| 🛠️ 平台支持                 | ✅ WPF, UWP, Xamarin, MAUI         | ✅ 全平台（WPF, WinUI, MAUI, Console）                  | ✅ WPF, WinUI, MAUI, Uno                      |
| 🪝 依赖注入（DI）支持            | ✅ 内建支持（Unity, DryIoc 等）           | ❌ 需自行接入                                           | ❌ 手动注入                                       |
| 🧭 导航系统支持                | ✅ Prism Regions                   | ❌ 无导航支持                                           | ❌ 无导航系统                                      |
| 📣 消息通信系统                | ✅ EventAggregator                 | ✅ MessageBus                                      | ✅ WeakReferenceMessenger                     |
| 🔗 命令支持                  | `DelegateCommand`                 | `ReactiveCommand`                                 | `[RelayCommand]` 注解生成                        |
| 🧮 属性绑定支持                | `BindableBase.SetProperty()`      | `this.RaiseAndSetIfChanged()`                     | `[ObservableProperty]` 注解生成                  |
| ⚡ 异步命令                   | ✅ 支持 Task 封装命令                    | ✅ 原生支持异步命令                                        | ✅ 支持 async 方法自动生成命令                          |
| 🧪 测试友好度                 | ✅ 强（ViewModel 易 Mock）             | ✅ 极强（Rx 可完全测试）                                    | ✅ 强（自动生成命令和属性可测试）                            |
| 🧶 使用复杂度                 | ⚠️ 中等偏高（依赖结构和导航配置）                | ⚠️ 高（需熟悉 Rx 思维）                                   | ✅ 低（非常简单直观）                                  |
| 🧰 命令/属性语法               | 手动 `new DelegateCommand()` / 手动通知 | `ReactiveCommand.Create()` / RaiseAndSetIfChanged | 纯注解 `[RelayCommand]`, `[ObservableProperty]` |
| 🧬 是否使用 Source Generator | ❌ 否                               | ❌ 否                                               | ✅ 是（属性/命令全自动生成）                              |
| ✨ ViewModel 基类           | `BindableBase`                    | `ReactiveObject`                                  | `ObservableObject`                           |
| 📦 NuGet 包名              | `Prism.*`                         | `ReactiveUI.*`                                    | `CommunityToolkit.Mvvm`                      |
| 📚 学习曲线                  | 中等偏高（结构繁杂）                        | 高（响应式概念较陡峭）                                       | 低（面向初学者友好）                                   |
| 🎯 推荐适用场景                | 企业级、模块化应用、WPF/MAUI 大项目            | 高响应性、复杂交互、函数式编程爱好者                                | 现代 .NET 项目、WPF/MAUI 普通 MVVM 项目               |
# Develop
`INotifyCollectionChanged`接口：集合的话需要实现INotifyCollectionChanged 接口  ，会一个事件叫：NotifyCollectionChangedEventHandler 

这个事件作用是：是当集合改变时会发生响应，从而会提供一个`ObservableCollection<T>` 动态数据集合
属性的话是需要继承一个INotifyPropertyChanged接口，会提供一个事件叫：PropertyChangedEventHandler  
这个事件作用是：在更改属性值时会发生响应

同时页面需要在绑定字段的时候设置监听属性：UpdateSourceTrigger=PropertyChanged  mode=TwoWay 获取或设置一个值，该值指示绑定的数据流方向。
# FAQ
## MVVM vs MVC vs MVP
如果没有绑定系统，MVVM 退化成 MVP 或 MVC，你就得写：
```
textBox.Text = viewModel.Name;
viewModel.Name = textBox.Text;
button.Click += (_, __) => viewModel.Save();
```
这让 View 直接控制逻辑，完全违背 MVVM 的目标（分离 UI 和逻辑）。

|比较项|MVC 特点|WPF 需求|冲突点|
|---|---|---|---|
|**View 与 Controller 的交互方式**|View 通过事件通知 Controller，Controller 决定更新 View|WPF 支持双向数据绑定，View 会自动响应数据变化|WPF 不需要 Controller 主动更新 View|
|**UI 更新方式**|控制器调用 View 方法手动更新界面|View 绑定 ViewModel 属性，自动刷新|控制器逻辑违背 WPF 的自动更新机制|
|**耦合度**|Controller 通常知道 View 的细节|WPF 倡导松耦合，ViewModel 不知道 View|MVC 的紧耦合不适合数据绑定|
|**事件驱动 vs 绑定驱动**|MVC 以事件回调为主|WPF 以绑定驱动为主|设计理念根本不同|
假设你有个按钮点击后，更新一个 Label 的文本。
MVC
```csharp
public class Controller {
    public void OnButtonClick() {
        var data = model.GetData();
        view.SetLabelText(data); // 控制器操作 UI
    }
}
```
> 这里，Controller 必须直接操纵 View 控件，破坏了 WPF 的绑定思想。

在 WPF + MVVM 中：
```csharp
// View.xaml
<Button Command="{Binding LoadCommand}" />
<TextBlock Text="{Binding Message}" />

// ViewModel.cs
public ICommand LoadCommand => new RelayCommand(() => Message = model.GetData());
public string Message { get; set; }  // 通知 UI 自动更新
```
> 这里，WPF 自动完成数据更新。ViewModel 根本不需要知道 UI 是什么控件，UI 根本不需要知道谁负责处理我的事件

总之，WPF 的设计理念是**绑定驱动的声明式 UI**，而 MVC 是**事件驱动的命令式逻辑**，两者冲突明显。所以 MVVM 才是 WPF 的“原生”架构模式。

下面是一个完整例子。分别用WPF方式实现MVC和MVVM模式
### 用WPF模拟MVC
model.cs
```csharp
public class MessageModel
{
    public string GetMessage() => "你好，WPF！";
}
```
MainWindow.xaml （界面View）
```xml
<Window x:Class="MvcDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MVC Demo" Height="150" Width="300">
    <StackPanel Margin="20">
        <Button Name="btnShow" Content="显示消息" Margin="0 0 0 10" />
        <TextBlock Name="lblMessage" FontSize="16" />
    </StackPanel>
</Window>
```
MainWindow.xaml.cs
```csharp
public partial class MainWindow : Window
{
    private MessageController controller;

    public MainWindow()
    {
        InitializeComponent();
        controller = new MessageController(this);
    }
}
```
Controller.cs
```csharp
public class MessageController
{
    private readonly MessageModel model;
    private readonly MainWindow view;

    public MessageController(MainWindow view)
    {
        this.view = view;
        this.model = new MessageModel();

        this.view.btnShow.Click += OnButtonClick;
    }

    private void OnButtonClick(object sender, RoutedEventArgs e)
    {
        string message = model.GetMessage();
        view.lblMessage.Text = message;  // 直接操作 UI
    }
}
```
可以运行，但 **Controller 强依赖 View 结构**，无法单元测试、无法复用，也违背了 WPF 的绑定哲学。
### 用WPM实现MVP
`MessageModel.cs`（保持不变）
```
public class MessageModel
{
    public string GetMessage() => "你好，WPF！";
}
```
`MainWindow.xaml`（与mvc一样）
```
<Window x:Class="MvpDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MVP Demo" Height="150" Width="300">
    <StackPanel Margin="20">
        <Button Name="btnShow" Content="显示消息" Margin="0 0 0 10" />
        <TextBlock Name="lblMessage" FontSize="16" />
    </StackPanel>
</Window>
```
`IMessageView.cs`（View 接口）
```
public interface IMessageView
{
    void SetMessage(string message);
    event RoutedEventHandler ShowButtonClicked;
}
```
`MainWindow.xaml.cs`（实现接口）
```csharp
public partial class MainWindow : Window, IMessageView
{
    private MessagePresenter presenter;

    public MainWindow()
    {
        InitializeComponent();
        presenter = new MessagePresenter(this);
    }

    public void SetMessage(string message)
    {
        lblMessage.Text = message;
    }

    public event RoutedEventHandler ShowButtonClicked
    {
        add { btnShow.Click += value; }
        remove { btnShow.Click -= value; }
    }
}
```
MessagePresenter.cs
```csharp
public class MessagePresenter
{
    private readonly MessageModel model = new();
    private readonly IMessageView view;

    public MessagePresenter(IMessageView view)
    {
        this.view = view;
        this.view.ShowButtonClicked += OnShowClicked;
    }

    private void OnShowClicked(object sender, RoutedEventArgs e)
    {
        string message = model.GetMessage();
        view.SetMessage(message);  // 不直接操作控件
    }
}
```
MVC vs MVP 对比

|比较点|MVC|MVP|
|---|---|---|
|控制器操作|直接操作控件|通过接口控制视图|
|解耦程度|Controller ←→ View 耦合|Presenter ←→ View 解耦（靠接口）|
|可测试性|较弱|强：Presenter 可单元测试|
|UI 变化影响|Controller 需改动|View 接口不变可复用 Presenter|
### 用WPF实现MVVM
`MessageModel.cs`（保持不变）
```csharp
public class MessageModel
{
    public string GetMessage() => "你好，WPF！";
}
```
ViewModel.cs
```csharp
public class MessageViewModel : INotifyPropertyChanged
{
    private readonly MessageModel model = new();

    private string _message = string.Empty;
    public string Message
    {
        get => _message;
        set {
            if (_message != value)
            {
                _message = value;
                OnPropertyChanged(nameof(Message));
            }
        }
    }

    public ICommand ShowMessageCommand => new RelayCommand(() =>
    {
        Message = model.GetMessage();  // 只修改属性，不直接操作 UI
    });

    public event PropertyChangedEventHandler? PropertyChanged;
    protected void OnPropertyChanged(string prop)
        => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(prop));
}
```
RelayCommand.cs（命令帮助类）
```csharp
public class RelayCommand : ICommand
{
    private readonly Action _execute;
    public RelayCommand(Action execute) => _execute = execute;

    public bool CanExecute(object? parameter) => true;
    public void Execute(object? parameter) => _execute();

    public event EventHandler? CanExecuteChanged;
}
```
MainWindow.xaml (View) 绑定命令和属性
```xml
<Window x:Class="MvvmDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MVVM Demo" Height="150" Width="300">
    <StackPanel Margin="20">
        <Button Content="显示消息" Command="{Binding ShowMessageCommand}" Margin="0 0 0 10"/>
        <TextBlock Text="{Binding Message}" FontSize="16"/>
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
        DataContext = new MessageViewModel(); // 绑定 ViewModel
    }
}
```
✅ View 只绑定 ViewModel，不知道 Model  
✅ ViewModel 不知道 UI 控件，容易测试  
✅ Model 是独立逻辑层，可复用

MVC / MVP / MVVM 文件结构对比

| 文件名                  | MVC   | MVP  | MVVM          |
| -------------------- | ----- | ---- | ------------- |
| Model.cs             | ✅     | ✅    | ✅             |
| View.xaml            | ✅     | ✅    | ✅（绑定）         |
| View.xaml.cs         | 控制器绑定 | 实现接口 | ❌ 无逻辑         |
| Controller/Presenter | ✅     | ✅    | ❌ ViewModel代替 |
| ViewModel.cs         | ❌     | ❌    | ✅             |
| RelayCommand.cs      | ❌     | ❌    | ✅ 用于命令绑定      |
MVC / MVP / MVVM实现效果对比

|比较维度|**MVC**|**MVP**|**MVVM**|
|---|---|---|---|
|控制方式|Controller|Presenter|ViewModel|
|是否依赖事件|✅ 需要|✅ 需要|❌ 不需|
|是否手动操作控件|✅ 是|❌ 通过接口|❌ 全自动|
|易测试性|❌ 差|✅ 强|✅ 强|
|是否支持绑定|❌ 否|❌ 否|✅ 原生支持|
## mvvm的设计，用到了哪些经典设计模式
MVVM（Model-View-ViewModel）模式本身并不是一个“设计模式”，而是一个**架构模式**，但它**内部广泛应用了多个经典设计模式**来实现松耦合、可测试和易扩展的架构。下面是 MVVM 中常用的设计模式：

| 设计模式                           | 用途/在 MVVM 中的角色                                                                      |
| ------------------------------ | ----------------------------------------------------------------------------------- |
| **Observer（观察者模式）**            | 用于通知 UI 更新。ViewModel 实现 `INotifyPropertyChanged` 接口，View 观察 ViewModel 属性变化，自动更新 UI。 |
| **Command（命令模式）**              | 用于将按钮等 UI 行为转化为命令对象。`ICommand` 接口 + `RelayCommand` 是典型命令模式实现。                       |
| **Mediator（中介者模式）**            | View 和 ViewModel 通过绑定机制间接通信，不直接依赖彼此，WPF 的 `Binding` 系统本质上是一个中介者。                    |
| **Dependency Injection（依赖注入）** | 将服务（如 Model 或 Repository）注入到 ViewModel 中，解耦依赖关系，便于测试和替换。                            |
| **Factory（工厂模式）**              | ViewModel、Model、服务对象的创建往往使用工厂方法（比如 ViewModelLocator 或 ServiceLocator）。              |
| **Service Locator（服务定位器）**     | 可选的模式，用于统一管理服务实例（但滥用会导致隐藏依赖）。某些 MVVM 框架如 Prism 提供此模式支持。                             |
| **Template Method（模板方法模式）**    | 用于定义数据加载、初始化过程的通用骨架，ViewModel 中常有 BaseViewModel 定义生命周期方法。                           |
| **Strategy（策略模式）**             | 有时用于在 ViewModel 中切换不同的业务处理方式，比如根据用户权限切换显示策略。                                        |
| **State（状态模式）**                | ViewModel 中的 UI 状态（比如是否加载中、是否只读）可视为状态机模型。                                           |
## `RelayCommand` 是谁写的？
❌ **不是 WPF 官方提供的**  
✅ 是 **社区约定俗成的写法**，**你需要自己写一份**（或者引用别人写好的）
你如果不用库，就必须写一个 `RelayCommand` 或 `DelegateCommand` 自己封装它。
RelayCommand基本写法：
```csharp
public class RelayCommand : ICommand
{
    private readonly Action<object?> _execute;
    private readonly Func<object?, bool>? _canExecute;

    public RelayCommand(Action<object?> execute, Func<object?, bool>? canExecute = null)
    {
        _execute = execute;
        _canExecute = canExecute;
    }

    public bool CanExecute(object? parameter) => _canExecute?.Invoke(parameter) ?? true;

    public void Execute(object? parameter) => _execute(parameter);

    public event EventHandler? CanExecuteChanged;

    public void RaiseCanExecuteChanged() =>
        CanExecuteChanged?.Invoke(this, EventArgs.Empty);
}
```
> 整个项目只写一个 RelayCommand 类（通常放在 Common 或 Infrastructure 文件夹）
然后在任何 ViewModel 中重复使用它：
MyViewMode:
```csharp
public class MyViewModel
{
    public ICommand SaveCommand { get; }

    public MyViewModel()
    {
        SaveCommand = new RelayCommand(_ => Save());
    }

    private void Save()
    {
        // 执行保存逻辑
    }
}
```
常见Command变体

|命名|来源 / 框架|
|---|---|
|`RelayCommand`|MVVM Light / 通用|
|`DelegateCommand`|Prism 框架|
|`AsyncRelayCommand`|支持异步命令执行|
|`MyCommand`|你自己随便起的名字|
总结

|问题|回答|
|---|---|
|WPF 官方提供 RelayCommand 吗？|❌ 没有，需要自己写|
|每个 ViewModel 都要写一份？|❌ 不需要，只写一个，全项目通用|
|为什么社区都叫它 RelayCommand？|因为它像“转接器”，帮你把方法转成 ICommand|
|推荐用现成框架吗？|✅ 是的，例如 MVVM Toolkit 或 Prism|
# References
[# MVC、MVP、MVVM模式的概念与区别](https://www.jianshu.com/p/ff6de219f988)