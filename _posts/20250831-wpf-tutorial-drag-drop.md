# Introduction
## WPF 拖拽常见方式分类

| 方式                                     | 描述                                                                              | 开发难易  | 扩展性 | 典型使用场景                                    | 优缺点                                                    |
| -------------------------------------- | ------------------------------------------------------------------------------- | ----- | --- | ----------------------------------------- | ------------------------------------------------------ |
| **1. WPF 原生拖拽事件**                      | 使用 `DragEnter`, `DragOver`, `DragLeave`, `Drop` 事件 + `DragDrop.DoDragDrop` 发起拖拽 | 简单    | 低   | 文件拖入、文本拖入、基本拖拽                            | ✅ 轻量，无额外依赖❌ MVVM 不友好，需要 code-behind❌ 内部列表排序需手动实现       |
| **2. GongSolutions.WPF.DragDrop**      | 第三方库，支持 MVVM 风格，使用 `IDropTarget` 或 `DropHandler` 处理拖拽                           | 中等    | 高   | ListBox/ItemsControl 内部排序、集合拖拽、文件夹拖拽、对象拖拽 | ✅ MVVM 完整支持✅ 内置拖拽排序✅ 支持文件夹、多类型对象❌ 需要额外 NuGet 库❌ 学习成本略高 |
| **3. Thumb + Canvas 拖拽**               | 使用 `Thumb` 控件在 `Canvas` 上拖动元素                                                   | 简单    | 中   | 可自由移动元素、绘图控件、拖动 UI 元素                     | ✅ 精确控制位置✅ 可随时获取 X/Y❌ 仅适合 Canvas❌ 不支持文件拖入               |
| **4. DragAdorner + AdornerLayer**      | 自定义拖拽可视化效果，通过 `DragDrop` + AdornerLayer 实现半透明拖拽影像                               | 中等到复杂 | 高   | 拖拽列表或控件时显示拖动影像、可自定义样式                     | ✅ 可自定义拖拽效果✅ 可和原生拖拽结合❌ 需要手动处理 DragDrop，开发复杂             |
| **5. Windows Forms 拖拽 (Interop)**      | WPF 调用 WinForms 拖拽机制                                                            | 中等    | 低   | 特殊场景，兼容老项目或 WinForms 控件                   | ✅ 可以复用 WinForms 组件❌ 与 WPF MVVM 不够契合❌ 风格不统一             |
| **6. Behaviors / Attached Properties** | 使用行为（Blend SDK）封装拖拽逻辑                                                           | 中等    | 高   | MVVM 风格拖拽封装，可在多个控件复用                      | ✅ MVVM 友好✅ 易复用❌ 对复杂拖拽（排序、多类型）仍需自定义逻辑                   |
## 开发难易度 vs 扩展性对比
|方式|开发难易度|扩展性|MVVM 友好度|备注|
|---|---|---|---|---|
|WPF 原生事件|★☆☆|★☆☆|低|适合简单文件拖入或文本拖入|
|GongSolutions.WPF.DragDrop|★★☆|★★★|高|推荐用于 ListBox/ItemsControl 拖拽排序或文件夹拖入|
|Thumb + Canvas|★☆☆|★★☆|中|精确位置拖动，适合绘图或控件自由拖动|
|DragAdorner|★★☆ ~ ★★★|★★★|中|可视化拖拽效果，自定义性高|
|WinForms 拖拽|★★☆|★☆☆|低|兼容老项目，WPF 不推荐|
|Behaviors/Attached Properties|★★☆|★★★|高|封装性强，适合跨项目复用|
## 推荐使用场景
| 需求               | 推荐方式                                  |
| ---------------- | ------------------------------------- |
| 文件或文件夹拖入到控件（如附件） | WPF 原生拖拽 / GongSolutions.WPF.DragDrop |
| MVVM 风格，列表排序     | GongSolutions.WPF.DragDrop            |
| 自由移动控件、画布元素      | Thumb + Canvas                        |
| 自定义拖拽影像效果        | DragAdorner + 原生拖拽                    |
| 兼容老 WinForms 组件  | WinForms 拖拽                           |
| 跨控件可复用拖拽逻辑       | Behaviors / Attached Properties       |
## 总结经验：
1. **简单拖拽文件或对象** → WPF 原生事件够用。
    
2. **MVVM + 集合操作 + 排序 + 文件夹支持** → GongSolutions.WPF.DragDrop 更合适。
    
3. **控件拖动 + 精确位置** → Thumb + Canvas。
    
4. **拖拽视觉效果** → AdornerLayer。
    
5. **可复用、项目级封装** → Behaviors / Attached Properties。
# WPF 原生拖拽（DragEnter/DragLeave/Drop）
直接给控件添加事件handler，
WPF 内置的拖拽事件主要有：

| 事件          | 说明              |
| ----------- | --------------- |
| `DragEnter` | 鼠标拖拽进入控件时触发     |
| `DragOver`  | 鼠标在控件上拖动时持续触发   |
| `DragLeave` | 鼠标拖拽离开控件时触发     |
| `Drop`      | 松开鼠标完成拖拽（放下）时触发 |
所有事件都传入 `DragEventArgs e`，可以通过 `e.Data` 获取拖入的数据。
常用的数据类型：
- `DataFormats.FileDrop`：拖入的文件/文件夹    
- `DataFormats.Text`：拖入的文本    
- `DataFormats.Xaml`：拖入的 XAML 对象
## 基本使用方法

XAML 绑定事件
```xml
<Border Name="AnswerDropArea"
        Background="#FFF5F5F5"
        BorderBrush="#FFCCCCCC"
        BorderThickness="1"
        AllowDrop="True"
        DragEnter="AnswerDropArea_DragEnter"
        DragLeave="AnswerDropArea_DragLeave"
        Drop="AnswerDropArea_Drop">
    <TextBlock Text="将文件拖拽到这里" HorizontalAlignment="Center" VerticalAlignment="Center"/>
</Border>
```
Code-Behind 示例
```csharp
// 拖入文件时，显示效果
private void AnswerDropArea_DragEnter(object sender, DragEventArgs e)
{
    if (e.Data.GetDataPresent(DataFormats.FileDrop))
        e.Effects = DragDropEffects.Copy; // 显示复制效果
    else
        e.Effects = DragDropEffects.None;
}

private void AnswerDropArea_DragLeave(object sender, DragEventArgs e)
{
    // 可以重置 UI 样式
}

private void AnswerDropArea_Drop(object sender, DragEventArgs e)
{
    if (!e.Data.GetDataPresent(DataFormats.FileDrop)) return;

    string[] paths = (string[])e.Data.GetData(DataFormats.FileDrop);
    foreach (var path in paths)
    {
		// 处理文件或文件夹
        if (Directory.Exists(path))
        {
            // 文件夹，递归读取图片/视频
            foreach (var file in Directory.GetFiles(path, "*.*", SearchOption.AllDirectories))
            {
                if (IsSupportedFile(file))
                    AddAttachment(file);
            }
        }
        else if (File.Exists(path) && IsSupportedFile(path))
        {
            AddAttachment(path);
        }
    }
}

// 判断文件类型
private bool IsSupportedFile(string file)
{
    var ext = Path.GetExtension(file).ToLower();
    return ext == ".jpg" || ext == ".jpeg" || ext == ".png"
        || ext == ".mp4" || ext == ".avi" || ext == ".mov";
}

private void AddAttachment(string file)
{
    var bytes = File.ReadAllBytes(file);
    var attachment = new EditAttachmentViewModel
    {
        FileName = Path.GetFileName(file),
        Bytes = bytes,
        IsPreferred = true,
        Type = GetAttachmentType(file)
    };
    Attachments.Add(attachment);
}

private AttachmentType GetAttachmentType(string file)
{
    var ext = Path.GetExtension(file).ToLower();
    if (ext == ".jpg" || ext == ".jpeg" || ext == ".png")
        return AttachmentType.Image;
    else
        return AttachmentType.Video;
}
```
✅ 特点
- 简单灵活，可自定义逻辑
- **原生机制**：不依赖第三方库    
- **灵活度高**：你可以自己判断数据类型（文件、文本、对象等）
- 支持任意控件拖拽    
- 可以处理文件夹、文件、多类型    

**缺点**：MVVM 友好度低，需要在 code-behind 写逻辑
- **手动实现逻辑**：
	- 判断文件类型、过滤    
	- 遍历文件夹    
	- 处理多个文件    
	- 更新 ViewModel  
	- 所有逻辑都需要你自己写
- **复杂操作需要写很多样板代码**
	- 比如拖拽到 `ListBox` 中排序    
	- 拖拽到不同控件做不同操作
## 例子
view
```xml
    <!-- Question Drop Area -->
    <Border Name="DropArea"
        Background="#FFF5F5F5"
        BorderBrush="#FFCCCCCC"
        BorderThickness="1"
        CornerRadius="5"
        Padding="10"
        HorizontalAlignment="Stretch"
        VerticalAlignment="Center"
        AllowDrop="True"
        DragEnter="DropArea_DragEnter"
        DragLeave="DropArea_DragLeave"
        Drop="DropArea_Drop">
     <TextBlock Name="DropText"
               Text="将主故事附件拖拽到这里"
               HorizontalAlignment="Center"
               VerticalAlignment="Center"
               FontSize="16" Height="50"/>
     </Border>
<!-- Answer Drop Area -->
  <Border Name="AnswerDropArea"
            Background="#FFF5F5F5"
            BorderBrush="#FFCCCCCC"
            BorderThickness="1"
            CornerRadius="5"
            Padding="10"
            HorizontalAlignment="Stretch"
            VerticalAlignment="Center"
            AllowDrop="True"
            DragEnter="AnswerDropArea_DragEnter"
            DragLeave="AnswerDropArea_DragLeave"
            Drop="AnswerDropArea_Drop">
    <TextBlock Name="AnswerDropText"
    Text="将场景附件拖拽到这里"
    HorizontalAlignment="Center"
    VerticalAlignment="Center"
    FontSize="16" Height="50"/>
    </Border>
```
view model
```csharp
        // 原背景色
        private Color originalColor = Color.FromRgb(245, 245, 245);
        // 高亮色
        private Color highlightColor = Color.FromRgb(200, 230, 255);

        //// 绑定的附件集合
        //public ObservableCollection<Attachment> QuestionAttachments { get; set; } = new();
        //public ObservableCollection<Attachment> AnswerAttachments { get; set; } = new();

        #region ===== 闪烁动画 =====
        /// <summary>
        /// 拖拽时闪烁提示
        /// </summary>
        /// <param name="target">Border控件</param>
        private void StartFlashAnimation(Border target)
        {
            ColorAnimation animation = new ColorAnimation
            {
                From = originalColor,
                To = highlightColor,
                Duration = TimeSpan.FromMilliseconds(200),
                AutoReverse = true
            };

            target.Background = new SolidColorBrush(originalColor);
            target.Background.BeginAnimation(SolidColorBrush.ColorProperty, animation);
        }

        /// <summary>
        /// 恢复原背景色
        /// </summary>
        private void ResetBackground(Border target)
        {
            target.Background = new SolidColorBrush(originalColor);
        }
        #endregion

        #region ===== Question Drop Area =====
        // 拖拽进入，闪一下高亮
        private void DropArea_DragEnter(object sender, DragEventArgs e)
        {
            if (e.Data.GetDataPresent(DataFormats.FileDrop))
            {
                e.Effects = DragDropEffects.Copy;
                StartFlashAnimation(DropArea);
            }
            else
            {
                e.Effects = DragDropEffects.None;
            }
        }

        private void DropArea_DragLeave(object sender, DragEventArgs e)
        {
            ResetBackground(DropArea);
        }

        private async void DropArea_Drop(object sender, DragEventArgs e)
        {
            ResetBackground(DropArea);

            if (e.Data.GetDataPresent(DataFormats.FileDrop))
            {
                string[] paths = (string[])e.Data.GetData(DataFormats.FileDrop);

                foreach (var path in paths)
                {
                    // 判断是否文件夹
                    if (Directory.Exists(path))
                    {
                        await HandleFolderDrop(path, isQuestion: true);
                    }
                    else if (File.Exists(path))
                    {
                        await HandleFileDrop(path, isQuestion: true);
                    }
                }
                MessageBox.Show("文件上传完成！");
            }
        }
        #endregion

        #region ===== Answer Drop Area =====
        private void AnswerDropArea_DragEnter(object sender, DragEventArgs e)
        {
            if (e.Data.GetDataPresent(DataFormats.FileDrop))
            {
                e.Effects = DragDropEffects.Copy;
                StartFlashAnimation(AnswerDropArea);
            }
            else
            {
                e.Effects = DragDropEffects.None;
            }
        }

        private void AnswerDropArea_DragLeave(object sender, DragEventArgs e)
        {
            ResetBackground(AnswerDropArea);
        }

        private async void AnswerDropArea_Drop(object sender, DragEventArgs e)
        {
            ResetBackground(AnswerDropArea);

            if (e.Data.GetDataPresent(DataFormats.FileDrop))
            {
                string[] paths = (string[])e.Data.GetData(DataFormats.FileDrop);

                foreach (var path in paths)
                {
                    if (Directory.Exists(path))
                    {
                        await HandleFolderDrop(path, isQuestion: false);
                    }
                    else if (File.Exists(path))
                    {
                        await HandleFileDrop(path, isQuestion: false);
                    }
                }
            }
            MessageBox.Show("所有文件上传完成！");
        }
        #endregion

        #region ===== 文件/文件夹处理方法 =====
        /// <summary>
        /// 处理单个文件
        /// </summary>
        private async Task HandleFileDrop(string filePath, bool isQuestion)
        {
            if (!IsImageFile(filePath)) return;

            var attachment = await UploadAttachmentAsync(filePath, isQuestion);
            //if (isQuestion)
                //QuestionAttachments.Add(attachment);
            //else
                //AnswerAttachments.Add(attachment);
        }

        /// <summary>
        /// 递归处理文件夹
        /// </summary>
        private async Task HandleFolderDrop(string folderPath, bool isQuestion)
        {
            // 获取所有文件（递归）
            var files = Directory.GetFiles(folderPath, "*.*", SearchOption.AllDirectories);
            foreach (var file in files)
            {
                await HandleFileDrop(file, isQuestion);
            }
        }

        /// <summary>
        /// 判断是否是图片文件
        /// </summary>
        private bool IsImageFile(string filePath)
        {
            string ext = System.IO.Path.GetExtension(filePath).ToLower();
            return ext == ".jpg" || ext == ".jpeg" || ext == ".png" || ext == ".bmp" || ext == ".gif";
        }
        #endregion

        #region ===== 上传逻辑示例 =====
        /// <summary>
        /// 模拟上传附件
        /// </summary>
        private async Task<Attachment> UploadAttachmentAsync(string filePath, bool isQuestion)
        {
            // 模拟异步上传延迟
            await Task.Delay(200);

            var attachment = new Attachment
            {
                FileName = System.IO.Path.GetFileName(filePath),
                CreatedAt = DateTime.Now,
                Type = "File",
                QuestionId = isQuestion ? 1 : null,
                AnswerId = isQuestion ? null : 1
            };

            // TODO: 替换为实际上传逻辑（HTTP/FTP/云存储）
            return attachment;
        }
        #endregion
```
# GongSolutions.WPF.DragDrop
[GongSolutions.WPF.DragDrop](https://github.com/punker76/gong-wpf-dragdrop)
这是一个专门为 WPF MVVM 封装的拖拽库。它封装了拖拽事件，并支持：
1. **直接绑定到 ViewModel**（通过 `IDropTarget` 或 `DragDrop.DropHandler`）    
    - 不用在 code-behind 写事件处理        
2. **内置数据类型检查**    
    - 自动识别拖拽的对象        
    - 支持 `ListBox`、`ItemsControl` 内部拖拽排序        
3. **支持拖拽排序、复制、移动操作**    
4. **支持 MVVM 命令**，更容易与 `ObservableCollection` 配合    
5. **扩展性强**，比如拖拽到不同控件、拖拽多个类型的数据
```powershell
Install-Package GongSolutions.WPF.DragDrop
```
![](images/posts/20250831-13.jpeg)
在 XAML 中添加命名空间：
```xml
xmlns:dd="urn:gong-wpf-dragdrop"
```
## 基本使用方法
XAML
```xml
<ListBox ItemsSource="{Binding Attachments}"
         dd:DragDrop.IsDropTarget="True"
         dd:DragDrop.IsDragSource="True"
         dd:DragDrop.DropHandler="{Binding}">
    <ListBox.ItemTemplate>
        <DataTemplate>
            <TextBlock Text="{Binding FileName}" />
        </DataTemplate>
    </ListBox.ItemTemplate>
</ListBox>
```
> - `IsDragSource="True"`：允许从控件拖出。    
- `IsDropTarget="True"`：允许拖入。    
- `DropHandler`：绑定到实现 `IDropTarget` 的 ViewModel 或类。
你不用写 `DragEnter/DragLeave/DragOver`，库会帮你管理这些事件。
## 实现 IDropTarget 接口
这是核心，你需要在 ViewModel 或 Handler 类中实现 `IDropTarget`：
```csharp
using GongSolutions.Wpf.DragDrop;
using GongSolutions.Wpf.DragDrop.Utilities;

public class EditAnswerViewModel : IDropTarget
{
    public ObservableCollection<EditAttachmentViewModel> Attachments { get; set; } = new();

    // 拖入时调用
    public void DragOver(IDropInfo dropInfo)
    {
        // dropInfo.Data：拖入的数据
        // dropInfo.TargetCollection：目标集合
        if (dropInfo.Data is EditAttachmentViewModel)
        {
            dropInfo.Effects = DragDropEffects.Move;
            dropInfo.DropTargetAdorner = DropTargetAdorners.Highlight; // 高亮提示
        }
        else if (dropInfo.Data is string[] files) // 文件拖入
        {
            dropInfo.Effects = DragDropEffects.Copy;
            dropInfo.DropTargetAdorner = DropTargetAdorners.Insert;
        }
    }

    // 放下时调用
    public void Drop(IDropInfo dropInfo)
    {
        if (dropInfo.Data is EditAttachmentViewModel vm)
        {
            // 内部排序逻辑
            var collection = dropInfo.TargetCollection as ObservableCollection<EditAttachmentViewModel>;
            var insertIndex = dropInfo.InsertIndex;
            if (collection != null)
            {
                collection.Remove(vm);
                collection.Insert(insertIndex, vm);
            }
        }
        else if (dropInfo.Data is string[] paths)
        {
            var collection = dropInfo.TargetCollection as ObservableCollection<EditAttachmentViewModel>;
            if (collection != null)
            {
                foreach (var path in paths)
                {
                    // 处理文件或文件夹
                }
            }
        }
    }
}
```

**DropInfo 重要属性**
- `Data`：拖入的数据对象。    
- `TargetCollection`：目标集合（ItemsSource）。    
- `InsertIndex`：插入位置。    
- `DropTargetAdorner`：拖拽效果（高亮、插入线等）。    
- `Effects`：拖拽效果（Copy/Move/None）。

✅ 特点
- **完全 MVVM**，不需要 code-behind    
- 支持内部拖拽排序    calc
- 可拖入文件夹、多文件、对象集合    
- 支持自定义效果（Copy/Move/Link）    
- 可以处理复杂场景：分组拖拽、列表排序、不同控件拖拽    
- **缺点**：需要额外库，初次理解接口稍复杂
## WPF 原生拖拽 vs GongSolutions.WPF.DragDrop
| 特性      | WPF 原生拖拽                            | GongSolutions.WPF.DragDrop                 |
| ------- | ----------------------------------- | ------------------------------------------ |
| 事件触发    | DragEnter/DragLeave/DragOver/Drop   | 通过 IDropTarget.Drop / DragDrop.DropHandler |
| MVVM 友好 | 差，必须在 code-behind 写逻辑。不能在viewmodel写 | 非常好，绑定 ViewModel                           |
| 内部排序    | 无，要手动实现                             | 内置支持 ListBox/ItemsControl 排序。推荐Listbox     |
| 文件夹拖拽   | 可以，需要手动递归                           | 可以，Drop 方法里处理即可                            |
| 多数据类型   | 手动判断                                | DropInfo.Data + 类型判断即可                     |
| 易用性     | 简单场景易用，复杂场景难                        | 复杂场景易用，简单场景也可以                             |
| 学习成本    | 低                                   | 略高（接口、DropInfo）                            |
| 易用性     | 灵活但样板代码多                            | 封装好，写少量逻辑即可                                |
✅ **结论**：
- 如果你只想拖拽文件到一个控件，逻辑简单，WPF 原生拖拽足够用。    
- 如果你希望 **MVVM 风格、支持拖拽排序、多文件/文件夹、减少 code-behind**，建议用 **GongSolutions.WPF.DragDrop**。
# FAQ