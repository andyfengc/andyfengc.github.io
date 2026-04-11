# Introduction
| **工具名称**                | **离线/云端** | **分离准确度** | **CPU 运行效率** | **批处理能力** | **操作难度** | **综合评分** |
| ----------------------- | --------- | --------- | ------------ | --------- | -------- | -------- |
| **UVR5 (推荐)**           | **完全离线**  | ⭐⭐⭐⭐⭐     | ⭐⭐⭐          | ✅ 原生支持    | 适中       | **9.5**  |
| **SpleeterGUI**         | 完全离线      | ⭐⭐⭐       | ⭐⭐⭐⭐⭐        | ✅ 支持      | 极简       | **7.5**  |
| **Audio-Separator**     | 命令行       | ⭐⭐⭐⭐⭐     | ⭐⭐⭐⭐         | ✅ 极强      | 较高       | **8.5**  |
| **Audacity + OpenVINO** | 完全离线      | ⭐⭐⭐⭐      | ⭐⭐⭐          | ❌ 较弱      | 适中       | **7.0**  |
| **VocalRemover.org**    | 云端        | ⭐⭐⭐⭐      | N/A (云端)     | ❌ 需逐个上传   | 极简       | **6.0**  |
# demucs，windows下测试失败
使用开源神经网络模型 **Demucs**, 把一首**原版人声歌曲**分离成两个独立的 MP3 文件：
- 🎤 **人声（vocals）*
- 🎶 **背景音乐（accompaniment / instrumental）**
分离后的音轨会输出：
```
separated/
├── vocals.mp3
└── accompaniment.mp3
```
先安装必要库：
```
pip install demucs
pip install torchcodec


optional:
pip install demucs[extra] pydub torch 
pip install soundfile
```
- `torch` 是 Demucs 的依赖（自动挑 CPU 或 GPU 版本）    
- `pydub` 用于把 wav 转 mp3    
- Windows 需要安装 **ffmpeg** 且加入系统 PATH
查看版本
```
pip show demucs
```
使用
```
demucs song.mp3
```


如果需要更干净的伴奏，可以尝试模型 htdemucs_ft(微调版本)
```
pip install demucs-infer[mp3]
```
然后替换
```
from demucs_infer.pretrained import get_model
from demucs_infer.apply import apply_model
```
# Speeter，测试失败
```
pip install spleeter
```
安装gpu版本（可选）
如果你有nvidia gpu并且安装了cuda
```
pip install spleeter-gpu
```
检查
```
spleeter -help
```
# audio-separator
install
```
pip install "audio-separator[gpu]"  # 如果有 NVIDIA 显卡
# 或者
pip install audio-separator          # 仅限 CPU
```
或
```
# 1. 创建并进入虚拟环境（推荐，防止污染全局环境）
python -m venv uvr_cpu_env
# Windows 激活环境:
uvr_cpu_env\Scripts\activate
# Mac/Linux 激活环境:
source uvr_cpu_env/bin/activate

# 2. 安装 audio-separator 基础版
pip install audio-separator

# 3. 安装 CPU 推理引擎 (ONNX Runtime)
# 这是 UVR5 运行 MDX-Net 模型必须的“引擎”
pip install onnxruntime
```
**用法示例**：
```
audio-separator input.wav --model_filename MDX_UVR_VOC_FT.onnx --output_dir ./output
```
分离人声和背景音乐
```
audio-separator input.wav --model_filename UVR-MDX-NET-Voc_FT.onnx

audio-separator "我的歌.mp3" \
    --model_filename UVR-MDX-NET-Voc_FT.onnx \
    --output_dir "./result" \
    --output_format WAV \
    --denoise True
```
**执行逻辑：**
1. 程序会自动从云端下载指定的模型文件（只需下载一次）。    
2. 在 CPU 上进行推理（速度取决于你的 CPU 核心数和主频）。    
3. 默认会在当前目录下生成两个文件：`Instrumental.wav` 和 `Vocals.wav`。
为了达到和 UVR5 软件里一样的效果，你可以调整以下参数：

| **参数**             | **说明**           | **建议值**                          |
| ------------------ | ---------------- | -------------------------------- |
| `--model_filename` | 指定模型（UVR5 核心模型名） | `UVR-MDX-NET-Voc_FT.onnx` (效果最好) |
| `--output_dir`     | 指定输出文件夹          | `./output`                       |
| `--output_format`  | 输出格式             | `wav`, `mp3`, `flac`             |
| `--denoise`        | 是否开启去噪           | `True`                           |
在 CPU 上运行时，模型的大小决定了处理速度。以下是 UVR5 中最受欢迎且 `audio-separator` 支持的模型：
- **MDX-Net 架构 (首选)**:    
    - `UVR-MDX-NET-Voc_FT.onnx` (目前公认的人声提取最强模型)        
    - `UVR-MDX-NET-Inst_HQ_4.onnx` (侧重提取高质量伴奏)        
- **VR Architecture (较快)**:    
    - `UVR-BVE-4_B_2.onnx` (适合配置较低的 CPU)
### 常见问题排查 (CPU 专用)
- **速度太慢？** CPU 处理 5 分钟的音频通常需要 3-8 分钟，这取决于你的 CPU 性能。如果想提速，可以尝试减小 `window_size` 参数（默认为 512，设为 320 会快一些但质量会下降）。    
- **内存报错？** 如果音频文件非常大（如 1 小时），CPU 内存可能会溢出。建议先将长音频切分成 10 分钟一段再处理。    
- **模型下载失败？** 由于模型文件在 GitHub 或 HuggingFace 上，如果下载不动，建议开启网络代理或手动下载模型文件放入程序的 `models` 文件夹。
相比 CPU 版本，**GPU 版本（基于 NVIDIA CUDA）的处理速度通常有 10 倍到 50 倍的质变提升。**
在 UVR5 或 `audio-separator` 中，音频处理属于典型的“计算密集型”任务，显卡并行计算的优势非常明显。
以处理一段 **5 分钟**的标准采样率歌曲为例（使用 MDX-Net 架构模型）：

| **硬件配置**                        | **处理时长 (预估)** | **速度倍率**      |
| ------------------------------- | ------------- | ------------- |
| **普通办公 CPU** (如 i5, i7 低压版)     | 8 ~ 12 分钟     | 0.5x (慢于实时)   |
| **高性能 CPU** (如 i9, Ryzen 9)     | 3 ~ 5 分钟      | 1x (接近实时)     |
| **入门级 GPU** (如 RTX 3050 Laptop) | 40 ~ 60 秒     | 5x ~ 7x       |
| **中高端 GPU** (如 RTX 3080 / 4070) | **15 ~ 25 秒** | **15x ~ 20x** |
| **顶级显卡** (如 RTX 4090)           | 10 秒以内        | 30x+          |