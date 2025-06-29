---
layout: post
title: WPF Tutorial 1
author: Andy Feng
---
# Introduction
`XAML` stands for Extensible Application Markup Language. Its a simple and declarative language based on XML.
- In XAML, it very easy to create, initialize, and set properties of objects with hierarchical relations.    
- It is mainly used for designing GUIs, however it can be used for other purposes as well, e.g., to declare workflow in Workflow Foundation.
XAML文件会由专门的解析器处理，编译成内部代码，再与c#代码link并编译，最后生成app。
# 扩展标记 Markup Extensions
标记扩展(Markup Extensions) 是微软为XAML专门设计的语法扩展，它**不是** W3C XML标准规范的一部分。这种设计使XAML在保持XML基础语法(可读性/工具支持)的同时，获得了强大的声明式编程能力。
XAML设计了一些扩展是因为：

1. **对象实例化需求**：需要表示复杂的.NET对象关系    
2. **动态绑定需求**：支持WPF的数据绑定系统    
3. **资源复用需求**：支持样式/模板等资源共享

下是 WPF 开发中最常用的扩展标记，按功能分类整理：
## 一、核心数据绑定

| 标记扩展              | 示例                                                              | 说明      |
| ----------------- | --------------------------------------------------------------- | ------- |
| `Binding`         | `{Binding Path=UserName}`                                       | 基本数据绑定  |
| `Binding` (简写)    | `{Binding UserName}`                                            | Path可省略 |
| `RelativeSource`  | `{Binding RelativeSource={RelativeSource AncestorType=Window}}` | 相对源绑定   |
| `TemplateBinding` | `{TemplateBinding Property}`                                    |         |
## 二、资源引用

|标记扩展|示例|说明|
|---|---|---|
|`StaticResource`|`{StaticResource MyBrush}`|静态资源引用|
|`DynamicResource`|`{DynamicResource MyColor}`|动态资源引用|
|`ThemeDictionary`|`{ThemeDictionary Location}`|主题资源字典|

## 三、XAML语言基础

|标记扩展|示例|说明|
|---|---|---|
|`x:Type`|`{x:Type Button}`|获取类型对象|
|`x:Null`|`{x:Null}`|显式null值|
|`x:Static`|`{x:Static SystemColors.ActiveCaptionBrush}`|引用静态成员|
|`x:Array`|`{x:Array Type={x:Type sys:String}}`|创建数组|

## 四、布局与可视化

|标记扩展|示例|说明|
|---|---|---|
|`TemplateBinding`|`{TemplateBinding Background}`|模板绑定|
|`RelativeSource` (Self)|`{Binding RelativeSource={RelativeSource Self}}`|绑定到自身|
|`RelativeSource` (FindAncestor)|`{Binding RelativeSource={RelativeSource AncestorType=ItemsControl}}`|查找父级|

## 五、样式与模板

|标记扩展|示例|说明|
|---|---|---|
|`StaticResource` (样式)|`Style="{StaticResource MyStyle}"`|引用样式|
|`DynamicResource` (模板)|`Template="{DynamicResource MyTemplate}"`|动态模板|

## 六、特殊用途

|标记扩展|示例|说明|
|---|---|---|
|`PriorityBinding`|`{PriorityBinding Bindings={Binding A}, {Binding B}}`|优先级绑定|
|`MultiBinding`|`<MultiBinding Converter="{StaticResource MyConverter}">`|多值绑定|
|`ComponentResourceKey`|`{ComponentResourceKey TypeInTargetAssembly={x:Type local:MyResources}, ResourceId=MyBrush}`|组件资源键|

## 七、设计时支持

|标记扩展|示例|说明|
|---|---|---|
|`d:DesignData`|`d:DataContext="{d:DesignData Source=/Data/SampleData.xaml}"`|设计时数据|
|`d:DesignInstance`|`d:DataContext="{d:DesignInstance Type=local:MyViewModel}"`|设计时实例|

# FAQ


# References 
