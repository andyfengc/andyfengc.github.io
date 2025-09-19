# PySceneDetect
PySceneDetect 是一个 **开源的自动场景检测工具**（用 Python 写的），能根据 **画面内容的变化** 自动找到视频中的镜头切换点。    
- 主要功能：    
    - **检测场景变化**（基于视频帧差异）        
    - **导出场景列表**（CSV/JSON/时间码）        
    - **自动切割视频**（需要配合 ffmpeg）        
- 适用场景：电影镜头分割、广告切分、访谈分段、纪录片分镜。
能够检测视频中的快速转场（cut）和淡入淡出（fade）场景。基于内容差异或亮度阈值进行分割。
GitHub
支持“content-aware”（内容感知）、“adaptive”（自适应）和“threshold”（阈值）检测方式，适配不同类型的视频内容。

这条指令会根据检测结果，将每个镜头拆分成单独的视频片段。
scenedetect -i video.mp4 split-video

支持只保存关键帧图像（`save-images`）、略过前几秒（`time -s 10s`）等功能

**进阶参数调节**  
可通过 `--high-quality`、`--preset`、`--crf` 控制输出质量，或选择 `--copy`/`--mkvmerge` 保持无损但可能牺牲帧级精度[scenedetect.com](https://www.scenedetect.com/cli/?utm_source=chatgpt.com)。同时也可以生成统计数据再调参数，更灵活准确。[scenedetect.com](https://www.scenedetect.com/cli/)
# Install
```python
pip install scenedetect[opencv]
```
（[opencv] 会额外安装 opencv-python 包。可以加速检测，不加也能用。）
安装 ffmpeg（若需要导出切割视频）：
下载 [ffmpeg](https://ffmpeg.org/download.html)解压后，把 bin 路径加入到 Windows 环境变量 PATH。
在命令行里输入 ffmpeg -version 确认安装成功。
# Use
PySceneDetect 提供三种常用检测方法：
输出：可以导出场景时间码（CSV/JSON），或直接切分视频（需要配合 ffmpeg）。
## Content Detector（内容检测，根据画面变化。最常用）  
算法：比较连续帧的像素差异。      
场景切换效果最自然，推荐。

列出场景切换点
```python
scenedetect --input input.mp4 detect-content list-scenes
```
输出 CSV 文件，包含场景编号、起止时间。
![](images/posts/20250909.jpeg)
生成ql-1-Scenes.csv
![](images/posts/20250909-1.jpeg)
你可以用这些时间点去切视频、做字幕、做片头片尾等。

导出时间码
```bash
scenedetect --input input.mp4 detect-content list-scenes --output scene_list.csv
```
`scene_list.csv` 里包含场景的 **起止时间戳**，可用于二次处理。
### 检测阈值
PySceneDetect 的 ContentDetector（内容检测器）主要原理：
它对视频的连续帧计算 帧间差异（通常是亮度、颜色差异）。
阈值（threshold）：判断场景变化的敏感程度。
数值越低 → 对小变化敏感 → 会检测出很多短场景
数值越高 → 只对明显变化敏感 → 场景检测较少

调整灵敏度
默认检测阈值比较稳健，如果想更敏感（检测更多场景）：
```bash
scenedetect --input input.mp4 detect-content --threshold 20 list-scenes
```
这里 threshold=20 表示帧差异超过 20 就判定为场景切换。

如果想更严格（只检测明显切换）：
```bash
scenedetect --input input.mp4 detect-content --threshold 40 list-scenes
```
默认值一般是 30.0，你可以根据视频类型调整：
电影、广告：30~40
静态或慢动作视频：20~30
高速运动、闪烁画面：可能需要更高阈值 50+

## Threshold Detector（阈值检测）根据亮度变化
    。
    - 算法：画面亮度达到阈值时，判断为新场景。
        
    - 适合光暗变化明显的场景（如闪光灯、黑屏切换）。
        

## **Adaptive Detector（自适应检测，实验性）**
    
    - 自动根据视频特性调整参数。
        
    - 在某些特殊视频上比 Content 更好。

# 切分
## PySceneDetect自己就能切割视频，边检测边切
```bash
scenedetect --input input.mp4 detect-content split-video
```
会调用 ffmpeg，把原视频按场景切分成多个小文件。
## 自动批量切割（根据 CSV）

你可以写一个 **批处理脚本 (Windows .bat)**, powershell 或 **Python 脚本**，自动读取 CSV 并调用 ffmpeg。

### 先 `list-scenes` 得到 `scene_list.csv`
如果已经有 CSV，可以用 ffmpeg 自己切分（更灵活）
有了 scene_list.csv，你能精确控制切割方式。
假设你的视频 `input.mp4` 有几个场景切换，PySceneDetect 生成的 CSV 可能长这样：

|Scene|Start Time|End Time|Length|
|---|---|---|---|
|1|00:00:00.000|00:00:12.345|12.3s|
|2|00:00:12.345|00:00:28.910|16.6s|
|3|00:00:28.910|00:01:05.000|36.0s|
### ffmpeg 切割基本原理
假设你要切第 2 段（12.345s → 28.910s）：
```bash
ffmpeg -i input.mp4 -ss 00:00:12.345 -to 00:00:28.910 -c copy scene_002.mp4
```
说明：
- `-ss` → 起始时间    
- `-to` → 结束时间    
- `-c copy` → 不重新编码（快，不损失画质）
- 
比如 Python 示例（假设 CSV 列名是 `Start Time` 和 `End Time`）：
```python
import csv
import subprocess

input_file = "input.mp4"
with open("scene_list.csv", newline='') as csvfile:
    reader = csv.DictReader(csvfile)
    for i, row in enumerate(reader, start=1):
        start = row['Start Time']
        end = row['End Time']
        output_file = f"scene_{i:03d}.mp4"
        cmd = [
            "ffmpeg", "-i", input_file,
            "-ss", start, "-to", end,
            "-c", "copy", output_file
        ]
        print(" ".join(cmd))
        subprocess.run(cmd)
```
运行后会自动生成 
```bash
scene_001.mp4
scene_002.mp4
scene_003.mp4
...
```
