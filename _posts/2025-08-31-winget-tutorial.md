winget 全称 Windows Package Manager，是微软官方推出的 Windows 应用包管理工具。
它类似于 Linux 的 apt、macOS 的 brew，可以用命令行的方式安装、升级和卸载软件。
- **首次发布**：2020 年（Build 2020 大会）    
- **来源**：微软官方开发并维护，默认内置在 Windows 10 (21H1 之后) 和 Windows 11。    
- **定位**：命令行软件包管理器，简化 Windows 下的软件获取和管理。

`winget` 默认使用微软官方维护的 **应用仓库**（Community Repository），其中包含大量常见软件：浏览器、IDE、开发工具、聊天软件、办公软件等。  
你也可以：
- 添加自己的软件源    
- 使用企业内部源（适合公司 IT 管理）
## 优点
- 官方维护，安全可信。
    
- 自动处理下载安装，无需手动去官网下载。
    
- 支持批量安装（配置 JSON 或脚本，一次安装多个软件）。
    
- 支持 **静默安装**，自动跳过安装向导。
## winget Community Repository
**Community Repository** 就是一个微软托管在 GitHub 上的 **官方应用清单库**，由社区提交、微软审核，保证了丰富度和安全性。`winget` 安装的大多数软件，都是从这个仓库里获取安装信息的。
- 它是 **微软官方维护的一个公共软件清单仓库**，存放在 GitHub 上：  
    👉 [microsoft/winget-pkgs](https://github.com/microsoft/winget-pkgs?utm_source=chatgpt.com)    
- 里面包含了大量软件的 **安装信息（manifest 文件）**，告诉 `winget` 如何下载安装这些软件。

简单来说，它就像一个“大目录”：
- 软件的名字、版本号、发布者    
- 下载地址（通常是软件官网下载的官方链接）    
- 安装方式（静默安装参数、安装包类型 MSI/EXE/MSIX 等）    
- 校验信息（SHA256 哈希，保证下载包没被篡改）
特点

社区驱动 + 微软审核
开发者或用户可以提交新软件或更新版本到 GitHub 仓库，微软会自动验证（安装、校验、安全检测），审核通过后才会加入仓库。

安全性高
安装包下载地址通常指向 软件的官网或可信来源，并强制哈希校验，防止中途被篡改。

覆盖范围广
常见的软件基本都有：浏览器（Chrome/Firefox/Edge）、开发工具（VS Code、Git、Node.js）、聊天软件（Zoom、Slack、QQ）、压缩工具（7zip、WinRAR）等等。

除了 Community Repository
- **微软商店（Microsoft Store）**：winget 也能访问商店里的应用。    
- **自定义源**：你可以添加自己公司或个人的私有源，比如：    
    `winget source add -n MyRepo https://myserver/repo`
## 工作机制
当你执行：
```powershell
winget install Google.Chrome
```
步骤是这样的：
1. `winget` 去 **Community Repository** 里查找 `Google.Chrome` 的 manifest 文件。    
2. 找到之后，里面写着官方下载地址和安装参数。    
3. `winget` 去官网下载 **Chrome 安装包**，并校验哈希值。    
4. 静默安装，无需用户手动点击“下一步”。

比如 **7zip** 的 manifest 文件（简化版）大概长这样：
```yaml
Id: 7zip.7zip
Publisher: 7zip
Name: 7-Zip
Version: 23.01
InstallerType: exe
Installers:
  - Architecture: x64
    InstallerUrl: https://www.7-zip.org/a/7z2301-x64.exe
    InstallerSha256: <哈希值>
    Silent: /S
```
这样 `winget` 就知道：
- 去哪里下载安装包 (`InstallerUrl`)    
- 下载完要验证哈希 (`InstallerSha256`)    
- 用什么参数静默安装 (`/S`)
## **Manifest**
在 **winget** 里，**Manifest** 是软件的 **安装说明文件**，本质上是一个或多个 **YAML 文件**，它告诉 winget：

- 这个应用的名字、版本、发布者是谁    
- 安装包在哪里下载    
- 如何安装（安装参数、静默安装方式等）    
- 卸载方式、依赖、许可信息等等
可以理解为：  
👉 **Manifest = 软件的身份证 + 安装说明书**
### wingetcreate 
wingetcreate 的用途：给那些 不在 Community Repo 的软件 生成 Manifest，让它可以被 winget 管理。
- **wingetcreate = winget 的 Manifest 管理助手**    
- 主要作用：    
    1. 生成 Manifest（本地或提交官方）        
    2. 校验 Manifest 是否合规        
    3. 支持自动化部署和私有源管理
        
- 对企业或高级用户非常实用，普通用户可以借它管理不在 Community Repo 的软件
- `wingetcreate` 会引导你创建 **YAML Manifest 文件**
    
- 你只需要提供软件的：    
    - 安装包 URL 或本地安装器路径（EXE、MSI、MSIX）        
    - 软件名称、发布者、版本号、架构等信息
        
- 它会自动生成：    
    - **Default Locale Manifest**（软件基本信息）        
    - **Version Manifest**（版本信息）        
    - **Installer Manifest**（下载安装信息，包括 SHA256 校验、安装参数等）        

生成后，你就拥有了完整的 Manifest，winget 就能识别这个软件了。

安装 wingetcreate
```powershell
winget install wingetcreate
```
查看所有命令和参数
```powershell
wingetcreate --help
```
创建新 Manifest
```powershell
wingetcreate new "https://xxxxx.com/xxxx-setup.exe"
wingetcreate new --allow-unsecure-downloads "http://localhost:8003/qalculate/qalc.exe"
```
![](images/posts/20250831.jpeg)
![](images/posts/20250831-1.jpeg)
![](images/posts/20250831-2.jpeg)
更新现有 Manifest
```powershell
wingetcreate update MyAppManifest.yaml
```
验证 Manifest
```powershell
wingetcreate validate MyAppManifest.yaml
```
- 检查是否符合官方 winget 规范    
- 检查 SHA256、版本格式、ID 唯一性
提交到官方仓库
```powershell
wingetcreate submit MyAppManifest.yaml
```
- 会在 GitHub 上自动生成 Pull Request    
- 微软审核通过后加入 Community Repo
使用场景
1. **个人 / 企业内部软件**    
    - 自研工具、内部安装包，通过 Manifest 管理
        
2. **官方仓库没有的软件**    
    - 免费软件、试用版、付费软件
        
3. **批量安装 / 自动化部署**    
    - 将多个 Manifest 放入私有源，一键 winget install
        
4. **教育 / 培训环境**    
    - 为实验室或教室准备统一的软件包
### 如果软件本身没有setup安装程序，wingetcreate能用吗
什么情况算“没有 setup 安装程序”
1. **绿色免安装程序**（只是一堆 exe/dll 文件直接运行）    
2. **单文件应用**（比如 Notepad++ 的 portable 版）    
3. **源码 / 脚本**（.py, .jar, .bat 等，需要编译或运行环境）

可以用的情况
- **绿色便携软件 / 单文件 exe**    
    - 虽然没有安装过程，但你依然可以用 wingetcreate 创建 Manifest。        
    - 方法：把 exe 文件放在一个 **可访问的 URL 或私有源** 上，让 Manifest 指向它。        
    - 在 Installer Manifest 里：
# 常用命令
```powershell
# 搜索软件
winget search <软件名>

# 安装软件
winget install <软件名或ID>

# 卸载软件
winget uninstall <软件名或ID>

# 升级单个软件
winget upgrade <软件名或ID>

# 升级所有可更新的软件
winget upgrade --all

# 查看已安装软件
winget list

# 显示软件详细信息
winget show <软件名或ID>
```
例子
```powershell
# 搜索 Google Chrome
winget search google chrome

# 安装 Google Chrome
winget install Google.Chrome

# 升级所有已安装的程序
winget upgrade --all
```
**批量安装**  可以一次性安装多个：

`winget install Google.Chrome Mozilla.Firefox 7zip.7zip VLC.VLC`

**批量升级**

`winget upgrade --all`

## **导出/导入软件清单**

- 导出：
    
    `winget export -o apps.json`
    
- 导入（重装系统后一键恢复）：
    
    `winget import -i apps.json`
当你执行：

`winget export -o apps.json`

`winget` 会逐个扫描本机已安装的软件，然后尝试在 **可用源（Community Repo + Store + 自定义源）** 中找到对应的 manifest。

- 如果 **能找到**：会写进 `apps.json`。    
- 如果 **找不到**：就提示 `"Installed package is not available from any source"`，并跳过。
    

这些软件就不会被导出，也就无法在 `import` 时自动安装。
# 常见软件安装清单
### 🌐 浏览器

- Google.Chrome
    
- Mozilla.Firefox
    
- Microsoft.Edge
    
- Opera.Opera
    
- Brave.Brave
    

### 🛠️ 开发工具

- Microsoft.VisualStudioCode
    
- Git.Git
    
- GitHub.GitHubDesktop
    
- Python.Python.3.12 （或最新版本）
    
- Nodejs.NodeJS
    
- Oracle.JavaRuntimeEnvironment
    
- EclipseAdoptium.Temurin.17.JDK
    
- Docker.DockerDesktop
    
- Postman.Postman
    
- JetBrains.IntelliJIDEA.Community
    
- JetBrains.PyCharm.Community
    
- JetBrains.WebStorm
    
- SublimeHQ.SublimeText.4
    
- Notepad++.Notepad++
    

### 💬 聊天通讯

- Tencent.QQ
    
- Tencent.WeChat
    
- Telegram.TelegramDesktop
    
- Discord.Discord
    
- SlackTechnologies.Slack
    
- Zoom.Zoom
    

### 📦 压缩工具

- 7zip.7zip
    
- WinRAR.WinRAR
    
- PeaZip.PeaZip
    

### 📹 多媒体

- VideoLAN.VLC
    
- Spotify.Spotify
    
- OBSProject.OBSStudio
    
- Audacity.Audacity
    

### 📂 云存储

- Google.Drive
    
- Dropbox.Dropbox
    
- Microsoft.OneDrive
    
- Mega.MEGASync
    

### 🖥️ 系统工具

- Microsoft.PowerToys
    
- Microsoft.WindowsTerminal
    
- Everything.Everything
    
- CrystalDewWorld.CrystalDiskInfo
    
- CrystalDewWorld.CrystalDiskMark
    
- Rufus.Rufus
    
- Oracle.VirtualBox
    
- VMware.WorkstationPlayer
# 搭建自己的私有winget
winget **不仅能用官方 Community Repo，还能接入自定义源**。
## 1. **最简单：用文件夹 + 本地源**

- 你可以准备一个文件夹，里面放自己的 **manifest YAML 文件** 和安装包。
    
- 然后告诉 winget 去这个目录作为源。
    
```powershell
# 添加一个本地源
winget source add -n MyRepo -t msstore -a C:\MyWingetRepo
```
缺点：只能本机用，不能远程分发。

## 2. **HTTP/HTTPS 静态网站源**

- 把 manifest 文件和安装包放到一个 Web 服务器（IIS, Nginx, Apache, GitHub Pages 都行）。    
- Winget 支持 HTTP/HTTPS 作为源。   

```powershell
winget source add -n MyRepo https://myserver.example.com/winget/
```

这样只要客户端能访问，就能用你的私有仓库。
### 3. **使用微软提供的 winget-pkgs 工具链**

微软推荐用 [winget-create](https://github.com/microsoft/winget-create?utm_source=chatgpt.com) 来快速生成 manifest：

```powershell
wingetcreate new <InstallerURL>
```

会自动帮你生成 YAML 文件，包含：
- 软件 ID    
- 版本号    
- 安装器 URL    
- SHA256 校验    
- 静默安装参数
然后你只需要把这个 manifest + 安装包放到你的私有源里。
## 4. **企业级方案（推荐给公司环境）**

微软官方有个产品 **WinGet Rest Source**，能搭建一个 **REST API 仓库**，用于企业内部软件分发。

- GitHub 项目地址：[https://github.com/microsoft/winget-cli-restsource](https://github.com/microsoft/winget-cli-restsource?utm_source=chatgpt.com)
    
- 功能：
    
    - REST API 提供 manifest
        
    - 可接入 Azure Blob / 内网存储
        
    - 支持签名和安全校验
        

这就类似一个「公司内部的官方 Community Repo」，企业 IT 部门可以管理所有软件版本。
## 📊 使用流程（简化版）
### 搭建好服务器，把安装包路径准备好
准备一台 Windows / Linux 服务器（可以用 IIS / Nginx / Apache / GitHub Pages）
安装包（比如 `7z2301-x64.exe`）
工具：`wingetcreate`（用于生成 manifest）
### 创建manifest
**写 manifest**（用 wingetcreate 或手动写 YAML）    

假设你要把 **7-Zip 23.01** 放进私有仓库：
wingetcreate new https://www.7-zip.org/a/7z2301-x64.exe
它会问你：
- 软件 ID（比如：`MyCompany.7zip`）    
- 版本号（比如：`23.01`）    
- 发布者（Publisher）    
- 名称（Name）    

然后会自动下载并计算 SHA256，生成 **manifest YAML 文件**。
## 把manifest**放到仓库**（本地文件夹、HTTP 静态目录、REST API 服务）    
在服务器上准备一个目录，例如 `/var/www/winget/`（Nginx/Apache） 或 `C:\inetpub\wwwroot\winget\`（IIS）。
放置以下文件：
```python
winget/
 ├─ manifests/
 │   └─ m/
 │      └─ mycompany/
 │          └─ 7zip/
 │              └─ 23.01/
 │                  ├─ MyCompany.7zip.installer.yaml
 │                  ├─ MyCompany.7zip.locale.en-US.yaml
 │                  └─ MyCompany.7zip.yaml
 └─ index.json   （可选，用于搜索加速）
```
配置 Web 服务器, Nginx 配置示例
```nginx
server {
    listen 80;
    server_name myrepo.local;

    location /winget/ {
        root /var/www/;
        autoindex on;
    }
}
```
访问测试：
```http
http://myrepo.local/winget/manifests/m/mycompany/7zip/23.01/MyCompany.7zip.yaml
```
### **客户端添加源**    
在需要使用的电脑上执行：
    `winget source add -n MyRepo https://myserver/winget/`
    winget source add -n MyRepo -t msstore -a http://myrepo.local/winget/
检查源是否添加成功：
```
winget source list
```
### **安装测试**    
搜索软件：
```
winget search MyCompany.7zip
```
安装：
```
winget install MyCompany.7zip
```
如果能正常下载安装，说明你的私有仓库已经搭建成功 

- **个人/小团队**：直接用静态文件夹/HTTP 目录，简单易行。    
- **公司/企业**：用 **WinGet Rest Source**，可集中管理、权限控制、自动更新。


# FAQ
## 如果软件不在官方仓库（Community Repo + Store）会怎么样？
如果软件不在官方仓库（Community Repo + Store）里，`export` 不会收录，`import` 也不会安装。自动跳过。
解决方法：
手动添加源（公司内网仓库、第三方 repo）
用 Chocolatey / Scoop 之类的工具补充
自己写 manifest 提交到 winget-pkgs（微软会自动构建和审核）

### winget export 的工作原理
winget export -o apps.json 会扫描你电脑上 已安装的软件
然后尝试在 已注册的源（默认 Community Repo + MS Store + 自定义源）里找到对应的软件 Manifest ID
只有 找到 Manifest 的软件 才会写入 apps.json

如果软件 没有对应的 Manifest（不在 Community Repo / Store / 自定义源中）不会把它写入 apps.json
也就是说，`import` 时无法自动安装这些软件，需要手动处理
export 会显示：
`Installed package is not available from any source: <软件名>`

补救办法：
1. 手动记录这些软件，重装系统后手动安装    
2. 给这些软件写 **自定义 Manifest**，放到 **私有源**，这样就能被 `export/import` 管理
## Community Repo 都是免费软件吗？
**不是所有 Community Repo 里的软件都是免费软件**，但绝大部分是 **免费 / 免费版**。
- **Community Repo ≠ 全部免费软件库**    
- 绝大部分是 **免费软件 / 开源软件 / 商业软件的免费版**    
- **商业软件的完整版**也可能在里面，但安装后你要自己激活或登录账号
### 1. **主要是免费软件**

- **常见的开源/免费软件**：
    
    - 7zip、VLC、Notepad++、Audacity、OBS Studio
        
- **常见的免费版软件**（带付费高级版）：
    
    - WinRAR（试用版 + 付费）
        
    - Zoom（基础功能免费）
        
    - Slack（团队版免费，企业版付费）
        
    - Spotify（有免费版，有 Premium）
        

这些都在 Community Repo 里能找到。

---

### 2. **也有商业软件**

一些商业公司会主动提交自家软件的 manifest（方便分发），比如：

- **JetBrains 系列 IDE**（IntelliJ IDEA、PyCharm、WebStorm）
    
    - Community 版本免费
        
    - Ultimate 版本付费
        
- **Oracle JDK**（部分版本需商业授权）
    
- **Adobe 系列部分工具**（部分可见，但大多数不会进 repo，更多走 Creative Cloud）
    

这些 manifest 通常指向 **官方下载地址**，安装后需要你自己输入 License 或订阅账号。

---

### 3. **不会出现的类别**

- **破解软件、盗版软件**：绝对不会进仓库。
    
- **大部分纯收费软件**：如果厂商不提供公开安装包，就不会在 repo 出现。
    
- **驱动、系统补丁**：这些由 Windows Update / 厂商工具维护，不走 winget。
## Chocolatey / Scoop 是什么
这两个和 **winget** 类似，都是 Windows 平台上的 **软件包管理器**。
### 🍫 Chocolatey

- **发布**：2011 年，比 winget 早很多。
    
- **定位**：最早、最流行的 Windows 包管理器之一。
    
- **实现方式**：基于 **PowerShell + NuGet**。
    
- **特点**：
    
    - 软件仓库巨大（几千上万个包），很多开发/运维工具第一时间会上架。
        
    - 不只是软件安装，还能安装 **系统组件、驱动、补丁**。
        
    - 很多公司内部会搭建私有 Chocolatey 仓库，用来自动化部署。
        
    - 但有个问题：**安装包质量参差不齐**，因为很多 manifest 是社区维护的，有些会直接从第三方站点下载，不如 winget 那么严格安全。
### 🥤 Scoop

- **发布**：2013 年，由开源社区维护。
    
- **定位**：轻量、简洁的 Windows 包管理器。
    
- **实现方式**：基于 **PowerShell 脚本**，安装时不依赖管理员权限。
    
- **特点**：
    
    - 安装路径干净：默认把所有软件装在用户目录下 `~/scoop`，不会乱写注册表。
        
    - 更新和卸载都很轻量，和 Linux 下的 `brew` 很像。
        
    - 很适合开发者：Node.js、Python、Git、ffmpeg、各种 CLI 工具，都能用 scoop 快速装。
        
    - 但它的软件范围相对小众，更偏向开发工具，不太覆盖日常软件（比如 Office、QQ、微信这类就没

|工具|发布者|软件范围|安全性|适用人群|
|---|---|---|---|---|
|**winget**|微软官方|常见主流软件为主|✅ 官方审核|普通用户 & 开发者|
|**Chocolatey**|社区 + 商业版|软件最多（系统组件也有）|⚠️ 质量参差不齐|DevOps / 企业部署|
|**Scoop**|开源社区|开发工具、CLI 工具多|✅ 干净轻量|开发者 / 极客用户|
## winget怎么下载offline程序
有的软件只有web installer
![](images/posts/20250831-3.jpeg)
![](images/posts/20250831-5.jpeg)![](images/posts/20250831-6.jpeg)