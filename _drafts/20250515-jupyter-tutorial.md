---
layout: post
title: Jupyter Tutorial
author: Andy Feng
---

# Jupyter
Jupyter Notebook https://jupyter.org/ 是一个开源的Web应用程序，允许用户创建和共享包含代码、方程式、可视化和文本的文档。它的用途包括：数据清理和转换、数值模拟、统计建模、数据可视化、机器学习等等。能让用户将说明文本、数学方程、代码和可视化内容全部组合到一个易于共享的文档中。它可以直接在代码旁写出叙述性文档，而不是另外编写单独的文档。也就是它可以能将代码、文档等这一切集中到一处，让用户一目了然。

Jupyter这个名字是它要服务的三种语言的缩写：Julia，PYThon和R，这个名字与“木星（jupiter）”谐音。Jupyter Notebook 已迅速成为数据分析，机器学习的必备工具。但是jupyter远远不止支持上面的三种语言，目前能够使用的语言他基本上都能支持，包括C、C++、C#，java、Go等等。

它具有以下优势：

- 可选择语言：支持超过40种编程语言，包括Python、R、Julia、Scala等。
- 分享笔记本：可以使用电子邮件、Dropbox、GitHub和Jupyter Notebook Viewer与他人共享。
- 交互式输出：代码可以生成丰富的交互式输出，包括HTML、图像、视频、LaTeX等等。
- 大数据整合：通过Python、R、Scala编程语言使用来探索同一份数据。

Jupyter可以在一个环境中，运行多种编程语言

Python是安装Jupyter Noterbook的必备条件，装好后的样子
![](images/posts/Pasted%20image%2020250515004810.png)
# Google Colab
Colaboratory 是一个 Google 研究项目，旨在帮助传播机器学习培训和研究成果。它是一个 [Jupyter 笔记本](https://zhida.zhihu.com/search?content_id=173944608&content_type=Article&match_order=1&q=Jupyter+%E7%AC%94%E8%AE%B0%E6%9C%AC&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NDc0NTgwNDcsInEiOiJKdXB5dGVyIOeslOiusOacrCIsInpoaWRhX3NvdXJjZSI6ImVudGl0eSIsImNvbnRlbnRfaWQiOjE3Mzk0NDYwOCwiY29udGVudF90eXBlIjoiQXJ0aWNsZSIsIm1hdGNoX29yZGVyIjoxLCJ6ZF90b2tlbiI6bnVsbH0.oHDcTjWS0-HAda5ejPX1WOOjJSReU7G36PNLWQLYXqQ&zhida_source=entity)环境，不需要进行任何设置就可以使用，并且完全在云端运行。

Colaboratory 笔记本存储在 Google 云端硬盘中，并且可以共享，就如同您使用 Google 文档或表格一样。Colaboratory 可免费使用。

Google Colab 分为免费版和pro版，免费版一次只可以使用一块GPU，且有时长限制(8小时)，pro版需要付费，可以使用多块GPU且没有时长限制，并且在容量上有一定扩展。如果是做inference，免费版的GPU就足够了。

在使用Colab时，建议使用Chrome浏览器。首先打开Google Drive。我们通过Colab跑代码时，code和dataset都可以放在Google Drive里直接调用。

打开Google drive > 右键 > colab
![](images/posts/Pasted%20image%2020250515012024.png)
edit > notebook settings > gpu
![](images/posts/Pasted%20image%2020250515012126%201.png)
运行命令测试gpu
```text
! /opt/bin/nvidia-smi 
```
# 安装一个应用MoneyPrinter Turbo
点开 https://colab.research.google.com/github/harry0703/MoneyPrinterTurbo/blob/main/docs/MoneyPrinterTurbo.ipynb
按步骤
## 1. Clone Repository and Install Dependencies
```
!git clone https://github.com/harry0703/MoneyPrinterTurbo.git
%cd MoneyPrinterTurbo
!pip install -q -r requirements.txt
!pip install pyngrok --quiet
```
## 2. Configure ngrok for Remote Access
We'll use ngrok to create a secure tunnel to expose our local Streamlit server to the internet.
first get your authentication token from the ngrok dashboard to use this service.
![](images/posts/Pasted%20image%2020250515012649.png)
enter command
```
from pyngrok import ngrok
# Terminate any existing ngrok tunnels
ngrok.kill()
# Set your authentication token
# Replace "your_ngrok_auth_token" with your actual token
ngrok.set_auth_token("your_ngrok_auth_token")
```
## 3. Launch Application and Generate Public URL
Now we'll start the Streamlit server and create an ngrok tunnel to make it accessible online:
```
import subprocess

import time

  

print("🚀 Starting MoneyPrinterTurbo...")

# Start Streamlit server on port 8501

streamlit_proc = subprocess.Popen([

    "streamlit", "run", "./webui/Main.py", "--server.port=8501"

])

  

# Wait for the server to initialize

time.sleep(5)

  

print("🌐 Creating ngrok tunnel to expose the MoneyPrinterTurbo...")

public_url = ngrok.connect(8501, bind_tls=True)

  

print("✅ Deployment complete! Access your MoneyPrinterTurbo at:")

print(public_url)
```


![](images/posts/Pasted%20image%2020250515013045.png)
申请Pexel API key
![](images/posts/Pasted%20image%2020250515013942.png)
# References
https://zhuanlan.zhihu.com/p/386162610