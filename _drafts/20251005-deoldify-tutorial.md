# Introduction
基于 **PyTorch 的深度学习模型**，可以自动把 **黑白照片或视频上色**，常用于历史影片修复、老照片复原等项目。
功能：
- 图片上色（最高质量）    
- 视频上色（带时间一致性）    
- Jupyter Notebook 可视化操作
输出效果：自然、柔和、带一点复古色调
## PyTorch
PyTorch 是一个 深度学习框架，它主要做两件事：

训练神经网络（比如 AI 上色模型）
推理/预测（用训练好的模型对新视频上色）

DeOldify 本质上就是一个基于 PyTorch 的深度学习项目：它里面有训练好的模型权重（.pth 文件），用 PyTorch 来加载，然后处理视频。
# Install
| 要素     | 推荐配置                          |
| ------ | ----------------------------- |
| 操作系统   | Windows 10/11, macOS, 或 Linux |
| Python | 3.8 或 3.9                     |
| GPU    | NVIDIA GPU（显存≥4GB）            |
| CUDA   | 11.x 版本（匹配 PyTorch）           |
| 磁盘空间   | 至少 10 GB                      |
快捷命令汇总（懒人版）
```bash
# 克隆项目
git clone https://github.com/jantic/DeOldify.git
cd DeOldify


# 启动上色
python - <<'EOF'
from deoldify.visualize import *
colorizer = get_video_colorizer()
colorizer.colorize_from_file_name('bw_video.mp4', render_factor=21, watermarked=False)
EOF
```
![](images/posts/20251005-6.jpeg)
## 克隆 DeOldify 仓库
```bash
git clone https://github.com/jantic/DeOldify.git
cd DeOldify
```
![](images/posts/20251005-3.jpeg)
## 下载预训练模型（权重文件）
DeOldify 需要加载两个模型之一：
- `ColorizeArtistic_gen.pth`（艺术风格，色彩鲜艳）    
- `ColorizeVideo_gen.pth`（视频专用，平滑自然）   
这两个文件下载地址（官方GitHub上也有）：
- https://data.deepai.org/deoldify/ColorizeArtistic_gen.pth    
- https://data.deepai.org/deoldify/ColorizeVideo_gen.pth
下载后放入：
```bash
DeOldify/models/
```
先确认你的文件夹结构像这样：
```css
DeOldify 目录/
├── models/
│   ├── ColorizeArtistic_gen.pth
│   └── ColorizeVideo_gen.pth
├── video/
│   ├── bw_video.mp4          ← 你的黑白视频放这里
│   └── result/               ← 上色后的视频会出现在这里
├── deoldify/
├── requirements.txt
├── ...
```
- 模型文件放在 `models` 文件夹    
- 你的视频文件放在 `video` 文件夹    
- 输出文件会自动存进 `video/result/`
## 创建虚拟环境（推荐）
进入Deoldify主目录
```
# 创建虚拟环境
python -m venv deoldify_env
```
> 这里创建了一个名为 `deoldify_env` 的虚拟环境，放在 DeOldify 文件夹下，不会影响全局 Python。
激活(进入)虚拟环境：
```
# Windows:
deoldify_env\Scripts\activate
# Linux/macOS:
source deoldify_env/bin/activate
# 退出虚拟环境
deactivate
```
激活后，你会看到命令行提示符前有 `(deoldify_env)`。
![](images/posts/20251005-1.jpeg)
## 安装依赖
注意因为deoldify兼容的pytorch版本问题，这里降级torch版本到2.1
```bash
pip install torch==2.1.0+cu121 torchvision==0.16.0+cu121 --extra-index-url https://download.pytorch.org/whl/cu121 fastai==1.0.61 numpy==1.25.1 opencv-python==4.8.1.78 matplotlib==3.8.0 jupyter==1.0.0 pillow==10.1.0 scikit-image==0.21.0 scipy==1.11.0 tqdm==4.66.1 requests==2.32.5 yt-dlp==2024.12.13
```
![](images/posts/20251005-2.jpeg)
⚠️ 注意：PyTorch 安装时请根据你的 GPU/CPU 版本选择正确的 CUDA。  
可到 https://pytorch.org/get-started/locally/ 复制命令。
![](images/posts/20251005-4.jpeg)
![](images/posts/20251005-5.jpeg)

在Deoldify根目录，继续安装依赖
```
pip install -r requirements.txt
```

## 启动 Jupyter 2Notebook（推荐方式）
```
jupyter notebook
```
打开浏览器后，进入：
```
DeOldify/jupyter/ColorizeVideo.ipynb
DeOldify/jupyter/ColorizeArtistic.ipynb
```
# 使用
## 视频上色示例（最常用）
假设你的黑白视频是 `bw_video.mp4`：
在 Python 脚本或 Notebook 中输入：
```python
from deoldify import device
from deoldify.visualize import *

colorizer = get_video_colorizer()

colorizer.colorize_from_file_name(
    file_name='bw_video.mp4',
    render_factor=21,
    watermarked=False
)
```
参数说明：

|参数|说明|
|---|---|
|`file_name`|输入视频路径|
|`render_factor`|上色强度（12–40，越高颜色越浓）|
|`watermarked`|是否添加水印|
|输出|在 `DeOldify/video/result/` 目录生成彩色视频|
启动方式有两种（任选一种）
### 用 Jupyter Notebook（推荐新手）

在命令行进入 DeOldify 目录
```bash
cd DeOldify
jupyter notebook
```
打开浏览器自动跳出页面（例如：http://localhost:8888/tree）
进入文件：
```bash
DeOldify/jupyter/ColorizeVideo.ipynb
```
在页面中逐格执行（Shift + Enter）
它会先导入库
然后加载模型
最后输入视频路径并上色
### 方法 2：直接用 Python 脚本（推荐想快速出结果的）
在 DeOldify 根目录新建一个文件：
run_deoldify_video.py
内容如下👇
```
from deoldify import device
from deoldify.visualize import *

# 创建上色器
colorizer = get_video_colorizer()

# 你的黑白视频路径（可以改成自己的）
# file_name = 'video/mom.mp4' #不支持相对路径
file_name = r"c:\Workspace\DeOldify\video\mom.mp4"

# 开始上色
colorizer.colorize_from_file_name(
    file_name=file_name,
    render_factor=21,     # 上色强度（12~40之间）
    watermarked=False,     # 是否加水印,
    post_process=True       # 对视频进行平滑处理,    
)

print("✅ 上色完成！请查看 video/result/ 文件夹。")
```
运行你的脚本：
```
python run_deoldify_video.py
```
![](images/posts/20251005-7.jpeg)
![](images/posts/20251005-8.jpeg)
## 图片上色示例
```python
from deoldify import device
from deoldify.visualize import *

colorizer = get_image_colorizer()

colorizer.plot_transformed_image(
    path='bw_photo.jpg',
    render_factor=35,
    figsize=(8,8),
    watermarked=False
)
```
输出会显示原图与上色图，可保存。
## 优化技巧
### 参数调优技巧

|参数|建议值|效果|
|---|---|---|
|`render_factor=21`|视频标准值|自然柔和|
|`render_factor=35`|图片推荐|色彩鲜明|
|`render_factor<15`|色彩偏灰|老电影风格|
|`render_factor>35`|容易偏色|实验性效果|
### 效果参考
|模式|输出风格|说明|
|---|---|---|
|Artistic 模式|鲜艳、现代感|适合人像、风景|
|Stable/Video 模式|柔和、连贯|适合历史影片|

# FAQ
## 常见问题

|问题|原因|解决办法|
|---|---|---|
|CUDA 错误|PyTorch/CUDA 版本不匹配|按 PyTorch 官网重装正确版本|
|视频上色太慢|CPU 模式运行|安装 NVIDIA 驱动 + CUDA|
|色彩怪异|render_factor 太高|调低到 20 左右|
|内存溢出|视频太长或太大|先用 ffmpeg 分段上色|
## 运行提示缺少lib包
ModuleNotFoundError: No module named 'matplotlib'
在命令行执行以下命令安装：
```
pip install matplotlib
```
如果你之前是在虚拟环境或 conda 环境中安装的 DeOldify，请先进入那个环境，再运行上面的命令

或者。DeOldify 用到不少依赖，可以一口气安装完整包，避免后面继续报错：
```bash
pip install -r requirements.txt
```

或者手动安装常见依赖：
```bash
pip install torch torchvision fastai pillow jupyter matplotlib tqdm opencv-python
```
## 上色后视频无音频
FFmpeg 配合
上色后视频通常无音频，需要合并：
```bash
ffmpeg -i colorized.mp4 -i bw_video.mp4 -c copy -map 0:v -map 1:a output_with_audio.mp4
```
## PyTorch版本运行出错
**卸载现有 PyTorch / torchvision**：
```
pip uninstall -y torch torchvision
```
**安装兼容版本**：
```
pip install torch==2.1.0 torchvision==0.16.0
```
确认其他依赖已经安装：
```
pip install fastai matplotlib opencv-python-headless yt-dlp ffmpeg-python pillow tqdm
```
## CUDA 驱动过旧
CUDA initialization: The NVIDIA driver on your system is too old (found version 11060)

**原因：**
- 你的显卡驱动太旧，无法兼容 PyTorch + CUDA 你安装的版本。
    - 即使 PyTorch 可以安装，如果驱动版本不够新，GPU 也无法使用。    

**解决方案：*
1. **升级 NVIDIA 驱动**
2. 
**或者改用 CPU 版本 PyTorch**（如果不打算升级 GPU）

- 安装 CPU 版本 PyTorch：
```
# 1. 卸载旧的 torch、torchvision、torchaudio
pip uninstall -y torch torchvision torchaudio
# 安装torch cpu最新版
# pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu
# 2. 安装 PyTorch 2.1.1 CPU 版本
pip install torch==2.1.1+cpu torchvision==0.16.1+cpu torchaudio==2.1.1+cpu --index-url https://download.pytorch.org/whl/cpu
```
修改requirements.txt
```
wandb
fastai==1.0.60
tensorboardX>=1.6
ffmpeg
ffmpeg-python
yt-dlp
jupyterlab
opencv-python>=4.2.0.32
Pillow==9.3.0
--extra-index-url https://download.pytorch.org/whl/cu113
ipywidgets
torch==2.1.1
torchvision==0.16.1
torchaudio==2.1.1```
## pip install -r requirements.txt报版本错误
ERROR: No matching distribution found for torch==1.11.0
- 你的 Python 版本可能过新，或者官方 PyPI 没有提供该版本的轮子。    
- 例如 PyTorch 1.11 对 Python 3.11 不支持，而你可能在 Python 3.11 或 3.12。

升级 DeOldify requirements.txt 中的 torch 版本：
```
torch==2.1.0
torchvision==0.16.1
torchaudio==2.1.0
```
然后重新安装：
```
pip install torch==2.1.1 torchvision==0.16.1 torchaudio==2.1.1
pip install -r requirements.txt --no-deps
```
`--no-deps` 避免重新安装旧版本的 torch。
## 视频已经成功上色了，但出现了 **背景颜色闪烁 / 不稳定**
这是 **DeOldify 视频上色的常见问题**，尤其在处理低光、平面背景或黑白电影老片时。原因和解决办法如下：
## **原因分析**

1. **帧之间独立上色**    
    - 默认 DeOldify 对每一帧进行上色，没有严格的时间连续性约束        
    - 导致相邻帧的颜色可能轻微波动 → 视觉上就会闪        
2. **`render_factor` 过高或过低**    
    - render_factor 影响上色强度        
    - 太高 → 高饱和度，可能背景跳色        
    - 太低 → 上色弱，容易出现闪烁感
        
3. **视频噪点 / 黑白质量差**    
    - 黑白老片、噪点多，算法对背景误判 → 颜色漂移
        
4. **缺少视频稳定选项**    
    - DeOldify 有 `post_process`、`video_filter` 等参数，但默认不做背景颜色平滑

方法 1：使用 render_factor + post_process=True
```python
colorizer.colorize_from_file_name(
    file_name=r"C:\Workspace\DeOldify\video\mom.mp4",
    render_factor=21,       # 可尝试 16~25，过高容易跳色
    watermarked=False,
    post_process=True       # 对视频进行平滑处理
)
```
- `post_process=True` 会对相邻帧颜色进行平滑，明显减少闪烁    
- 如果还闪，可以尝试 **降低 render_factor**（例如 18 或 16）
方法 2：对每帧后处理平滑
如果仍然不稳定，可以在视频上色完成后用 OpenCV 或 ffmpeg 做简单帧平滑：
提取视频帧
对每帧做 颜色均值或高斯平滑
再合成视频
示例（Python + OpenCV）：
```python
import cv2
import numpy as np

cap = cv2.VideoCapture("result_video.mp4")
fourcc = cv2.VideoWriter_fourcc(*'mp4v')
out = cv2.VideoWriter("smoothed_video.mp4", fourcc, cap.get(cv2.CAP_PROP_FPS), 
                      (int(cap.get(3)), int(cap.get(4))))

ret, prev_frame = cap.read()
while ret:
    ret, frame = cap.read()
    if not ret:
        break
    # 平滑：当前帧与前一帧加权平均
    smooth_frame = cv2.addWeighted(frame, 0.7, prev_frame, 0.3, 0)
    out.write(smooth_frame)
    prev_frame = smooth_frame

cap.release()
out.release()
```
> 这种方法可以明显减少闪烁，但会略微模糊画面

方法 3：尝试不同 DeOldify 模型
DeOldify 提供 Stable / Artistic / Video / HQ 模型
视频闪烁严重时，尝试：
```
colorizer = get_video_colorizer(model_type='video')
```
`video` 模型对连续帧颜色更稳定

方法 4：调节 frames_skip
默认 DeOldify 会对每一帧上色
你可以设置 frames_skip=1~2，跳帧上色再插值，有时也能减少闪烁
```
colorizer.colorize_from_file_name(
    file_name=file_name,
    render_factor=21,     # 上色强度（12~40之间）
    watermarked=False,     # 是否加水印,
    post_process=True,       # 对视频进行平滑处理,    
    frames_skip=2           # 每隔 2 帧上色一次
)
```