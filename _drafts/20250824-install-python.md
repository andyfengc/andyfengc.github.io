
![](images/posts/Pasted%20image%2020250824105307.png)
![](images/posts/Pasted%20image%2020250824105339.png)Python 3.7+ 默认自带 pip
可在命令行测试：
python --version
![](images/posts/Pasted%20image%2020250824111527.png)

pip --version
![](images/posts/Pasted%20image%2020250824110029.png)可选 GPU 支持
如果希望加速，可安装 PyTorch GPU 版本
官网：https://pytorch.org/get-started/locally/
手动把scripts目录加到path
C:\Users\Owner\AppData\Roaming\Python\Python311\Scripts
![](images/posts/Pasted%20image%2020250824110549.png)
visual studio code
![](images/posts/Pasted%20image%2020250824114808.png)
![](images/posts/Pasted%20image%2020250824115753.png)
# 安装rembg
先安装 onnxruntime
pip install onnxruntime
如果你的电脑有 NVIDIA GPU，并希望加速，可以安装 GPU 版本：
pip install onnxruntime-gpu

打开 **命令行（CMD 或 PowerShell）**，执行：
`pip install rembg[cli]`

安装完成后，重启电脑。可测试版本：
`rembg -v`
![](images/posts/Pasted%20image%2020250824112602.png)
- 输出类似 `rembg 2.x.x` 表示安装成功

如果报错ModuleNotFoundError: 直接强制重装 rembg 及其所有依赖：
pip install --upgrade --force-reinstall rembg[cli]
这个命令会自动装齐 `click、filetype、pillow、onnxruntime` 等所有 rembg 需要的依赖。


## 基本使用方法（命令行）
单张图片去背景
`rembg i input.png output.png`
- `i` = input/output 模式    
- 支持 PNG、JPEG 等格式    
- 输出为 **透明背景 PNG**
批量处理图片（测试失败，转向用python代码批处理运行）
`rembg i input_folder output_folder`
- 会处理 `input_folder` 下所有支持格式的图片    
- 自动保存到 `output_folder`
## 高级参数
- **指定模型**：    
`rembg -m u2net input.png output.png`

- **保持原图尺寸**： 
`rembg i --alpha-matting input.png output.png`

- **透明度混合**：
`rembg i --alpha-matting-foreground-threshold 240 input.png output.png`

> ⚠️ 渐变绿背景或者复杂背景可通过 `--alpha-matting` 提高边缘处理效果
5️⃣ 注意事项

透明背景：输出格式建议使用 PNG，保持 alpha 通道
渐变背景：可使用 --alpha-matting 参数，提高边缘精度
GPU 加速：安装 GPU 版 PyTorch，可大幅提升批量处理速度
视频不直接支持：rembg 本身是针对图片，但可用 逐帧处理视频 或结合 FFmpeg
# opencv
管理员cmd
pip install opencv-python
这会安装 基础 OpenCV 功能，可以处理图像、视频、绘图、滤镜等。
如果你需要 额外功能（如支持 ffmpeg、GUI 窗口显示），可以安装完整版：
pip install opencv-contrib-python

## 安装 NumPy（OpenCV 依赖）

一般 `opencv-python` 会自动安装，但如果报错缺少 NumPy，可以单独安装：

`pip install numpy`

验证安装
在命令行输入 Python：
python

然后测试导入：
import cv2
print(cv2.__version__)
![](images/posts/Pasted%20image%2020250824125239.png)