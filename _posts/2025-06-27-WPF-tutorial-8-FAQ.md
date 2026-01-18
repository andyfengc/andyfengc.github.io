---
layout: post
title: Dependency Injection
author: Andy Feng
---
# 子 ViewModel 访问父 ViewModel
- 父 ViewModel：`ParentViewModel`    
- 子 ViewModel：`ChildViewModel`（例如某个控件或子区域对应的 VM）    
- 子 ViewModel 需要访问父 ViewModel 的属性或方法。

| 方法                       | 父子耦合 | 异常处理 | MVVM 风格 | 适用场景       | 备注              |
| ------------------------ | ---- | ---- | ------- | ---------- | --------------- |
| 构造函数注入                   | 高    | 可控   | 一般      | 父子 VM 同时创建 | 简单、类型安全         |
| 属性绑定 / 父传子               | 中    | 可控   | ✅       | 父控子 VM     | XAML 风格，UI 绑定友好 |
| 属性注入                     | 中    | 可控   |         |            | 需要手动控制          |
| 回调 / 委托                  | 低    | 父可捕获 | ✅       | 事件触发型      | 子 VM 不依赖父 VM 类型 |
| 消息机制 / EventAggregator   | 无    | 可控   | ✅       | 跨组件 / 跨层   | 完全解耦，框架依赖       |
| VisualTree / LogicalTree | 高    | 不可控  | ❌       | 临时快速访问     | 不推荐             |
## 构造函数注入
让子 ViewModel 在创建时就接收父 ViewModel 的引用。
```csharp
public class ParentViewModel
{
    public ChildViewModel Child { get; }

    public ParentViewModel()
    {
        Child = new ChildViewModel(this); // 注入父 VM
    }

    public string ParentName { get; set; } = "Parent";
}

public class ChildViewModel
{
    private readonly ParentViewModel _parent;

    public ChildViewModel(ParentViewModel parent)
    {
        _parent = parent;
    }

    public void PrintParentName()
    {
        Console.WriteLine(_parent.ParentName);
    }
}
```
✅ 优点：类型安全、简单明了
❌ 缺点：父子耦合明显，不适合动态生成场景，偶尔用一用
## 属性绑定 / 父传子
在 XAML 中把父 ViewModel 的某个属性绑定给子控件的 DataContext 或属性。
```xml
<UserControl x:Class="ChildView">
    <UserControl.DataContext>
        <Binding Path="Child" /> <!-- 父 VM 的 Child 属性 -->
    </UserControl.DataContext>
</UserControl>
```
等同于 父VM通过子VM的构造函数，注入自己
```csharp
public class ParentViewModel
{
    public ChildViewModel Child { get; }

    public ParentViewModel()
    {
        Child = new ChildViewModel(this);
    }
}
```
## 属性注入
给子 ViewModel 添加一个 **可写的父 VM 属性**，在父 VM 创建子 VM 后直接设置：
```csharp
public class ChildViewModel
{
    // 构造函数固定，不可修改
    public ChildViewModel() { }

    // 额外提供父 VM 属性
    public ParentViewModel? Parent { get; set; }

    public void DoSomething()
    {
        Console.WriteLine(Parent?.SomeProperty);
    }
}

public class ParentViewModel
{
    public ChildViewModel Child { get; }

    public ParentViewModel()
    {
        Child = new ChildViewModel();
        Child.Parent = this; // 注入父 VM
    }
}
```
✅ 优点：
简单明了
类型安全
保持 MVVM 风格
❌ 缺点：
子 VM 会依赖父 VM 类型（耦合略高）

或者通过一个初始化方法
```csharp
public class ChildViewModel
{
    public ParentViewModel? Parent { get; private set; }

    public ChildViewModel() { }

    public void Initialize(ParentViewModel parent)
    {
        Parent = parent;
    }

    public void DoSomething()
    {
        Console.WriteLine(Parent?.SomeProperty);
    }
}

public class ParentViewModel
{
    public ChildViewModel Child { get; }

    public ParentViewModel()
    {
        Child = new ChildViewModel();
        Child.Initialize(this);
    }
}
```
## 回调 / 委托方式（Loose Coupling）
子 VM 不直接引用父 VM，而是通过回调调用父 VM 的方法。
```csharp
public class ChildViewModel
{
    private readonly Action<string> _notifyParent;

    public ChildViewModel(Action<string> notifyParent)
    {
        _notifyParent = notifyParent;
    }

    public void DoSomething()
    {
        _notifyParent?.Invoke("Hello from Child");
    }
}

public class ParentViewModel
{
    public ChildViewModel Child { get; }

    public ParentViewModel()
    {
        Child = new ChildViewModel(OnChildNotify);
    }

    private void OnChildNotify(string message)
    {
        Console.WriteLine("Received from child: " + message);
    }
}
```
✅ 优点：父子松耦合
✅ 子 VM 不依赖父 VM 类型
❌ 缺点：只能通过定义好的委托接口交互，灵活性有限
## 事件聚合 / 消息机制（完全解耦）
适合子 VM 不直接依赖父 VM 的场景，或者跨层通信。
使用 MVVM 框架的 `Messenger` / `EventAggregator` 让子 VM **发送消息**，父 VM **订阅消息**：
```csharp
// 子 VM 发送消息
Messenger.Default.Send(new ChildActionMessage { Info = "Hello" });

// 父 VM 响应
Messenger.Default.Register<ChildActionMessage>(this, msg =>
{
    Console.WriteLine(msg.Info);
});
```
✅ 优点：完全松耦合  
✅ 支持跨组件、跨层通信  
❌ 缺点：需要额外消息机制，逻辑稍复杂
## 通过 VisualTree / LogicalTree 获取父控件 DataContext（不推荐）

在控件 code-behind 或子 VM（绑定到控件）中：
```csharp
var parentVm = (ParentViewModel)LogicalTreeHelper.GetParent(this)?.DataContext;
```
❌ 缺点：
- 违反 MVVM 原则    
- 依赖 UI 树结构    
- 可测试性差
# References 
