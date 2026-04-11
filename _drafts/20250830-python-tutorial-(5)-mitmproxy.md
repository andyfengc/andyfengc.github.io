# Introduction
`mitmproxy` 是一个开源的 **交互式 HTTPS 抓包/调试代理工具**，功能类似 Fiddler、Charles，但更偏向 **开发者和自动化场景**。
- **跨平台**：支持 Windows / macOS / Linux。    
- **免费开源**：不像 Charles、Fiddler Everywhere 那样有收费限制。    
- **支持 HTTP/1、HTTP/2、WebSocket**，HTTPS 也能解密。    
- **可交互操作**：带有一个类似控制台的 TUI（文本界面），可以实时拦截、修改、放行请求。    
- **强大脚本扩展**：支持用 **Python 脚本** 自定义请求/响应的处理逻辑，比如改参数、替换返回值、做自动测试。    
- **可作为库使用**：你甚至可以直接把 mitmproxy 当成 Python 模块，写自动化测试或数据采集脚本。
- **开源免费**：MIT 协议。
- **核心功能**：    
    - 抓取 HTTP/HTTPS 流量（支持 TLS 证书生成与安装）。        
    - 命令行 / Web / 图形界面 3 种交互方式。        
    - 可用 Python 脚本编写插件，对流量进行拦截、修改、分析。        
    - 可保存流量到文件 (`.mitm`)，方便离线分析。
## 应用场景
- 抓包分析 App/网页的 HTTPS 请求。    
- 调试 API 接口。    
- 流量劫持测试 / 安全测试。
- 自动化修改 HTTP 流量（广告过滤、接口 mock）。    
- 数据采集（配合 Python 插件）。
- 调试手机 APP 请求，分析 API。    
- 自动篡改响应数据做测试。    
- 写 Python 脚本模拟/修改流量，做自动化 API Mock。    
- 作为安全测试工具（检测流量是否被篡改、验证 API 加密等）。
## Install
way1:
```
pip install mitmproxy
```
![](images/posts/20250830-Fiddler-Tutorial.jpeg)

way2: 直接下载官方**Windows 安装包（免 pip）**
[https://mitmproxy.org/downloads/](https://mitmproxy.org/downloads/)
```
mitmproxy-windows-x64-installer.exe
```
安装后直接有：
- mitmproxy.exe    
- mitmdump.exe    
- mitmweb.exe    
不需要 Python，不需要 pip

安装成功验证
```
mitmweb --version
或
mitmproxy --version
```
# 使用
常用组件：
- `mitmproxy` —— 交互式控制台模式, 命令行 UI（类似 Wireshark 的 CLI 界面）。    
- `mitmdump` —— 无界面的版本，方便脚本和自动化, 类似 tcpdump，命令行输出流量。    
- `mitmweb` —— 带浏览器 Web 界面，浏览器查看与控制，比较适合习惯图形化的人。

## 证书安装（HTTPS 抓包必做）
当你第一次运行 `mitmproxy` 或 `mitmweb` 时，它会在用户目录下自动生成一套 CA 根证书：
- Windows: `C:\Windows\UserName\.mitmproxy\`    
- Linux/Mac: `~/.mitmproxy/`    
![](images/posts/20250830-Fiddler-Tutorial-5.jpeg)
主要文件：
- `mitmproxy-ca-cert.pem` （根证书，用来导入系统或浏览器）    
- `mitmproxy-ca.pem`    
- `mitmproxy-ca-cert.cer` （Windows/手机更常用）
### 在浏览器中安装证书
🔹 Chrome / Edge（基于系统证书）
1. 打开 **设置 → 隐私和安全 → 安全 → 管理证书**    
2. 切到 **受信任的根证书颁发机构**    
3. 点击 **导入**，选择 `mitmproxy-ca-cert.cer`    
4. 确认后重启浏览器即可    
chrome://settings/security > Manage certificates > 查看证书 > installed by you > import > 
![](images/posts/20250830-Fiddler-Tutorial-7.jpeg)
![](images/posts/20250830-Fiddler-Tutorial-8.jpeg)
![](images/posts/20250830-Fiddler-Tutorial-10.jpeg)
🔹 Firefox（独立证书库）
5. 打开 **设置 → 隐私与安全 → 证书 → 查看证书**    
6. 进入 **Authorities（证书颁发机构）**    
7. 点击 **导入**，选择 `mitmproxy-ca-cert.pem`    
8. 勾选 “信任此 CA 来标识网站” → 确认
### 安装系统证书:
 正常启动 mitmproxy
在命令行里运行：
`mitmproxy`
默认监听 `127.0.0.1:8080`。终端里会显示：
`Proxy server listening at http://*:8080`

让浏览器走 mitmproxy
你需要把浏览器的代理改成 mitmproxy：
- **Chrome/Edge/系统代理设置**：    
    1. 打开 Windows 设置 → 网络和 Internet → 代理。        
    2. 打开 “使用代理服务器”。        
    3. 地址填：`127.0.0.1`，端口：`8080`。        
    4. 保存。        
- 或者用 **Proxifier**，让浏览器走 127.0.0.1:8080。

![](images/posts/20250918.jpeg)
代理设置好后，再打开浏览器访问：
`http://mitm.it`
这时 mitmproxy 会拦截并返回证书安装页面。你会看到一个下载选项列表（Windows / Mac / Android / iOS）。
![](images/posts/20250918-1.jpeg)
![](images/posts/20250918-2.jpeg)
![](images/posts/20250918-3.jpeg)

1. 下载 **Windows 证书文件**（一般是 `mitmproxy-ca-cert.cer`）。    
2. 双击文件 → 安装证书。    
3. 选择：    
    - 存储位置：**本地计算机(local machine)**（如果提示需要管理员权限就确认）。        
    - 存储到：**受信任的根证书颁发机构**。        
4. 装好之后，可以重新启动一次浏览器。    

测试证书是否生效
- 打开一个 HTTPS 网站（比如 https://example.com），在 mitmproxy 界面里应该能看到完整解密后的请求和响应。    
- 如果还是 CONNECT 隧道没有解密，要检查证书是否真的安装到 “受信任的根证书颁发机构”。

⚠️ 注意：
- **只有流量经过 mitmproxy，才能安装证书**，所以一定要先改代理。    
- 如果只是直接访问 mitm.it，而没有设置代理，就会出现你看到的提示。
### 在手机中安装证书
🔹 Android
1. 访问 `http://mitm.it`（手机需走代理到 mitmproxy）
    
2. 下载 Android 提供的 `.cer` 文件    
3. 打开 **设置 → 安全 → 加密与凭据 → 安装证书**
    
4. 安装为 **VPN 和应用证书**
    
5. ⚠️ Android 7.0+ 默认**不再信任用户证书**，除非应用明确允许 → 如果要抓 HTTPS APP，需要 **root + 修改系统证书存储**，或用 Frida/VirtualApp 等绕过
    
🔹 iOS
6. 在 Safari 打开 `http://mitm.it`    
7. 下载证书（会提示“已下载描述文件”）    
8. 打开 **设置 → 通用 → 描述文件 → 安装**    
9. 安装完成后，还要到 **设置 → 通用 → 关于本机 → 证书信任设置**    
10. 勾选刚刚导入的 `mitmproxy` 根证书，启用“完全信任”

### 验证是否安装成功
1. 在目标设备/浏览器上访问 `https://example.com`    
2. mitmproxy 控制台应该能解密出 HTTPS 内容    
3. 如果提示 `certificate error`，说明证书未正确导入或未启用信任
![](images/posts/20250830-Fiddler-Tutorial-11.jpeg)
![](images/posts/20250830-Fiddler-Tutorial-9.jpeg)

4. 启动 mitmproxy 后，浏览器或手机配置代理为 **电脑IP:8080**。    
5. 在被代理的设备浏览器里访问：    
    `http://mitm.it`    
6. 选择对应系统下载并安装证书：    
    - Windows / macOS：双击证书，导入到系统“受信任的根证书颁发机构”。        
    - Android：下载后安装，或手动放到 `/system/etc/security/cacerts/`。        
    - iOS：设置 → 通用 → 描述文件与设备管理 → 安装证书 → 信任。
### 注意事项
- 安装 CA 证书相当于信任 mitmproxy，可解密所有 HTTPS，⚠️ 只在测试环境使用！
1. 证书安装仅限于 **测试环境**，不要随意在生产环境安装 CA。    
2. 部分应用（如银行 App）启用了 **证书固定（Certificate Pinning）**，需要额外工具绕过（Frida/Xposed）。    
3. 推荐用 **mitmweb**（`mitmweb -p 8080`），能图形化查看请求响应，更直观。
4. 长期使用建议 **单独设置虚拟机/测试机**，避免污染主环境。

## 启动 Web 界面（最常用）
```
mitmweb
```
![](images/posts/20250830-Fiddler-Tutorial-1.jpeg)
![](images/posts/20250830-Fiddler-Tutorial-2.jpeg)
Web 界面
- `Flows` 面板：显示所有 HTTP/HTTPS 请求。    
- 点击单个请求，可查看：    
    - **Request**（请求行、头、body）        
    - **Response**（状态码、响应头、body）        
    - **Details**（协议、TLS、客户端等信息）        
- 可以修改请求/响应，重放请求。
## 启动命令行界面
```
mitmproxy
```
![](images/posts/20250830-Fiddler-Tutorial-3.jpeg)
常用命令行
```bash
监听指定端口
mitmproxy -p 8888

指定上游代理
mitmproxy --mode upstream:http://proxy.example.com:3128

保存流量
mitmdump -w capture.mitm

读取已保存的流量
mitmproxy -r capture.mitm
```
## 后台抓包（输出日志）
```
mitmdump -w flows.mitm
```
(`-w` 保存流量文件，之后用 `mitmproxy -r flows.mitm` 回放）
![](images/posts/20250830-Fiddler-Tutorial-4.jpeg)
# Python 脚本扩展
mitmproxy 的扩展语言就是 **Python**，你想要的逻辑（规则、过滤、文件保存、图片格式转换）都可以写在脚本里，比 Fiddler/Charles 更灵活

写一个简单的插件 `modify.py`：
```python
from mitmproxy import http

def response(flow: http.HTTPFlow) -> None:
    if "example.com" in flow.request.pretty_url:
        flow.response.text = flow.response.text.replace("Example", "MITMProxy Demo")
```
运行时加载
```bash
mitmdump -s modify.py
```
拦截所有返回的 HTML，把里面的关键字替换掉
这样所有网页里 "Google" 就会被替换成 "MyProxy"。
```python
from mitmproxy import http

def response(flow: http.HTTPFlow):
    if flow.response and "text/html" in flow.response.headers.get("content-type", ""):
        text = flow.response.text
        text = text.replace("Google", "MyProxy")
        flow.response.text = text
```
运行时：
```bash
mitmdump -s script.py
```
## 针对不同网站预设规则

- 可以在 Python 脚本里维护一个 JSON/YAML 配置：
    
    `{   "youtube.com": { "types": ["webp","mp4"], "save_dir": "D:/Youtube/Download" },   "example.com": { "types": ["jpeg","png"], "save_dir": "D:/Example/Img" } }`
    
- 在 `response(flow)` 事件里根据 `flow.request.host` + `文件扩展名` 判断是否保存。
- 规则在 JSON 配置    
- 嗅探某个 host 下的 `.webp/.mp4/.jpg/.png`
## 自动转换图片格式

- 用 Python 的 **Pillow (PIL)**：
    
    `from PIL import Image import io  img = Image.open(io.BytesIO(flow.response.content)) img.convert("RGB").save("output.jpg", "JPEG")`
    
- 在保存之前判断 `Content-Type` 是否是 webp/tiff，自动转成 jpeg 存盘。
## 全自动后台运行

- 用 `mitmdump -s script.py` 启动，不需要 UI。
    
- 你的脚本会自动处理所有匹配到的流量，保存到硬盘，不需要手动干预。
    
- 热键/信号可随时控制是否启用。

## 自动保存到硬盘并命名

- `flow.response.content` 就是文件的二进制内容，直接 `open(path, "wb").write(content)` 就行。
    
- 命名规范你可以自定义，比如 `时间戳 + host + 路径hash + 后缀`。

## 快捷键开启/关闭嗅探

- 你可以写一个**全局热键监听器**（Python 里用 `keyboard` 或 `pynput` 模块）控制一个标志位 `enabled`，脚本里判断 `if enabled:` 才保存。
    
- 或者更简单：直接用命令行参数/配置文件来启动和停止 mitmproxy。
# FAQ
## 支持 python 脚本语言，最好的网络嗅探工具有哪些？ 
要求支持https, 支持生成证书，工具长期稳定维护。列个表比较

| 工具名称                | 支持 Python 脚本                                                                                                                     | 支持 HTTPS 拦截（证书生成）                                                                                                                                                                                 | 开发语言          | 长期维护情况                                                                                                                                                                                      | 适合场景                               |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------- |
| **mitmproxy**       | 完全支持（Python 脚本 + Addon API )                                                                                                     | 完全支持，内置 CA 生成，也支持自定义证书([mitmproxy Documentation](https://docs.mitmproxy.org/stable/?utm_source=chatgpt.com "Introduction"))                                                                       | Python + C    | 活跃维护中（近期更新到 v7、支持 HTTP/2、TCP、WebSocket）([mitmproxy](https://mitmproxy.org/posts/releases/mitmproxy7/?utm_source=chatgpt.com "Mitmproxy 7"))                                                 | 自动化测试、安全研究、API 修改拦截                |
| **proxy.py**        | 支持 Python 插件机制([Proxy.Py](https://proxypy.readthedocs.io/?utm_source=chatgpt.com "Proxy.Py 2.4.11.dev3+gfec682b documentation")) | 支持静态 HTTPS 拦截，可生成或签署证书([Proxy.Py](https://proxypy.readthedocs.io/?utm_source=chatgpt.com "Proxy.Py 2.4.11.dev3+gfec682b documentation"))                                                          | Python        | 开源维护中（Docker 支持）([Proxy.Py](https://proxypy.readthedocs.io/?utm_source=chatgpt.com "Proxy.Py 2.4.11.dev3+gfec682b documentation"))                                                          | 轻量级 HTTP(S) 代理、容器内调试               |
| **Scapy**           | 是（Python API）([Wikipedia](https://en.wikipedia.org/wiki/Scapy?utm_source=chatgpt.com "Scapy"))                                   | 不自动处理 HTTPS 或生成 CA，适合报文级操作                                                                                                                                                                        | Python        | 持续更新（2024最近版本）([Wikipedia](https://en.wikipedia.org/wiki/Scapy?utm_source=chatgpt.com "Scapy"))                                                                                             | 网络探测、构造数据包、协议分析                    |
| **Justniffer**      | 支持通过脚本扩展（包括 Python）([Wikipedia](https://en.wikipedia.org/wiki/Justniffer?utm_source=chatgpt.com "Justniffer"))                   | 不支持 HTTPS 解密（仅 TCP 层抓包）                                                                                                                                                                           | C / Python 扩展 | 活跃，但主要用于性能分析                                                                                                                                                                                | TCP 层流量分析与日志记录                     |
| **Proxyfor (Rust)** | 暂不支持 Python 脚本                                                                                                                   | 支持 HTTPS 拦截与证书生成([Reddit](https://www.reddit.com/r/rust/comments/1hl2e08?utm_source=chatgpt.com "Proxyfor: A Powerful and Flexible Proxy CLI for HTTP(S) and WS(S) Traffic, with TUI and WebUI")) | Rust          | 社区活跃（近期讨论）([Reddit](https://www.reddit.com/r/rust/comments/1hl2e08?utm_source=chatgpt.com "Proxyfor: A Powerful and Flexible Proxy CLI for HTTP(S) and WS(S) Traffic, with TUI and WebUI")) | 跨平台、Web UI 嗅探 HTTP/HTTPS/WebSocket |
### **mitmproxy：最强大**

- **Python 脚本能力最丰富**：几乎所有自定义逻辑都能写脚本实现。
    
- **HTTPS 支持全面**：自动生成 CA，轻松部署 HTTPS 抓包。
    
- **开发活跃、功能先进**：支持 HTTP/2、TCP、WebSocket，跨平台体验一致。
    

### **proxy.py：轻量替代**

- 支持 Python 插件、HTTPS 拦截、证书生成。
    
- 适合快速部署、集成到工具链或容器环境使用。
    

### **Scapy：底层灵活**

- 报文级抓包与构造更灵活，但不具备自动 HTTPS 解密能力，需要自己处理握手等工作。
    

### **Justniffer：TCP 分析为主**

- 简单 TCP 层嗅探与日志生成，适合性能分析，不适合 HTTPS 内容处理。
    

### **Proxyfor：Rust 实现新锐者**

- 支持 HTTPS、WebSocket，有 TUI 和 Web UI 界面，适合跨平台用户和喜欢 Rust 的开发者。
### 总结推荐

- 若你想要**高度脚本化＋HTTPS 自动拦截**，**mitmproxy 是首选**。
    
- 若你偏好轻量部署或内嵌到工具中，**proxy.py 是不错的替代品**。
    
- 报文级精细控制需求，选 **Scapy**；日志/性能分析则看 **Justniffer**；跨平台 GUI 想尝鲜 Rust，**proxyfor** 值得试试。
## 使用proxifier
Proxifier = 强制程序走代理的工具
它本身 不是代理服务器，而是强迫把程序的网络连接 → 转发到代理服务器，让不支持代理的 exe 走代理，还能精确控制“哪个程序走哪个代理”

运行原理
```
目标程序 → Proxifier → mitmproxy → 真实网络
```
- **Proxifier**：强制某个程序走代理    
- **mitmproxy**：提供本地代理 + 抓包 / 解密    
- **浏览器 / App / exe**：被抓对象

配置顺序是相反的：
提前安装配置好mitmproxy证书，启动 mitmweb（--no-http2）
```
mitmweb --listen-host 127.0.0.1 --listen-port 8080 --no-http2
```
- 代理地址：`127.0.0.1`    
- 代理端口：`8080`    
- Web 管理界面：`http://127.0.0.1:8081`

再启动 Proxifier，配置程序走mitweb的代理端口8080

添加一个proxy server
![](images/posts/20250918-4.jpeg)
点check 进行测试
![](images/posts/20250918-5.jpeg)
添加一个app规则，设置为通过https
![](images/posts/20250918-6.jpeg)
![](images/posts/20250918-7.jpeg)
![](images/posts/20251214.jpeg)

mitmproxy + proxifier 一键脚本
```
@echo off
echo Starting mitmproxy...
start "" mitmweb --listen-host 127.0.0.1 --listen-port 8080
timeout /t 3 >nul
start "" "C:\Program Files\Proxifier\Proxifier.exe"
```

最后启动目标程序
## 怎么reset mitmproxy的所有配置
在 **Windows** 上，**reset（重置）mitmproxy 的所有配置** 本质上就是：  
👉 **删除它的配置目录 + 证书 + 插件 + 历史数据**

关闭所有 mitmproxy 进程
```bat
taskkill /F /IM mitmproxy.exe
taskkill /F /IM mitmweb.exe
taskkill /F /IM mitmdump.exe
```
删除配置目录, mitmproxy 的所有配置都在这里👇
```
C:\Users\%USERNAME%\.mitmproxy
```
- CA 证书    
- 配置    
- 脚本    
- http flows    
- state

重新启动（自动重建）
```
mitmweb
```
首次启动会：
- 自动生成新证书    
- 重建默认配置