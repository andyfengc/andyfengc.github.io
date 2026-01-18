# Introduction
**n8n** 是一个可视化的、低代码的工作流自动化工具（workflow automation platform）。n8n 是一个可连接各种 API、数据库、文件、AI 模型的“自动化编排系统”，能让你在可视化界面中搭建复杂逻辑流程。

| 概念                  | 说明                  | 举例                                              |
| ------------------- | ------------------- | ----------------------------------------------- |
| **Workflow（工作流）**   | 一组节点（Nodes）组成的自动化流程 | “视频上传后 → 调用AI生成字幕 → 上传到YouTube”                 |
| **Node（节点）**        | 执行单一功能的模块           | HTTP Request、IF 条件、Function 脚本、Google Drive 上传等 |
| **Trigger（触发器）**    | 触发工作流的起点            | Webhook、定时器、文件监控、数据库变化                          |
| **Execution（执行）**   | 工作流运行一次             | 每次触发时 n8n 会启动一次执行实例                             |
| **Data (Item)**     | 节点之间传递的数据对象         | JSON 格式数据，可被后续节点读取、修改                           |
| **Parameter（参数）**   | 每个节点的输入配置           | URL、Header、变量表达式等                               |
| **Expression（表达式）** | `{{ }}` 包裹的动态变量     | `{{ $json["filename"] }}`                       |
# install
3种方式
## 本地部署
### use command 
```
docker volume create n8n_data

docker run -it --rm \
 --name n8n \
 -p 5678:5678 \
 -e GENERIC_TIMEZONE="<YOUR_TIMEZONE>" \
 -e TZ="<YOUR_TIMEZONE>" \
 -e N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true \
 -e N8N_RUNNERS_ENABLED=true \
 -v n8n_data:/home/node/.n8n \
 docker.n8n.io/n8nio/n8n
```
### from yaml，推荐
创建文件夹 
```
mkdir n8n-dev
cd n8n-dev
```
创建yaml文件
```yaml
version: '3.8'

volumes:
  n8n_data:

services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: always
    # 端口映射：宿主机 5678 端口 -> 容器 5678 端口
    ports:
      - '5678:5678'
    environment:
      # --- 关键配置 ---
      # 1. 设置时区，确保定时任务准确
      - GENERIC_TIMEZONE=Canada/Eastern
      # 2. 设置 Webhook 的基础 URL (非常重要!)
      #    - 如果在本地测试，使用 http://localhost:5678/
      #    - 如果在云服务器上部署，请替换为您的公网域名或IP:端口，例如：https://your.domain.com/
      - WEBHOOK_URL=http://localhost:5678/ 
      # 3. 启用基本权限以限制访问
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=test # 替换为您希望的用户名
      - N8N_BASIC_AUTH_PASSWORD=12345678 # 替换为您希望的密码
      # ----------------
    volumes:
      # 持久化存储工作流数据、凭证和设置
      - n8n_data:/home/node/.n8n
      # 挂载 C:/workplace/n8n-data 到容器 /data
      - "C:/Workspace/n8n-share:/share"
```
在包含 `docker-compose.yml` 文件的目录下，执行以下命令. 此命令将拉取 n8n 镜像，并以后台模式启动容器：
```
docker compose up -d
```
> up: 启动服务
> -d: 分离模式 (Detach Mode)告诉 Docker 在后台（detached mode）运行容器，并立即打印出容器 ID，然后释放您的命令行终端。
移除容器
```
docker-compose down
```
查询环境变量
```
docker exec -it n8n printenv | Select-String NODE_FUNCTION
```

启动临时容器
```powershell
docker run -it --rm --name n8n -p 5678:5678 -v n8n-workflow_n8n_data:/home/node/.n8n -v "C:/workplace/n8n-share:/share" docker.n8n.io/n8nio/n8n

# 启动后，n8n 在容器内能访问路径 /share/xxx.txt。
docker exec -it n8n sh
ls /share
```
### use ui
create  a volume 
![](images/posts/20251105-1.jpeg)
download  image 
![](images/posts/20251105-2.jpeg)
![](images/posts/20251105-3.jpeg)
![](images/posts/20251105-4.jpeg)
#### Starting n8n
click run > optional settings > run
新建空目录> host path
![](images/posts/20251105-6.jpeg)
![](images/posts/20251105-5.jpeg)
![](images/posts/20251105-7.jpeg)
![](images/posts/20251105-9.jpeg)
![](images/posts/20251105-8.jpeg)
## 服务器部署
## 官方云服务
约20刀/月
# Workflow
工作流是**一系列相连的节点**，它定义了一个完整的自动化流程，从触发开始，到数据的获取、处理、转换，直到最终的操作（例如发送邮件、更新数据库等）。
## 运行机制
1️⃣ **Trigger 节点激活**  
→ 例如 Webhook 收到外部请求、Cron 定时任务触发

2️⃣ **数据进入管线**  
→ 形成 `$json` 数据结构（数组或对象）

3️⃣ **节点按顺序执行**  
→ 每个节点可以：
- 访问前一个节点的输出    
- 动态使用变量表达式（如 `{{ $json["id"] }}`）    

4️⃣ **可条件分支、并行执行**  
→ 支持多条路径同时运行

5️⃣ **执行结束后可保存结果 / 触发下一流程**
# Node
## **触发节点（Trigger Nodes）**
> 工作流的启动点
常见类型：
- 🕒 **Cron Trigger**（定时任务）    
- 🌐 **Webhook Trigger**（接收外部事件）    
- 📩 **IMAP Email Trigger**（邮箱监控）    
- 🧠 **AI Event Trigger**（例如OpenAI或自定义WebHook）    

## **动作节点（Action Nodes）**
> 执行实际操作
常见类型：
- **HTTP Request**：访问任意API    
- **Execute Command**：执行Shell/Python/PowerShell命令    
- **Function / Function Item**：运行自定义JavaScript逻辑    
- **Google Drive / Notion / Airtable / GitHub / Slack / Telegram** 等连接节点

## **逻辑控制节点（Logic & Flow Control）**
> 决策、循环、条件分支
- `IF`（条件判断）    
- `Switch`（多分支）    
- `Merge`（汇合多个输入）    
- `Split In Batches`（分批执行）    
- `Wait`（等待一定时间）
    
## **数据处理节点**
> 转换和结构化数据
- `Set`：创建或重命名字段    
- `Rename Keys`：改字段名    
- `Code`：用JS处理数据结构    
- `Spreadsheet File`：处理CSV/XLSX    
- `JSON Parse / Stringify`
## AI 节点 (AI Nodes)：
用于集成 AI 模型和服务，例如 OpenAI 的 GPT 模型，实现智能决策和数据处理。
## 连接线 (**Connections**)
节点之间通过连接线相连，表示**数据流和执行顺序**。数据从一个节点流向下一个节点，每个节点对数据进行处理或操作。
# Data
## 变量与表达式系统
n8n 的每个节点都可用 **表达式（Expression）** 读取上游数据：

|表达式|含义|
|---|---|
|`{{$json["filename"]}}`|当前项中的字段|
|`{{$node["HTTP Request"].json["id"]}}`|获取指定节点输出|
|`{{$workflow.name}}`|工作流名称|
|`{{$now}}`|当前时间|
|`{{$env["API_KEY"]}}`|读取环境变量|
# demos
## AI视频自动发布
自动从数据库中取素材 → 生成视频 → 上传YouTube → 发Discord通知
节点链：
```
1. Cron Trigger（每天上午9点）
2. MySQL（读取未发布任务）
3. HTTP Request（调用你的视频生成API）
4. Wait（等待生成完成）
5. HTTP Request（上传到YouTube）
6. Discord Node（发送通知）
```
## AI内容批处理（文本→语音→视频）
```
Webhook Trigger
  ↓
Split In Batches（批量文本）
  ↓
OpenAI Node（生成TTS脚本）
  ↓
HTTP Request（调用语音合成API）
  ↓
Execute Command（调用FFmpeg合成视频）
  ↓
Google Drive（上传结果）
```

# reference 
https://docs.n8n.io/
https://docs.n8n.io/hosting/
https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List
https://github.com/Zie619/n8n-workflows
https://zie619.github.io/n8n-workflows/