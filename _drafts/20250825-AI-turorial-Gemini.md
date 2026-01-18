---
layout: post
title: AI Tutorial Gemini
author: Andy Feng
---
# 申请API KEY
https://ai.google.dev/gemini-api/docs/api-key
**Google Cloud 项目默认没启用 “Generative Language API”**
- 创建 API Key 前，你的项目必须启用 Gemini API（Generative Language API / Vertex AI Generative AI）。    
- 如果没有手动启用，就会报权限错误 Failed to generate API key: permission denied. Please try again.。

在 Google Cloud Console → Manage Resources 里新建一个项目，再去 AI Studio 尝试生成 API Key。
打开 Google Cloud Console → APIs & Services → Library 
https://console.cloud.google.com/apis/library
搜索 “Generative Language API” 或 “Vertex AI API”
如果没启用，点击 Enable。
![](images/posts/2025-08-25-AI-turorial-Gemini-1.jpeg)
![](images/posts/2025-08-25-AI-turorial-Gemini-2.jpeg)
![](images/posts/2025-08-25-AI-turorial-Gemini-3.jpeg)

**Google Cloud Console → Credentials → Create Credentials** 这一步，系统会问：
> **What data will you be accessing?**
> > - **User data**
> - 这是针对 **访问用户的私人信息**（例如 Gmail 邮件、Drive 文件、联系人信息）的场景。    
- 选择它时，Google 要求你走 **OAuth 2.0 授权流程**，用户必须点击同意授权，才能让应用访问他们的数据。    
- 会生成 **OAuth Client ID**。    
- 适用场景：做一个 App，需要访问某个用户的 Google Drive 文件、Gmail 等。 
👉 对 Gemini API（文本生成、图像生成）来说 **完全不需要 User data**。

> - **Application data**
>- 指访问 **应用本身的数据**，而不是个人用户的私人数据。    
- 这种情况会生成 **Service Account**（服务账号），应用可用该账号调用 API。    
- 适用场景：后台服务调用 AI API、数据库 API，或你只需要调用 **Gemini / Generative Language API** 来生成文本、图像，而不需要访问用户的 Gmail、Drive 等。  
👉 **Gemini API 用 application data** 就够了。
![](images/posts/2025-08-25-AI-turorial-Gemini-4.jpeg)

在 **Google Cloud Console → 创建 Service Account** 这一步。  
系统会问你给这个 Service Account 分配 **角色 (role)**。
需要让 Service Account 能够调用 Gemini API（Generative Language API），
需要具备 **Vertex AI 相关的权限**。
如果找不到 Vertex AI 角色：
- 可以临时用 **`Editor` (roles/editor)**，它有几乎所有权限，但不安全，适合测试。    
- 后续建议收紧权限，改成 `Vertex AI User`。
![](images/posts/2025-08-25-AI-turorial-Gemini.jpeg)

https://console.cloud.google.com/apis/library/cloudaicompanion.googleapis.com
enable
![](images/posts/2025-08-25-AI-turorial-Gemini-5.jpeg)
![](images/posts/2025-08-25-AI-turorial-Gemini-6.jpeg)
![](images/posts/2025-08-25-AI-turorial-Gemini-7.jpeg)

如果在ai studio里面无法创建apikey
https://console.cloud.google.com/iam-admin/iam
检查自己的role，确认是owner
![](images/posts/2025-08-25-AI-turorial-Gemini-8.jpeg)
然后在console里面创建key
回到 Google Cloud Console → APIs & Services → Credentials
点击 Create Credentials → API Key
正常情况下就能生成 AIza... 开头的 Key 了
https://console.cloud.google.com/apis/credentials
![](images/posts/2025-08-25-AI-turorial-Gemini-9.jpeg)

**Google Cloud Console API Key** 和 **AI Studio API Key** 的区别

| 特性          | Google Cloud Console API Key                         | AI Studio API Key                                  |
| ----------- | ---------------------------------------------------- | -------------------------------------------------- |
| **生成位置**    | Google Cloud Console → APIs & Services → Credentials | AI Studio 界面 → API Keys                            |
| **权限来源**    | 继承所在 GCP 项目已启用的 API 权限                               | AI Studio 管理，专门绑定 Gemini / Generative Language API |
| **可调用 API** | 项目中启用的所有 API（如 Gemini、Maps 等）                        | 仅支持 Gemini / Generative Language API               |
| **通用性**     | 高，可在任何支持 API Key 的环境使用                               | 低，仅限 AI Studio 或支持 Studio Key 的 SDK 调用             |
| **安全性控制**   | 可设置 API 限制、IP/Referrer 限制                            | 限制较少，主要由 Studio 管理                                 |
| **典型用途**    | 后端服务、脚本调用、生产环境                                       | 快速测试、AI Studio 内部演示                                |
| **IAM 关联**  | 间接继承项目 IAM 权限（Key 只能访问启用的 API）                       | 不直接继承 IAM 权限                                       |
| **适合场景**    | 稳定调用、可控、生产使用                                         | 快速测试、演示或 Studio 内部使用                               |
|             |                                                      |                                                    |
## API Key、Service Account 和 Project Owner 的权限、适用场景、安全风险

|特性|API Key|Service Account|Project Owner|
|---|---|---|---|
|**能做的事**|- 调用项目内已启用的 API- 生成文本、调用模型、访问 Cloud Endpoints|- 代表项目调用 API- 可授予细粒度 IAM 角色（如 Vertex AI User）|- 管理整个项目- 开启/关闭 API- 管理 IAM 用户- 生成 API Key / Service Account|
|**典型用途**|- 前端快速测试- 后端调用单一 API|- 后端服务或脚本调用 Gemini- 自动化任务|- 项目管理员|
|**安全风险**|- Key 被泄露可能被滥用- 无法限制细粒度操作|- JSON Key 被泄露，攻击者可按角色权限操作- 需妥善保管|- 权限过大，一旦泄露可造成严重损失|
|**备注**|- 可以限制 API 类型或 IP/Referrer|- 角色可控制调用权限- 可配合 Vertex AI SDK 使用|- 只给少数可信用户使用- 不建议直接用于日常 API 调用|
1. **API Key**    
    - 快速、方便        
    - 只能访问项目启用的 API        
    - 建议加 API/IP 限制
        
2. **Service Account**    
    - 可细粒度控制        
    - 更安全、适合后端调用
        
3. **Project Owner**    
    - 管理员权限，功能最强        
    - **不适合直接用于调用 API** 
## **API Key、Service Account、Project Owner** 与 **Console Key vs AI Studio Key** 区别
**一张图对比所有调用方式的权限、用途、安全性**，让整个调用体系一目了然

|特性|Console API Key|AI Studio API Key|Service Account|Project Owner|
|---|---|---|---|---|
|**生成位置**|Google Cloud Console → Credentials|AI Studio 界面 → API Keys|Google Cloud Console → Service Accounts → Create Key|Google Cloud Console → IAM → Add Member|
|**权限来源**|项目内已启用 API|AI Studio 管理，专门绑定 Gemini|IAM 角色授予的权限（如 Vertex AI User）|拥有整个项目的所有权限|
|**可调用 API**|项目中启用的所有 API（Gemini、Maps 等）|仅 Gemini / Generative Language API|受分配角色控制，调用对应 API|项目中所有 API，可管理 IAM/资源|
|**通用性**|高，可在任何支持 API Key 的环境使用|低，仅限 AI Studio 或 SDK 支持|高，可在服务端、脚本中通用|高，管理权限，但不适合直接调用 API|
|**安全性控制**|API 限制、IP/Referrer 限制|Studio 管理，限制少|JSON Key 需妥善保管，角色可细粒度控制|权限过大，一旦泄露风险严重|
|**典型用途**|后端调用、生产环境、快速测试|Studio 内部测试、演示|后端服务调用、自动化任务|项目管理、资源分配、权限管理|
|**IAM 关联**|间接继承项目 IAM 权限（访问已启用 API）|不直接继承 IAM 权限|完全依赖分配的 IAM 角色|拥有所有 IAM 权限|
|**适合场景**|调用 Gemini、Maps 等 API，稳定可控|快速测试 Gemini 模型|后端调用 Gemini、批量任务、安全可控|管理项目、启用/禁用 API、分配权限|
1. **Console API Key**：通用、灵活，可在后端或外部调用 Gemini，推荐用于生产和测试环境。    
2. **AI Studio API Key**：快速、内部测试专用，不通用，不建议生产环境使用。    
3. **Service Account**：安全、可控、适合后端调用和自动化任务，可精细控制权限。    
4. **Project Owner**：管理项目必备权限，但不适合直接调用 API，安全风险大。
# FAQ

# References 
[