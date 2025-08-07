---
layout: post
title: Windows Forms PropertyGrid Tutorial
author: Andy Feng
tags:
  - "#wpf"
  - "#dotnet"
---
# Introduction
**PropertyGrid** 是 Windows Forms 中的一个强大控件(Control)，它提供了一个用户界面，**用于可视化显示和编辑对象的属性**。它就像一个"属性查看器"，可以自动列出对象的属性，并提供合适的编辑器（如文本框、下拉框、颜色选择器等）。
它提供了类似于 Visual Studio 属性窗口的功能，能够自动显示和编辑任何 .NET 对象的属性。
PropertyGrid 是创建专业属性编辑界面的便捷工具，特别适合需要动态配置或开发工具的场景。
如果你想要一个**快速、自动化的属性编辑界面**，PropertyGrid 是最佳选择！ 🚀
> PropertyGrid 是针对**整个类(对象)**的，而不是针对单个属性的。你不需要为每个属性单独生成 PropertyGrid，而是只需要为整个类实例创建一个 PropertyGrid 即可。会根据属性类型自动选择合适的编辑器(文本框、复选框、颜色选择器等)
## 典型使用场景
- 开发环境的属性窗口（如 Visual Studio 中的属性窗口）    
- 应用程序的配置界面    
- 游戏/3D 编辑器属性面板
- 对象编辑工具    
- 动态表单生成

| 要点       | 说明                                           |
| -------- | -------------------------------------------- |
| **是什么**  | 一个自动显示和编辑对象属性的控件                             |
| **用途**   | 快速构建属性编辑界面，减少手动 UI 代码                        |
| **核心方法** | `SelectedObject = myObject`                  |
| **常用特性** | `[Category]`, `[Browsable]`, `[Description]` |
| **高级功能** | 自定义 `UITypeEditor`、动态属性控制                    |
## **不适用场景：**

❌ 需要完全自定义 UI 布局（PropertyGrid 样式有限）  
❌ 超大数据量（性能可能受影响）

## 主要功能

1. **属性查看和编辑**：显示选定对象的属性和它们的值，并允许用户编辑这些值。**自动反射**：通过 .NET 反射机制获取对象的属性，无需手动编写 UI。
    
2. **分类显示和排序**：可以按类别或字母顺序，组织排列属性。
    
3. **类型编辑器**：为不同类型的属性提供适当的编辑界面（如颜色选择器、字体对话框等）。根据属性类型自动选择合适的编辑器（如 `bool` 显示复选框，`Color` 显示颜色选择器）。
    
4. **属性描述**：显示选定属性的描述信息。

5. **可扩展**：支持自定义属性显示方式、编辑器、验证逻辑等。

PropertyGrid 的强大之处在于它的可扩展性，通过合理组合 TypeConverter、UITypeEditor 和各种特性，可以创建出非常专业的属性编辑界面，而无需从头开发复杂的UI逻辑。
# Why use it?
### **主要用途：**

1. **快速构建属性编辑界面**
    
    - 不需要手动为每个属性设计输入框、下拉框等控件，PropertyGrid 自动生成。
        
    - 适合动态配置、设置面板、工具选项等场景。
        
2. **简化对象调试和配置**
    
    - 在开发工具、游戏引擎、设计软件中，常用于动态调整对象参数。
        
3. **替代手动编写大量 UI 代码**
    
    - 如果手动实现类似功能，可能需要几十个 `TextBox`、`ComboBox`、`CheckBox`，而 PropertyGrid 一行代码搞定。
# # How to use?
## 基本使用
```csharp
// 创建一个 PropertyGrid 实例
PropertyGrid propertyGrid1 = new PropertyGrid();

// 设置要显示的对象
propertyGrid1.SelectedObject = yourObject;

// 常用属性
propertyGrid1.ToolbarVisible = true;  // 显示工具栏
propertyGrid1.HelpVisible = true;     // 显示帮助区域
propertyGrid1.PropertySort = PropertySort.Categorized; // 按类别排序
```
## 自定义 PropertyGrid

您可以通过以下方式自定义 PropertyGrid 的行为：
### 使用 `[Browsable]`、`[Category]`、`[Description]` 等特性控制属性的显示
```csharp
public class MySettings
{
    [Category("外观")]
    [Description("窗体背景颜色")]
    public Color BackgroundColor { get; set; } = Color.White;

    [Category("行为")]
    [Description("是否启用自动保存")]
    public bool AutoSave { get; set; } = true;
}
// 使用
var settings = new MySettings();
propertyGrid1.SelectedObject = settings;
```
example 2:
```csharp
public class Configuration
{
    [Browsable(false)]  // 隐藏属性
    public string InternalId { get; set; }

    [Category("Display")]
    [Description("Main window title")]
    [DefaultValue("My Application")]
    public string WindowTitle { get; set; } = "My Application";

    [ReadOnly(true)]  // 只读
    public string Version { get; } = "1.0.0";

    [DisplayName("Max Connections")]
    public int MaxConnections { get; set; }
}
```
实现自定义类型转换器和 UI 类型编辑器
    
处理 `PropertyValueChanged` 事件响应属性更改
# 底层原理
## 反射机制
PropertyGrid 底层使用 .NET 的反射机制来获取对象的属性信息。当设置 SelectedObject 时，它会：

- 通过 TypeDescriptor 获取对象类型信息
    
- 分析所有公共属性
    
- 根据属性特性决定显示方式
    
- 为每个属性创建适当的编辑器
可以实现 ICustomTypeDescriptor 或使用 TypeDescriptionProvider 动态控制属性：
```csharp
public class DynamicPropertyObject : ICustomTypeDescriptor
{
    private Dictionary<string, object> properties = new Dictionary<string, object>();

    public void AddProperty(string name, object value, 
                          string category = "Dynamic", 
                          string description = "")
    {
        properties[name] = value;
        // 可以扩展存储更多元数据
    }

    // 实现 ICustomTypeDescriptor 接口方法...
}
```
## 编辑器系统

PropertyGrid 包含一个完整的编辑器系统：

- **基本类型编辑器**：文本框(字符串)、数值框(数字)、复选框(bool)
    
- **复杂类型编辑器**：
    
    - ColorEditor - 颜色选择器
        
    - FontEditor - 字体对话框
        
    - DateTimeEditor - 日期时间选择
        
    - CollectionEditor - 集合编辑器
如果这些都不能满足需求，可以自定义编辑器，方法是基于UITypeEditor
> UITypeEditor帮助自定义对象属性的高级编辑器
```csharp
public class ImageFileNameEditor : UITypeEditor
{
    public override UITypeEditorEditStyle GetEditStyle(ITypeDescriptorContext context)
    {
        return UITypeEditorEditStyle.Modal;
    }

    public override object EditValue(ITypeDescriptorContext context, 
                                   IServiceProvider provider, 
                                   object value)
    {
        OpenFileDialog dlg = new OpenFileDialog();
        dlg.Filter = "Image Files|*.jpg;*.png;*.bmp";
        if (dlg.ShowDialog() == DialogResult.OK)
        {
            return dlg.FileName;
        }
        return value;
    }
}

// 应用编辑器
[Editor(typeof(ImageFileNameEditor), typeof(UITypeEditor))]
public string LogoPath { get; set; }
```

## 类型转换系统

依赖 TypeConverter 体系在属性值和显示字符串之间转换：

- 内置转换器：ColorConverter、FontConverter 等
    
- 自定义转换器：继承自 TypeConverter
# 使用场景
| 场                | 说明                                    || ---------------- | ------------------------------------- ||
| **应用程序设置窗口*      | 编辑 `AppSettings` 类的属性（如主题颜色、字体、默认路径等） |
| **游戏/3D 编辑器*     | 调整对象的位置、旋转、缩放等参数                      |
| **动态表单生成*        | 根据数据库或配置文件动态生成可编辑的属性表                 |
| **开发工具*          | 类似 Visual Studio 的属性窗口，用于调整控件属性       |
 <br><br>**对象调试**  | 在测试时快速查看和修改对象的内部状态                    |

## 高级应用场景

###  多对象编辑

csharp

// 同时编辑多个对象的共同属性
PropertyGrid.SelectedObjects = new object[] { obj1, obj2, obj3 };

### 属性依赖

csharp

[RefreshProperties(RefreshProperties.All)]
public int Mode 
{
    get { return mode; }
    set { mode = value; UpdateDependentProperties(); }
}

private void UpdateDependentProperties()
{
    // 强制刷新属性网格
    TypeDescriptor.Refresh(this);
}

### 动态属性可见性

csharp

public bool ShouldSerializePropertyName()
{
    // 控制属性是否应序列化(影响属性网格显示)
    return condition;
}

[Browsable(false)]
public bool PropertyNameSpecified 
{
    get { return ShouldSerializePropertyName(); }
    set { }
}

### 深入事件处理

csharp

propertyGrid1.PropertyValueChanged += (s, e) => 
{
    Console.WriteLine($"Property {e.ChangedItem.PropertyName} changed");
};

propertyGrid1.SelectedGridItemChanged += (s, e) => 
{
    // 当前选中属性变化时触发
};

propertyGrid1.PropertySortChanged += (s, e) => 
{
    // 排序方式变化时触发
};

### 样式定制

csharp

// 修改外观
propertyGrid1.BackColor = Color.White;
propertyGrid1.CategoryForeColor = Color.Blue;
propertyGrid1.LineColor = Color.LightGray;
propertyGrid1.HelpBackColor = Color.AliceBlue;

// 完全自定义绘制
public class CustomPropertyGrid : PropertyGrid
{
    protected override void OnPaint(PaintEventArgs e)
    {
        // 自定义绘制逻辑
        base.OnPaint(e);
    }
}
# FAQ
## 性能优化技巧

1. **延迟加载**：对于复杂对象，实现 IPropertyValueUIService
    
2. **批量更新**：使用 BeginUpdate/EndUpdate 模式
    
3. **缓存反射结果**：自定义 TypeDescriptionProvider
    
4. **虚拟化**：对于大型属性集实现虚拟属性
## Tips
### 调试技巧

1. 检查 PropertyGrid 的 SelectedGridItem 属性获取当前选中项
    
2. 使用 PropertyGrid.GetPropertyEntry 方法访问内部属性条目
    
3. 通过 PropertyGrid.Controls 访问内部工具栏和帮助区域
### 最佳实践建议

1. 对于频繁更新的对象，考虑使用 BindingSource 作为中间层
    
2. 复杂对象应该实现 INotifyPropertyChanged 接口
    
3. 为常用属性提供合理的 DefaultValue
    
4. 对大型属性集考虑分页或分类显示
# References 
[PropertyGrid.cs](https://learn.microsoft.com/en-us/dotnet/api/system.windows.forms.propertygrid?view=windowsdesktop-9.0)