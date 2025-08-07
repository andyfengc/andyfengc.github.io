---
layout: post
title: MaterialDesignInXamlToolkit Tutorial
author: Andy Feng
---
# Introduction 
一个基于 **Google Material Design** 规范的WPF UI控件库，提供完整的Material风格组件和主题系统。
### **兼容性**

|框架|支持情况|
|---|---|
|.NET Framework 4.6.1+|✅|
|.NET Core 3.1+|✅|
|.NET 5/6/7/8/9|✅|
|MAUI/UWP|❌ (仅限WPF)|
## 基本用法
预制配色
```xml
<ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Light.xaml" />
<ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Defaults.xaml" />
```
view
```xml
<!-- XAML中放置Snackbar -->
<materialDesign:Snackbar 
    MessageQueue="{Binding NotificationQueue}" 
    HorizontalAlignment="Left"/>
```
view model
```csharp
// ViewModel中调用
var message = new SnackbarMessage { Content = "文件保存成功!" };
NotificationQueue.Enqueue(message);
```
**MVVM集成**：
```csharp
public class MainViewModel
{
    public ISnackbarMessageQueue NotificationQueue { get; } = new SnackbarMessageQueue();
}
```
**全局异常处理**
```csharp
AppDomain.CurrentDomain.UnhandledException += (s, e) => 
    NotificationQueue.Enqueue($"错误: {(e.ExceptionObject as Exception)?.Message}");
```
# 安装使用
## 安装
```bash
# 核心库
Install-Package MaterialDesignThemes

# Material Design 图标（可选）
Install-Package MaterialDesignColors
```
![](images/posts/Pasted%20image%2020250720020642.png)
## 配置主题
app.xmal 基础配置
```xml
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <!-- 主题（Light/Dark） -->
            <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Light.xaml" />            
            <!-- 控件默认样式 -->
            <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Defaults.xaml" />            
            <!-- 颜色（可选） -->
            <ResourceDictionary Source="pack://application:,,,/MaterialDesignColors;component/Themes/Recommended/Primary/MaterialDesignColor.Indigo.xaml" />
            <ResourceDictionary Source="pack://application:,,,/MaterialDesignColors;component/Themes/Recommended/Accent/MaterialDesignColor.Teal.xaml" />
        </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
</Application.Resources>
```

**设置启动窗口主题**
在 `MainWindow.xaml` 中添加命名空间和主题属性：
```xml
<Window ...
        xmlns:materialDesign="http://materialdesigninxaml.net/winfx/xaml/themes"
        TextElement.Foreground="{DynamicResource MaterialDesignBody}"
        Background="{DynamicResource MaterialDesignPaper}"
        TextElement.FontWeight="Medium"
        FontFamily="pack://application:,,,/MaterialDesignThemes.Wpf;component/Resources/Roboto/#Roboto">
</Window>
```
## 功能使用
使用material按钮
```xml
<Button Style="{StaticResource MaterialDesignRaisedButton}"
        Content="确定"
        Foreground="White"
        Background="{DynamicResource PrimaryHueMidBrush}"/>
```
**Snackbar 通知**
```xml
<xml...
        xmlns:materialDesign="http://materialdesigninxaml.net/winfx/xaml/themes">

<!-- 在 XAML 中添加 Snackbar -->
<materialDesign:Snackbar MessageQueue="{Binding NotificationQueue}" />
```
```csharp
// 在 ViewModel 中调用
var message = new SnackbarMessage { Content = "操作成功！" };
NotificationQueue.Enqueue(message);
```

**对话框服务**
```xml
<!-- 在根布局添加 DialogHost -->
<materialDesign:DialogHost Identifier="RootDialog">
    <!-- 页面内容 -->
</materialDesign:DialogHost>
```
```csharp
// 打开对话框
await DialogHost.Show(new UserControl(), "RootDialog");

// 关闭对话框
DialogHost.Close("RootDialog");
```
# 高级配置
## **动态切换主题**

## 自定义snackbar样式
```xml
<materialDesign:Snackbar.MessageTemplate>
    <DataTemplate>
        <Border Background="{DynamicResource SecondaryHueMidBrush}"
                CornerRadius="4">
            <TextBlock Text="{Binding}" 
                       Margin="16"
                       Foreground="White"/>
        </Border>
    </DataTemplate>
</materialDesign:Snackbar.MessageTemplate>
```
## **对话框服务**
```csharp
// 打开对话框
await DialogHost.Show(new UserEditorView(), "MainDialogHost");

// 关闭对话框
DialogHost.Close("MainDialogHost");
```
响应式布局
```xml
<materialDesign:Card UniformCornerRadius="8">
    <Grid>
        <materialDesign:RatingBar Value="{Binding Rating}" />
    </Grid>
</materialDesign:Card>
```
# FAQ
## 性能优化建议
 **虚拟化列表**：对大数据集使用`MaterialDesignVirtualizingStackPanel`
    
**按需加载资源**：
```xml
<ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.OnlyEssential.xaml" />
```
**禁用复杂动画**：
```csharp
new PaletteHelper().SetBaseTheme(BaseTheme.Inherit); // 继承系统性能设置
```
# References 
[MaterialDesignInXamlToolkit Github](https://github.com/MaterialDesignInXAML/MaterialDesignInXamlToolkit)