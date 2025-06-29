---
layout: post
title: WPF Tutorial 1
author: Andy Feng
tags:
  - "#wpf"
  - "#dotnet"
---
to watch
[https://www.youtube.com/watch?v=PoPUB1_q2kE](https://www.youtube.com/watch?v=PoPUB1_q2kE)
[https://www.youtube.com/watch?v=CABv5xIDC08](https://www.youtube.com/watch?v=CABv5xIDC08)
[https://www.youtube.com/watch?v=mlmyFXJy8gQ](https://www.youtube.com/watch?v=mlmyFXJy8gQ)
[WPF - Responsive UI Design | MVVM | XAML | C# | Tutorial](https://www.youtube.com/watch?v=EfXz4C5cSVI)
# Introduction
WPF stands for Windows Presentation Foundation. It is a powerful framework for building Windows applications.
早起的GUI程序中，比如Windows Forms，程序的外观和行为没有完全分离，都用同一种语言开发，比如C#，往往需要同时修改UI和逻辑代码，维护成本高。
In WPF, UI elements are designed in XAML while behaviors can be implemented in procedural languages such C# and VB.Net. So it very easy to separate behavior from the designer code.
With XAML, the programmers can work in parallel with the designers. The separation between a GUI and its behavior can allow us to easily change the look of a control by using styles and templates.
WPF架构
![](images/posts/Pasted%20image%2020250626182610.png)
# Setup
Microsoft provides two important tools for WPF application development. Both the tools can create WPF projects, but the fact is that Visual Studio is used more by developers, while Blend is used more often by designers.

- Visual Studio
- Expression Blend
XAML
![](images/posts/Pasted%20image%2020250626182749.png)
# Conceptions
## Logical Tree vs Visual Tree
Visual tree includes logic tree.
开发中常用-逻辑树，可以聚焦在写代码
## Dependency property
依赖属性不是普通的c#内部属性字段，他可以注册到WPF的属性系统中统一管理。有很多新特性，比如双向绑定，样式，动画，资源等等，是一种超级属性。
依赖属性必须继承 **DependencyObject** class
## Routed Events
本质上是一个事件event，能监听树。
有3种：
- Direct Event - 等同与Windows form的event
- Bubbling Event，从下往上传递，顶层是Window
- Tunnel Event
## Controls
WPF有100多种视觉控件。
![](images/posts/Pasted%20image%2020250626213338.png)
## Data binding
有多种数据绑定方法
### **DataContext** is used in View to bind data with ViewModel。最常用
 比如有一个view model
```csharp
 public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
}

```
XAML 中这样绑定控件：
```xml
<!-- MainWindow.xaml -->
<StackPanel>
    <TextBlock Text="{Binding Name}" />
    <TextBlock Text="{Binding Age}" />
</StackPanel>
```
xaml代码中绑定数据源
```csharp
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();

        // 设置 DataContext（绑定源）
        this.DataContext = new Person { Name = "Alice", Age = 30 };
    }
}
```
DataContext 层级继承行为
- 如果你在 `Window` 上设置了 `DataContext`，所有内部子控件默认继承它。
- 你也可以在某个控件上局部替换 `DataContext`。
```csharp
<StackPanel DataContext="{StaticResource AnotherViewModel}">
    <!-- 这里绑定的是另一个 ViewModel -->
</StackPanel>
```
### 使用 `Binding.Source` 显式绑定（绕过 DataContext）
```xml
<Window.Resources>
    <local:Person x:Key="MyPerson" Name="Alice" Age="30"/>
</Window.Resources>

<TextBlock Text="{Binding Source={StaticResource MyPerson}, Path=Name}" />
```
好处：不依赖 `DataContext`，适合多个绑定源的情况  
❗ 缺点：写法稍复杂，不够通用
### 使用 `ElementName`：控件之间绑定
```xml
<TextBox x:Name="txtInput" Text="Hello" />
<TextBlock Text="{Binding ElementName=txtInput, Path=Text}" />
```
📌 应用场景：两个控件之间同步内容，无需 ViewModel
### 使用 `RelativeSource`
```xml
<TextBlock Text="{Binding RelativeSource={RelativeSource AncestorType=Window}, Path=Title}" />
```
应用场景：从控件绑定到父窗口、父控件、模板宿主等  
常见用法包括：
- `Self`    
- `FindAncestor`    
- `TemplatedParent`
### 绑定到静态类属性
```csharp
public static class Globals
{
    public static string AppVersion => "1.0.0";
}
```
xaml
```xml
xmlns:local="clr-namespace:YourNamespace"

<TextBlock Text="{x:Static local:Globals.AppVersion}" />
```
📌 WPF 支持 `x:Static`，WinUI 中可通过 `Binding Source={x:Bind}` 或使用静态资源类实现。
### 使用 Binding 对象绑定（纯 C#）
```csharp
Binding binding = new Binding("Name")
{
    Source = person,
    Mode = BindingMode.TwoWay
};

textBox.SetBinding(TextBox.TextProperty, binding);
```
📌 用于动态创建绑定、复杂逻辑、在后台绑定控件属性  
常用于控件模板或控件库中

数据绑定方式对比总表

| 编号  | 绑定方式                  | 示例语法                                                                        | 是否依赖 `DataContext` | 优点                       | 缺点 / 限制                                       | 常见用途                   |
| --- | --------------------- | --------------------------------------------------------------------------- | ------------------ | ------------------------ | --------------------------------------------- | ---------------------- |
| ①   | 默认绑定（DataContext）     | `{Binding Name}`                                                            | ✅ 是                | 简洁、通用、支持 MVVM、自动继承上下文    | 必须先设置 `DataContext`，否则绑定失败                    | MVVM 绑定、页面绑定 ViewModel |
| ②   | 显式设置 Source           | `{Binding Path=Name, Source={StaticResource MyPerson}}`                     | ❌ 否                | 不依赖 DataContext，支持绑定多个对象 | 写法冗长，无法继承                                     | 多个绑定源场景、资源绑定           |
| ③   | ElementName           | `{Binding Path=Text, ElementName=txtInput}`                                 | ❌ 否                | 控件之间联动，实时响应              | 需设置好控件 `Name`，只限同一视觉树中使用                      | 输入框联动、用户控件交互           |
| ④   | RelativeSource        | `{Binding Path=Title, RelativeSource={RelativeSource AncestorType=Window}}` | ❌ 否                | 跨层级绑定、绑定模板外部对象           | 写法复杂、调试困难                                     | 控件绑定父级、模板控件绑定宿主        |
| ⑤   | 静态资源（x:Static）        | `{x:Static local:Globals.AppVersion}`                                       | ❌ 否                | 常量绑定、静态属性绑定              | WPF 支持 `x:Static`，WinUI 需使用 `x:Bind` 静态方法或类包装 | 显示常量、系统信息、单例 ViewModel |
| ⑥   | C# 手动绑定（代码创建）         | `textBox.SetBinding(TextBox.TextProperty, new Binding { ... })`             | ❌ 否                | 绑定逻辑更灵活，适用于运行时动态数据绑定     | 可读性差，不适合大规模开发                                 | 动态控件生成、运行时绑定、控件库开发     |
| ⑦   | x:Bind（仅 WinUI / UWP） | `<TextBlock Text="{x:Bind ViewModel.Name}" />`                              | ❌ 否（编译期绑定）         | 编译期类型检查，性能高，支持函数绑定       | 只能绑定 public 属性，不能继承 DataContext               | WinUI 中替代 Binding 的主方式 |

## Resources
资源通常定义在资源词典中，也可以定义在很多其他地方，比如页面里，layout里等。
创建资源词典时，放在单独文件里，并在父xaml里引用
![](images/posts/Pasted%20image%2020250626224010.png)
## template vs style
template 指control的外观，但功能比style强大。
style只能指定缺省外观
template能指定缺省外观，还能指定新外观
## trigger
A trigger basically enables you to change property values or take actions based on the value of a property.
`XAML` 中的 **`Trigger`** 是一种在 **界面层（View）** 控制样式和行为的机制，**本质上和 `ViewModel` 没有直接关系**，它的作用是根据某个属性的值来触发样式变化或动作，属于 **UI 响应式逻辑的一部分**。
`Trigger` 是 XAML 中用于“**当某个条件满足时，就改变样式或属性**”的机制。
有3种trigger
- Property Triggers: when a change occurs in one property, it will bring either an immediate or an animated change in another property.
- Data Triggers: it performs some actions when the bound data satisfies some conditions.
- Event Triggers: it performs some actions when a specific event is fired.
### `DataTrigger` 与 ViewModel 的关系（唯一有关联的）
你可以这样绑定 ViewModel 的属性：
```xml
<Window.DataContext>
    <local:MyViewModel />
</Window.DataContext>

<Button Content="Submit">
    <Button.Style>
        <Style TargetType="Button">
            <Style.Triggers>
                <DataTrigger Binding="{Binding IsSaving}" Value="True">
                    <Setter Property="IsEnabled" Value="False" />
                    <Setter Property="Content" Value="Saving..." />
                </DataTrigger>
            </Style.Triggers>
        </Style>
    </Button.Style>
</Button>
```
🔍 如果 `ViewModel.IsSaving == true`，按钮将禁用，文字变成“Saving…”
✅ 此时 Trigger 依赖 ViewModel 的属性，这种是 `MVVM` 模式推荐用法。

# FAQ
## How to use fontawesome?
nuget > fontawesome.Sharp
![](images/posts/Pasted%20image%2020250628024926.png)
```xml
<Window xmlns:fa="http://schemas.awesome.incremented/wpf/xaml/fontawesome.sharp"> ... </Window>

<fa:IconImage Icon="Book" Width="24" Height="24" Foreground="DeepSkyBlue"></fa:IconImage>

<Button.Content> <fa:IconBlock Icon="Info" Foreground="Chocolate"/> </Button.Content>

<TextBlock />
<IconBlock />
{fa:Icon [Icon]}
{fa:ToText [Icon]}
{fa:Geometry [Icon]}
<fa:IconImage />
{fa:IconSource [Icon]}
<fa:IconToImageConverter />
````

# References 
[WPF Tutorial](https://www.tutorialspoint.com/wpf/index.htm)
[# WPF Walkthroughs](https://learn.microsoft.com/en-us/dotnet/desktop/wpf/get-started/wpf-walkthroughs)
[Windows Presentation Foundation documentation](https://learn.microsoft.com/en-us/dotnet/desktop/wpf/)
[# How to use Font Awesome icon in WPF](https://www.youtube.com/watch?v=-mGeICjMOxQ)
[# FontAwesome.Sharp](https://awesome-inc.github.io/FontAwesome.Sharp/#wpf)
[# WPF & MVVM/ Modern Main UI Design (Part 2/2) - Header Design, Open & Switch Child Views](https://www.youtube.com/watch?v=76JLBZJR5gE)