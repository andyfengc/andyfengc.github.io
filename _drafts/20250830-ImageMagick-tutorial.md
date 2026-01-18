# Introduction
ImageMagick（图像魔术师），是一个非常强大、跨平台的开源图像处理工具。它主要以命令行方式使用，但也提供了丰富的 API（C、C++、Python、Java 等），几乎是图像处理领域的“瑞士军刀”
它支持 命令行操作图像的几乎所有工作，适合批量处理、脚本自动化、跨平台使用，特别适合你这种做视频、图像叠加、遮罩的场景。

| 类型       | 功能示例                                       |
| -------- | ------------------------------------------ |
| ✅ 格式转换   | PNG ↔ JPG ↔ GIF ↔ WebP ↔ BMP ...           |
| ✅ 缩放/裁剪  | -resize, -crop, -extent, -gravity          |
| ✅ 圆角/遮罩  | 自动绘制圆角矩形、生成透明遮罩                            |
| ✅ 批量操作   | 一行代码处理成百上千张图                               |
| ✅ 加文字/水印 | -draw, -annotate, -font, -fill, -pointsize |
| ✅ 图层合成   | 图像叠加、混合、加遮罩、加 alpha、加边框                    |
| ✅ 生成动画   | 多图合成 GIF/APNG（控制帧率/延迟）                     |
| ✅ 图像信息提取 | 获取宽高、颜色空间、通道、透明度信息                         |
| ✅ 图像处理算法 | 模糊、锐化、边缘检测、去噪、直方图、阈值等                      |
| ✅ 颜色操作   | 灰度化、alpha通道、通道分离与合并、调色                     |
| ✅ 图像绘制   | 用命令生成图形、绘制线条、圆、矩形、文字等                      |
新版从 7.x 开始，把核心命令统一到 magick 这个入口，比如：
```bash
magick input.png -resize 200x200 output.jpg
```
在旧版本中常用的 `convert`、`mogrify` 等命令，现在推荐通过 `magick` 前缀调用。
## **特点**：    
1. 开源免费（Apache 2.0 License）。        
2. 跨平台：支持 Linux、Windows、macOS。        
3. 支持 **200+ 图像格式**（JPEG、PNG、GIF、TIFF、SVG、PDF、HEIC、WebP 等）。        
4. 支持 **批处理**：命令行脚本一次性处理成千上万张图像。        
5. 丰富的图像操作功能：转换、缩放、裁剪、旋转、颜色调整、特效滤镜、合成、水印、动画、格式转换……几乎涵盖所有常见需求。
## 使用场景
Web开发：动态生成缩略图、头像裁剪、水印。

批量图像处理：摄影师、大量图片格式转换。

机器学习/深度学习：数据集图片预处理。

出版与印刷：高分辨率 PDF/图像转换。

视频帧处理：抽帧、序列图合成。
## 优点与缺点

优点
功能极其强大，几乎涵盖所有图像处理需求。
支持脚本化和自动化，批处理效率高。
跨平台，生态成熟，文档丰富。

缺点
命令行语法相对复杂，参数多，新手上手需要时间。
性能上虽然不错，但处理超大图像时内存消耗会比较高。
# Install
- **Windows**：下载安装包（自带 magick.exe）。
    [https://imagemagick.org/script/download.php#windows](https://imagemagick.org/script/download.php#windows)
![](images/posts/20251113.jpeg)
- **Linux**（Ubuntu 举例）：    
    `sudo apt install imagemagick`
    
- **macOS**（Homebrew）：    
    `brew install imagemagick`
# 常用命令
magick：主命令入口。
## 基础操作

| 功能     | 命令                                 |
| ------ | ---------------------------------- |
| 查看图片信息 | `magick identify image.jpg`        |
| 批量查看信息 | `magick identify *.jpg`            |
| 格式转换   | `magick in.png out.jpg`            |
| 批量转换格式 | `magick mogrify -format jpg *.png` |
magick convert（7.x 已集成进 magick）：图像格式转换与处理。
magick mogrify（7.x 已集成进 magick）：批量修改图片（覆盖原文件）。

格式转换：magick a.png b.jpg
批量转换：`magick *.png -set filename:f "%t" "%[filename:f].jpg"`
## 尺寸与裁剪
|功能|命令|
|---|---|
|缩放到固定尺寸|`magick in.jpg -resize 200x200 out.jpg`|
|按比例缩放 50%|`magick in.jpg -resize 50% out.jpg`|
|裁剪 100x100 区域|`magick in.jpg -crop 100x100+10+10 out.jpg`|
|裁剪并居中|`magick in.jpg -gravity center -crop 200x200+0+0 out.jpg`|
|添加白色边框|`magick in.jpg -bordercolor white -border 20 out.jpg`|
|预设解码尺寸（加速）|`magick -size 800x600 in.jpg -resize 200x200 out.jpg`|
缩放：magick in.png -resize 200x200 out.png
裁剪：magick in.jpg -crop 100x100+10+10 out.jpg
旋转：magick in.jpg -rotate 90 out.jpg
-resize, -crop, -extent, -gravity
## 旋转与翻转
|功能|命令|
|---|---|
|旋转 90°|`magick in.jpg -rotate 90 out.jpg`|
|垂直翻转|`magick in.jpg -flip out.jpg`|
|水平翻转|`magick in.jpg -flop out.jpg`|
## 颜色与亮度
| 功能             | 命令                                                 |
| -------------- | -------------------------------------------------- |
| 调整亮度+10，对比度+20 | `magick in.jpg -brightness-contrast 10x20 out.jpg` |
| 转灰度图           | `magick in.jpg -colorspace Gray out.jpg`           |
| 调整饱和度          | `magick in.jpg -modulate 100,150,100 out.jpg`      |
调整亮度/对比度：magick in.jpg -brightness-contrast 10x20 out.jpg
锐化/模糊：magick in.jpg -sharpen 0x1 out.jpg
## 滤镜与特效
|功能|命令|
|---|---|
|模糊|`magick in.jpg -blur 0x5 out.jpg`|
|锐化|`magick in.jpg -sharpen 0x2 out.jpg`|
|阴影效果|`magick in.png -background none -shadow 60x4+10+10 out.png`|
|边缘检测|`magick in.jpg -edge 1 out.jpg`|
## 文本与水印
|功能|命令|
|---|---|
|添加文字|`magick in.jpg -pointsize 36 -fill white -annotate +10+50 "Hello" out.jpg`|
|图片水印|`magick in.jpg watermark.png -gravity southeast -composite out.jpg`|
|半透明水印|`magick in.jpg ( watermark.png -alpha set -channel A -evaluate set 30% ) -gravity southeast -composite out.jpg`|
添加文字：magick in.jpg -pointsize 36 -draw "text 10,50 'Hello World'" out.jpg
加水印：magick in.jpg watermark.png -gravity southeast -composite out.jpg
## 多图操作
| 功能    | 命令                                                                           |
| ----- | ---------------------------------------------------------------------------- |
| 横向拼接  | `magick convert +append img1.jpg img2.jpg out.jpg`                           |
| 纵向拼接  | `magick convert -append img1.jpg img2.jpg out.jpg`                           |
| 创建拼贴画 | `magick montage img1.jpg img2.jpg img3.jpg -tile 3x1 -geometry +2+2 out.jpg` |
| 图像合成  | -composite                                                                   |
拼接多图：magick montage a.jpg b.jpg c.jpg -tile 3x1 -geometry +2+2 out.jpg
图层合成：magick background.jpg overlay.png -gravity center -composite out.jpg
## 动画与序列
|功能|命令|
|---|---|
|GIF → PNG 序列|`magick anim.gif frame_%02d.png`|
|PNG 序列 → GIF|`magick *.png out.gif`|
|调整帧率|`magick -delay 20 -loop 0 *.png out.gif`|
GIF 转 PNG 序列：`magick anim.gif frame_%02d.png`

## PDF 与矢量
|功能|命令|
|---|---|
|PDF → PNG|`magick file.pdf page_%02d.png`|
|多图 → PDF|`magick *.png out.pdf`|
|SVG → PNG|`magick file.svg out.png`|
多图合并成 PDF：`magick *.png out.pdf`
## 图像比较与分析
|功能|命令|
|---|---|
|比较两图差异|`magick compare img1.jpg img2.jpg diff.png`|
|直方图|`magick in.jpg histogram.png`|
# 应用
## magick -size
-size WxH 用来 指定生成或处理图像的尺寸：
W = 宽度（像素）
H = 高度（像素）

它通常在 生成新图像、渐变图、文本画布 时使用。

在创建空白图像时指定尺寸：生成纯色画布
```
magick -size 200x200 canvas:white out.png
```
处理某些格式（如 JPEG、PNG）时，作为解码提示，加快处理速度。
```
magick -size 800x600 photo.jpg -resize 200x200 out.jpg
```
 > 这里 `-size 800x600` 告诉解码器“原图大约是这个尺寸”，优化速度。
生成渐变图, 在**540px x 960px** 的画布上生成从白到黑的渐变
```
magick -size 540x960 gradient:white-black gradient.png
```
> 没有 `-size`，ImageMagick 不知道生成多大画布，会报错或者使用默认尺寸。

把渐变放到屏幕下半部分，上半部分自动填充背景色
```bash
magick -size 540x960 gradient:white-black -gravity south -extent 540x960 mask_half.png
```
![](images/posts/20251004-1.jpeg)

## 常用技巧
处理多张图时，常用 `mogrify`（会覆盖原图），如：
```
magick mogrify -resize 800x800 *.jpg
```
> 如果不想覆盖，推荐用循环或者 `-path` 指定输出目录。
## 生成蒙版
### 圆角蒙版
假如你有一张图片 image.png，想让它变成圆角效果，流程通常是：
1. 生成一个白色圆角矩形的蒙版 mask.png（白色是圆角内区域，黑色是外部）
2. 用蒙版和图片做 alphamerge（合成 alpha 通道）
3. 得到一张带圆角透明边的图片

生成一个820x600的蒙版，四个角圆形40px
```
magick -size 820x600 xc:black -fill white -draw "roundrectangle 0,0 819,599 40,40" mask-rounded.png
```
![](images/posts/20251004.jpeg)
参数逐个解释
1. **-size 820x600**    
    - 指定画布大小为 **820×600 像素**。        
    - 注意这里没有输入图片，所以这是在新建一张图。        
2. **`xc:black**    
    - `xc:` 是 **X Constant**，表示用单一颜色填充画布。        
    - `xc:black` → 生成一张纯黑色背景的图（820×600）。        
3. **`-fill white`**    
    - 设置接下来绘制的图形使用的填充颜色 → 白色。        
4. **`-draw "roundrectangle 0,0 819,599 40,40"`**    
    - `-draw` 用于在画布上绘制形状。        
    - `roundrectangle`：绘制一个圆角矩形。        
    - 参数说明：        
        - **`0,0`** → 左上角坐标。            
        - **`819,599`** → 右下角坐标（因为像素是从0开始，所以正好覆盖整个 820×600 的画布）。            
        - **`40,40`** → 圆角的水平半径和垂直半径（即圆角大小为 40px）。    
    👉 结果：在黑色背景上画了一个白色的圆角矩形，刚好覆盖整张图。    
5. **`mask-rounded.png`**    
    - 输出文件名。
### 圆形蒙版
magick -size 540x960 radial-gradient:white-black -blur 0x50 -gravity center -crop 540x960+0+0 +repage vignette-540x960.png

| 部分                            | 含义                    |
| ----------------------------- | --------------------- |
| `-size 540x960`               | 创建一张 540x960 的画布      |
| `radial-gradient:white-black` | 生成一个径向渐变，从中心的白色到外圈的黑色 |
| `-blur 0x50`                  | 模糊边缘，使过渡更柔和           |
| `-gravity center`             | 以中心为参考点               |
| `-crop 540x960+0+0 +repage`   | 裁剪回指定大小，去掉多余画布        |
| `vignette-540x960.png`        | 输出文件名                 |
这会生成一张中间白色、四周黑色的径向渐变遮罩图。
如果你把它叠加在视频上，中心区域会透过（透明），四周会变暗。

如果你希望白色范围更大，黑色只出现在边角，可以这样：
magick -size 540x960 radial-gradient:white-black -level 30%,100% -blur 0x60 vignette-soft.png
-level 80%,100%：把黑色的部分提升，让渐变更“宽”，中间更亮。→ 把黑色压缩到画面最边缘，保留中心 80% 为纯白 → 将输入亮度 80%~100% 映射到输出 0~100%

// 中间透明，四周黑色的遮罩图
magick -size 540x960 radial-gradient:none-black -blur 0x50 -gravity center -crop 540x960+0+0 +repage vignette-540x960.png
### 蒙版技巧
#### 希望中间白色区域大一些，黑色部分薄一点
缩小渐变的黑色部分，然后再拉回原尺寸。
```bash
magick -size 540x960 radial-gradient:white-black -resize 70%x70% -background black -gravity center -extent 540x960 -blur 0x20 vignette-540x960.png
```

| 参数                                | 作用                       |
| --------------------------------- | ------------------------ |
| `radial-gradient:white-black`     | 生成中心白、外圈黑的径向渐变           |
| `-resize 70%x70%`                 | 把整个渐变缩小到 70%，白区变大、黑区退到边缘 |
| `-gravity center -extent 540x960` | 重新扩展回原始尺寸，并保持居中          |
| `-blur 0x20`                      | 最后柔化边缘，让过渡自然             |
效果示意：
```
原版：radial-gradient:white-black
        white▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒black

缩放后：
        white██████████████████▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒black
```
如果你想进一步控制亮区大小：

| 想要效果 | 调整方法                                     |
| ---- | ---------------------------------------- |
| 白区更大 | 把 `-resize 70%x70%` 改成 `-resize 50%x50%` |
| 白区稍大 | 改成 `-resize 80%x80%`                     |
| 白区更小 | 改成 `-resize 90%x90%`                     |

##### 另一种（更可靠/可控）的办法：生成更大渐变再裁剪中心
有时更直观的做法不是缩小再填充，而是先做一个**比画布大的渐变**（把黑推得远一点），然后**裁取中间区域**。例如：

`magick -size 2000x2000 radial-gradient:white-black \   -gravity center -crop 540x960+0+0 +repage \   -blur 0x20 \   vignette-540x960.png`
原理：在更大的渐变上取中心区域，中心自然更“白”，四周黑色被推远到裁剪区之外 → 得到更大白心与更细的黑边。优点是更可控且没有“背景填充”歧义。

##### 用径向渐变 + level 压缩黑色区域
```bash
magick -size 540x960 radial-gradient:white-black \
  -level 90%,100% \
  -blur 0x15 \
  vignette-thin.png
```

| 参数                            | 功能                                 |
| ----------------------------- | ---------------------------------- |
| `radial-gradient:white-black` | 生成从中心白色到边缘黑色的渐变                    |
| `-level 90%,100%`             | 压缩黑色区域，只保留最外围 10% 的黑色 → 中心 90% 完全白 |
| `-blur 0x15`                  | 模糊边缘，使黑色形成薄薄渐变                     |
| `vignette-thin.png`           | 输出图像                               |
✅ 效果：
- 中心纯白    
- 只有最外围薄薄黑色渐变    
- 可调节 `-level` 数值来控制白区大小（例如 95%,100% → 白区更大，黑色更薄）

### 中间镂空蒙版
一张基本纯白 图，正中心一小个圆形黑色模糊
```
magick -size 540x960 canvas:white -fill black -draw "circle 270,480 270,470" -blur 0x15 vignette-thin.png
```
1. **整个画布都是白色**    
    - 由 `-size 540x960 canvas:white` 创建。        
2. **正中心有一个小黑色圆圈**    
    - 由 `-fill black -draw "circle 270,480 270,470"` 生成        
    - 圆心在 `(270,480)`，圆半径 = `480-470 = 10px`        
3. **黑色圆圈被模糊扩散**    
    - `-blur 0x15` 让黑色边缘向外扩散，形成一圈薄薄渐变        
    - 中心白色仍然纯白，只有边缘黑色渐变

### -blur
ImageMagick 的模糊是基于 高斯模糊 (Gaussian Blur)。
```bash
-blur radiusxsigma
```
通常写法：`-blur 0x50`    
- `0` → 自动计算 radius 。让 ImageMagick 自动根据 sigma 选择合适的卷积半径      
- `50` → sigma 值，控制模糊强度。sigma 越大，模糊越厚

| 参数              | 说明                                            | 效果                                                                   |
| --------------- | --------------------------------------------- | -------------------------------------------------------------------- |
| radius          | 模糊卷积核半径（多少像素范围参与模糊）<br>radius = 0 -> 自动计算卷积半径 | 保持效率，同时模糊平滑                                                          |
| sigma           | 高斯函数的标准差，控制模糊强度                               |                                                                      |
| sigma 小（如 0x2）  | 模糊范围小                                         | 边缘过渡非常锐利，几像素渐变                                                       |
| sigma 中（如 0x15） | 中等模糊                                          | 边缘渐变明显，但不是太厚;<br>渐变区域相对窄，白到黑过渡较明显<br>制作光晕、聚焦、局部模糊                    |
| sigma 大（如 0x50） | 模糊范围大                                         | 边缘扩散很厚，黑白渐变面积大<br>渐变非常平滑，中间白色范围更大，边缘过渡柔和<br>制作暗角、vignette、alpha mask |
## 矩形蒙版，外边缘模糊
先生成一张540x960的白色画布，然后在上面画一个500x860的白色矩形，接着把外面blur
中间纯白矩形区域，周围画布保留黑色（或者透明）模糊边缘，形成薄薄的暗角效果。
```
magick -size 540x960 canvas:white \
  -fill black -draw "rectangle 0,0 540,960" \
  -fill white -draw "rectangle 20,50 520,910" \
  -blur 0x15 \
  vignette-thin.png
```
- `-size 540x960 canvas:white` → 创建全白画布    
- `-fill black -draw "rectangle 0,0 540,960"` → 整个画布先涂黑    
- `-fill white -draw "rectangle 20,50 520,910"` → 中间绘制白色矩形    
    - 宽度 = 500 px (`520-20`)        
    - 高度 = 860 px (`910-50`)        
    - 位置居中，边缘留黑色或模糊空间        
- `-blur 0x15` → 模糊黑色边缘，形成薄薄渐变    
- 输出 `vignette-thin.png`
调整

|目标|修改方法|
|---|---|
|白色矩形更大|改变 `rectangle` 坐标，比如 `rectangle 10,20 530,940`|
|渐变更薄|减小 `-blur`，例如 `-blur 0x8`|
|渐变更厚|增大 `-blur`，例如 `-blur 0x25`|
```
magick -size 540x960 xc:black -fill white -draw "rectangle 20,50 520,910" vignette-sharp.png
magick -size 540x960 xc:black -fill white -draw "rectangle 20,50 520,910" -blur 0x2 vignette-sharp.png
```
### 圆角矩形蒙版
```
magick -size 540x960 xc:black \
  -fill white -draw "roundrectangle 20,100 520,860 100,100" \
  longpill.png
```

| 参数                                 | 功能                                                                                                              |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| `roundrectangle x1,y1 x2,y2 rx,ry` | 绘制圆角矩形：左上角 `(x1,y1)`，右下角 `(x2,y2)`，<br>圆角半径 `rx,ry`. `rx,ry` 越大，圆角越圆。<br>当 rx/ry = 高度的一半 → 就变成了 长条圆形 / capsule。 |
| `-fill white`                      | 内部填充白色                                                                                                          |
| `xc:black`                         | 背景黑色                                                                                                            |
| 输出 `longpill.png`                  | 图像文件                                                                                                            |
### 椭圆蒙版，推荐
比 roundrectangle 更灵活，适合完全圆的两端。
```
magick -size <宽>x<高> xc:<背景色> -fill <填充色> -draw "ellipse cx,cy rx,ry 0,360" output.png
```
椭圆的单位是像素
-fill 控制填充颜色，-stroke 控制边框颜色
可以配合 -blur 做柔边效果
用椭圆可以直接生成 长条圆形 capsule，替代 roundrectangle

|参数|说明|
|---|---|
|`cx,cy`|椭圆中心坐标|
|`rx`|水平半径（横向拉伸）|
|`ry`|垂直半径（纵向拉伸）|
|`0,360`|椭圆开始和结束角度（0~360 表示完整椭圆）|
|`-fill`|填充颜色|
|`xc:<颜色>`|画布背景颜色|
例子：居中椭圆
```
magick -size 540x960 xc:black -fill white -draw "ellipse 270,480 200,400 0,360" ellipse.png
```
- 画布 540×960 黑色背景    
- 椭圆中心 `(270,480)`（画布中心）    
- 水平半径 200 → 宽 400 px    
- 垂直半径 400 → 高 800 px    
- 0~360 → 完整椭圆    
- 填充白色  
✅ 结果：竖直长条椭圆（长条圆形 capsule 的效果）

例子 横向椭圆
```
magick -size 540x960 xc:black -fill white -draw "ellipse 270,480 400,200 0,360" ellipse_horizontal.png
```
- 水平半径 400 → 横向拉伸    
- 垂直半径 200 → 高度缩小    
- 结果：水平长条椭圆

例子 ：部分椭圆（弧形）如果不想完整 360°，可以画弧：
```
-draw "ellipse 270,480 200,400 0,180"
```
- 0~180 → 只画上半弧    
- 180~360 → 只画下半弧

例子
```
magick -size 540x960 xc:black \
  -fill white -draw "ellipse 270,480 250,430 0,360" \
  longpill.png
  
magick -size 540x960 canvas:black -fill white -draw "ellipse 270,480 200,430 0,360" -blur 0x50 vignette-540x960.png

magick -size 720x1280 xc:black -fill white -draw "ellipse 360,640 270,540 0,360" -blur 0x50 vignette-720x1280.png

magick -size 720x1280 xc:black -fill white -draw "ellipse 360,640 250,520 0,360" -blur 0x60 vignette-720x1280.png
```

| 参数                          | 功能                                                                                 |
| --------------------------- | ---------------------------------------------------------------------------------- |
| `ellipse cx,cy rx,ry 0,360` | 绘制椭圆，中心 `(cx,cy)`，<br>水平半径 `rx`，垂直半径 `ry`，角度 0~360<br>可以拉伸 X 或 Y 半径，生成 水平或垂直的长条圆形。 |
| `-fill white`               | 内部填充白色                                                                             |

vignette, ok
```
magick -size 540x960 canvas:black -fill white -draw "ellipse 270,480 200,430 0,360" -blur 0x30 vignette-540x960.png
```
解释：
- 画布 540x960    
- 椭圆中心 `(270,480)` → 居中    
- 水平半径 230 → 宽度 200x2=400    
- 垂直半径 430 → 高度 430x2=860   
- `0,360` → 完整椭圆    
- `-blur 0x30` → 生成模糊暗角    

✅ 效果：
- 中心椭圆白色    
- 周围黑色模糊    
- 椭圆居中，大小适合 540x960 画布
## 生成白色圆角frame
使用 ImageMagick 创建 1020x1020 的白色圆角边框图像，中心透明，四角透明圆角向外凸出.
生成一张 1020×1020 的图片，画一个 外层白色圆角矩形，再在内侧画一个 圆角矩形（无填充），最后输出文件 white-frame-rounded-1020x1020x40.png。通常目的是得到一个“圆角白色边框 / 框架”效果（内侧留空）。

before 7.x
```bash
magick convert -size 1019x1019 canvas:none -fill white -draw "roundrectangle 0,0,1020,1020,40,40" -fill none -draw "roundrectangle 40,40,980,980,40,40" white-frame-rounded-1020x1020x40.png
```
canvas:none：创建全透明背景
第一个 roundrectangle：绘制外圆角白框（0-1019 - 1020x1020，半径 40）
第二个 roundrectangle：挖掉中间的圆角透明区域（从边缘内缩 40）
最终形成一个带透明圆角的白框

after 7.x
```bash
magick -size 1020x1020 canvas:none -fill white -draw "roundrectangle 0,0,1019,1019,40,40" -fill none -draw "roundrectangle 40,40,980,980,40,40" white-frame-rounded-1020x1020x40.png
```
分行解释如下：
- `magick`  
    ImageMagick 的 7.x+ 主命令入口。    
- `-size 1020x1020`  
    指定要创建/处理的画布大小为 **宽 1020 × 高 1020 像素**。    
    > 注意：像素坐标通常从 0 开始。    
- `canvas:none`  
    使用名为 `canvas` 的伪图像源并把背景设为 `none`（透明）。也可以写成 `xc:none`，作用相同：创建一个透明背景的新图像。    
- `-fill white`  
    设置后续绘制形状的**填充颜色**为白色（即绘制一个白色的 filled 形状）。    
- `-draw "roundrectangle 0,0,1020,1020,40,40"`  
    `-draw` 接受一串绘图指令（可用多条）。`roundrectangle x1,y1,x2,y2 rx,ry` 的含义：    
    - `(x1,y1)`：左上角坐标        
    - `(x2,y2)`：右下角坐标        
    - `rx,ry`：圆角的横/纵半径（控制圆角大小）  
        上面这条命令在画布上画了一个**填充的白色圆角矩形**，覆盖（或接近覆盖）整张画布；圆角半径为 40px。          
    > 注：命令中用逗号或空格都可（ImageMagick 对 `-draw` 的坐标分隔比较宽松）。    
- `-fill none`  
    将后续绘制的**填充**设为 `none`（透明），表示不填充后面的形状。**注意**：`-fill none` 只是设置“填充为空”，并不会自动把先前已经填的像素“挖掉”。    
- `-draw "roundrectangle 40,40,980,980,40,40"`  
    在内侧绘制另一个圆角矩形，其左上在 (40,40)，右下在 (980,980)，圆角同样为 40px。因为当前 `-fill` 是 `none`，这条 `draw` 要么只绘制轮廓（outline），要么什么也不画（取决于是否设置了 `-stroke`）。**它不会把外层白色的内部填充区域自动变为透明**（不会“镂空”已填充的像素）。    
- `white-frame-rounded-1020x1020x40.png`  
    输出文件名（PNG，支持透明）。
# 编程接口
ImageMagick 提供了 **MagickWand API** 和 **Magick++ API**，同时社区也封装了很多语言接口，比如：
- Python: [Wand](https://docs.wand-py.org/en/0.6.12/)    
- PHP: `Imagick` 扩展    
- Java: JMagick    
- Node.js: gm (GraphicsMagick 兼容库，但也能调用 ImageMagick)

# others
magick -size 1080x1920 canvas:black -fill white -draw "ellipse 540,960 400,860 0,360" -blur 0x50 vignette-thin.png
magick -size 540x960 canvas:black -fill white -draw "roundrectangle 80,100 460,860, 40,40" -blur 0x50 vignette-thin.png



magick -size 540x960 canvas:black -fill white -draw "ellipse 270,480 200,430 0,360" -blur 0x30 vignette-540x960.png
magick -size 540x960 canvas:black -fill white -draw "ellipse 270,480 200,430 0,360" -blur 0x30 vignette-thin.png

magick -size 540x960 canvas:black -fill white -draw "ellipse 540,960 400,860 0,360" -blur 0x50 vignette-thin.png
magick -size 540x960 canvas:black -fill white -draw "ellipse 270,480 200,430 0,360" -blur 0x50 vignette-thin.png
magick -size 540x960 canvas:black -fill white -draw "ellipse 270,480 200,430 0,360" -blur 0x100 vignette-thin.png
magick -size 540x960 canvas:black -fill white -draw "ellipse 270,480 250,430 0,360" -blur 0x100 vignette-thin.png
magick -size 540x960 canvas:black -fill white -draw "ellipse 270,480 250,430 0,360" -blur 0x50 vignette-thin.png


















magick -size 540x960 gradient:white-black -blur 0x50 mask-gradient.png

magick -size 540x960 canvas:white full_black.png
magick -size 540x480 gradient:white-black bottom_gradient.png
magick full_black.png bottom_gradient.png -geometry +0+480 -composite mask_half.png



magick -size 540x960 radial-gradient:white-black -blur 0x50 -gravity center -crop 540x960+0+0 +repage vignette-540x960.png
magick -size 540x960 radial-gradient:white-black -blur 0x10 -gravity center -crop 540x960+0+0 +repage vignette-540x960.png
magick -size 540x960 -function polynomial 0,1,0.5 radial-gradient:none-black -blur 0x30 vignette-thin.png

magick -size 540x960 radial-gradient:white-black -resize 70%x70% -gravity center -extent 540x960 -blur 0x50 vignette-540x960.png
magick -size 540x960 radial-gradient:white-black -resize 150%x150% -background black -gravity center -extent 540x960 -blur 0x50x40 vignette-540x960.png

magick -size 540x960 radial-gradient:white-black -resize 150%x150% -gravity center -extent 540x960 -blur 0x15 vignette-thin.png
magick -size 540x960 radial-gradient:white-black -fill white -draw "rectangle 20,50 500,860" -resize 70%x70% -gravity center -extent 500x860 -blur 0x5x50 vignette-thin.png
magick -size 540x960 radial-gradient:white-black -fill white -draw "rectangle 20,50 500,860" -resize 70%x70% -gravity center -extent 500x860 -blur 0x40x55 vignette-thin.png


magick -size 540x960 xc:black -fill white -draw "roundrectangle 40,100 500,860 40,40" base.png 
magick base.png -blur 0x50 -level 0%,30% vignette-thin.png





 

ffmpeg  -y -i "Broken Angel (feat. Helena)-Arash&Helena Noguerra.mp3" -ss 00:00:37.300 -to 00:00:54.700 -acodec libmp3lame -b:a 128k -vn "Broken Angel (feat. Helena)-Arash&Helena Noguerra-17s.mp3"

ffmpeg  -y -i "More Than I Can Say-Leo Sayer.mp3" -ss 00:00:20.000 -to 00:00:37.500 -acodec libmp3lame -b:a 128k -vn "More Than I Can Say-Leo Sayer-16s.mp3"


# FAQ
