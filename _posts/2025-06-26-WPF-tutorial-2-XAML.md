---
layout: post
title: WPF Tutorial 1
author: Andy Feng
---
# Introduction
`XAML` stands for Extensible Application Markup Language. Its a simple and declarative language based on XML.
- In XAML, it very easy to create, initialize, and set properties of objects with hierarchical relations.    
- It is mainly used for designing GUIs, however it can be used for other purposes as well, e.g., to declare workflow in Workflow Foundation.
XAML作用：用 XML 描述界面结构
XAML文件会由专门的解析器处理，编译成内部代码，再与c#代码link并编译，最后生成app。
# 扩展标记 Markup Extensions
标记扩展(Markup Extensions) 是微软为XAML专门设计的语法扩展，它**不是** W3C XML标准规范的一部分。这种设计使XAML在保持XML基础语法(可读性/工具支持)的同时，获得了强大的声明式编程能力。
XAML设计了一些扩展是因为：

1. **对象实例化需求**：需要表示复杂的.NET对象关系    
2. **动态绑定需求**：支持WPF的数据绑定系统    
3. **资源复用需求**：支持样式/模板等资源共享

下是 WPF 开发中最常用的扩展标记，按功能分类整理：

## XAML语言基础

|标记扩展|示例|说明|
|---|---|---|
|`x:Type`|`{x:Type Button}`|获取类型对象|
|`x:Null`|`{x:Null}`|显式null值|
|`x:Static`|`{x:Static SystemColors.ActiveCaptionBrush}`|引用静态成员|
|`x:Array`|`{x:Array Type={x:Type sys:String}}`|创建数组|
## 控件（Controls）
控件 = UI 元素
ContentControl：可放置单个内容（Button、Label、ContentControl 本身）
ItemsControl：可放置集合（ListBox、ListView、ComboBox）
Panel：布局容器（StackPanel、Grid、DockPanel、Canvas）
Decorator：修饰控件（Border、ScrollViewer）
## 布局（Layout）
6大布局控件决定子控件的摆放方式：

| 布局控件        | 特点                  |
| ----------- | ------------------- |
| StackPanel  | 垂直或水平堆叠             |
| Grid        | 行列网格布局，最常用          |
| DockPanel   | 停靠布局（顶部/底部/左/右）     |
| Canvas      | 绝对定位布局，子元素用x/y 坐标定位 |
| WrapPanel   | 自动换行堆叠              |
| UniformGrid | 规则均匀网格，所有单元格大小一样    |
其他布局容器（常用但不是六大核心）：

| 布局容器             | 说明                  |
| ---------------- | ------------------- |
| **Viewbox**      | 自动缩放其内容（UI 随容器放大缩小） |
| **Border**       | 包裹单个子元素，可设置边框、圆角    |
| **ScrollViewer** | 提供滚动功能              |
| **Decorator**    | 装饰类布局基类（如 Border）   |

### SharedSizeGroup：让多个 Grid 之间共享列（或行）的尺寸
- **不同的 Grid，可以让指定的列宽（或行高）保持一致。
- 让多个 Grid 中的 **RowDefinition / ColumnDefinition** 成为“同步尺寸”的一组    
- 这在做表单、设置窗口、属性面板时特别有用
- 由最大内容决定最终宽度/高度
使用前提：父 Grid 必须设置 Grid.IsSharedSizeScope="True"。然后把需要同步的多个子grid放进去，为每一列通过SharedSizeGroup起个名字。
示例：让两个 Grid 的第一列保持同样的宽度
```xml
<Grid Grid.IsSharedSizeScope="True">
    <StackPanel>

        <!-- 第一个 Grid -->
        <Grid Margin="0 0 0 10">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="50*" SharedSizeGroup="colLine"/>
                <ColumnDefinition Width="13*" SharedSizeGroup="colCharacter"/>
                <ColumnDefinition Width="10*" SharedSizeGroup="colVoice"/>
            </Grid.ColumnDefinitions>

            <TextBlock Grid.Column="0" Text="User Name:"/>
            <TextBox Grid.Column="1" Width="120"/>
            <TextBox Grid.Column="2" Width="120"/>
        </Grid>

        <!-- 第二个 Grid -->
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="50*" SharedSizeGroup="colLine"/>
                <ColumnDefinition Width="13*" SharedSizeGroup="colCharacter"/>
                <ColumnDefinition Width="10*" SharedSizeGroup="colVoice"/>
            </Grid.ColumnDefinitions>

            <TextBlock Grid.Column="0" Text="User Name:"/>
            <TextBox Grid.Column="1" Width="120"/>
            <TextBox Grid.Column="2" Width="120"/>
        </Grid>

    </StackPanel>
</Grid>
```
对于控制多个grid的宽度很好用。但是缺点是横向总是会按最宽的撑开父容器，出现水平滚动，这是设计原理，无法解决。
## 数据绑定
**Binding = 把 UI 控件属性和数据对象属性连接起来**，让 UI 自动显示/更新数据。
```xml
<TextBox Text="{Binding Name}" />
```
- 控件会去 DataContext 找 Name 属性。
- Name 改变 → UI 自动更新
- UI 改变 → Name 自动更新（如果双向绑定）

| 标记扩展              | 示例                                                              | 说明      |
| ----------------- | --------------------------------------------------------------- | ------- |
| `Binding`         | `{Binding Path=UserName}`                                       | 基本数据绑定  |
| `Binding` (简写)    | `{Binding UserName}`                                            | Path可省略 |
| `RelativeSource`  | `{Binding RelativeSource={RelativeSource AncestorType=Window}}` | 相对源绑定   |
| `TemplateBinding` | `{TemplateBinding Property}`                                    |         |
## 资源引用
Resource = 可复用对象仓库
可以存放 Brush、Style、DataTemplate、ControlTemplate、动画、对象
作用域：Element / Window / UserControl / Application
使用方式：StaticResource / DynamicResource
支持 MergedDictionaries 拆分管理

|标记扩展|示例|说明|
|---|---|---|
|`StaticResource`|`{StaticResource MyBrush}`|静态资源引用|
|`DynamicResource`|`{DynamicResource MyColor}`|动态资源引用|
|`ThemeDictionary`|`{ThemeDictionary Location}`|主题资源字典|

## 布局与可视化

|标记扩展|示例|说明|
|---|---|---|
|`TemplateBinding`|`{TemplateBinding Background}`|模板绑定|
|`RelativeSource` (Self)|`{Binding RelativeSource={RelativeSource Self}}`|绑定到自身|
|`RelativeSource` (FindAncestor)|`{Binding RelativeSource={RelativeSource AncestorType=ItemsControl}}`|查找父级|
## 样式与模板
**Style = 一组控件属性的集合**，可以集中管理控件的外观。
```xml
<Window.Resources>
    <Style x:Key="RedButton" TargetType="Button">
        <Setter Property="Background" Value="Red"/>
        <Setter Property="Foreground" Value="White"/>
        <Setter Property="FontSize" Value="16"/>
    </Style>
</Window.Resources>

<Button Content="Click Me" Style="{StaticResource RedButton}" />
```
- TargetType = 控件类型（Button）    
- Setter = 设置控件属性的值

| 标记扩展                   | 示例                                        | 说明   |
| ---------------------- | ----------------------------------------- | ---- |
| `StaticResource` (样式)  | `Style="{StaticResource MyStyle}"`        | 引用样式 |
| `DynamicResource` (模板) | `Template="{DynamicResource MyTemplate}"` | 动态模板 |
## 特殊用途

|标记扩展|示例|说明|
|---|---|---|
|`PriorityBinding`|`{PriorityBinding Bindings={Binding A}, {Binding B}}`|优先级绑定|
|`MultiBinding`|`<MultiBinding Converter="{StaticResource MyConverter}">`|多值绑定|
|`ComponentResourceKey`|`{ComponentResourceKey TypeInTargetAssembly={x:Type local:MyResources}, ResourceId=MyBrush}`|组件资源键|
## 设计时支持

|标记扩展|示例|说明|
|---|---|---|
|`d:DesignData`|`d:DataContext="{d:DesignData Source=/Data/SampleData.xaml}"`|设计时数据|
|`d:DesignInstance`|`d:DataContext="{d:DesignInstance Type=local:MyViewModel}"`|设计时实例|
## DataTrigger（数据触发器）
**DataTrigger = 根据绑定数据改变控件属性**，实现“条件样式”。
```xml
<Style TargetType="Button">
    <Setter Property="Background" Value="Gray"/>
    <Style.Triggers>
        <DataTrigger Binding="{Binding IsEnabled}" Value="True">
            <Setter Property="Background" Value="Green"/>
        </DataTrigger>
        <DataTrigger Binding="{Binding IsEnabled}" Value="False">
            <Setter Property="Background" Value="Red"/>
        </DataTrigger>
    </Style.Triggers>
</Style>
```
- 控件背景默认灰色    
- 当绑定数据 IsEnabled 为 True → 背景变绿    
- 当 IsEnabled 为 False → 背景变红
# FAQ


# References 
